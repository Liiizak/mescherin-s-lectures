```c++
struct {

} a; // объявнение анонимной структуры в переменной a
```

## friend
```c++
struct C {
  friend int main(); // можно определить на месте
  friend class CC;
}
```