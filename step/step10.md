## step10

By default, `Ctrl-S` and `Ctrl-Q` are used for software flow control. `Ctrl-S` stops data from being transmitted to the terminal until you press `Ctrl-Q`. This originates in the days when you might want to pause the transmission of data to let a device like a printer catch up. Let’s just turn off that feature.

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
  raw.c_iflag &= ~(IXON); // new line
  raw.c_lflag &= ~(ECHO | ICANON | ISIG);

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

`IXON` comes from `<termios.h>`. The `I` stands for “input flag” (which it is, unlike the other `I` flags we’ve seen so far) and `XON` comes from the names of the two control characters that `Ctrl-S` and `Ctrl-Q` produce: `XOFF` to pause transmission and `XON` to resume transmission.

Now `Ctrl-S` can be read as a `19` byte and `Ctrl-Q` can be read as a `17` byte.
