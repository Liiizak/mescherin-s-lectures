- auto выводит тип с помощью шаблонной подстановки
- отбрасывает const и ссылки
```c++
int main() {
  auto x = 5;
  auto&& y = x; // OK, auto&& - универсальная ссылка
}

template <typename Container>
void f(Container&& container) {
  for (auto&& value: container) // make sense, позволяет правильно обрабатывать как и rvalue, так и lvalue случаи
}
```

- auto может использоваться в качестве типа возвращаемого параметра функции

```c++
template <typename T>
auto g(T x) {
  return ++x;
}

int main() {
  auto x = g(1); // return auto = int
  auto& x = g(1); // CE, rvalue initialize lvalue ref
  auto&& x = g(1); // OK
}


template <typename T>
auto& g(T x) {
  return ++x;
}

int main() {
  auto x = g(1); // копируем
  auto& x = g(1); // битая ссылка
  auto&& x = g(1); // битая ссылка, ub
}
```

Возвращение разных типов в зависимости от runtime условия - **CE**
От compile-time условия - **OK**

```c++
template <typename T>
auto g() {
  if constexpr (std::is_same_v<T, int>)
    return 0;
  else:
    return 1u;
}

```

```c++
template <typename T>
void h(t) {}

int main() {
  auto lst = {1, 2, 3}; // auto = initializer_list, OK
  h(t); // CE
}
```

## trailing return type
```c++
template <typename T>
std::remove_reference_t<T>&& move(T&& value) {
  return static_cast<std::remove_reference_t<T>&&>(value);
} // выглядит вообще не cool

template <typename T>
auto move(T&& value) -> std::remove_reference_t<T>&& {
  return static_cast<std::remove_reference_t<T>&&>(value);
} // тоже не особо cool по-моему...
```

## auto в принимаемых типах (since c++20)

```c++
void f(auto&& x) {...}
// equal to
template <typename T>
void f(T&& x) {...}
```

