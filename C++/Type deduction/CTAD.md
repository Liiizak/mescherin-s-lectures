**Class Template Argument Deduction** (since c++17)

```c++
int main() {
  std::vector v = {1, 2, 3, 4, 5};
  std::vector v2{v.begin(), v.end()}; // вектор из итераторов..
  std::vector v2(v.begin(), v.end()); // вектор интов
}
```

## template deduction guides
```c++
template <typename T>
class vector {
  template <typename Iterator>
  vector(Iterator, Iterator) {}
}

template <typename Iterator>
vector(Iterator, Iterator)
-> vector<typename std::iterator_traits<Iterator>::value_type>
```

## агрегатная инициализация

- since c++20
```c++
template <typename T, typename U>
struct S {
  T x;
  U y;
};

int main() {
  S s{1, 2.0};
}
// или
struct A {};
struct B {};

// может быть переменное число шаблонных аргументов
template <typename T, typename U>
struct S: A, B {};

int main() {
  S s{A(), B()};
}
```