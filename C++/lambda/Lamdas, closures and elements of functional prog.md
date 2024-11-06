```c++
int main() {
  std::vector v = {5, 1, 3, -4};
  std::sort(v.begin(), v.end(), [](int x, int y) { return x*x < y*y; });
}
```
- Можно вынести в переменную
```c++
int main() {
  std::vector v = {5, 1, 3, -4};
  auto cmp = [](int x, int y) { return x*x < y*y; };
  std::sort(v.begin(), v.end(), cmp);
}
```
- Можно не принимать аргументы
```c++
auto hello = [] {
  std::cout << "hello";
};
```
- Можно вызвать на месте (Immediate invocation)
```c++
[] { std::cout << "hello"; }();
[](){}(); // корректный код)))))
```
	Юзкейсы:
	1. инициализация константы в зависимости от какого-то сложного условия
	2. какой-то прикол в списке инициализации

```c++
int main() {
  auto cmp = [](int x, int y) { return x*x < y*y; };
  std::set<int, decltype(cmp)> my_set; // since c++20, так как объект лямбды не был default constructible
  std::set<int, decltype(cmp)> my_set(cmp); // till c++20
}
```

```c++
int main() {
  auto cmp = [](int x, int y) { return x*x < y*y; };
  std::set<int, decltype([](int x, int y) { return x*x < y*y; })> my_set;
   // OK, since c++20
  std::set<int, decltype([](int x, int y) { return x*x < y*y; })> my_set(cmp);
  // CE, так как у каждой лямбы свой тип, даже если они одинаковые
}
```
***Def***: замыкание - тип, сгенерированный компилятором для лямбды
***Def*** объект замыкания - объект такого типа
```c++
int main() {
  auto cmp = [](int x, int y) -> int {
    if (x > y)
      return 1;
    else
      return 1u;
  }
}
// trailing return type, если хотим явно указать возвращаемый тип, иначе тип выводится по обычныи правилам шаблонной подстановки (auto)
```

#### Лямбды как объекты
- Все, что захватываем, хранится в полях класса => влияет на размер
- Можно копировать и мувать
- Присваивать при пустом списке захвата (since c++20)
-  Есть оператор неявного приведения типа к сишному поинтеру при пустом списке захвата