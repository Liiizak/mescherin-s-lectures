## Idea
- когда бросается исключение, происходит раскрутка стека и уничтожаются локальные переменные (вызываются их деструкторы)                                            (если исключение не ловится - ub)
- полностью run-time вещь
- throw с аргументом копирует объект (все что можно мувнуть мувается)
- без аргумента - пробросить дальше
- catch по значению копирует объект
- выбирается первый походящий вариант
- можно ловить наследника или не конст тип с помощью конст
```c++
//syntax
int divide(int a, int b) {
  throw std::logic_error("Divide by zero")
}

int main() {
  try {
    divide(1, 0);
  }
  catch (std::logic_error& err) {
    std::cout << err.what(); 
  }
}
```

Исключения бросают
- new
- dynamic_cast
- typeid (при вызове typeid от nullpointer)
- throw

## Разница между RE и exceptions
- Seg fault - RE
- Divide by zero - RE
- runtime error происходят на более низком уровне, чем при throw

## **Низкоуровневые ошибки**
1) segmentation fault (**array out of bounds, nullptr dereference, stack overflow**)
2) Floating point exception (division by zero)
3) Abort (abort <- std::terminate or false assertion)
#### std::terminate вызывается автоматически, если
- исключение брошено и не поймано
- pure function call
- брошено исключение из функции, помеченной как noexcept
- брошено исключение в деструкторе по причине того, что раньше было брошено исключение
- .....

