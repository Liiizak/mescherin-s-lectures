```c++
template <typename T, typename U>
class vector {};

// Full specialization
template <>
class vector<bool, bool> {};

// Partial specialization
template <typename T>
class vector<T, T> {};
```

- Можно специализировать по ссылке
- Навесить ссылку приоритетнее чем const 

```c++
template <typename T>
struct A<T*> {};

template <typename T>
struct A<const T> {};

int main() {
A<const int*> // Указатель на const int, 1 ver
A<int* const> // const ptr, 2 ver
}
```

```c++
template <typename T, typename U>
void f(T, U) {
std::cout << 1;
}

template <>
void f(int, int) {
std::cout << 3;
}

template <typename T>
void f(T, T) {
std::cout << 2;
}

int main() {
f(0, 0); // 2. Специализировали первую перегрузку
}
```

- Для функций есть только полная специализация
- Сначала выбирается подходящая перегрузка, затем специализация