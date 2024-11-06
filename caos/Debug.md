# gdb
```bash
g++ -g test.cpp (на выходе DWARF file) - дебажная сборка
```
![[gdb_refcard-1.png]]

![[gdb_refcard-2.png]]
- watchpoints - остановливается на моменте, когда ячейка памяти/переменная впервые была изменена
- disassemble - умеет дизассемблировать 
### core dumped files
**Прежде чем убить программу, операционная система сохраняет состояние в специальный core файл**
По умолчанию на ubuntu хранятся в /var/lib/apport/coredump, но не сохраняются (лимит core файла - 0)
- ulimit -c - позволяет узнать максимальный разрешенный размер core файлов
- ulimit -c unlimited - меняет максимально разрешенный размер
Можно:
1. читать core файл при помощи readelf
2. если собрать cpp с флагом -g, то
``` bash
gdb ./a.out path-to-core-file
# Program received signal SIGSEGV, Segmentation fault.
# 0x0000555555555327 in main () at test.cpp:6
# 6	  std::cout << v[100000000];
```
### debug while proccessing
Если cpp собран с флагом -g, можно подключиться к работающему (зависшему) процессу
``` bash
sudo gdp -p proccess-id
```
### standard lib gdb
В код стандартной либы можно зайти, скачав дебажную сборку:
``` bash
1. sudo apt install libstdc++6-10-dbg
2. g++ -g test.cpp -Wl,-rpath,/usr/lib/x86_64-linux-gnu/degub 
# (-> /usr/lib/x86_64-linux-gnu/debug/)
3. g++ -g -D_GLIBCXX_DEBUG test.cpp -Wl,-rpath,/usr/lib/x86_64-linux-gnu/debug/
4. directory path-to-source-code (необходимо заранее скачать исходники)
```
