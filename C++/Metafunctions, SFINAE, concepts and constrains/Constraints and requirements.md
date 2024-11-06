```c++
template <typename T>
requires std::is_class_v<T>
void f(const T& x) {
  std::cout << 1;
}

template <typename T>
requires std::is_integral_v<T>
void f(const T& x) {
  std::cout << 1;
}
```
> 1. Можно писать перед сигнатурой функцией или после (до определения)
> 2. Нельзя перегружать функции по "строгости" требований

Requires проверяется на стадии поиска имен, то есть до перегрузки, до инстанцирования шаблонов. То есть перегруженные функции для компилятора являются функциями "с разными названиями". 
```c++

requires (sizeof(long) == 4)
void g(long x) {
  std::cout << 1;
}

requires (sizeof(long) == 8)
void g(long x) {
  std::cout << 1;
}
// CE, нельзя вешать requires на non-template function. Иначе линкеру будет плохо

```
