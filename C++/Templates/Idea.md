```c++
//useless swap
template <typename T>
void swap(T x, T y) {
T t = x;
x = y;
y = t;
}

int main() {
int a = 10;
long long b = 1;
swap(a, b); // CE
swap<long long>(a, b) // OK
}
```

Шаблонами могут быть: 
- Типы (typename)
- Целочисленные типы
- bool и char
- template template parameters

> При выводе типа шаблона ссылка отбрасывается

