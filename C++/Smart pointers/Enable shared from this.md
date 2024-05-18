```c++
template <typename T>
struct enable_shared_from_this {
  weak_ptr<T> sptr;
  shared_ptr<T> shared_from_this() const {
    return sptr;
  }

  template <typename U>
	friend class shared_ptr;
};

strcut S: public enable_shared_from_this<S> {
  std::shared_ptr<S> getObject() {
    return shared_from_this();
  }
};
```

- если S создается не при помощи shared_ptr, то кидается исключение
- weak_ptr для избежания циклов