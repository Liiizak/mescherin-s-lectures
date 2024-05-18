
```c++
void print() {}

template <typename Head, typename... Tail>
void print(const Head& head, const Tail&... tail) {
  std::cout << head << ' ';
  print(tail...);
}
```

### Выражения свертки
 ```c++
 template <typename... Types>
 struct all_pointers {
   static const bool value = (std::is_pointer_v<Types> && ...);
 };
// разворачивает пакет Types и все "соединяет" через конъюнкцию
 ```
 - обязательны скобки
 - разные конструкции расставляют скобки по-разному
 
```c++
syntax
(packet op ...)
(... op packet)
(packet op ... op init)
(init op ... op pack)
//init: std::cout << ... << packet;
```

## CRTP

```c++
// curiously resursive template pattern
template <typename T>
struct Base {
  void interface() {
    static_cast<T*>(this)->implementation();
  }
};

struct Derived: Base<Derived> {
  void  implementation();
  static void static_sub_func();
}
```
- имимтирует поведение виртуальных функций
- у родительского класса не могут быть поля типа T, но могут быть T* или T&