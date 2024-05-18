> new выделяет память (можно переопределить) и вызывает конструктор (нельзя переопределить)

```c++
void* operator new(size_t n) {
  return malloc(n);  
}

void operator delete(void* ptr) {
  free(ptr);
}

```

- new(std::nothrow) int\[1000000000] - не бросает исключение, возвращает nullptr
- стандарт гарантирует, что если создается объект через new с кастомными параметрами, то вызовется симметричный delete для объектов

```c++
struct Base {};
struct Derived : Base {}

int main() {
  Base* b = new Derived;
  delete b; // вызывается деструктор base
}
```

## Реализация new в stl
```c++
static void* operator_new_impl(std::size_t size) {
  if (size == 0)
    size = 1; // выделяет 1 байт когда просят 0...
  void* p
  // перед тем, как бросать исключение, пытается вызвать new_handler. Можно переопределить с помощью set_new_hadler (будет вызываться функция вместо бросания исключения)
  while ((p = std::malloc(size)) == nullptr) {
    std::new_handler nh = std::get_new_handler();
    if (nh)
      nh;
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