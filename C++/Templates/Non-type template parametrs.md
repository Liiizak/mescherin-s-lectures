```c++
template <typename T, size_t U>
class array {
T arr[N];
}

int main() {
array<int, 100> a;
}
```
- Числовые параметры должны быть известны на этапе компиляции
## Template template parameters

```c++
template <typename T, template <typename> typename Container>
class Stack {
Container<T> container;
};
```