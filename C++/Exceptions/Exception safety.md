## Уровни безопасности относительно исключений
- nothrow - без исключений
- strong guarantee - функция возвращает все в исходное состояние
- basic guarantee - все остается в валидном состоянии, нет утечек памяти
- no guarantee - поведение не регламентировано

**noexcept**
- пометка функции, что в ней (фактически) не может пойти что-то не так (формально: не бросает исключения)

*Условный noexcept*
```c++
template <typename T>
void g() {}

template <typename T>
void f() noexcept(std::is_reference_v<T>) {
}
// если T - ссылка, то noexcept
template <typename T>
void f() noexcept(noexcept(g<T>())) {
}
// если g noexcept, то f noexcept
```