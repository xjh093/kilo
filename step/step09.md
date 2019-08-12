## step9

By default, `Ctrl-C` sends a `SIGINT` signal to the current process which causes it to terminate, and `Ctrl-Z` sends a `SIGTSTP` signal to the current process which causes it to suspend.

kilo.c
```
#include <ctype.h>
#include <stdio.h>
#include <stdlib.h>
#include <termios.h>
#include <unistd.h>

struct termios orig_termios;

void disableRowMode() {
  tcsetattr(STDIN_FILENO, TCSAFLUSH, &orig_termios);
}

void enableRawMode() {
  tcgetattr(STDIN_FILENO, &orig_termios);
  atexit(disableRowMode);

  struct termios raw = orig_termios;

  tcgetattr(STDIN_FILENO, &raw);
  raw.c_lflag &= ~(ECHO | ICANON | ISIG); // modified

  tcsetattr(STDIN_FILENO, TCSAFLUSH, &raw);
}

int main(){
  enableRawMode();

  char c;
  while (read(STDIN_FILENO, &c, 1) == 1 && c != 'q') {
    if (iscntrl(c)){
      printf("%d\n", c);
    }else{
      printf("%d ('%c')\n", c, c);
    }
  }
  return 0;
}

```

`ISIG` comes from `<termios.h>`. Like `ICANON`, it starts with I but isn’t an input flag.

Now `Ctrl-C` can be read as a 3 byte and `Ctrl-Z` can be read as a 26 byte.

This also disables `Ctrl-Y` on macOS, which is like `Ctrl-Z` except it waits for the program to read input before suspending it.
