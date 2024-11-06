**Substitution failure is not an error**
```c++
template <typename T>
auto f(T value) -> typename T::value_type {
  std::cout << 1;
}

// c-style variadic function
void f(...) {
  std::cout << 2;
}

int main() {
  f(0); // 2
  std::vector<int> v;
  f(v); // 1
}
```

==Важно!== Работает только в объявлении функций и в сигнатуре шаблона
```c++
// CE для типов, не имеющих value_type
template <typename T>
auto f(T) {
  typename T::value_type result;
  return result;
}
```

## Enable_if
```c++
template <typename B>
struct enable_if {};

template <>
struct enable_if<true> {
  using type = void;
}

template <bool B>
using enable_if_t = enable_if<B>::type;

template <typename T, typename f = enable_if_t<std::is_integral_v<T>>>
void f(T) {}

template <typename... Types>
void f(Types...) {}
```

**Проблема:** функция может принимать и 0 аргументов. Что если она всегда должна принимать один аргумент?

```c++
// 1 вариант
template <typename T, typename f = enable_if_t<std::is_integral_v<T>>>
void f(T) {}

template <typename... Types,  typename f = enable_if_t<!std::is_integral_v<T>>,
typename = void>
void f(T) {}

// 2 вариант (правильная реализация enable_if)
template <typename B, typename T = void>
struct enable_if {};

template <typename T = void>
struct enable_if<true, T> {
  using type = T;
}

template <bool B, typename T = void>
using enable_if_t = enable_if<B>::type;

template <typename T, enable_if_t<std::is_integral_v<T>, bool> = true>
void f(T);

template <typename T, = enable_if_t<!std::is_integral_v<T>, bool> = true>
void f(T);
```

