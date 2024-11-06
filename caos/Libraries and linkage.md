```table-of-contents
```

Простейшая программа на c++:
```c++
#include <iostream>
int main() {
  int x;
  std::cin >> x;
  std::cout << x + 5 << '\n';
}
```
В файле по адресу /usr/include/c++/11/iostream есть переменные объявленные переменные cin и cout, но **без определения**
Как находятся определения этих переменных? Линковка

``` bash
 g++ -v test.cpp - вывести подробную информацию
```
 **Выполнение стадий компиляции по отдельности:**
	1. Препроцессинг - g++ -E test.cpp > test_preprossessed.cpp
	2. Компиляция - g++ -S test.cpp (> test.s)
	3. Ассемлирование - g++ -c test.s (> test.o) 
	4. Линковка - g++ test.o
# Чтение бинаря
1. hexdump - чтение по байтам
2. readelf
3. objdump
## readelf
> Elf - Executable Linkable Format (файл, подлежащий либо выполнению, либо линковке)
   Magic в хедере - байты, с которых начинается файл
  
**Виды**: 
1.	DYN (Position-Independent Executable file) - readelf -a a.out
2.  REL (Relocatable file) - readelf -a test.o
3.  DYN (shared object) - динамическая либа
4.  EXEC (Executable file) - файл со статической линковкой
5. CORE (Core file)
#### symtab - таблица символов
>**Символы** - сущности, которыми оперирует линковщик

 Столбец **Bind** принимает значения LOCAL, GLOBAL, WEAK. 
 GLOBAL - объявления глобальных функций и переменных, видны из-под других translation unit'ов.
 LOCAL - глобальные static функции и переменные, локальные
 WEAK - GLOBAL, но если встретится такой же символ без пометки WEAK, то он перезатрется
> Процесс преобразования имен между стадиями компиляыции и линковки - манглирование.
>  В обратную сторону - деманглинг (c++filt, nm -C)

	nm      перечисляет символы
	nm -C   сразу выполняет деманглинг
## objdump
	objdump -d a.out - дизассемлирование, по бинарю восстановить ассемблер
Тоже позволяет читать **таблицу символов**
# Линковка
ld - стандартный линкер
1. Статическая - последняя стадия компиляции
2. Динамическая - код подгружается в рантайме, если его нет в исполняемом файле (пример - библиотеки)
### Динамическая линковка
	ldd a.out - от каких динамических библиотек зависит исполняемый файл
	LD_LIBRARY_PATH=/lib/...     ./a.out - указать путь, где искать либу
стандартные либы  c++ уже скомпилированы и хранятся в виде ELF файлов типа DYN

**Можно прилинковать все статически:**
``` bash
g++ -static test.cpp
```
### Создание своей библиотеки 
``` bash
1. g++ -shared lib.cpp -o libmyfunc.so (создание shared object)
2. подключаем header с объявлением функции
Вариант 1:
	3. sudo mv libmyfunc.so /usr/lib/
	4. g++ test.cpp -lmyfunc (порядок имеет значение)
Вариант 2:
	3. g++ test.cpp -lmyfunc -L.  (слинковать файл в либой libmyfunc.so,                которую нужно искать в текущей директории статическому линковщику (-L))
	4. LD_LIBRARY_PATH=. ./a.out
Вариант 3:
	3. g++ test.cpp -lmyfunc -L. -Wl,-rpath,/caos (динамический линковщик будет         искать либу по указанному абсолютному пути)
```
### Линковка вручную
``` bash
ld test.o /usr/lib/x86_64-linux-gnu/libstdc++.so.6 /usr/lib/x86_64-linux-gnu/libc.so.6 /usr/lib/gcc/x86_64-linux-gnu/11/crtend.o /usr/lib/gcc/x86_64-linux-gnu/11/crtbegin.o

./a.out - No such file or directory. Проблема с динамическим линковщиком

ld myfile.o /usr/lib/x86_64-linux-gnu/libstdc++.so.6 /usr/lib/x86_64-linux-gnu/libc.so.6 /usr/lib/gcc/x86_64-linux-gnu/11/crtend.o /usr/lib/gcc/x86_64-linux-gnu/11/crtbegin.o -I /usr/lib64/ld-linux-x86-64.so.2
# ld: warning: cannot find entry symbol _start; defaulting to 00000000004010b0
./a.out
# segfault
```
- Линковщик поставил неправильно **точку входа** в программу, так как она не указана. В данном случае программа начинается с функции main, но до main должны создаться статические и глобальные объекты
- В случае отсутствия таких объектов тоже будет segfault, так как в конце main есть return, который некуда делать. Можно решить при помощи exit(0) до return.