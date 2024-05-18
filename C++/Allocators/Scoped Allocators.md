```c++
template <typename T>
class scoped_allocator_adaptor {
  // allocate and deallocate are default
  template <typename T, typename... Args>
  void construct(T* ptr, const Args&... args) {
	if constexpr (std::uses_allocator_v<T, Alloc>) {
      using InnerAlloc = typename T::alocator_type;
      alloc.construct(ptr, args..., InnerAlloc(alloc));
    } else {
      alloc.construct(ptr, args...);
    }
  }
}

template <typename T>
using MyAlloc = std::allocator<T>;

int main() {
  using MyString = std::basic_string<char, std::char_traits<char>, MyAlloc<char>>;
  MyAlloc<MyString> alloc;
  // все объекты будут выделяться на одном аллокаторе, а не копировать его каждый раз
  std::vector<MyString, std::scoped_allocator_adaptor<MyAlloc<MyString>>>;
  v(alloc);
  v.push_back("abc");
  v.push_back("cde);
}
```