## is_class
```c++
template <typename T>
std::true_type test(int T::*);

template <typename>
std::false_type test(...);

template <typename T>
struct is_class : decltype(test<T>(nullptr)) {};

template <typename T>
inline constexpr bool is_class_v = is_class<T>::value;
```
- int T::* - указатель на поле типа int от T. Проверяется корректность выражения на этапе компиляции

## is_polymorphic
```c++
template <typename T>
std::true_type detect_is_polymorphic(
decltype(dynamic_cast<const volatile void*>(static_cast<T*>(nullptr)))
)

template <typename T>
std::false_type detect_is_polymorphic(...);

template <typename T>
struct is_polymophic : decltype(detect_is_polymorphic<T>(nullptr))
```
## has_method_construct
```c++
template <typename T, typename... Args>
std::true_type helper(decltype(T().construct(Args()...))* );

template <typename...>
std::false_type helper(...);

template <typename T, typename... Args>
struct has_method_construct: decltype(test<T, Args...>(nullptr)) {}

struct S {
  int& construct(int, int); // CE
}

int main() {
  static_assert(!has_method_construct<S, int, int, int>::value);
}
```
***Решение*** comma trick

```c++
template <typename T, typename... Args>
std::true_type helper(decltype(T().construct(Args()...), nullptr));
```
Пояснение: хотим, чтобы выражение имеет тип T* и корректно только при наличии метода construct. 
#### declval
	Что если нет конструктора по умолчанию?

Принимает T в качестве шаблонного параметра, возвращает объект типа T.
```c++
template <typename T, typename... Args>
std::true_type helper(decltype(std::declval<T>()
            .construct(std::declval<Args>()...), nullptr));
```

```c++
template <typename T>
typename std::add_rvalue_reference<T>::type declval() noexcept;
// T declval() не работает, если T - incomplete type
```
### is_copy_constructible
```c++
template <typename T>
std::true_type helper(decltype(T(std::declval<T&>()), nullptr));

template <typename...>
std::false_type helper(...);

template <typename T>
struct is_copy_constructible: decltype(test<T>(nullptr)) {}
```

## is_nothow_move_constructible
```c++
template <typename T>
std::true_type helper(
  std::enable_if_t<noexcept(T(std::declval<T>())), decltype(nullptr)>
);

template <typename...>
std::false_type helper(...);

template <typename T>
struct is_nothow_move_constructible: decltype(test<T>(nullptr)) {}
```

## is_base_of
```c++
template <typename B, typename D>
std::true_type helper(B*);

template <typename...>
std::false_type helper(...)

template <typename B, typename D>
struct is_base_of: 
std::conjunction<
std::is_class<B>,
std::is_class<D>,
decltype(helper<B, D>(static_cast<D*>(nullptr))>
```
- рассмотрим случай с приватным копированием, сейчас - CE
```c++
template <typename B>
std::true_type test_ptr(const volatile B*);

template <typename>
std::false_type test_ptr(const volatile void*);

template <typename B, typename D>
auto helper(int) -> decltype(test_ptr<B>(static_cast<D*>(nullptr)));
// в случае с множественным или приватным наследованием (и только в них) версия выше не подходит, т.к. было бы CE

template <typename...>
auto helper(...) -> std::true_type;

template <typename B, typename D>
struct is_base_of: 
std::conjunction<
std::is_class<B>,
std::is_class<D>,
decltype(helper<B, D>(0)>
```
## move_if_noexcept
```c++
template <typename T>  
auto my_move_if_noexcept(T& x) noexcept ->  
    typename std::conditional_t<
    !std::is_nothrow_move_constructible_v<T> && std::is_copy_constructible_v<T>,      const T&, T&&> {  
  return std::move(x);  
}
```