<u>Компилируемый язык</u> прежде чем запустить программу, компилирует ее, то есть превращает в машинный код
<u>Интерпретируемый язык</u>  - код считывается построчно и превращается в инструкции для процессора по ходу выполнения программы

<u>Статическая типизация</u>  - тип переменной определяется на этапе компиляции
<u>Динамическая типизация</u>  - тип переменной определяется в runtime

<u>Литерал</u> - последовательность символов в коде, которая обозначает значение какого-то типа
<u>Идентификатор</u> - последовательность символов, которые обозначают какую-либо сущность
## Types and operations
- integral type: char! 
  **Целочисленные литералы:**
  0x - 16-тиричная система
  0 - 8-миричная система
- floating point types: long double
## Statements:
#### Declarations (Definition as a special case)
1. В глобальной области видимости переменные инициализируются значением по умолчанию
2. ODR: класс можно определить неколько раз, если все объявления дословно идентичны
3.  extern для глобальных переменных или в namespace означает, что переменная объявлена, но не определена. (extern int x;)
4. для переменных объявление переменной означает и ее определение (при отсутствии extern)
5. внутри класса все методы можно использовать в произвольном порядке (до объявления)
#### Expressions
1. Ленивые вычисления логических операторов гарантируются стандартом и сохраняются при перегрузке. Тернарный оператор так же вычисляется лениво
2. Операторы присваивания возвращают ссылку на объект
3. Операторы присваивания правооассиациативные, остальные - левоассоциативные, при перегрузке эти свойства сохраняются (since c++17)
4. Инкремент/декремент применимы только к lvalue
5. Запятая гарантирует, что при правый аргумент начнет вычисляться только после вычисления левого
- **"sequenced before" rules**
1. в выражении вычисления функции выражения, именующие функцию, предшествуют любому выражению, которое обозначает аргумент и вычислению любого аргумента по умолчанию
2. все параметры функции могут вычисляться и производить side эффекты независимо от других параметров
3. в операторе побитового сдвига каждое вычисление и каждый side эффект левого операнда предшествует каждому вычислению и side эффекту правого
4. в каждом присваивании вычисление и side эффект правого операнда предшествует вычислению и side эффекту правого
- **priority of operators***
1. :: - scope
2. постфиксные унарные операторы -> префиксные унарные опираторы
3. бинарные операторы
4. операторы сравнения
5. логические операции
#### Control statements
```c++
int main() {
  int x;
  std::cin >> x;
  switch(x) {
  case 1:
    std::cout << "1";
    break; // без break выведется 1other
  default:
    std::cout << "other";
  }
}
```

```c++
int main() {
  do {
  ....
  } while (); // сначала делать, потом думать
}
```

```c++
label:
...
goto label;
```


<u>cout</u> - переменная типа std::ostream (при include iostream вносится в глобальную область видимости)
#### Области видимости.
  Разновидности: global, namespace, class, function scope или просто новый scope фигурными скобками. Более локальные переменные **затмевают** менее глобальные
- using namespace в глобальной области видимости привносит все переменные в нее
- открытие одного namespace несколько раз - добавление новых сущностей
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
## CE, RE, UB
#### CE:
- лексический парсер упал на некорректном токене
- синтакстическая ошибка (x +) 
- семантическая ошибка (++x++)
- ошибка линковщика
#### RE:
- segfault
- floating point exception
- std::abort
![image|600](exceptions.png)
#### UB
- обращение за границы массива
- использование переменной без инициализации
- два неупоряченных относительно друг друга изменения одной и той же переменной
#### unspecified behavior
- результат гарантирован, но промежуточный результат может варьироваться (order of evaluation)
## Pointers
- сравнивать, вычитать, складывать указатели можно только для одинаковых типов
- любой указатель приводится неявно к void*, и в обратную сторону
- const нельзя навешивать глубже чем на один уровень
#### pointer to function
```c++
bool comp(int x, int y) {
  return x > y;
}

int main() {
  bool (*p)(int, int) = &comp; // указатель на компаратор, можно без &
  std::sort(a.begin(), a.end(), &comp);
}
```

```c++
void f(int) {}
void f(double) {}

int main() {
  std::cout << &f; // CE
  void (*p)(int) = &f; // OK, p - указатель на функцию, которая принимает int
}
```
## Виды памяти
#### Статическая (data)
- глобальные, статические переменные (за переменной закреплен один адрес на протяжении исполнения всей программы)
- переменные в namespace
- литералы
- таблицы виртуальных функций
#### Динамическая
#### Автоматическая (stack)
- локальные переменные
- адреса возвратов
#### text
- бинарный код исполняемой программы
## Arrays
- можно инициализировать следующими способами
```c++
int a[5] = {1, 2, 3, 4, 5};
int a[5] = {1, 2}; // дозаполнит нулями
int a[] = {1, 2, 3, 4, 5} // size = 5
int a[5]; // запонится рандомными значениями
int a[5]{}; // инициализирует значениями по умолчанию
```

#### Array to pointer conversion
```c++
int main() {
  int a[5] = {1, 2, 3, 4, 5};
  int* p = a + 3;
  std::cout << p[-1]; // 3

  a[2] ~ 2[a];
}
```
- при передаче массива в функцию он превращается в указатель
#### Операции
-  нельзя присваивать массиву
- sizeof(a) - суммарный размер всех элементов массива а
- нельзя инкрементировать
- нельзя перегружать функции f(int* a) и f(int a\[3])

#### Variable length array

- по стандарту не разрешены в с++
- фактически поддерживаются для обратной совместимости с C
```c++
int main() {
  std::cin >> n;
  int a[n];
}
```

#### Pointers and arrays

**ЧИТАЕМ СПРАВА НАЛЕВО**
```c++
int main() {
  int* b[5]; // массив из 5 указателей на  int
  int (*b)[5]; // указатель на массив из 5 int
}
```
## References
Продление жизни работает **только** внутри функции при объявлении локальной переменной!!
```c++
struct C {
  const int& r;

  C(): r(5) {} // UB
}
```

## Стадии компиляции
#### 1. Препроцессинг
<u>Обработка директив препроцессора</u>
- include копипастит файл
- Выходной файл - .cpp
- -E
#### 2. Компиляция
<u>Превращение кода на c++ в код на ассемблере</u>
- Выходной файл - .s
- -S
#### 3. Ассемблирование
<u>Сборка кода на ассемблере</u>
- Выходной файл - .o - машинный код для процессора
- -c
#### 4. Линковка
<u>Подставляеет в места вызова функций их определения</u>
- Выходной файл - .out
- Необходима для того, чтобы не компилировать каждый раз код библиотек
- g++ a.o
## OOP
1. **Абстракция** -  выделение значимой информации и исключение из рассмотрения незначимой.
2. **Инкапсуляция** - свойство системы, позволяющее объединить данные и методы, работающие с ними, в классе.
3. **Полиморфизм** - свойство системы, позволяющее использовать объекты с одинаковым интерфейсом без информации о типе и внутренней структуре объекта.
4. **Наследование** - свойство системы, позволяющее описать новый класс на основе уже существующего с частично или полностью заимствованной функциональностью.
<u>Класс</u> - объявляемые типы
<u>Объект</u> - переменная, имеющие данный тип (класс)
<u>Методы</u> - операции, разрешенные над данными типами (классами)
<u>Поля</u> - данные, хранящиеся в классе
```c++
struct A {
  int a;
  double b;
  struct AA {
    char c;
  }
}
// sizeof(A) = 16
```

```c++
struct {
  ...
} a; // объявление анонимной структуры в переменной a
```
*Класс можно объявлять внутри функции*
#### Rule of three
- Оператор присваивания
- Конструктор копирования
- Деструктор
#### Оператор присваивания
Используем copy and swap
Почему T&?
- void - нельзя делать несколько присваиваний в одну строку
- T - аналогично. Несколько присвавиваний работали бы некорректно 
- const T& - а зачем константа??
#### Статические поля и методы
Статические методы работают как функции, но находятся в поле видимости класса
```c++
struct S {
  static int x = 1; // CE
  static const int x = 1; // OK
}

int S::x = 1; // OK
```
#### Перегрузка операторов
- нельзя доопределить свой
- нельзя поменять порядок вычисления и приоритет
- нельзя поменять ассоциативность
```c++
struct Complex {
  long long re;
  double im;
  
  Complex operator""_c(unsigned long long) { // литеральный суффикс;
  }

  // partial_ordering, weak_ordering, strong_ordering
  std::strong_ordering operator<=>(const Complex& other) const = default;
   // сравнения оперделяются лексикографически
  friend std::ostream& operator<<(std::ostream&, const Complex&);
}

Complex operator+(const Complex& a, const Complex& b) const {
  Complex copy = a;
  copy += b;
  return copy;
}

std::ostream& operator<<(std::ostream& out, const Complex& a) {
  out << a.re << " " << a.im;
}
```
- == и != не доопределяются spaceship'ом при нетривиальной реализации
#### Enum
- enum - равносильно внесению константы в глобальную область видимости, enum class - нет
- enum разрешает неявные конверсии, enum class - запрещает
```c++
enum E {
  White, // 0
  Gray, // 1
  Black // 2
}

enum class E {
  White = 10,
  Gray, // 11
	  Black // 12
}
```
## Casts
#### 1. static_cast
#### 2. reinterpret_cast
Указатель можно трактовать как указатель на что угодно.
Переменную можно трактовать как ссылку на что угодно.
- нельзя делать к новому объекту, только к ссылке или указателю
- нельзя снять const
#### 3. const_cast
Можно const переменную (не указатель) скастовать к nonconst ссылке
Можно снять const из-под указателя
#### 4. c-style cast

## ctors and dtors
```c++
class S {
  S(int a): a(a) {} // member initializer list
  int a = 0; // default initialization, since c++11
}

int main() {
  std::vector<int> a = {1, 2, 3, 4}; 
  // initializer list, принимаем по значению, по приоритету конструктор от него рассматривается первым
}
```
## Templates
#### General
- Если есть точное совпадение, выбирается эта версия
- Лучше использовать шаблон с полным соответствием типа, чем сделать какой-либо каст
- Указание шаблонного параметра обязывает выбрать шаблонную версию
```c++
template <typename T>
void f(T& x) {
std::cout << 1;
}

template <typename T>
void f(T x) {
std::cout << 1;
}

int main() {
int x = 0;
f(x); // CE
f(1); // 2
}
```

```c++
template <typename T>
void f(const T& x) {
std::cout << 1;
}

template <typename T>
void f(T x) {
std::cout << 1;
}

int main() {
int x = 0;
f(1); // CE
}
```
также CE между const Т и Т, но не между T& и const T&
```c++
template <typename T, typename U>
class vector {};

// Full specialization
template <>
class vector<bool, bool> {};

// Partial specialization
template <typename T>
class vector<T, T> {};
```

- Можно специализировать по ссылке
- Навесить ссылку приоритетнее чем const 

```c++
template <typename T>
struct A<T*> {};

template <typename T>
struct A<const T> {};

int main() {
A<const int*> // Указатель на const int, 1 ver
A<int* const> // const ptr, 2 ver
}
```

```c++
template <typename T, typename U>
void f(T, U) {
std::cout << 1;
}

template <>
void f(int, int) {
std::cout << 3;
}

template <typename T>
void f(T, T) {
std::cout << 2;
}

int main() {
f(0, 0); // 2. Специализировали первую перегрузку
}
```

- Для функций есть только полная специализация
- Сначала выбирается подходящая перегрузка, затем специализация
#### Non-type template parametrs
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
#### Template template parameters

```c++
template <typename T, template <typename> typename Container>
class Stack {
Container<T> container;
};
```
#### Dependent names
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
#### Variadic templates
```c++
template <typename Head, typename... Types>
 struct is_same_many {
   static const bool value = (std::is_same_v<Head, Types> && ...);
 };
```
**sizeof...** возвращает количество элементов в пакете
## static, mutuable, friend, explicit
#### Static:
- в глобальной области видимости static переменная видима только в данном translation unit'е (файле)
#### friend
```c++
struct C {
  friend int main();
  friend class CC; // можно определить на месте
}
```
#### mutable
Пример использования - подсчет каких-либо действий. Например, вызовов функции
```c++
struct S {
  mutable int x = 1;
  
  void f() const {
    ++x;
  }
}
```
#### explicit
Применяется для методов, которые запрещено неявно вызывать
1. Конструкторы
2. Операторы приведения типа

```c++
struct Latitude {
  double value;
  explicit Latitude(double value): value(value) {} // 1.
  explicit operator double() const { // 2.
    return value;
  }
}

struct Longitude {
  double value;
  explicit Longitude(double value): value(value) {} // 1.
  explicit operator double() const {
    return value;
  }
}
```
## Наследование
#### Видимость при наследовании
- происходит затмение имен функций (частное предпочтительнее общего), а не перегрузка

```c++
struct Base {
  int x;
  void f(int) {
    std::cout << 1;
  }
}

struct Derived: Base {
  int y;
  int f(double) {
    std::cout << 2;
    return 1;
  }
}

int main() {
  Derived d;
  d.f(0); // 2
}
```

```c++
struct Base {
 protected:
  void foo() {
    std::cout << 1;
  }
};

struct Derived: Base { 
  using Base::foo; // можно вызывать функцию родителя, не CE!!!!!
  void foo(int) { 
    std::cout << 2; 
  }
};
```
#### Размещение в памяти
- все поля родителя становятся нашими (вне зависимости от модификаторов доступа)
```c++
// пустая структура занимает 1 байт
struct Empty {}

struct Derived: Empty {
  int a;
}
// sizeof Derived - 4. Empty base optimization
```


- только друзья (и сам класс) знают о наследовании
- можно кастовать ребенка к родителю
private - только друзья (и сам класс) знают о наследовании
protected - +наследники
#### Наследование конструкторов
Наследуем конструктор родителя, свои поля инициализируем по умолчанию
```c++
using Base::Base // since c++11
```
**Конструктор копирования** при этом выбирается от Derived, а не от Base 
#### Приведение типов при наследовании
```c++
struct Base {};
struct Derived: Base {};

int main() {
  Base b;
  Derived d =  b; // CE
}
```

```c++
struct Base {
  int x = 0;
}

struct Derived: Base {
  int y = 0;
}

// Slicing, полноценно копируется часть Base у Derived 
void f(Base b) {}

int main() {
  Derived d;
  f(d);
}
```

![image|400](casts.png)
#### Множественное наследование
Расположение в памяти - сначала родители, потом наследники
```c++
struct Mom {
  int x;
}
struct Dad {
  int y;
}
struct Son: Mom, Dad {}

int main() {
  Son s;
  Dad* p = &s; // OK, но придется сдвинуть укзатель (т.е &s != p)
}
```
##### Ромбовидное наследование
```c++
struct Granny {}
struct Dad: Granny {}
struct Mom: Granny {}
struct Son: Mom, Dad {}

int main() {
  Son s;
  Granny& g = s; // CE, ambigious cast
}
```
Расположение в памяти - G (from mom) -> M -> G(from dad) -> D -> S
```c++
Son s;
Granny g;
Mom& m = s;
static_cast<Dad&>(m); // CE
static_cast<Son*>(g); // CE
```
##### inaccessible base class
```c++
struct Granny {}
struct Dad: Granny {}
struct Son: Dad, Granny {}
// нельзя обратиться к родителю Granny в классе Son (кроме reinterpret_cast)
```
## Exceptions
#### Иерархия
- logic_error
- runtime_error
#### Исключения в конструкторе и деструкторе
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
## Стандартные контейнеры
#### vector
- указатели, итераторы и ссылки инвалидируются при реаллокации 
```c++
template <>
class Vector<bool> {
 public:
  struct BitReference {
    int8_t* cell;
    uint8_t index;
    BitReference(char* cell, uint8_t index) : cell(cell), index(index) {}
    BitReference& operator=(bool b) {
      if (b) {
        *cell |= (1u << index);
      } else {
        *cell &= ~(1u << index);
      }
      return *this;
    }
    // оператор неявного каста в const
    operator bool() const {
      return *cell & (1u << index);
    }
  }

  BitReference operator[](size_t i) {
      return BitReference(arr_ + i / 8, i % 8);
  }
 private:
  int8_t* arr;
  size_t size_;
  size_t capacity_;
};

int main() {
  Vector<bool> v(10);
  v[5] = true; // correct, присваеваем значение rvalue
}
```
#### deque
- итератор инвалидируется
- отсутствуют capacity, shrink_to_fit, reserve
- Ссылки и указатели не инвалидируются
![image|600](deque.png)
#### list
- Храним fake_node которая хранит указатель на начало и на конец (реальную, не указатель на base_node)
- node - наследница fake_node, для всех существующих элементов, хранит T
###### Нетривиальные методы list
- собственный sort, работающий на основе bidirectional итератора
- splice - вырезать кусок из одного листа и вставить в другой. Если листы разные, то работает за O(to-from), т.к. нужно посчитать новый size
- merge
#### map
Все операции работают за гарантированный логарифм.

- std::pair<iterator, bool> ***insert***(const pair<key, value>&); 
(bool - произошла ли вставка. **Можно вставлять только по новому ключу**)

- ***erase***(const key&);     ***erase***(iterator)

- Value& ***at***(const key&);
(если есть элемент, возвращает его, иначе кидает исключение)

- Value& operator\[]\(const key&);
**нет константной версии**, т.к. создает элемент со значением по умолчанию при отсутствии элемента

- iterator find(const key&);
 ```c++
if (auto iter = m.find(key); iter != m.end()) {
   it->second = 2;
}
// эффективный поиск элемента
```

begin -  самый нижний левый лист
fake_node - корень
```c++
struct BaseNode {
  BaseNode* left;
  BaseNode* right;
  BaseNode* parent;
}

struct Node : BaseNode {
  pair<const key, value> kv; // при изменении ключа дерево перестает быть упорядоченным
  bool red;
}
```

Весь обход дерева работает за O(n) (посещаем каждую вершину не больше 3-х раз)
![[map.png]]
##### Set
- все то же самое, только вместо пары хранится значение

##### Multimap
- полезны lower_bound upper_bound
- equal_range возвращает два итератора на lower_bound и upper_bound
#### unordered_map
##### Общая идея
В общем векторе хранятся **итераторы** на ноды, **пару ключ-значение и хеш**. 
##### Реализация операций
***find*** проходим по общему вектору, если нашли нужный хеш, то по итератору ищем в односвязном списке элемент с нужным ключом
***insert*** докаладываем в начало/конец бакета элемент. Если ключ новый (бакет по этому ключу пустой), докладываем в начало forward_list'а, и у первого элемента в forward_list обновляем итератор с фейковой вершины на новую (для этого обращаемся к массиву по хешу). 
***erase*** удаление последнего элемента в бакете:
Если элемент единственный:
	1. Обращаемся к вектору по хешу удаляемого элемента. (находим итератор на предыдущий бакет)
	2. Удаляем его.
	3. Обращаемся по хешу следующего элемента и находим итератор, указывающий на удяляемый элемент. 
	4. Ставим итератор на предыдущий бакет.
Если элемент не единственный:
	1. Обращаемся к вектору по хешу удаляемого элемента. (находим итератор на предыдущий бакет)
	2. Двигаем итератор до вершины перед удаляемой
	3. Переставляем итератор, указывающий на удаляемый элемент

***rehash*** идем по forward_list, erase из него и insert в новый. (не удаляем вершину, переставляем указатели)
	exception_safety: не используются hash и сравнение при rehash

 > 
 > Не инвалидируются указатели и ссылки при insert, erase, rehash
 > Итератор инвалидируется 
 > 
![[unordered_map.png]]
#### Адаптеры над контейнером
1. std::stack
```c++
template<typename T, typename std::deque<T>>
class Stack {};
```
- Используется фиксированный тип std::deque\<T>, а не шаблонный шаблонный параметр, так как неизвестно, сколько шаблонных параметров (и какого вида) имеет контейнер.
- pop() возвращает void, а не T, для избежания копирования
1. std::queue
2. std::priority_queue

## Iterators
#### Input iterator
- конструтор, деструктор, оператор присванивания по умолчанию
- разыменовывание
- инкрементирование (префиксное и постфиксное)
- сранение на равенство
#### Forward Iterator
 - гарантия того, что можно ходить вперед несколько раз (значения будут одинаковыми, если пройти по одному промежутку несколько раз)
 (forward_list, unordered_set, unordered_map)
#### Bidirectional iterator
- декремент
(set, map, list)
#### RandomAccess iterator
- +=, -= число
- +, - число
- сравнения <, >, <=, >=
- разность итераторов
(deque)
#### Contiguous iterator
- элементы лежат в памяти непрерывно
(vector, array, pointer)
#### Output Iterator
- можно разыменовывать, инкрементировать присваивать сколько угодно раз (писать в итератор)
```c++
 
template <typename Container>
class back_insert_iterator {
  Container& container;
 public:
 // back_insert_iterator - адаптер над интератором
   back_insert_iterator(Container& container) : container(container) {}
   back_insert_iterator& operator=(const typename Container::value_type& value) {
     container.push_back(value);
     return *this;
   }
  back_insert_iterator& operator++() {
    return *this; // ничего не делает
  }
  back_insert_iterator operator++(int) {
    return *this; // ничего не делает
  }
  back_insert_iterator& operator*() {
    return *this; // ничего не делает
  }
}

template <typename Container>
back_insert_iterator<Container> back_inserter(Container& container) {
  return {container};
}

int main() {
  int a[10] = {1, 2, 3, 4, 5};
  std::vector<int> v;
  std::copy(a, a + 10, std::back_insert_iterator<std::vector<int>>(v); // ub
  // or std::cope(a,  a + 10, back_inserter(v));
}
```

```c++
int main() {
  int a[1]{};
  std::vector<int> v(5);
  std::copy(v.begin(), v.end(), a); // ub
  std::copy(v.begin(), v.end(), a, pred()); // pred - предикат
  std::copy(v.begin(), v.end(), std::back_inserter(a));
}
```
#### Потоковые итераторы
***istream_iterator***
```c++
template <typename T>
class istream_iterator {
  T value;
  std::istream& in;
 public:
  istream_iterator(std::istream& in): in(in) {
    in >> value;
  }
  istream_iterator& operator++() {
    in >> value;
    return *this;
  }
  istream_iterator& operator*() {
    return value;
  }
}

std::istream_iterator<int> it(std::cin);
std::vector<int> v;
for (int i = 0; i < 10; ++i, ++it) {
  v.push_back(*it);
}
for (int i = 0; i < v.size(); ++i) {
  std::cout << v[i] << ' ';
}
```
- end у такого итератора - значение по умолчанию (итератор становится равным значению по умолчанию когда нечего считывать)
- является input итератором

***ostream_iterator***
```c++
std::copy(iter, std::istream_iterator<int>(), 
		  std::ostream_iterator(std::cout, " "));
		  //   ostream_iterator принимает вторым аргументом разделитель
		  // реализован как back_insert_iterator
```

#### std::advance
```c++
template <typename Iter>
void advance(Iter& iter, int n) {
  if constexpr (is_base_of_v<
     typename std::iterator_traits<Iter>::iterator_category,                          std::random_access_iterator_tag>) {
    iter += n;
    return;
  }
  for (int i = 0; i < n; ++i) {
    ++iter;
  }
}
```
#### implementation
```c++
template <bool IsConst>
class Iterator {
 public:
  using pointer_type = std::conditional_t<IsConst, const T*, T*>;
  using reference_type = std::conditional_t<IsConst, const T&, T&>;
  using value_type = T; // ! without const
  private:
  friend class deque<T>;
  T* ptr;
  iterator(T* ptr): ptr(ptr) {}
 public:
  operator Iterator<true>() const {
    return {ptr};
  }
  iterator(const iterator&) = default;
  iterator& operator=(const iterator&) = default
  T& operator*() const {
    return *ptr;
  }
  T* operator->() {
    return ptr;
  }
  iterator& operator++() {
    ++ptr;
    return *this;
  }
  iterator operator++(int) {
    iterator copy = *this;
    ++ptr;
    return copy;
  }
};
using iterator = Iterator<false>;
using const_iterator = Iterator<true>;
using reverse_iterator = std::reverse_iterator<iterator>;
using const_reverse_iteratot = std::reverse_iterator<const_iterator>;
iterator begin() {
  return iterator{arr_};
}
iterator end() {
  return iterator{arr_ + size_};
}
const_iterator begin() const {
  return const_iterator{arr_};
}
const_iterator end() const {
  return const_iterator{arr_ + size_};
}
const_iterator cbegin() const {
  return const_iterator{arr_};
}
const_iterator end() const {
  return const_iterator{arr_ + size_};
}
```


## Виртуальное
#### Наследование
```c++
struct Mom: virtual Granny {}
struct Dad: virtual Granny {}
struct Son: Mom, Dad {}
// в памяти - ptr M ptr D S G
```
Для классов, у которых есть виртуальный предок, хранится указатель на виртуальную таблицу, которая хранит сдвиги относительно начала виртуальных предков
- static_cast к предку при виртуальном наследовании неприменим 
#### Функции
**Опр**: тип называется полиморфным, если у него есть хотя бы одна виртуальная функция
- виртуальные функции работают только при полном совпадении сигнатуры функций (решение - override)
```c++
struct S {
  virtual void f(int a = 10) = 0;
}

struct C: S {
  void f(int a = 20) override { std::cout << a; }
}

int main() {
  C c;
  S&s = c;
  s.f(); // 10
}
```

```c++
struct Base {
  /*virtual*/void f() { // 2
    std::cout << 1;
  }
}

struct Derived: Base {
  void f() {
    std::cout << 2;
  }
}

int main() {
  Derived d;
  Base& b = d;
  b.f(); // 1
}
```

```c++
struct Base {}
struct Derived: Base {}

int main() {
  Base* b = new Derived();
	  delete b; // dtor of Base
}
```

```c++
struct Base {
  virtual void f() {
    std::cout << "1";
  }
}

struct Derived: Base {
 private:
  void f() final {
    std::cout << "2";
  }
}

int main() {
  Derived d;
  Base& b = d;
  b.f(); // 2
}
```

```c++
struct Mom {
  virtual void f() {
    std::cout << "1";
  }
}

struct Dad {
  void f() {
    std::cout << "2";
  }
}

struct Son: Mom, Dad {
  void f() {
    std::cout << "3";
  }
}

int main() {} // OK, not CE
```

```c++
struct Mom {
  virtual void f() {
    std::cout << "1";
  }
}

struct Dad {
  virtual void f() {
    std::cout << "2";
  }
}

struct Son: Mom, Dad { 
  void f() override {
    std::cout << "3";
  }
}

int main() {
  Son s;
  Mom& m = s;
  Dad& d = s;
  m.f(); // 3
  d.f(); // 3
}
```
##### pure virtual functions
Класс называется абстрактным, если он имеет хотя бы одну pure virtual function.
Объекты абстрактных классов нельзя создавать
```c++
struct Base {
  virtual void f(int a) = 0;
  virtual ~Base() = default;
}
```

#### dynamic_cast
**Опр**: RTTI - RunTime Type Information
В чем отличие от static_cast? static_cast даёт UB, если не угадали тип
dynamic_cast - бросит исключение
- от любого полиморфного типа можно делать каст к void*
- всегда можно делать каст вверх
```c++
struct Base {
  virtual void f() {}
}

struct Derived: Base {
  void f() override {}
}

int main() {
  Derived d;
  Base& b = d;
  dynamic_cast<Derived&>(b);
}
```

```c++
// каст вбок. Полиморфным должен быть только тип, от кого мы кастимся.
struct Mother {
  virtual ~Mother() = default;
};
struct Dad {};
struct Son: Mother, Dad {}

int main() {
  Son s;
  Mother& m = s;
  Dad& d = dynamic_cast<Dad&>(m);
}
```

#### Устройство виртуальных функций
- у полиморфных объектов сначала лежит ptr
```c++
struct Base {
  virtual void f() {} // sizeof(Base) = 8
}
// поля:    |  ptr  |
// vtable:  |  &type_info   |   &Base::f  |
```
У Base есть vtable. Хранит указатель на Base 
```c++
struct Base {
  virtual void f() {}
  int x;
}

struct Derived: Base {
  void f() override {}
  virtual void g() {}
  int y;
}
// объект Derived выглядит как:
// |  ptr  |  x  |  y  |
// vtable:  |  &Derived_typeinfo  | &Derived::f  |  &Derived::g  |
```

```c++
struct Granny {}
struct Mom: Granny {
  virtual ~Mom() = default;
}
struct Son: Mom{}

int main() {
  Son s;
  Granny& g = s;
  dynamic_cast<Son&>(g); // RE
  dynamic_cast<Granny&>(s); // OK
}
```
#### Проблемы виртуальных функций
- нельзя оставить виртуальную функцию без определения
```c++
struct Base {
  virtual void h() = 0;
  void f() {}
  Base() {
    std::cout << "Base";
    h();
    // в конструкторах механизм виртуальных функций не работает
  }
  virtual ~Base() = default;
}

struct Derived: Base {
  int x;
  void h() override {
    std::cout << "h";
  }
  Derived() = default;
 }

int main() {
  Derived d; // ошибка линкера..
}
```
## Аллокаторы
#### implementation
```c++
// на самом деле немного не так
// должны присутствовать только allocate и deallocate
template <typename T>
struct allocator {
  T* allocate(size_t count) {
	return static_cast<T*>(operator new(count * sizeof(T)));
    //return reinterpret_cast<T*>(new std::byte[count * sizeof(T)]);
  }
  
  void deallocate(T* ptr, size_t) {
    operator delete(ptr);
    //delete[] reinterpret_cast<std::byte*>(ptr);
  }

  template<typename U, typename... Args>
  void construct(U* ptr, const Args&... args) {
    new (ptr) U(args...);
  }

  template<typename U>
  void destroy(U* ptr) {
    ptr->~U();
  }

  template <typename U>
	  allocator(sllocator<U>) {}
}
```

#### использование аллокаторов (на примере вектора)
```c++
template <typename T>
class vector {
  T* arr_;
  size_t size_;
  size_t cap_;
  
  void reserve(size_t new_cap) {
    if (new_cap <= cap_) {
      return;
    }
    T* new_arr = AllocTraits::allocate(alloc_, new_cap);
    size_t i = 0;
    try {
      for (; i < sz_; ++i) {
        AllocTraits::construct(alloc_, new_arr + i, std::move(arr_[i]));
      }
    } catch (...) {
      for (size_t new_index = 0; new_index < i; ++new_index) {
        AllocTraits::destroy(alloc_, new_arr + new_index);
      }
      AllocTraits::deallocate(alloc_, new_arr, new_cap);
      throw;
      }
    for (size_t i = 0; i < size_; ++i) {
      AllocTraits::destroy(alloc_, arr_ + i);
    }
    AllocTraits::deallocate(alloc_, arr_, cap_);
    arr_ = new_arr;
    cap_ = new_cap;
  }

  void push_back(const T& value) {
    emplace_back(value);
  }
  
  void push_back(T&& value) {
    emplace_back(std::move(value));
  }

  template <typename Args...>
  void emplace_back(Args&&... args) {
	if (size_ == cap_) {
      reserve(cap_ > 0 ? cap_ * 2 : 1);
    }
    AllocTraits::construct(alloc_, arr_ + size_, std::forward<Args>(args)...);
    ++size_;
  }
}

```
#### select_on_container_copy_construction
Что должен делать контейнер при копировании? 
Создавать новый аллокатор по умолчанию или копировать аллокатор (использовать тот же пул)?
- по умолчанию копирует аллокатор
```c++
template <typename T, typename Alloc = std::allocator<T>>
template <typename T, typename Alloc>
class List {
  List(const List& other)
     :  alloc_(node_alloc_traits::select_on_container_copy_construction(  
          other.alloc_)) ... {...}
}
```
#### propogate_on_container_copy_assignment
На чьем пуле выделять память? Если true, копируем аллокатор. Иначе создаем новый
```c++
vector& operator=(const vector& other) {
  // на самом деле нужно проверить не равны ли аллокаторы. Если равны, то используем старую память вместо выделения новой
  Alloc newalloc = AllocTraits::propagate_on_container_copy_assignment::value ?
  other.alloc_ : alloc_;
  T* newarr = AllocTraits::allocate(newalloc, other.cap_);
  size_t i = 0;
  try {
    for (; i < other.sz_; ++i) {
	  AllocTraits::construct(newalloc, newarr + i, other[i]);
	}
  } catch(...) {
   for (size_t j = 0; j < i; ++j) {
     AllocTraits::destroy(newalloc, newarr + j);
   }
   AllocTraits::deallocate(newalloc, newarr, other.cap_);
   throw;
  }
  for (size_t i = 0; i < sz_; ++i) {
    AllocTraits::destroy(alloc_, arr_ + i);
  }
  AllocTraits::deallocate(alloc_, arr_, cap_);

  arr = newarr;
  sz_ = other.sz_;
  cap_ = other.cap_;
  alloc_ = newalloc;
}
```
#### propagate_on_container_move_assignment
1. Если значение true, то можно мувнуть аллокатор. Довыделяем необходимую память и муваем поля, предварительно вызвав деструкторы у старых
2. Если значение - false, то у аллокатора должен быть другой пул.
- аллокаторы равны: 1-ый пункт, муваем аллокатор
- аллокаторы не равны: деаллоцируем память, выделяем новую другим аллокатором, муваем поля
#### new and delete
>new выделяет память (можно переопределить) и вызывает конструктор (нельзя переопределить)

- стандартный new\[] вызывает operator new
- new(std::nothrow) int\[1000000000] - не бросает исключение, возвращает nullptr
- стандарт гарантирует, что если создается объект через new с кастомными параметрами, то вызовется симметричный delete для объектов
- операторы new и delete, перегруженные в классе, по умолчанию static
```c++
void* operator new(size_t n) {
  return malloc(n);  
}

void operator delete(void* ptr) {
  free(ptr);
}

```

```c++
struct Example {
  int* x;
  Example(): x(new int) {}
  ~Example() { delete x; }
};

int main() {
  Example* example = new Example[1];
  example[0].~Example();
  operator delete[](example);
   // для нетривиальных типов (у кого надо вызывать деструктор) new[] делает сдвиг влево
}
```
##### Реализация new в STL
```c++
static void* operator_new_impl(std::size_t size) {
  if (size == 0)
    size = 1; // выделяет 1 байт когда просят 0...
  void* p;
  // перед тем, как бросать исключение, пытается вызвать new_handler. Можно переопределить с помощью set_new_hadler (будет вызываться функция вместо бросания исключения)
  while ((p = std::malloc(size)) == nullptr) {
    std::new_handler nh = std::get_new_handler();
    if (nh)
      nh();
    else
      break;
  }
  return p
}

static void* operator_new(std::size_t size) {
  void* p = operator_new_impl(size);
  if (p == nullptr) 
    __throw_bad_alloc_shim();
  return p;
}
```
> чтобы запретить создание объекта на куче - operator new(size_t size) = delete
>  на стеке - сделать декструктор приватным. чтобы удалить объект вызываем void operator delete(T* ptr, std::destroying_delete_t), которая избавляет от необходимости вызывать деструктор
## Аттрибуты
- no_unique_address (вместо empty base optimization)
- nodiscard - ставится перед функциями, и кидает warning, если результат функции не используется
> empty помечена nodiscard, чтобы люди не путали ее с clear
- deprecated - компилятор кидает warning при использовании помеченной функции
- maybe_unused - компилятор не будет кидать warning, если функция/переменная не используется. Особенно полезно в шаблонном коде
- likely/unlikely - можно ставить после if'а, помечая, что определенная ветка более вероятна чем другая. Помогает оптимизировать код
- assume(expression) (пример - assume(h(x), x == z)) - оптимизация. Помогает сказать в каких пределах лежит переменная компилятору
## Move-семантика
Для rvalue применяются мувающие операции, для lvalue - копирующие
#### Проблема
создается два объекта типа string
```c++
vector<string> v;
v.push_back("abc"); // push_back принимает по const ссылке объект типа T, то есть создается временный string, который фактически не нужен
v.emplace_back // не работает если вектор векторов векторов векторов....
```
#### Правило пяти
> Если нетривиально реализован хотя бы один из методов, нужно реализовать всех
- Оператор присванивания
- Конструктор копирования
- Деструктор
- Move-конструктор
- Move оператор присваивание

>Если не определены move конструктор и move оператор присваивания, но есть нетривиальные конструктор копирования и оператор присваивания, то move не вызывается 
#### swap
```c++
void swap(T& x, T& y) {
  T tmp = std::move(x);
  x = std::move(y);
  y = std::move(tmp);
}
```
#### Реализация на примере string

```c++
class string {
  string(string&& other) : arr(other.arr), sz(other.sz), cap(other.cpp) {
    other.arr = nullptr;
    other.sz = other.cap = 0;
  }

  string& operator=(string&& other) {
    if (this == &other) {
      return *this;
    }
	delete[] arr;
    arr = other.arr;
    other.arr = nullptr;
    cap = other.cap;
    sz = other.sz;
    other.cap = other.sz = 0;
  }

  char* arr_;
  size_t sz_
  size_t cap_;
}
```
для тривиальных типов move просто **копирует**. default move у string вызывает move всех объектов, то есть копирует -> работает некорректно
#### Реализация std::move
##### Наивная реализация
```c++
template <typename T>
T&& move(T& x) {
  return static_cast<T&&>(x);
}
```
##### Не совсем наивная
```c++
// move от T& возвращает lvalue. Проблема..
template <typename T>
std::remove_reference_t<T>&& move(T&& value) {
  return static_cast<std::remove_reference_t<T>&&>(value);
}
```

#### lvalue and rvalue
Ошибочное представление - "rvalue - то, чему нельзя присваивать"
Контрпримеры:
1. rvalue, которому можно присваивать - vector\<bool>
2. lvalue, которому нельзя присваивать - переменная с удаленным оператором присваивания
> Это категории **выражений**, а не типов.

каждое выражение обладает двумя независимыми свойствами: *type и value*

|                            lvalue                             |                               rvalue                                |
| :-----------------------------------------------------------: | :-----------------------------------------------------------------: |
|                        id (переменные)                        |                     литералы (кроме строковых)                      |
|                                                               |                 + - * / % >> << & \| ^ && \|\| ~ !                  |
|              = \*= += -= >>= <<= %= /= &= \|= ^=              |                           > < <= >= == !=                           |
|                      ++expr      --expr                       |                          expr++     expr--                          |
|                        \*ptr    a\[i]                         |                                 &a                                  |
|               запятая, если правая часть lvalue               |                  запятая, если правая часть rvalue                  |
|                 ?: если оба выражения lvalue                  |                ?: если хотя бы одно выражение rvalue                |
| function call when return type is T&<br> (or cast expression) | function call when return type is T or T&&<br> (or cast expression) |
#### rvalue ссылки
##### Rules!
> 1. Будучи возвращенной из функции является rvalue
> 2. Проинициализировать можно только rvalue выражением
> 3. Могут продлевать жизнь объектов

```c++
int&& y = 6;
y = 7; // OK

int&& z = y; // CE, y - lvalue
int&& z = std::move(y); // OK
int&& z = static_cast<int&&>(y); //OK

int t = 1;
z = t; // OK, z = 1, y = 1; 

string x = "abc";
string&& y = std::move(x);
string t = std::move(x); // x, y - пустые строки
```

##### Взимодействие const и rvalue-references
> Применяются обычные правила

```c++
void f(const T& x) {
  g(std::move(x));
}
// если g определена для const T& и от T&&, то выберется const T& из-за константности, попадаем в копирующую версию

T&& move(T& x) {
  return static_cast<T&&>(x);
}

// или

const string x = "abc";
string y = std::move(x); // вызывается конструктор копирования
```
##### Reference qualifiers
```c++
void f() {
  return S{"abc"}; 
}

struct S {
  std::string str;
  std::string getData() const {
    return str;
  }
}

int main() {
  f().getData(); // создается копия 
}

std::string getData() && {
  return std::move(str); // не будет копирования
}

std::string getData() & {
  return std::move(str); // только от lvalue
}

std::string getData() const & {
  return std::move(str); // приняли себя по const ссылке, от lvalue and rvalue
}
```

```c++
a + b = 5 // никто не запрещает вызывать оператор присванивания от rvalue


T& operator=(const T& value) & {

}
// чтобы это избежать, надо поставить &
```
#### Forwarding references
Почему rvalue ссылок недостаточно?
Если часть аргументов передали как rvalue, а другую - как lvalue, то нужно и дальше передать аргументы с сохранением вида value
```c++
template <typename... Args>
void emplace_back(const Args&... args); // копирование rvalue
// перегрузка не спасает, так как переменное число аргументов

template <typename U, typename... Args>
void contruct(U* ptr, Args&... args) //никакой тип не подходит для args
```

***Решение***
```c++
void construct(U* ptr, Args&&... args) {
  new(ptr) U(std::forward<Args>(args)...);
}
// многоточие через запятую повторяет паттерн!!!
void emplace_back(Args&&... args) {
  alloc_.construct(ptr, std::forward<Args>(args)...);
}
```

> **великий костыль** rvalue ссылка, являясь шаблонным параметром ***функции*** вида T&&, на самом деле универсальная ссылка
#### Правила вывода шаблонного типа
```c++
template <typename T>
void h(T&& x);

template <typename T>
void g(T& x);

template <typename T>
void f(T x);

// работает правила - навешивание & на T, если вызвались от lvalue в f(T&&
int main() {
  int x = 0;
  f(x); // T = int, x = int
  g(x); // T = int, x = int&
  h(x); // T = int&, x = int&
  int& y = x;
  f(y); // T = int, x = int
  g(y); // T = int, x = int&
  h(y); // T = int&, x = int&
  int&& z = std::move(x);
  f(z); // T = int, x = int
  g(z); // T = int, x = int&
  h(z); // T = int&, x = int&

  f(std::move(x)); // T = int, x = int
  g(std::move(x)); // CE
  h(std::move(x)); // T = int, x = int&&
}
```

**reference collapsing rules**
- & + & = &
- && + & = &
- & + && = &
- && + &&  = &&
#### std::forward implementation
```c++
template <typename T>
T&& forward(T&& value) {
  return value;
}
/*
1) Args = T = int& (т.к. явно указываем шаблонный параметр)
   typeof(args) = int&
   typeof(value) = int&
2) Args = T = int
   typeof(args) = int&&, но args - lvalue!
*/
// std::forward<Args>. Args - изначально rvalue, передаем в forward args (lvalue)
// то есть пытаемся инициализировать T&& от lvalue

template <typename T>
T&& forward(T& value) {
  return value;
}
// пытаемся инициализировать возвращаемое значение T&& от value типа  T&

template <typename T>
T&& forward(std::remove_reference_t<T>& value) {
  return static_cast<T&&>(value);
}
```
Причины принимаемого типа:
1. Компилятор не сможет неявно вывести тип аргумента, и тогда move применялся бы неправильно. Пример: передаем int&, компилятор выводит тип args как int => обращаемся с lvalue как с rvalue
2. есть forward от rvalue, поэтому есть следующая перегрузка

```c++
template <typename T>
T&& forward(std::remove_reference_t<T>& value) {
  return static_cast<T&&>(value);
}

template <typename T>
T&& forward(std::remove_reference_t<T>&& value) {
  static_assert(!std::is_lvalue_reference_v<T>);
  return static_cast<T&&>(value);
}

// forward<Args>(f<Args>(args)...);
```
#### push-back with move semantics
```c++
template <typename T>
class vector {
  T* arr_;
  size_t size_;
  size_t cap_;
  
  void reserve(size_t new_cap) {
    if (new_cap <= cap_) {
      return;
    }
    T* new_arr = AllocTraits::allocate(alloc_, new_cap);
    size_t i = 0;
    try {
      for (; i < sz_; ++i) {
        AllocTraits::construct(alloc_, new_arr + i, std::move(arr_[i]));
      }
    } catch (...) {
      for (size_t new_index = 0; new_index < i; ++new_index) {
        AllocTraits::destroy(alloc_, new_arr + new_index);
      }
      AllocTraits::deallocate(alloc_, new_arr, new_cap);
      throw;
      }
    for (size_t i = 0; i < size_; ++i) {
      AllocTraits::destroy(alloc_, arr_ + i);
    }
    AllocTraits::deallocate(alloc_, arr_, cap_);
    arr_ = new_arr;
    cap_ = new_cap;
  }

  void push_back(const T& value) {
    emplace_back(value);
  }
  
  void push_back(T&& value) {
    emplace_back(std::move(value));
  }

  template <typename Args...>
  void emplace_back(Args&&... args) {
	if (size_ == cap_) {
      reserve(cap_ > 0 ? cap_ * 2 : 1);
    }
    AllocTraits::construct(alloc_, arr_ + size_, std::forward<Args>(args)...);
    ++size_;
  }
}

```

- теряется exception safety
 **Решение**
 move_if_noexcept(arr_\[i])
 => если move конструктор, move конструктор копирования не noexcept, то объекты копируются
 ***Мораль***: помечать конструкторы noexcept
#### Expired values, RVO and copy elision
![[value_types.png]]
##### xvalue:
- результат вызова функции, если возвращаемый тип T&&
- cast к rvalue ссылке
- обращение через . -> \[]
- запятая, тернарный оператор при определенных условиях

> value - объект, которому соответствует оперделенная якейка в памяти.
>  prvalue  - не соответствует (till c++17). до этого - может не соответствовать
>  xvalue (rvalue) создается и положится в память, но ведет себя как rvalue\
##### guaranteed copy elision (since c++17)

```c++
int main() {
  string a = string("abc"); // 1 конструктор
  string b = std::move(string("abc")) // 2 конструктора
}
```

> temporary materialization (материализация ввременного) происходит в слеующих случаях

- инициализация ссылки через prvalue
- обращение через . ->
- array-to-pointer conversion
- применение typeid, sizeof
- игнорируем результат prvalue выражения

##### lvalue-to-rvalue conversion

- glvalue может сконвертировать к prvalue
> неформально: считывание из памяти в регистр процессора

**Пример** : int x = \*p;

##### Return value optimization
RVO происходит в случае возврата из функции локальной переменной, созданной в теле функции
```c++
std::string f() {
  std::string str = ("abc");
  return str; // std::move испортит NRVO (named rvo)
}
```

Даже если компилятор не может использовать RVO, но возращается локальная переменная, то он ее мувает.

```c++
S f(S&& a) {
  return std::move(a); 
  // в таком случае без std::move объект будет копироваться, т.к. a - rvalue
}
```
## Type deduction
#### auto
- auto выводит тип с помощью шаблонной подстановки
- отбрасывает const и ссылки
```c++
int main() {
  auto x = 5;
  auto&& y = x; // OK, auto&& - универсальная ссылка
}

template <typename Container>
void f(Container&& container) {
  for (auto&& value: container) // make sense, позволяет правильно обрабатывать как и rvalue, так и lvalue случаи
}
```

- auto может использоваться в качестве типа возвращаемого параметра функции

```c++
template <typename T>
auto g(T x) {
  return ++x;
}

int main() {
  auto x = g(1); // return auto = int
  auto& x = g(1); // CE, rvalue initialize lvalue ref
  auto&& x = g(1); // OK
}


template <typename T>
auto& g(T x) {
  return ++x;
}

int main() {
  auto x = g(1); // копируем
  auto& x = g(1); // битая ссылка
  auto&& x = g(1); // битая ссылка, ub
}
```

Возвращение разных типов в зависимости от runtime условия - **CE**
От compile-time условия - **OK**

```c++
template <typename T>
auto g() {
  if constexpr (std::is_same_v<T, int>)
    return 0;
  else:
    return 1u;
}
```

**в initializer_list**
```c++
template <typename T>
void h(T) {}

int main() {
  auto lst = {1, 2, 3}; // auto = initializer_list, OK
  h({1, 2, 3}); // CE
}
```
**в шаблонах**
```c++
template <auto N>
void f() {}
```
#### trailing return type
```c++
template <typename T>
std::remove_reference_t<T>&& move(T&& value) {
  return static_cast<std::remove_reference_t<T>&&>(value);
} // выглядит вообще не cool

template <typename T>
auto move(T&& value) -> std::remove_reference_t<T>&& {
  return static_cast<std::remove_reference_t<T>&&>(value);
} // тоже не особо cool по-моему...
```

#### auto в принимаемых типах (since c++20)

```c++
void f(auto&& x) {...}
// equal to
template <typename T>
void f(T&& x) {...}
```
#### decltype
- **НЕ** отбрасывает ссылки
- можно брать от выражений

```c++
int main() {
  int x = 0;
  decltype(x) y = x;
  decltype(x)&& z = x; // CE - rvalue ref
  decltype(++x) u = x; // не вычисляет выражение под собой, берет return type
  decltype(throw 1)* p = &x; // void*.......
  const decltype(throw 1)* p = &x; // const void*
  const decltype(&x) l = &x; //o int* const
  decltype((x)) k = x; // k - ссылка на x
}
```

###### Decltype от expression

	1. Если expression xvalue, то decltype = T&&
	2. Если expression lvalue, то decltype = T&
	3. Если expression rvalue, то decltype = T

###### Decltype as return type

```c++
// 1 не сработает, если возвращаемый тип не ссылка
template <typename Container>
auto& getElement(Container& cont, size_t index) {
  return cont[index];
}

// 2 все ок, на месте auto нельзя написать decltype
template <typename Container>
auto getElement(Container& cont, size_t index)
  -> decltype(cont[index]) {
  return cont[index];
}

// 3 same as 2
template <typename Container>
decltype(auto) getElement(Container& cont, size_t index) {
  return cont[index];
}

int main() {
  std::vector<bool> v(5);
  getElement(v, 0) = 1;
}
```

**причина** по которой нельзя оборачивать возвращаемое значение в скобки
```c++
template <typename Container>
decltype(auto) getElement(Container& cont, size_t index) {
  decltype(auto) elem = cont[index];
  return (elem); // навесится & в люьом случае
}
```

#### Deducing this

нужны версии
1. const от lvalue
2. const от rvalue
3. не const от lvalue
4. не const от rvalue
**Решение:** (since c++23)

```c++
template <typename Self>
decltype(auto) value(this Self&& self) {
  return reinterpret_cast<...>(*self.value);
}
// или
decltype(auto) value(this auto&& self) { // auto& or auto&& or auto
  return reinterpret_cast<...>(*self.value);
}
```

**forward_like** (since c++23)
Хотим вернуть U со свойствами (константность и вид value) типа T
```c++
template <typename T, typename U>
auto&& forward_like(U&& x) {
  bool is_adding_const = std::is_const_v<std::remove_reference_t<T>>;
  if constexpr (std::is_lvalue_reference_v<T&&>) {
    if constexpr (is_adding_const)
      return std::as_const(x);
    else 
      return static_cast<U&>(x);
  } else {
      if constexpr (is_adding_const)
        return std::move(std::as_const(x));
      else 
        return std::move(x);
  }
}
```
## Tuple
```c++
int main() {
  std::tuple t{2, 5, "abc"};
  auto& d = std::get<1>(t); // обращение по индексу 1
  // structured bindings since c++17
  auto& [x, y, z] = t; // распаковка кортежа

  int x, y;
  std::tuple<char, short> coord(6, 9);
  std::tie(x, y) = coord; // implicit conversions are allowed
}
```

**Structured bindings** применимы к:
1. array
2.  tuple-like type (определена tuple_size<\E>::value, tuple_element)
3. структура которая опускает агрегатную инициализацию

- Make tuple  **отбрасывает ссылки**, принимает по универсальной ссылке
- tie **не отбрасывает**, принимает по lvalue ссылке
-  forward_as_tuple принимает по универсальной, **не отбрасывает ссылки**

```c++
template <typename... Args>
struсt S {
  S(const Args&...) : values(std::make_tuple(values...)) {} // UB
  std::tuple<const Args...> values;
}
```

#### idea of tuple implementation
```c++
// 1 способ хранить аргументы
template <typename Head, typename... Tail>
struct tuple {
  Head head;
  tuple<Tail...> tail;
}

// 2 (на стеке переменные хранятся в обратном порядке)
template <typename Head, typename... Tail>
struct tuple: tuple<Tail...>{
  Head head;
}


template <typename Head, typename... Tail>
struct tuple {
  Head head;
  tuple<Tail...> tail;
}

// tuple можно конкатенировать
```

#### std::ignore
```c++
std::tie(iter, std::ignore) = set.insert(value);
auto [iter, _] = set.insert(value); // since c++23
// можно использовать несколько _ в одной области видимости
```

std::piecewise_construct - позволяет принимать tuple в конструктор пары

#### std::enable_if
позволяет "отключать" версии перегрузки в зависимости от compile time условий

```c++
template <typename T, std::enable_if_t<условие, bool> = true> // присвоили значение по умолчанию
```
## smart pointers
#### unique_ptr
- Запрещается **copy** копирование и присваивание
- Разрешается **move** копирование и присваивание
- есть метод get (возвращает T*), reset (зануляет)
- вторым шаблонным параметром имеет Deleter, который имеет перегруженные (ptr)
##### make_unique
- указатель на объект создается не пользователем в отличие от инициализации объекта типа make_unique

```c++
template <typename T, typename... Args>
unique_ptr<T> make_unique(Args&&... args) {
  return unique_ptr<T>(new T(std::forward<Args>(args)...));
}
```
- защита от внезапных исключений
```c++
template <typename T>
void f(unique_ptr<T> ptr, int a);

int main() {
  f(unique_ptr<int>(new int(5)), g());
  // порядок вычисления аргументов функции не стандартизовано
  // до c++17 возможно было поведение: new -> g -> unique_ptr<int>{}
  // g может бросить исключение)))))
}
```
#### shared_ptr
```c++
template <typename T>
class shared_ptr {
 public:
  shared_ptr(T* ptr) : ptr(ptr), count(new size_t(1)) {
	if constexpr (std::is_base_of_v<std::enable_shared_from_this<T>, T>) {
		ptr->sptr = *this;
	}
  }
  shared_ptr(const shared_ptr& other) : ptr(ptr), count(count) {
    ++count;
  }

  template <typename Deleter>
  shared_ptr(T* ptr, Deleter del) {
  }
  
  ~shared_ptr() {
    if (!count) {
      return;
    }
    --*count;
    if (!*count) {
      delete count;
      delete ptr;
    }
  }
 private:
  T* ptr;
  size_t* count;
  template <typename U, typename... Args>
  friend shared_ptr<U> make_shared(Args&&...);

  shared_ptr(ControlBlock* ptr)
}
```
- можно создавать shared_ptr от типа, являющегося наследником T => ControlBlock может хранить U - наследника T
##### make_shared
- указатель на объект создается не пользователем в отличие от инициализации объекта типа shared_ptr
- **позволяет вызывать new один раз** вместо двух (на счетчик и на поинтер сразу)
```c++
template <typename T>
shared_ptr<T>::ControlBlock {
  T value;
  size_t count;
};

template <typename T, typename... Args>
shared_ptr<T> make_shared(Args&&... args) {
    auto* p = new typename shared_ptr<T>::ControlBlock{T(std::forward<Args>     (args)...), 1}
  return shared_ptr<T>(p);
}
```

	Как определять, есть ли в конкретном shared_ptr ControlBlock?
1. хранить bool, зашитый в последний бит шарика
2. size_t* count = nullptr, если в классе есть ConrolBlock, иначе нет
***reset()***
- уменьшает счетчик / удаляет объект в зависимости от количества поинтеров на объект
***use_count()***
##### enable shared from this
- возвращает количество объектов с таким же ptr
```c++
template <typename T>
struct enable_shared_from_this {
  weak_ptr<T> sptr;
  shared_ptr<T> shared_from_this() const {
    return sptr;
  }

  template <typename U>
	friend class shared_ptr;
};

strcut S: public enable_shared_from_this<S> {
  std::shared_ptr<S> getObject() {
    return shared_from_this();
  }
};
```

- если S создается не при помощи shared_ptr, то кидается исключение
- weak_ptr для избежания циклов
##### weak ptr (+shared)
- при наличии циклических зависимостей возникают траблы

***lock()***
- создается новый shared_ptr на объект под weak_ptr
- при отсутствии объекта создается пустой shared_ptr
***expired()***
- проверяет жив ли объект
- для реализации необходим еще один count на weak_ptr

```c++
template <typename T>
class shared_ptr {
 public:
  shared_ptr(T* ptr) : ptr(ptr), count(new size_t(1)) {
    if constexpr (std::is_base_of_v<std::enable_shared_from_this<T>, T>) {
	  ptr->sptr = *this;
	}
  }
  shared_ptr(const shared_ptr& other) : ptr(ptr), count(count) {
    ++count;
  }
  
  ~shared_ptr() {
    if (!count) {
      return;
    }
    --*count;
    if (!*count) {
      delete ptr;
    }
  }
 private:
   
  struct BaseControlBlock {
    size_t shared_count;
    size_t weak_count;
    virtual ~BaseControlBlock() = 0;
  };

  template <typename U, typename Deleter, typename Alloc>
  struct ControlBlockRegular: BaseControlBlock {
    Deleter del;
    Alloc alloc;
  }

  template <typename U, typename Alloc>
  struct ControlBlockMakeShared: BaseControlBlock {
    U value;
    Alloc alloc;
    // конструкторы, getcopy, move
  }

  struct ControlBlockWithValue: ControlBlock{
    T value;
  };
  
  T* ptr;
  BaseControlBlock* count;
  template <typename U, typename... Args>
  friend shared_ptr<U> make_shared(Args&&...);
  shared_ptr(ControlBlock* ptr)
}

```

Тоооо есть
1. shared_ptr(T* ptr, Deleter del) - хотим создать count через new, но удалить объект по-особенному
2. shared_ptr(T* ptr, Alloc alloc) - count создаем по-особому, в деструкторе зовем delete
3. shared_ptr(T* ptr, Alloc alloc, Deleter del) - все по-особому
4. создание через make_shared - не нужен нестандартный Deleter

Деструктор работает следующим образом:
- если shared_count == 0 и weak_count == 0 - вызывается деструктор control block при помощи лежащего в нем аллокатора
- shared_count == 0 и weak_count != 0 - вызывается деструктор T
- shared_count != 0 - shared_count -= 1
#### Type erasure
##### std::any
- Idea
```c++
int main() {
  std::any a = 5;
  a = 4.13; // OK
  a = std::vector<int>{1, 2, 3, 4};
  std::cout << std::any_cast<std::vector<int>&>(a)[0]; // copy without ref
}
```
- Realization
```c++
class any {
 private:
 
  struct Base {
    virtual Base* getCopy() const = 0;
    virtual ~Base() = default;
  };

   template <typename T>
   struct Derived: public Base {
     T value;
     Derived(const T& value): value(value) {}
     Derived(T&& value): value(std::move(value)) {}
     Base* getCopy() const override {
       return new Derived(value);
     }
   }
  Base* ptr;

 public:
  template <typename T>
  any(const T& value): ptr(new T(value)) {}

  template <typename T>
  any(const any& other): ptr(other.ptr->getCopy()) {}
  
  ~any() {
    delete ptr;
  }
};

template <typename T>
T any_cast(any& a) {
  auto* p = dynamic_cast<any::Derived<std::remove_reference_t<T>>*>(a.ptr);
  if (!p) {
    throw std::bad_any_cast;
  }
  return p->value;
}
```
##### Allocate shared
```c++
template <typename T, typename Alloc, typename... Args>
shared_ptr<T> allocate_shared(const Alloc& alloc, Args&&... args) {
  using BlockAlloc = typename std::alocator_traits<Alloc>::rebind_alloc<ControlBlockMakeShared<T, Alloc>>
  BlockAlloc ba = alloc;
  auto* ptr = ba.allocate(1);
  ba.construct(ptr, 1, 0, std::forward<Args>(args)..., alloc);
  return //shared ptr от приватного конструктора
}
```
## SFINAE
#### Basic primitives
```c++
// 1. тождестванная метафункция
template <typename T>
struct type_identity {
  using type = T;
}

template <typename T>
using type_identity_t = type_identity<T>::type;

// 2.
template <typename T, T x>
struct integral_constant {
  static constexpr T value = x;
};

template <bool b>
using bool_constant = integral_constant<bool, b>;

using true_type = bool_constant<true>;
using false_type = bool_constant<false>;

// 3.
template <typename T>
struct is_lvalue_reference: false_type {};

template <typename T>
struct is_lvalue_reference<T&>: true_type {};

template <typename T>
inline constexpr bool is_lvalue_reference_v = is_lvalue_reference<T>::value;

// 4. лишнее инстанцирование
template <typename... Types>
struct conjuction {
  static constexpr bool value = (Types::value && ...);
}

// правильная реализация
template <typename...>
struct conjuction: true_type {};

template <typename T>
struct conjuction<T>: T {};

template <typename T, typename... U>
struct conjuction<T, U...>:
conditional_t<bool(T::value), conjuction<U...>, T> {};

```
#### enable if, the idea of SFINAE
**Substitution failure is not an error**
```c++
template <typename T>
auto f(T value) -> typename T::value_type {
  std::cout << 1;
}

// c-style variadic function
void f(...) {
  std::cout << 2;
}

int main() {
  f(0); // 2
  std::vector<int> v;
  f(v); // 1
}
```

==Важно!== Работает только в объявлении функций и в сигнатуре шаблона
```c++
// CE для типов, не имеющих value_type
template <typename T>
auto f(T) {
  typename T::value_type result;
  return result;
}
```
##### Enable_if
```c++
template <typename B>
struct enable_if {};

template <>
struct enable_if<true> {
  using type = void;
}

template <bool B>
using enable_if_t = enable_if<B>::type;

template <typename T, typename f = enable_if_t<std::is_integral_v<T>>>
void f(T) {}

template <typename... Types>
void f(Types...) {}
```

**Проблема:** функция может принимать и 0 аргументов. Что если она всегда должна принимать один аргумент?

```c++
// 1 вариант
template <typename T, typename f = enable_if_t<std::is_integral_v<T>>>
void f(T) {}

template <typename... Types,  typename f = enable_if_t<!std::is_integral_v<T>>,
typename = void>
void f(T) {}

// 2 вариант (правильная реализация enable_if)
template <typename B, typename T = void>
struct enable_if {};

template <typename T = void>
struct enable_if<true, T> {
  using type = T;
}

template <bool B, typename T = void>
using enable_if_t = enable_if<B>::type;

template <typename T, enable_if_t<std::is_integral_v<T>, T> = true>
void f(T);

template <typename T, = enable_if_t<!std::is_integral_v<T>, T> = true>
void f(T);
```
#### metafunctions using SFINAE
##### is_class
```c++
template <typename T>
std::true_type test(int T::*);

template <typename>
std::false_type test(...);

template <typename T>
struct is_class : decltype(test<T>(nullptr)) {};

template <typename T>
inline constexpr bool is_class_v = is_class<T>::value;
```
- int T::* - указатель на поле типа int от T. Проверяется корректность выражения на этапе компиляции
##### is_polymorphic
```c++
template <typename T>
std::true_type detect_is_polymorphic(
decltype(dynamic_cast<const volatile void*>(static_cast<T*>(nullptr)))
)

template <typename T>
std::false_type detect_is_polymorphic(...);

template <typename T>
struct is_polymophic : decltype(detect_is_polymorphic<T>(nullptr))
```
##### has_method_construct
```c++
template <typename T, typename... Args>
std::true_type helper(decltype(T().construct(Args()...))* );

template <typename...>
std::false_type helper(...);

template <typename T, typename... Args>
struct has_method_construct: decltype(test<T, Args...>(nullptr)) {}

struct S {
  int& construct(int, int); // CE
}

int main() {
  static_assert(!has_method_construct<S, int, int, int>::value);
}
```
***Решение*** comma trick

```c++
template <typename T, typename... Args>
std::true_type helper(decltype(T().construct(Args()...), nullptr));
```
Пояснение: хотим, чтобы выражение имеет тип T* и корректно только при наличии метода construct. 
##### declval
	Что если нет конструктора по умолчанию?

Принимает T в качестве шаблонного параметра, возвращает объект типа T.
```c++
template <typename T, typename... Args>
std::true_type helper(decltype(std::declval<T>()
            .construct(std::declval<Args>()...), nullptr));
```

```c++
template <typename T>
typename std::add_rvalue_reference<T>::type declval() noexcept;
// T declval() не работает, если T - incomplete type
```
##### is_copy_constructible
```c++
template <typename T>
std::true_type helper(decltype(T(std::declval<T&>()), nullptr));

template <typename...>
std::false_type helper(...);

template <typename T>
struct is_copy_constructible: decltype(test<T>(nullptr)) {}
```

##### is_nothow_move_constructible
```c++
template <typename T>
std::true_type helper(
  std::enable_if_t<noexcept(T(std::declval<T>())), decltype(nullptr)>
);

template <typename...>
std::false_type helper(...);

template <typename T>
struct is_nothow_move_constructible: decltype(test<T>(nullptr)) {}
```

##### is_base_of
```c++

template <typename B, typename D>
std::true_type helper(B*);

template <typename...>
std::false_type helper(...)

template <typename B, typename D>
struct is_base_of: 
std::conjunction<
std::is_class<B>,
std::is_class<D>,
decltype(helper<B, D>(static_cast<D*>(nullptr))>
```
- рассмотрим случай с приватным копированием, сейчас - CE
```c++
template <typename B>
std::true_type test_ptr(const volatile B*);

template <typename>
std::false_type test_ptr(const volatile void*);

template <typename B, typename D>
auto helper(int) -> decltype(test_ptr<B>(static_cast<D*>(nullptr)));
// в случае с множественным или приватным наследованием (и только в них) версия выше не подходит, т.к. было бы CE

template <typename...>
auto helper(...) -> std::true_type;

template <typename B, typename D>
struct is_base_of: 
std::conjunction<
std::is_class<B>,
std::is_class<D>,
decltype(helper<B, D>(0)>
```
##### move_if_noexcept
```c++
template <typename T>  
auto my_move_if_noexcept(T& x) noexcept ->  
    typename std::conditional_t<
    !std::is_nothrow_move_constructible_v<T> && std::is_copy_constructible_v<T>,      const T&, T&&> {  
  return std::move(x);  
}
```
## constraints and requirements
#### ключевое слово requires
```c++
template <typename T>
requires std::is_class_v<T>
void f(const T& x) {
  std::cout << 1;
}

template <typename T>
requires std::is_integral_v<T>
void f(const T& x) {
  std::cout << 1;
}
```
> 1. Можно писать перед сигнатурой функцией или после (до определения)
> 2. Нельзя перегружать функции по "строгости" требований

Requires проверяется на стадии поиска имен, то есть до перегрузки, до инстанцирования шаблонов. То есть перегруженные функции для компилятора являются функциями "с разными названиями". 
```c++

requires (sizeof(long) == 4)
void g(long x) {
  std::cout << 1;
}

requires (sizeof(long) == 8)
void g(long x) {
  std::cout << 1;
}
// CE, нельзя вешать requires на non-template function. Иначе линкеру будет плохо

```
#### requires-expressions
```c++
template <typename T>
requires // че писать-то..
T add(const T& a, const T& b) {
  return a + b;
}
```

```c++
template <typename T>
void test() {
  std::cout << requires(T a, T b) { a + b; }
}

int main() {
  test<int>(); // 1
  test<void>(); // 0
}
```

```c++
template<typename T, size_t N>
requires requires(T a) {
  // сюда можно писать выражения, которые будут проверены на компилируемость
  // static_assert нельзя писать
  requires sizeof(T) == N;
}
void test(const T& a) {}
```
##### Виды requirements
1. simple requirement
```c++
template<typename T, size_t N>
requires requires(T a) {
   *a; // проверка на true
}
void test(const T& a) {}

```
2. nested requirement
```c++
template<typename T, size_t N>
requires requires(T a) {
  requires sizeof(T) == N;
}
void test(const T& a) {}
```
3. type requirement
```c++
template<typename T, size_t N>
requires requires(T a) {
  typename T::value_type; // ограничение на T
}
void test(const T& a) {}
```
4. compound-requirements
```c++
template <typename T, typename U>
concept same_as = std::is_same_v<T, U>

template <typename T>
concept InputIterator = requires(T it) {
  {++it} -> same_as<T&>; // 4.
  *it;
};
```

Есть синтаксис для noexcept
```c++
requires requires(T x) {
  {++x} noexcept;
}
```
##### requires внутри requires
```c++
template<class T>
concept Semiregular = DefaultConstructible<T> &&
    CopyConstructible<T> && CopyAssignable<T> && Destructible<T> &&
requires(T a, std::size_t n)
{
	requires Same<T*, decltype(&a)>; // nested: "Same<...> evaluates to true"
	 { a.~T() } noexcept; // compound: "a.~T()" is a valid expression that doesn't throw
	requires Same<T*, decltype(new T)>; // nested: "Same<...> evaluates to true"
	requires Same<T*, decltype(new T[n])>; // nested
	{ delete new T }; // compound
	{ delete new T[n] }; // compound
};
```
То есть внутри requires будет происходить вычисление другого requires, а не проверка на синтаксическую корректность
#### concepts
```c++
template <typename InputIter, typename Predicate>
requires requires (InputIter it, Predicate p) {
  ++it;
  *it;
  p(*it);
}
InputIter find_if(InputIter begin, InputIter end, Predicate p) {
  for (auto it = begin; it != end; ++it) {
    if (p(*it)) {
      return it;
    }
  }
  return end;
}
```
	Проблема: каждый раз перечислять все необходимые требования неудобно
	Решение: объединить их в именованную сущность concepts
```c++
template <typename T, typename U>
concept same_as = std::is_same_v<T, U>

template <typename T>
concept InputIterator = requires(T it) {
  {++it} -> same_as<T&>;
   // при использовании concept в compount requirement первый тип подставляется тип левого выражения (++it в данном случае)
   typename std::iterator_traits<T>::value_type;
  {*it} -> same_as<std::iterator_traits<T>::reference_type>;
};

// юзаем сокращенный синтаксис
template <InputIterator It,
   std::predicate<decltype(*std::declval<It>())> Predicate>
It find_if(It begin, It end, Predicate p) {
  for (auto it = begin; it != end; ++it) {
    if (p(*it)) {
      return it;
    }
  }
  return end;
}
```
##### std::advance
```c++
template <std::input_iterator Iter>
void advance(Iter& iter, size_t n) {
  for (size_t i = 0; i < n; ++i) {
    ++iter;
  }
}

template <std::random_access_iterator Iter>
void advance(Iter& iter, size_t n) {
  iter += n;
}
```
	Можно делать перегрузку по концептам, если компилятор понимает, что один          концепт вложен в другой
## Lamdas
```c++
int main() {
  std::vector v = {5, 1, 3, -4};
  std::sort(v.begin(), v.end(), [](int x, int y) { return x*x < y*y; });
}
```
- Можно вынести в переменную
```c++
int main() {
  std::vector v = {5, 1, 3, -4};
  auto cmp = [](int x, int y) { return x*x < y*y; };
  std::sort(v.begin(), v.end(), cmp);
}
```
- Можно не принимать аргументы
```c++
auto hello = [] {
  std::cout << "hello";
};
```
- Можно вызвать на месте (Immediate invocation)
```c++
[] { std::cout << "hello"; }();
[](){}(); // корректный код)))))
```
	Юзкейсы:
	1. инициализация константы в зависимости от какого-то сложного условия
	2. какой-то прикол в списке инициализации

```c++
int main() {
  auto cmp = [](int x, int y) { return x*x < y*y; };
  std::set<int, decltype(cmp)> my_set; // since c++20, так как объект лямбды не был default constructible
  std::set<int, decltype(cmp)> my_set(cmp); // till c++20
}
```

```c++
int main() {
  auto cmp = [](int x, int y) { return x*x < y*y; };
  std::set<int, decltype([](int x, int y) { return x*x < y*y; })> my_set;
   // OK, since c++20
  std::set<int, decltype([](int x, int y) { return x*x < y*y; })> my_set(cmp);
  // CE, так как у каждой лямбы свой тип, даже если они одинаковые
}
```
***Def***: замыкание - тип, сгенерированный компилятором для лямбды
***Def*** объект замыкания - объект такого типа
```c++
int main() {
  auto cmp = [](int x, int y) -> int {
    if (x > y)
      return 1;
    else
      return 1u;
  }
}
// trailing return type, если хотим явно указать возвращаемый тип, иначе тип выводится по обычныи правилам шаблонной подстановки (auto)
```

#### Лямбды как объекты
- Все, что захватываем, хранится в полях класса => влияет на размер
- Можно копировать и мувать
- Присваивать при пустом списке захвата (since c++20)
-  Есть оператор неявного приведения типа к сишному поинтеру при пустом списке захвата
#### Списки захвата
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
###### Захват с инициализацией
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
#####  Захват по умолчанию
```c++
auto cmp = [=](int x) {...}; //  по значению
auto cmp = [&, sub2](int x) {...}; // по ссылке все, кроме sub2
auto cmp = [a...](int x) {...} // захватили переменное число аргументов
```
##### Захват полей класса
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