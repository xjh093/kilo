## step4

kilo.c:
```
#include <unistd.h>

int main(){
   char c;
   while (read(STDIN_FILENO, &c, 1) == 1 && c != 'q'); // modified
   return 0;
}

```

To quit this program, you will have to type a line of text that includes a `q` in it, and then press enter. The program will quickly read the line of text one character at a time until it reads the `q`, at which point the `while` loop will stop and the program will exit. Any characters after the `q` will be left unread on the input queue, and you may see that input being fed into your shell after your program exits.

