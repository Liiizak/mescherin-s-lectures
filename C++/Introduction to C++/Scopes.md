- в глобальной области видимости переменные инициализируются значением по умолчанию
- using namespace в глобальной области видимости привносит все переменные в нее
- открытие одного namespace несколько раз - добавление новых сущностей
```c++
namespace N {
  int x = 5;
}

int main() {
  using N::x; // CE - redeclaration
  using namespace N; // OKМ
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
  using x = std::vector<int>; // alias
  // CE - redeclared as different type of entity
  {
     using x = std::vector<int>; // OK
  }
}
```

```c++
// since c++17
namespace N {}

namespace N::NN {
  int x = 0;
}
```

```c++
int x = 10;

int main() {
  int x = x; // equal to int x;, после объявления переменная сразу "начинает действовать"
}
```

- N::x - qualified id
- x - unqualified id

## Перегрузка функций
```c++
int f(int x) {
  return 0;
}
// нельзя перегружать подобным образом
void f(int x) {}
```

## one definition rule
- класс можно опеределять несколько раз, если их код полностью совпадает
- extern для глобальных переменных или в namespace означает, что переменная объявлена, но не определена. (extern int x;)
- для переменных объявление переменной означает и ее определение (при отсутствии extern)