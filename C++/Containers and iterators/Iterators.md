### $$ Категории $$
## Input iterator
- конструтор, деструктор, оператор присванивания по умолчанию
- разыменовывание
- инкрементирование
## Forward Iterator
 - гарантия того, что можно ходить вперед несколько раз (значения будут одинаковыми, если пройти по одному промежутку несколько раз)
 (forward_list, unordered_set, unordered_map)
## Bidirectional iterator
- декремент
(set, map, list)
## RandomAccess iterator
- +=, -= число
- +, - число
- сравнения <, >, <=, >=
- разность итераторов
(deque)
## Contiguous iterator
- элементы лежат в памяти непрерывно
(vector, array, pointer)
## Output Iterator
- можно разыменовывать, инкрементировать присваивать сколько угодно раз (писать в итератор)
```c++
template <typename Container>
class back_insert_iterator {
  Container& container;
 public:
   back_insert_iterator(Container& container) : container(container) {}
   back_insert_iterator& operator=(const typename Container::value_type& value) {
     container.push_back(value);
     return *this;
   }
  back_insert_iterator& operator++() {
    return *this; // ничего не делает
  }
  back_insert_iterator operator++(int) {
    return *this; // ничего не делает
  }
  back_insert_iterator& operator*() {
    return *this; // ничего не делает
  }
}

template <typename Container>
back_insert_iterator<Container> back_inserter(Container& container) {
  return {container};
}

int main() {
  int a[10] = {1, 2, 3, 4, 5};
  std::vector<int> v;
  std::copy(a, a + 10, std::back_insert_iterator<std::vector<int>>(v); // ub
  // or std::cope(a,  a + 10, back_inserter(v));
}
```

## move iterator
адаптер над итератором, который ведет себя в точности как и нижележащий итератор, но при разыменовывании возвращает rvalue ссылку

## Потоковые итераторы

***istream_iterator***
```c++
std::istream_iterator<int> it(std::cin);
std::vector<int> v;
for (int i = 0; i < 10; ++i, ++it {
  v.push_back(*it);
}
for (int i = 0; i < v.size(); ++i) {
  std::cout << v[i] << ' ';
}
```
- end у такого итератора - значение по умолчанию (итератор становится равным значению по умолчанию когда нечего считывать)
- является input итератором

***ostream_iterator***
```c++
std::copy(iter, std::istream_iterator<int>(), 
		  std::ostream_iterator(std::cout, " "));
		  //   ostream_iterator принимает вторым аргументом разделитель
		  // реализован как back_insert_iterator
```

#### Определение типа под итератором
```c++
std::iterator_traits<InputIterator>::value_type

//auto x = *begin; не работает с vector bool
```
Также можно узнать iterator_category, reference, pointer, difference_type

## Манипуляторы над потоками

```c++
std::cout << std::hex;
int 123;
std::cout << x; // выводит в 16-тиричном формате
std::cout << set_precision(2) //точность
std::cin >> std::noskipws; // не пропускает пробелы (для char)
```