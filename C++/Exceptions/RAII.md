`` Resource Acquisition Is Initialization (получение ресурса - инициализация)
```c++
void g(int y) {
  if (y == 0) {
    throw 1;
  }
}

void f(int x) {
   int* p = new int(x);
   g(*p);
   // не вызывается delete
   delete p;
}

int main() {
  f(0);
}
```
**Решение - умный указатель**
```c++
template <typename T>
struct unique_ptr {
  T* p;
  unique_ptr(T* p): p(p) {}
  unique_ptr(const unique_ptr&) = delete;
  unique_ptr& operator=(const unique_ptr&) = delete;
  ~unique_ptr() { delete p; }
  T& operator*() {
    return *p;
  }
};
```
