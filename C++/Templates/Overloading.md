- Если есть точное совпадение, выбирается эта версия
- Лучше использовать шаблон с полным соответствием типа, чем сделать какой-либо каст
- Указание шаблонного параметра обязывает выбрать шаблонную версию
```c++
template <typename T>
void f(T& x) {
std::cout << 1;
}

template <typename T>
void f(T x) {
std::cout << 1;
}

int main() {
int x = 0;
f(x); // CE
f(1); // 2
}
```

```c++
template <typename T>
void f(const T& x) {
std::cout << 1;
}

template <typename T>
void f(T x) {
std::cout << 1;
}

int main() {
int x = 0;
f(1); // CE
}
```
также CE между const Т и Т, но не между T& и const T&