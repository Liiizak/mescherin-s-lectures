- **НЕ** отбрасывает ссылки
- можно брать от выражений

```c++
int main() {
  int x = 0;
  decltype(x) y = x;
  decltype(x)&& z = x; // CE - rvalue ref
  decltype(++x) u = x; // не вычисляет выражение под собой, берет return type
  decltype(throw 1)* p = &x; // void*.......
  const decltype(throw 1)* p = &x; // const void*
  const decltype(&x) l = &x; //o int* const
  decltype((x)) k = x; // k - ссылка на x
}
```

## Decltype от expression

	1. Если expression xvalue, то decltype = T&&
	2. Если expression lvalue, то decltype = T&
	3. Если expression rvalue, то decltype = T

## Decltype as return type

```c++
// 1 не сработает, если возвращаемый тип не ссылка
template <typename Container>
auto& getElement(Container& cont, size_t index) {
  return cont[index];
}

// 2 все ок, на месте auto нельзя написать decltype
template <typename Container>
auto getElement(Container& cont, size_t index)
  -> decltype(cont[index]) {
  return cont[index];
}

// 3 same as 2
template <typename Container>
decltype(auto) getElement(Container& cont, size_t index) {
  return cont[index];
}

int main() {
  std::vector<bool> v(5);
  getElement(v, 0) = 1;
}
```

**причина** по которой нельзя оборачивать возвращаемое значение в скобки
```c++
template <typename Container>
decltype(auto) getElement(Container& cont, size_t index) {
  decltype(auto) elem = cont[index];
  return (elem); // навесится & в люьом случае
}
```

## Deducing this

нужны версии
1. const от lvalue
2. const от rvalue
3. не const от lvalue
4. не const от rvalue
**Решение:** (since c++23)

```c++
template <typename Self>
decltype(auto) value(this Self&& self) {
  return reinterpret_cast<...>(*self.value);
}
// или
decltype(auto) value(this auto&& self) { // auto& or auto&& or auto
  return reinterpret_cast<...>(*self.value);
}
```

**forward_like** (since c++23)
Хотим вернуть U со свойствами (константность и вид value) типа T
```c++
template <typename T, typename U>
auto&& forward_like(U&& x) {
  bool is_adding_const = std::is_const_v<std::remove_reference_t<T>>;
  if constexpr (std::is_lvalue_reference_v<T&&>) {
    if constexpr (is_adding_const)
      return std::as_const(x);
    else 
      return static_cast<U&>(x);
  } else {
      if constexpr (is_adding_const)
        return std::move(std::as_const(x));
      else 
        return std::move(x);
  }
}
```