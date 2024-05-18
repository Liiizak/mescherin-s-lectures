```c++
template <typename T, typename Alloc, typename... Args>
shared_ptr<T> allocate_shared(const Alloc& alloc, Args&&... args) {
  using BlockAlloc = typename std::alocator_traits<Alloc>::rebind_alloc<ControlBlockMakeShared<T, Alloc>>
  BlockAlloc ba = alloc;
  auto* ptr = ba.allocate(1);
  ba.construct(ptr, 1, 0, std::forward<Args>(args)..., alloc);
  return //shared ptr от приватного конструктора
}
```