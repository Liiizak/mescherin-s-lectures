```c++
int* a  = new int[10];
char* ac = reinterpret_cast<char*>(a);
++ac; // инкрементируем указатель на char, то есть сдвигаемся на 1 байт
int* b = reinterpret_cast<int*>(ac); // b лежит на адресе, который не кратен 4

// не все процессоры умеют читать инт по адресу не кратному 4
*b = 1;
int x = *b;
std::cout << x;

delete[] a;
```

## std::align
void* align(std::size_t alignment, std::size_t size, void*& ptr, std::size_t& space)
space - размер буффера
```c++
alignof - определяет нужный alignment
struct alignas(32) aligned;
alligned_alloc
// malloc выравнивает по max_align_t, поэтому всегла работает корректно
```

## Bit fields
```c++
struct S {
  unsigned int b : 3; // 3 бита на число
}
```