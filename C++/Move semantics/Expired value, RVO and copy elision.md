![[value_types.png]]
## xvalue:
- результат вызова функции, если возвращаемый тип T&
- cast к rvalue ссылке
- обращение через . -> \[]
- запятая, тернарный оператор при определенных условиях

## Как понимать rvalue и lvalue
> value - объект, которому соответствует оперделенная якейка в памяти.
>  prvalue  - не соответствует. (till c++17 может не соответствовать)
>  xvalue (rvalue) создается и положится в память, но ведет себя как rvalue\

## guaranteed copy elision

```c++
int main() {
  string a = string("abc"); // 1 конструктор
  string b = std::move(string("abc")) // 2 конструктора
}
```

> temporary materialization (материализация ввременного) происходи в слеующих случаях

- инициализация ссылке prvalue
- обращение через .
- array-to-pointer conversion
- применение typeid, sizeof
- игнорируем результат prvalue выражения

## lvalue-to-rvalue conversion

- glvalue может сконвертировать к prvalue
> неформально: считывание из памяти в регистр процессора

**Пример** : int x = \*p;

## Return value optimization
```c++
std::string f() {
  std::string str = ("abc");
  return str; // std::move испортит NRVO (named rvo)
}
```

Даже если компилятор не может использовать RVO, но возращается локальная переменная, то он ее мувает.

```c++
S f(S&& a) {
  return std::move(a); // в таком случае без std::move объект будет копироваться
}
```