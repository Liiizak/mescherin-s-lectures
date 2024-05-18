```c++
vector<string> v;
v.push_back("abc"); // push_back принимает по const ссылке объект типа T, то есть создается временный string, который фактически не нужен
v.emplace_back // не работает если вектор векторов векторов векторов....
```

```c++
class string {
  strring(string&& other) : arr(other.arr), sz(other.sz), cap(other.cpp) {
    other.arr = nullptr;
    other.sz = other.cap = 0;
  }

  string& operator=(string&& other) {
    if (this == &other) {
      return;
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

# Правило пяти

> Если нетривиально реализован хотя бы один из методов, нужно реализовать всех
- Оператор присванивания
- Конструктор копирования
- Деструктор
- Move-конструктор
- Move оператор присваивание

>Если не определены move конструктор и move оператор присваивания, но есть нетривиальные конструктор копирования и оператор присваивания, то move не вызывается 