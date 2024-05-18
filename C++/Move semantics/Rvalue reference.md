> 1. Будучи возвращенной из функции является rvalue
> 2. Проинициализировать можно только rvalue выражением
> 3. Могут продлевать жизнь объектов

```c++
int&& y = 6;
y = 7; // OK

int&& z = y; // CE, y - lvalue
int&& z = std::move(y); // OK
int&& z = static_cast<int&&>(y); //OK

z = 1; // OK, z = 1, y = 1; 

string x = "abc";
string&& y = std::move(x);
string t = std::move(x); // x, y - пустые строки
```

## Взимодействие const и rvalue-references

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

## Reference qualifiers
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

}// чтобы это избежать, надо поставить &
```