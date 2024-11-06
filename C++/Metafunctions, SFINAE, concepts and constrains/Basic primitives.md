```c++
// 1. тождестванная метафункция
template <typename T>
struct type_identity {
  using type = T;
}

template <typename T>
using type_identity_t = type_identity<T>::type;

// 2.
template <typename T, T x>
struct integral_constant {
  static constexpr T value = x;
};

template <bool b>
using bool_constant = integral_constant<bool, b>;

using true_type = bool_constant<true>;
using false_type = bool_constant<false>;

// 3.
template <typename T>
struct is_lvalue_reference: false_type {};

template <typename T>
struct is_lvalue_reference<T&>: true_type {};

template <typename T>
inline constexpr bool is_lvalue_reference_v = is_lvalue_reference<T>::value;

// 4. лишнее инстанцирование
template <typename... Types>
struct conjuction {
  static constexpr bool value = (Types::value && ...);
}

// правильная реализация
template <typename...>
struct conjuction: true_type {};

template <typename T>
struct conjuction<T>: T {};

template <typename T, typename... U>
struct conjuction<T, U...>:
conditional_t<bool(T::value), conjuction<U...>, T> {};

```