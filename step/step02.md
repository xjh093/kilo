## step2

Create a new file named `Makefile`:
```
kilo: kilo.c
	$(CC) kilo.c -o kilo -Wall -Wextra -pedantic -std=c99
```

The first line says that `kilo` is what we want to build, and that `kilo.c` is what’s required to build it.

The second line specifies the command to run in order to actually build kilo out of kilo.c. 

Make sure to indent the second line with an actual `tab` character, and not with `spaces`. 

You can indent C files however you want, but Makefiles `must` use tabs.

- `$(CC)` is a variable that `make` expands to `cc` by default.

- `-Wall` stands for "all Warnings", and gets the compiler to warn you when it sees code in your program that might not technically be wrong, but is considered bad or questionable usage of the C language, like using variables before initializing them.

- `-Wextra` and `-pedantic` turn on even more warnings. For each step in this tutorial, if your program compiles, it shouldn’t produce any warnings except for "unused variable" warnings in some cases. If you get any other warnings, check to make sure your code exactly matches the code in that step.

- `-std=c99` specifies the exact version of the C language standard(std) we’re using, which is C99. `C99` allows us to declare variables anywhere within a function, whereas `ANSI C` requires all variables to be declared at the top of a function or block.

try running `make` to compile the program.

It may output make: `kilo' is up to date.`. because `kilo.c` is not modified.
