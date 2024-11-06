```c++
template <typename T>
requires // че писать-то..
T add(const T& a, const T& b) {
  return a + b;
}
```

```c++
template <typename T>
void test() {
  std::cout << requires(T a, T b) { a + b; }
}

int main() {
  test<int>(); // 1
  test<void>(); // 0
}
```

```c++
template<typename T, size_t N>
requires requires(T a) {
  // сюда можно писать выражения, которые будут проверены на компилируемость
  // static_assert нельзя писать
  requires sizeof(T) == N;
}
void test(const T& a) {}
```
##### Виды requirements
1. simple requirement
```c++
template<typename T, size_t N>
requires requires(T a) {
   *a; // проверка на true
}
void test(const T& a) {}

```
2. nested requirement
```c++
template<typename T, size_t N>
requires requires(T a) {
  requires sizeof(T) == N;
}
void test(const T& a) {}
```
3. type requirement
```c++
template<typename T, size_t N>
requires requires(T a) {
  typename T::value_type; // ограничение на T
}
void test(const T& a) {}
```
4. compound-requirements
```c++
template <typename T, typename U>
concept same_as = std::is_same_v<T, U>

template <typename T>
concept InputIterator = requires(T it) {
  {++it} -> same_as<T&>; // 4.
  *it;
};
```

Есть синтаксис для noexcept
```c++
requires requires(T x) {
  {++x} noexcept;
}
```
##### requires внутри requires
```c++
template<class T>
concept Semiregular = DefaultConstructible<T> &&
    CopyConstructible<T> && CopyAssignable<T> && Destructible<T> &&
requires(T a, std::size_t n)
{
	requires Same<T*, decltype(&a)>; // nested: "Same<...> evaluates to true"
	 { a.~T() } noexcept; // compound: "a.~T()" is a valid expression that doesn't throw
	requires Same<T*, decltype(new T)>; // nested: "Same<...> evaluates to true"
	requires Same<T*, decltype(new T[n])>; // nested
	{ delete new T }; // compound
	{ delete new T[n] }; // compound
};
```
То есть внутри requires будет происходить вычисление другого requires, а не проверка на синтаксическую корректность