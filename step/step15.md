## step15

Let’s turn off a few more flags.

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
    raw.c_iflag &= ~(BRKINT | ICRNL | INPCK | ISTRIP | IXON); // modified
    raw.c_oflag &= ~(OPOST);
    raw.c_cflag |= (CS8); // new line
    raw.c_lflag &= ~(ECHO | ICANON | IEXTEN | ISIG);

    tcsetattr(STDIN_FILENO, TCSAFLUSH, &raw);
}

int main(){
    enableRawMode();

    char c;
    while (read(STDIN_FILENO, &c, 1) == 1 && c != 'q') {
        if (iscntrl(c)){
            printf("%d\r\n", c);
        }else{
            printf("%d ('%c')\r\n", c, c);
        }
    }
    return 0;
}

```

`BRKINT`, `INPCK`, `ISTRIP`, and `CS8` all come from `<termios.h>`.

This step probably won’t have any observable effect for you, because these flags are either already turned off, or they don’t really apply to modern terminal emulators. But at one time or another, switching them off was considered (by someone) to be part of enabling “raw mode”, so we carry on the tradition (of whoever that someone was) in our program.

As far as I can tell:

- When `BRKINT` is turned on, a break condition will cause a `SIGINT` signal to be sent to the program, like pressing `Ctrl-C`.

- `INPCK` enables parity checking, which doesn’t seem to apply to modern terminal emulators.

- `ISTRIP` causes the 8th bit of each input byte to be stripped, meaning it will set it to `0`. This is probably already turned off.

- `CS8` is not a flag, it is a bit mask with multiple bits, which we set using the bitwise-OR (`|`) operator unlike all the flags we are turning off. It sets the character size (CS) to 8 bits per byte.

