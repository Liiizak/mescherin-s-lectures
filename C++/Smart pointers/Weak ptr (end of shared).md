- при наличии циклических зависимостей возникают траблы

#### lock()
- создается новый shared_ptr на объект под weak_ptr
- при отсутствии объекта создается пустой shared_ptr
#### expired()
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

Деструктор работает следующим образом:
- если shared_count == 0 и weak_count == 0 - вызывается деструктор control block **??**
- shared_count == 0 и weak_count != 0 - вызывается деструктор T
- shared_count != 0 - shared_count -= 1