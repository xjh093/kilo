## step5

kilo.c:
```
#include <termios.h>
#include <unistd.h>

void enableRawMode() {
    struct termios raw;

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

`struct termios`, `tcgetattr()`, `tcsetattr()`, `ECHO`, and `TCSAFLUSH` all come from `<termios.h>`.

The `ECHO` feature causes each key you type to be printed to the terminal, so you can see what you’re typing. This is useful in canonical mode, but really gets in the way when we are trying to carefully render a user interface in raw mode. So we turn it off. 

After the program quits, depending on your shell, you may find your terminal is still not echoing what you type. Don’t worry, it will still listen to what you type. Just press `Ctrl-C` to start a fresh line of input to your shell, and type in `reset` and press `Enter`.

Terminal attributes can be read into a termios struct by `tcgetattr()`. After modifying them, you can then apply them to the terminal using `tcsetattr()`. The `TCSAFLUSH` argument specifies when to apply the change: in this case, it waits for all pending output to be written to the terminal, and also discards any input that hasn’t been read.

The `c_lflag` field is for “local flags”. A comment in macOS’s `<termios.h>` describes it as a “dumping ground for other state”. So perhaps it should be thought of as “miscellaneous flags”. The other flag fields are `c_iflag` (input flags), `c_oflag` (output flags), `and c_cflag` (control flags), all of which we will have to modify to enable raw mode.

`ECHO` is a bitflag, defined as `00000000000000000000000000001000` in binary. We use the bitwise-NOT operator (~) on this value to get `11111111111111111111111111110111`. We then bitwise-AND this value with the flags field, which forces the fourth bit in the flags field to become `0`, and causes every other bit to retain its current value. Flipping bits like this is common in C.



