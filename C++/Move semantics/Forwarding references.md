Суть проблемы:

```c++
template <typename... Args>
void emplace_back(const Args&... args); // копирование rvalue
// перегрузка не спасает, так как переменное число аргументов

template <typename U, typename... Args>
void contruct(U* ptr, Args&... args) //никакой тип не подходит для args
```

$$ Решение $$
```c++
void construct(U* ptr, Args&&... args) {
  new(ptr) U(std::forward<Args>(args)...);
}
// многоточие через запятую повторяет паттерн!!!
void emplace_back(Args&&... args) {
  alloc_.construct(ptr, std::forward<Args>(args)...);
}
```

> **великий костыль** rvalue ссылка, являясь шаблонным параметром ***функции*** вида T&&, на самом деле универсальная ссылка

## Правила вывода шаблонного типа
```c++
template <typename T>
void h(T&& x);

template <typename T>
void g(T& x);

template <typename T>
void f(T x);

// работает правила - навешивание & на T, если вызвались от lvalue в f(T&&
int main() {
  int x = 0;
  f(x); // T = int, x = int
  g(x); // T = int, x = int&
  h(x); // T = int&, x = int&
  int& y = x;
  f(y); // T = int, x = int
  g(y); // T = int, x = int&
  h(y); // T = int&, x = int&
  int&& z = std::move(x);
  f(z); // T = int, x = int
  g(z); // T = int, x = int&
  h(z); // T = int&, x = int&

  f(std::move(x)); // T = int, x = int
  g(std::move(x)); // CE
  h(std::move(x)); // T = int, x = int&&
}
```

**reference collapsing rules**
- & + & = &
- && + & = &
- & + && = &
- && + &&  = &&
#### Создание структуры с конструктором от переменного количества строк (как rvalue, так и lvalue)
```c++
struct S {
  template <typename... Args>
  S(Args&&... args) {
    (static_assert<is_convertible_v<Args, std::string>> , ...);
    v.reserve(sizeof...(args));
    (v.push_back(std::forward<Args>(args)), ...);
  }
  vector<string> v;
}
```

## Реализация std::forward
```c++
template <typename T
T&& forward(T&& value) {
  return value;
}
// std::forward<Args>. Args - изначально rvalue, передаем в forward args (lvalue)
// то есть пытаемся инициализировать T&& от lvalue

template <typename T>
T&& forward(T& value) {
  return value;
}
// пытаемся инициализировать возвращаемое значение T&& от value типа  T&

template <typename T>
T&& forward(std::remove_reference_t<T>& value) {
  return static_cast<T&&>(value);
}
```
Причины принимаемого типа:
1. Компилятор не сможет неявно вывести тип аргумента, и тогда move применялся бы неправильно. Пример: передаем int&, компилятор выводит тип args как int => обращаемся с lvalue как с rvalue
2. есть forward от rvalue

```c++
template <typename T>
T&& forward(std::remove_reference_t<T>&& value) {
  static_assert(!std::is_lvalue_reference_v<T>);
  return static_cast<T&&>(value);
}

// forward<Args>(f<Args>(args)...);
```

##  Пример с конструкторами

```c++
template <typename T>
class Optional {
 public:
  Optional() = default;
  // навешиваем const
  Optional(const Optional&) {
    std::cout << "A" << std::endl;
  }
  // принимаем Rvalue ссылку
  // Optional(Optional<U>&) - будет B, частный случай общего 
  template <typename U>
  Optional(Optional<U>&&) {
    std::cout << "B" << std::endl;
  }
  // точное соотвествие
  template <typename U>
  Optional(U&&) {
    std::cout << "C" << std::endl;
  }
}

int main() {
  Optional<int> a;
  Optional<int> b(a); // C
}
```