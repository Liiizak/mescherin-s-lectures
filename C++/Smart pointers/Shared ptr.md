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

  template <typename Deleter>
  shared_ptr(T* ptr, Deleter del) {
  }
  
  ~shared_ptr() {
    if (!count) {
      return;
    }
    --*count;
    if (!*count) {
      delete count;
      delete ptr;
    }
  }
 private:
  T* ptr;
  size_t* count;
  template <typename U, typename... Args>
  friend shared_ptr<U> make_shared(Args&&...);

  shared_ptr(ControlBlock* ptr)
}
```
- можно создавать shared_ptr от типа, являющегося наследником T => ControlBlock может хранить U - наследника T
## make_shared
- указатель на объект создается не пользователем в отличие от инициализации объекта типа shared_ptr
- **позволяет вызывать new один раз** вместо двух (на счетчик и на поинтер сразу)
```c++
template <typename T>
shared_ptr<T>::ControlBlock {
  T value;
  size_t count;
};

template <typename T, typename... Args>
shared_ptr<T> make_shared(Args&&... args) {
		auto* p = new typename shared_ptr<T>::ControlBlock{T(std::forward<Args>     (args)...), 1}
  return shared_ptr<T>(p);
}
```

	Как определять, есть ли в конкретном shared_ptr ControlBlock?
1. хранить bool, зашитый в последний бит шарика
2. size_t* count = nullptr, если в классе есть ConrolBlock, иначе нет

#### reset()
- уменьшает счетчик / удаляет объект в зависимости от количества поинтеров на объект
#### use_count()
- возвращает количество объектов с таким же ptr