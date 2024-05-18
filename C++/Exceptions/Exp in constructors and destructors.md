$$ Исключение\ в\ конструкторе $$
```c++
struct A {
  A() { std::cout << "A"; }
  ~A() { std::cout << "~A" }
};

struct S {
  A a;
  S(int x) {
    std::cout << "S";
    if (x == 0) throw 1;
  }
  // вызывается деструктор A, деструктор S не вызывается
  ~S() {
    std::cout << "~S";
  }
};

int main() {
  try {
    S s(0);
  }
  catch (...) {
    
  }
}
```

```c++
struct A {
  A(int x) {
    std::cout << "A";
    if (x == 0) throw 1;
  }
  ~A() {
    std::cout << "~A";
}
};

struct S {
  A a;
  A aa;
  A aaa;
  // исключение в списке инициализации
  S(int x) try: aa(1), a(0), aaa(2) {
    std::cout << "S";
  } catch (...) {
    std::cout << "caught";
    // автоматически приписывается throw;
  }
  // вызываются деструкторы создавшихся полей
  // ловим исключение на уровне инициализации
  // если try в делегирующем конструкторе, и до этого броска исключения конструктор отработал, вызывается деструктор
  ~S() {
    std::cout << "~S";
  }
};

int main() {
  try {
    S s(0);
  }
  catch (...) {
    
  }
}
```

$$ Исключения\ в\ деструкторе $$
```c++
struct S {
  S(int) {
    std::cout << "S";
  }
  ~S() noexcept(false) {
    std::cout << "~S";
    throw 1;
  }
}

int main() {
  try {
    S s(0);
    throw 1;
    // terminate
  } catch (...) {
  
  }
}
// if (std::uncaught_exceptions()) - возвращает int = количество летящих исключений
```