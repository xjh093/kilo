## step7

kilo.c:
```
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
    raw.c_lflag &= ~(ECHO | ICANON);

    tcsetattr(STDIN_FILENO, TCSAFLUSH, &raw);
}

int main(){
    enableRawMode();

    char c;
    while (read(STDIN_FILENO, &c, 1) == 1 && c != 'q');
    return 0;
}

```

`ICANON` comes from `<termios.h>`. Input flags (the ones in the `c_iflag` field) generally start with `I` like `ICANON` does. However, `ICANON` is not an input flag, it’s a “local” flag in the c_lflag field. So that’s confusing.

Now the program will quit as soon as you press `q`.
