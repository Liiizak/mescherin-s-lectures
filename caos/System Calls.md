> Системный вызов - интерфейс на C, который предоставляет операционная система для взаимодействия с ней
```bash
man 2 write # документация по syscall'y
```

	POSIX — набор стандартов, описывающих интерфейсы между операционной системой и прикладной программой (https://en.wikipedia.org/wiki/POSIX)
```bash
strace ./a.out # трассирует все syscall'ы, которые вызываются по мере выполнения программы
strace -p "process id" # трассировка процесса
ltrace ./a.out # трассирует все библиотечные вызовы
```
#### write
> ssize_t write(int fd, const void* buf, size_t count)
- возвращает количество записанных байт или -1
- fd - файловый дескриптор. По умочанию выданы 0 (ввод), 1 (вывод), 2 (ошибки)
<span style="background:rgba(247, 38, 122, 0.31)">errno</span> - глобальная переменная, куда syscall'ы и билиотечные функции записывают код последней случившейся ошибки(-l: вывести значения ошибок)
#### read
> ssize_t read(int fd, void*  buf, size_t count)
- возвращает количество считанных байт
- читает из файлового дескриптора
#### exit
> void \_exit(int status)
- exit - обертка над системным вызовом
#### open
> int open(const char* pathname, int flags \[, mode_t mode])
> int creat(const char* pathname, mode_t mode)
- есть openat и openat2 - принимают дополнительный аргумень int dirfd
- можно получить новый файловый дескриптор при помощи open
```c++
int main() {
  int fd = open("./input.txt", O_RDONLY);
  char buff[100];
  read(fd, buf, 10);
  write(1, but, 10);
}
```
#### close
> int close(int fd)
#### lseek
> off_t lseek(int fd, off_t offeset, int whence)
- задает отступ при чтении из файла/запись
```c++
int main() {
  int fd = open("./output.txt", O_WRONLY);
  int offset = lseek(fd, 50 SEEK_SET);
  const char* buf = "Hello world";
  int res = write(fd, buf, 10);
  std::cout << res;
}
```

```bash
cat output.txt #Hello worl
# размер output.txt 60 байт. Реально занимает 10
cp output.txt output2.txt
# пустота заполнится нулями, размер output2.txt - 60 и реально занимает 60
```

#### stat
> int stat(const char* pathname, struct stat* statbuf)
- позволяет по имени файла получить информацию об этом файле
- заполняет структуру stat
- при вызове от ссылки выдаст информацию об объекте, на который она указывает. lstat выдает информацию о ссылке.
#### dup
> int dup(int oldfd)
> int dup2(int oldfd, int newfd)
- возвращает новый файловый дескриптор, который будет указывать на то же самое, на что указывал старый
- занимает наименьший незанятый
- dup2 использует номер, который указан в newfd. Если newfd открыт, то он закрывается
#### link, unlink, rename
#### mkdir and rmdir
#### chmod
```bash
chmod 567 "file" # можно менять права восьмиричной маской
```
#### ioctl
##### Аттрибуты файлов
```bash
lsattr # Выводит аттрибуты
chattr attr "test.cpp"
# пример chattr +a text.txt - в файл можно только дописывать информацию, но не перезатирать
# chattr +i text.txt - нельзя менять файл
```
#### mount, umount