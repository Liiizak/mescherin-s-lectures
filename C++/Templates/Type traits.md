> Метафункции возвращают тип/значение, в зависимости от переданного типа

```c++
template <typename T, typename U>
struct is_same {
  static constexpr bool value = false;
};

template <typename T>
struct is_same<T, T> {
  static constexpr bool value = true;
}

template <typename T, typename U>
void f(const T& x, const U& y) {
  if constexpr (is_same<T, U>::value) {
    x = y;
  }
}
```

```c++
#include <type_traits>
std::is_same<T, U>::value;
std::is_same_v<T, U>;
std::conditional_t<B, T, V> x; // Если B = true, то тип T, иначе V
std::is_base_of_v<T, U> // true, если U - насследник T или равен T
```

```c++
template <typename T>
struct remove_const {
  using type = T;
};

template <typename T>
struct remove_const<const T> {
    using type = T
};

template <typename T>
void f() {
  typename remove_const<T>::type x;
}
```