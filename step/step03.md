## step3 

kilo.c:
```
#include <unistd.h>

int main(){
  char c;
  while (read(STDIN_FILENO, &c, 1) == 1)
  return 0;
}
```

`read()` and `STDIN_FILENO` come from `<unistd.h>`. We are asking `read()` to read 1 byte from the standard input into the variable `c`, and to keep doing it until there are no more bytes to read. `read()` returns the number of bytes that it read, and will return `0` when it reaches the end of a file.

To exit the above program, press `Ctrl-D` to tell `read()` that it’s reached the end of file. Or you can always press `Ctrl-C` to signal the process to terminate immediately.
