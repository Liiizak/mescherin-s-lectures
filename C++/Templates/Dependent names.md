```c++
template <typename T>
  struct S {
  using A = int;
};

template <>
struct S<double> {
  static const int A = 5;
};

template <typename T>
  void f() {
  S<T>::A* x; // declaration or expression depend on specialization
};

int main() {
  f<int>();
}
```

- По дефолту все зависимые имена парсятся как expressions
```c++
// Решение:
typename S<T>::A* x; // declaration
```


```c++
template <typename T>
  struct S {
  template <int N>
  using A = std::array<int, N>;
};

template <>
struct S<double> {
  static const int A = 5;
};

template <typename T>
  void f() {
  S<T>::A<10> x; // expression
  typename S<T>::template A<10> x; // пофикшено с g++11, не нужно template
};

int main() {
  f<int>();
}
```

```c++
template <typename T>
struct S {
  template <int N>
  void foo(int) {}
};

template<>
struct S<double> {
  const int foo = 1;
};

template <typename T>
void bar(int x, int y) {
  s<T> s;
  s.template foo<5>(x + y);
}

int main() {
  bar<int>(2, 3);
}
```


```c++
template <typename T>
struct Base {
  int x = 0;
}

template <>
struct Base<double> {};

template <typename T>
struct Derived: Base<T> {
  void f() {
    ++this->x; // без this-> не работает обращение к полю шаблонного родителя
    // или ++Base<T>::x  
  }
}
```