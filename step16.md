## step16

Currently, `read()` will wait indefinitely for input from the keyboard before it returns. What if we want to do something like animate something on the screen while waiting for user input? We can set a timeout, so that `read()` returns if it doesn’t get any input for a certain amount of time.

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
    raw.c_iflag &= ~(BRKINT | ICRNL | INPCK | ISTRIP | IXON);
    raw.c_oflag &= ~(OPOST);
    raw.c_cflag |= (CS8);
    raw.c_lflag &= ~(ECHO | ICANON | IEXTEN | ISIG);
    raw.c_cc[VMIN] = 0; // new line
    raw.c_cc[VTIME] = 1; // new line

    tcsetattr(STDIN_FILENO, TCSAFLUSH, &raw);
}

int main(){
    enableRawMode();

    while (1) { // modified
        char c = '\0'; // new line
        read(STDIN_FILENO, &c, 1); // new line
        if (iscntrl(c)){
            printf("%d\r\n", c);
        }else{
            printf("%d ('%c')\r\n", c, c);
        }
        if (c == 'q') break; // new line
    }
    return 0;
}

```

`VMIN` and `VTIME` come from `<termios.h>`. They are indexes into the `c_cc` field, which stands for “control characters”, an array of bytes that control various terminal settings.

The `VMIN` value sets the minimum number of bytes of input needed before `read()` can return. We set it to `0` so that `read()` returns as soon as there is any input to be read. 

The `VTIME` value sets the maximum amount of time to wait before `read()` returns. It is in tenths of a second, so we set it to 1/10 of a second, or 100 milliseconds. If `read()` times out, it will return `0`, which makes sense because its usual return value is the number of bytes read.

When you run the program, you can see how often `read()` times out. If you don’t supply any input, `read()` returns without setting the `c` variable, which retains its `0` value and so you see `0`s getting printed out. If you type really fast, you can see that `read()` returns right away after each keypress, so it’s not like you can only read one keypress every tenth of a second.

