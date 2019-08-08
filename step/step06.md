## step6

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
    raw.c_lflag &= ~(ECHO);

    tcsetattr(STDIN_FILENO, TCSAFLUSH, &raw);
}

int main(){
    enableRawMode();

    char c;
    while (read(STDIN_FILENO, &c, 1) == 1 && c != 'q');
    return 0;
}
```

`atexit()` comes from `<stdlib.h>`. We use it to register our `disableRawMode()` function to be called automatically when the program exits, whether it exits by returning from `main()`, or by calling the `exit()` function. This way we can ensure we’ll leave the terminal attributes the way we found them when our program exits.
