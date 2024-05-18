```c++
int main() {
  std::tuple t{2, 5, "abc"};
  auto& d = std::get<1>(t); // обращение по индексу 1
  // structured bindings since c++17
  auto& [x, y, z] = t; // распаковка кортежа

  int x, y;
  std::tuple<char, short> coord(6, 9);
  std::tie(x, y) = coord; // implicit conversions are allowed
}
```

**Structured bindings** применимы к:
1. array
2. tuple-like type (определена tuple_size<\E>::value, tuple_element)
3. структура которая опускает агрегатную инициализацию

- Make tuple  **отбрасывает ссылки**, принимает по универсальной ссылке
- tie **не отбрасывает**, принимает по lvalue ссылке
-  forward_as_tuple принимает по универсальной, **не отбрасывает ссылки**

```c++
template <typename... Args>
strcut S {
  S(const Args&...) : values(std::make_tuple(values...)) {} // UB
  std::tuple<const Args...> values;
}
```

## idea of tuple implementation

```c++
// 1 способ хранить аргументы
template <typename Head, typename... Tail>
struct tuple {
  Head head;
  tuple<Tail...> tail;
}

// 2 (на стеке переменные хранятся в обратном порядке)
template <typename Head, typename... Tail>
struct tuple: tuple<Tail...>{
  Head head;
}


template <typename Head, typename... Tail>
struct tuple {
  Head head;
  tuple<Tail...> tail;
}

// tuple можно конкатенировать
```

## std::ignore
```c++
std::tie(iter, std::ignore) = set.insert(value);
auto [iter, _] = set.insert(value); // since c++23
// можно использовать несколько _ в одной области видимости
```

std::piecewise_construct - позволяет принимать tuple в конструктор пары

## std::enable_if
позволяет "отключать" версии перегрузки в зависимости от compile time условий

```c++
template <typename T, std::enable_if_t<условие, bool> = true> // присвоили значение по умолчанию
```