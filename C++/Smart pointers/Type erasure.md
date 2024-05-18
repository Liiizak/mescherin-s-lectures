## std::any

- Idea
```c++
int main() {
  std::any a = 5;
  a = 4.13; // OK
  a = std::vector<int>{1, 2, 3, 4};
  std::cout << std::any_cast<std::vector<int>&>(a)[0]; // copy without ref
}
```
- Realization
```c++
class any {
 private:
 
  struct Base {
    virtual Base* getCopy() const = 0;
    virtual ~Base() = default;
  };

   template <typename T>
   struct Derived: public Base {
     T value;
     Derived(const T& value): value(value) {}
     Derived(T&& value): value(std::move(value)) {}
     Base* getCopy() const override {
       return new Derived(value);
     }
   }
  Base* ptr;

 public:
  template <typename T>
  any(const T& value): ptr(new T(value)) {}

  template <typename T>
  any(const any& other): ptr(other.ptr->getCopy()) {}
  
  ~any() {
    delete ptr;
  }
};

template <typename T>
T any_cast(any& a) {
  auto* p = dynamic_cast<any::Derived<std::remove_reference<T>>*>(a.ptr);
  if (!p) {
    throw std::bad_any_cast;
  }
  return p->value;
}
```