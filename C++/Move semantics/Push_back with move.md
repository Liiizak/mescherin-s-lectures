```c++
template <typename T>
class vector {
  T* arr_;
  size_t size_;
  size_t cap_;
  
  void reserve(size_t new_cap) {
    if (new_cap <= cap_) {
      return;
    }
    T* new_arr = AllocTraits::allocate(alloc_, new_cap);
    size_t i = 0;
    try {
      for (; i < sz_; ++i) {
        AllocTraits::construct(alloc_, new_arr + i, std::move(arr_[i]));
      }
    } catch (...) {
      for (size_t new_index = 0; new_index < i; ++new_index) {
        AllocTraits::destroy(alloc_, new_arr + new_index);
      }
      AllocTraits::deallocate(alloc_, new_arr, new_cap);
      throw;
      }
    for (size_t i = 0; i < size_; ++i) {
      AllocTraits::destroy(alloc_, arr_ + i);
    }
    AllocTraits::deallocate(alloc_, arr_, cap_);
    arr_ = new_arr;
    cap_ = new_cap;
  }

  void push_back(const T& value) {
    emplace_back(value);
  }
  
  void push_back(T&& value) {
    emplace_back(std::move(value));
  }

  template <typename Args...>
  void emplace_back(Args&&... args) {
	if (size_ == cap_) {
      reserve(cap_ > 0 ? cap_ * 2 : 1);
    }
    AllocTraits::construct(alloc_, arr_ + size_, std::forward<Args>(args)...);
    ++size_;
  }
}

```

- теряется exception safety
 **Решение**
 move_if_noexcept(arr_\[i])
 => если move конструктор, move конструктор копирования не noexcept, то объекты копируются

## Переосмысление жизни..
```c++
template <typename T>
void f(T x) {
...
}
// Если T поддерживает move-семантику и передано rvalue, то значение не будет скопировано
```