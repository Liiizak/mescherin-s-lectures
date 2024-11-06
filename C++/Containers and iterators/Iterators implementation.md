```c++
template <bool IsConst>
class Iterator {
 public:
  using pointer_type = std::conditional_t<IsConst, const T*, T*>;
  using reference_type = std::conditional_t<IsConst, const T&, T&>;
  using value_type = T; // ! without const
  private:
  friend class deque<T>;
  T* ptr;
  iterator(T* ptr): ptr(ptr) {}
 public:
  operator Iterator<true>() const {
    return {ptr};
  }
  iterator(const iterator&) = default;
  iterator& operator=(const iterator&) = default
  T& operator*() const {
    return *ptr;
  }
  T* operator->() {
    return ptr;
  }
  iterator& operator++() {
    ++ptr;
    return *this;
  }
  iterator operator++(int) {
    iterator copy = *this;
    ++ptr;
    return copy;
  }
};
using iterator = Iterator<false>;
using const_iterator = Iterator<true>;
using reverse_iterator = std::reverse_iterator<iterator>;
using const_reverse_iteratot = std::reverse_iterator<const_iterator>;
iterator begin() {
  return iterator{arr_};
}
iterator end() {
  return iterator{arr_ + size_};
}
const_iterator begin() const {
  return const_iterator{arr_};
}
const_iterator end() const {
  return const_iterator{arr_ + size_};
}
const_iterator cbegin() const {
  return const_iterator{arr_};
}
const_iterator end() const {
  return const_iterator{arr_ + size_};
}
```