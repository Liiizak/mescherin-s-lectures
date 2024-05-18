> Для реализации push_back нужно две функции от const T& и от T&&. Во втором случае отличие лишь в **new(ptr) T(std::move(value))**.

## Правильная реализация std::swap
```c++
void swap(T& x, T& y) {
  T tmp = std::move(x);
  x = std::move(y);
  y = std::move(tmp);
}
```

## Наивная реализация std::move
```c++
template <typename T>
T&& move(T& x) {
  return static_cast<T&&>(x);
}
```
## Не совсем наивная реализация std::move
```c++
// move от T& возвращает lvalue. Проблема..
template <typename T>
std::remove_reference_t<T>&& move(T&& value) {
  return static_cast<std::remove_reference_t<T>&&>(value);
}
```
## std::move_if_noexcept
```c++
template typenaame<T>
constexpr typename std::conditional_t<!std::is_nothrow_move_constructible_v<T> && std::is_copy_contructible_v<T>, const T&, T&&> move_if_noexcept(T& x) noexcept {
  return std::move(x);
}
```

## Забавный пример
```c++
vector<string> v(5, "abc");
v.push_back(v[3]); // UB
```
Решение - push_back сначала добавляет элемент, потом выполняется realloc