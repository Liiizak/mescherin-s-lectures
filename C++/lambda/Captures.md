Если нужна локальная переменная, ее надо захватить
```c++
int main() {
  std::vector v = {1, 4, -3, 6};
  int center = 1;
  auto comp = [center](int x, int y) {
    return (x - center) * (x - center) < (y - center) * (y - center);
  }'
  std::sort(v.begin(), v.end(), comp);
}
```

```c++
auto cmp = [&a](int x) {...} // захватили a неконстантной поссылке
auto cmp = [a](int x) {...} // захватили по значению, менять нельзя
auto cmp = [a](int x) mutable {...} // захватили по значению, менять нельзя
```
> Причина такого поведения - оператор ()  константый
#### Захват с инициализацией
```c++
std::string sub = "123"
auto contains_substring = [sub = sub + '4'](const std::string& str) {
  return str.find(sub) != std::string::npos;
}
```

```c++
// захват по rvalue ссылке
std::string sub = "123"
auto contains_substring = [sub = std::move(sub)](const std::string& str) {
  return str.find(sub) != std::string::npos;
}
```
####  Захват по умолчанию
```c++
auto cmp = [=](int x) {...}; //  по значению
auto cmp = [&, sub2](int x) {...}; // по ссылке все, кроме sub2
auto cmp = [a...](int x) {...} // захватили переменное число аргументов
```
#### Захват полей класса
```c++
struct S {
  int a = 1;

  auto f() {
  // auto f = [=](int x) - UB, так как захватили this
    auto f = [this](int x) {
    std::cout << a;
    return x;
    }
    return f;
  }
}

int main() {
  S* s = new S();
  auto f = s->f();
  delete s;
  std::cout << f(5); // still ub
}
```