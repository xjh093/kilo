## step11

On some systems, when you type `Ctrl-V`, the terminal waits for you to type another character and then sends that character literally. For example, before we disabled `Ctrl-C`, you might’ve been able to type `Ctrl-V` and then `Ctrl-C` to input a `3` byte. We can turn off this feature using the `IEXTEN` flag.

Turning off `IEXTEN` also fixes `Ctrl-O` in macOS, whose terminal driver is otherwise set to discard that control character.

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
    raw.c_iflag &= ~(IXON);
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

`IEXTEN` comes from `<termios.h>`. It is another flag that starts with `I` but belongs in the `c_lflag` field.

`Ctrl-V` can now be read as a `22` byte, and `Ctrl-O` as a `15` byte.
