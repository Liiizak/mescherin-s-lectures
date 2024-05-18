указатели, итераторы и ссылки инвалидируются при реаллокации 
### push_back в векторе
- через reinterpret_cast
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
    T* new_arr = reinterpret_cast<T*>(new char[new_cap * sizeof(T)]);
    size_t i = 0;
    try {
      for (; i < sz_; ++i) {
        new(new_arr + i) T(arr_[i]);
      }
      // memcpy не сработает с типами, у которого поле - ссылка на свое же поле
    } catch (...) {
      for (size_t new_index = 0; new_index < i; ++new_index) {
        (new_arr + new_index)->~T();
      }
    for (size_t i = 0; i  < sz_; ++i) {
      (arr_ + i)->~T();
      }
    delete[] reinterpret_cast<char*>(arr_);
    arr_ = new_arr;
    cap_ = new_cap;
    }
  }

  void push_back(const T& elem) {
    if (size_ == cap_) {
      reserve(cap_ > 0 ? cap_ * 2 : 1);
      // T(elem) can throw an exception, must be processed.
    }
  }
}
// тут написан какой-то бред
```
Исключения может выкинуть **T в конструкторе** (при перекопировании или при добавлении нового элемента)
**деструктор исключения не кидает**
## vector_bool
```c++
template <>
class Vector<bool> {
 public:
  struct BitReference {
    int8_t* cell;
    uint8_t index;
    BitReference(char* cell, uint8_t index) : cell(cell), index(index) {}
    BitReference& operator=(bool b) {
      if (b) {
        *cell |= (1u << index);
      } else {
        *cell &= ~(1u << index);
      }
      return *this;
    }
    // оператор неявного каста в const
    operator bool() const {
      return *cell & (1u << index);
    }
  }

  BitReference operator[](size_t i) {
      return BitReference(arr_ + i / 8, i % 8);
  }
 private:
  int8_t* arr;
  size_t size_;
  size_t capacity_;
};

int main() {
  Vector<bool> v(10);
  v[5] = true; // correct, присваеваем значение rvalue
}
```
создается объект вида BitReference при обращении []