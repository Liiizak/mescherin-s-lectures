## Pointer
- применимы ptr + int, ptr  - int, ++, --, +=, -=
- сравнивать, вычитать указатели можно только для одинаковых типов
- любой указатель приводится неявно к void*, и в обратную сторону
- const нельзя навешивать глубже чем на один уровень

## Указатель на функции
```c++
void f(int) {}
void f(double) {}

int main() {
  bool (*p)(int, int) = func; // корректно и без указателя

  &f; // CE, непонятно какую функцию брать
  void (*p)(int) = &f; // OK, указатель на функцию от int
}
```

## Array
- можно инициализировать следующими способами
```c++
int a[5] = {1, 2, 3, 4, 5};
int a[5] = {1, 2}; // дозаполнит нулями
int a[] = {1, 2, 3, 4, 5} // size = 5
int a[5]; // запонится рандомными значениями
int a[5]{}; // инициализирует значениями по умолчанию
```

## Array to pointer conversion
```c++
int main() {
  int a[5] = {1, 2, 3, 4, 5};
  int* p = a + 3;
  std::cout << p[-1]; // 3

  a[2] ~ 2[a];
}
```
- при передаче массива в функцию он превращается в указатель

## Variable length array

- по стандарту не разрешены в с++
- фактически поддерживаются для обратной совместимости с C
```c++
int main() {
  std::cin >> n;
  int a[n];
}
```

## Pointers and arrays

**ЧИТАЕМ СПРАВО НАЛЕВО**
```c++
int main() {
  int* b[5]; // массив из 5 указателей на  int
  int (*b)[5]; // указатель на массив из 5 int
}
```