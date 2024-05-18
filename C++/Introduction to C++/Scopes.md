- в глобальной области видимости переменные инициализируются значением по умолчанию
```c++
namespace N {
  int x = 5;
}

int main() {
  using N::x; // CE - redeclaration
  using namespace N; // OK
  int x = 7;
}
```

```c++
namespace N {
  int x = 5;
}

namespace NN {
  int x = 6;
}

int main() {
  using namespace N;
  using namespace NN;
  // CE
}
```

```c++
namespace N {
  int x = 5;
}

int main() {
  using N::x;
  using x = std::vector<int>;
  // CE - redeclared as different type of entity
}
```

```c++
int x = 0;

int main() {
  int x = x; // equal to int x;, после объяявления переменная сразу "начинает действовать"
}
```

- N::x - qualified id
- x - unqualified id

## one definition rule
- класс можно опеределять несколько раз, если их код полностью совпадает
- extern для глобальных переменных или в namespace означает, что переменная объявлена, но не определена. (extern int x;)