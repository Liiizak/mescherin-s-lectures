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

> Каждый контейнер хранит аллокатор как поле (есть empty base optimization)

```c++
template <typename T, template<typename> Alloc = std::allocator>
// не может использоваться, так как среди шаблонных параметров могут быть числа
```

## Виды нестандартных аллокаторов
- PoolAllocator - выделяет сразу много
- StackAllocator<\int> alloc(array) - array это выделенный на стеке массив
- FreeListAllocator - не удаляет ноды по запросу, а запоминает их как свободные

## Аллоцирование других типов

```c++
template <typename U>
struct rebind {
  using other = allocator<U>;
}

template <typename T, typename Alloc = std::allocator<T>>
class list {
  struct BaseNode;
  struct Node : BaseNode;

  list(const Alloc& alloc) : alloc_(alloc) {}

  typename Alloc::template rebind<Node>::other alloc_;
}

```

> Есть контейнеры, которые можно мувать, но не копировать (std::unique_ptr)

