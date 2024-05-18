**При копировании аллокатора хотим, чтобы он указывал на тот же пул, но не копировал его**. 
Можно:
- использовать shared_ptr, чтобы очищать память после удаления последнего аллокатора.
- завести класс Pool, который нельзя копировать, аллокатор хранит указатель на Pool

Allocator_traits опеределяет using'и, construct, destroy, deallocate, destroy
```c++
template <typename Alloc>
struct allocator_traits {
  template <typename U, typename... Args>
  static void construct(Alloc& alloc, U* ptr, const Args&... args) {
    if constexpr (/* Alloc has method construct */) {
      alloc.construct(ptr, args...);
    } else {
      new (ptr) U(args...);
    }
  }
  static destroy;
  static deallocate;
  static allocate;
  static rebind_alloc;
  static rebind_traits;
}
```

> Container -> allocator_traits -> allocator -> operator new -> malloc -> OC

## Allocator-aware контейнеры

> Реализация оператора присваивания в векторе

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