## step12

If you run the program now and go through the whole alphabet while holding down `Ctrl`, you should see that we have every letter except `M`. `Ctrl-M` is weird: it’s being read as `10`, when we expect it to be read as `13`, since it is the 13th letter of the alphabet, and `Ctrl-J` already produces a `10`. What else produces `10`? The `Enter` key does.

It turns out that the terminal is helpfully translating any carriage returns (`13`, `'\r'`) inputted by the user into newlines (`10`, `'\n'`). Let’s turn off this feature.

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

`ICRNL` comes from `<termios.h>`. The `I` stands for “input flag”, `CR` stands for “carriage return”, and `NL` stands for “new line”.

Now `Ctrl-M` is read as a `13` (carriage return), and the `Enter` key is also read as a `13`.
