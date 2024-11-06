```c++
template <typename InputIter, typename Predicate>
requires requires (InputIter it, Predicate p) {
  ++it;
  *it;
  p(*it);
}
InputIter find_if(InputIter begin, InputIter end, Predicate p) {
  for (auto it = begin; it != end; ++it) {
    if (p(*it)) {
      return it;
    }
  }
  return end;
}
```
	Проблема: каждый раз перечислять все необходимые требования неудобно
	Решение: объединить их в именованную сущность concepts
```c++
template <typename T, typename U>
concept same_as = std::is_same_v<T, U>

template <typename T>
concept InputIterator = requires(T it) {
  {++it} -> same_as<T&>;
   // при использовании concept в compount requirement первый тип подставляется тип левого выражения (++it в данном случае)
   typename std::iterator_traits<T>::value_type;
  {*it} -> same_as<std::iterator_traits<T>::reference_type>;
};

// юзаем сокращенный синтаксис
template <InputIterator It,
   std::predicate<decltype(*std::declval<It>())> Predicate>
It find_if(It begin, It end, Predicate p) {
  for (auto it = begin; it != end; ++it) {
    if (p(*it)) {
      return it;
    }
  }
  return end;
}
```
### std::advance
```c++
template <std::input_iterator Iter>
void advance(Iter& iter, size_t n) {
  for (size_t i = 0; i < n; ++i) {
    ++iter;
  }
}

template <std::random_access_iterator Iter>
void advance(Iter& iter, size_t n) {
  iter += n;
}
```
	Можно делать перегрузку по концептам, если компилятор понимает, что один          концепт вложен в другой