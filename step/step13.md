## step13

It turns out that the terminal does a similar translation on the output side. It translates each newline (`"\n"`) we print into a carriage return followed by a newline (`"\r\n"`). The terminal requires both of these characters in order to start a new line of text. The carriage return moves the cursor back to the beginning of the current line, and the newline moves the cursor down a line, scrolling the screen if necessary. (These two distinct operations originated in the days of typewriters and teletypes.)

We will turn off all output processing features by turning off the `OPOST` flag. In practice, the `"\n"` to `"\r\n"` translation is likely the only output processing feature turned on by default.

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
    raw.c_iflag &= ~(ICRNL | IXON);
    raw.c_oflag &= ~(OPOST); // new line
    raw.c_lflag &= ~(ECHO | ICANON | IEXTEN | ISIG);

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

`OPOST` comes from `<termios.h>`. `O` means it’s an output flag, and I assume `POST` stands for “post-processing of output”.
