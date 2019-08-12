## step8

kilo.c
```
#include <ctype.h> // new line
#include <stdio.h> // new line
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
  while (read(STDIN_FILENO, &c, 1) == 1 && c != 'q') { // modified
    if (iscntrl(c)){ // new line
      printf("%d\n", c); // new line
    }else{ // new line
      printf("%d ('%c')\n", c, c); // new line
    } // new line
  } // new line
  return 0;
}

```

`iscntrl()` comes from `<ctype.h>`, and `printf()` comes from `<stdio.h>`.

`iscntrl()` tests whether a character is a control character. Control characters are nonprintable characters that we don’t want to print to the screen. ASCII codes 0–31 are all control characters, and 127 is also a control character. ASCII codes 32–126 are all printable. 

`printf()` can print multiple representations of a byte. %d tells it to format the byte as a decimal number (its ASCII code), and %c tells it to write out the byte directly, as a character.



