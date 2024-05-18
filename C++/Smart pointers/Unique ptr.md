- Запрещается **copy** копирование и присваивание
- Разрешается **move** копирование и присваивание
- есть метод get (возвращает T*), reset (зануляет)
- вторым шаблонным параметром имеет Deleter, который имеет перегруженные (ptr)

## make_unique
- указатель на объект создается не пользователем в отличие от инициализации объекта типа make_unique

```c++
template <typename T, typename... Args>
unique_ptr<T> make_unique(Args&&... args) {
  return unique_ptr<T>(new T(std::forward<Args>(args)...));
}
```
- защита от внезапных исключений
```c++
template <typename T>
void f(unique_ptr<T> ptr, int a);

int main() {
  f(unique_ptr<int>(new int(5)), g());
  // порядок вычисления аргументов функции не стандартизовано
  // до c++17 возможно было поведение: new -> g -> unique_ptr<int>{}
  // g может бросить исключение)))))
}
```
