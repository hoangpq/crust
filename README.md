# CRUST
[![Build Status](https://travis-ci.com/onehr/crust.svg?branch=master)](https://travis-ci.com/onehr/crust)


A simple C compiler written in the Rust-lang.

## Project Goal
Support C11 Standard and generate X86_64 Assembly Code from C source code.

This compiler is in a Beta state (started at Mar 30, 2019), using it to develop would be like using a stick to face a dragon.
The plan is to developing it until it can compile real-world applications.
At the moment the main focus is on how to use Rust and build the basic structures.

## TODOLIST
I will add project todo-list soon for better organization.

## Currently Supports
**Because of the Beta nature, crust supports few C features**.
1. Local Variables, declaration and assignment.
2. The `return` statement.
3. Unary Operators: `!`, `~`, `-`(Negation).
4. Binary Operators: `||`, `&&`, `<`, `>`, `>=`, `<=`, `==`, `+`, `-`, `/`, `*`.
5. Ternary Operator: `exp1 ? exp2 : exp3`.
5. `int` and `char` data type.
6. `if` keyword.
7. `else` keyword.
8. `for` keyword.
9. `while` keyword.
10. `do` keyword.
11. `break` keyword.
12. `continue` keyword.
14. Local scope binding.
16. Function definition.
17. Global variables.
18. Global one-dimensional(1-D) Array
19. `string` literals.

## Requirements

You need a valid rust environment, Cargo, and gcc (7.3.0). Gcc is needed to translate the output assembly to binary code.

## Build

```bash
$ cargo build # use this command to build the project
```
You can run with

```bash
$ cargo run source_file.c output_file.s # compile source_file.c => output_file.s
```

You can get [output_file] as an assembly code file, which can be assembled into an ELF executable file.
If you want to run the program, you should type:

```bash
$ gcc -o a.out output_file.s
$ ./a.out
$ echo $?
```

Gcc is currently used as the back-end of the compiler to produce the binary from the output assembly file.

## Running Tests

Make sure you are running a Linux 64 bit system.

Run:
```bash
$ mkdir gen/
$ ./test.sh
```


## Usage Example
Due to the Beta state the compiler only supports a few features.

At the moment, `main` can not take any input arguments, but functions can be defined and called.

As the tradition in programmer community, we should print the "hello world" first.
Code: `sample_code/hello_crust.c`
```c
int main(void) {
	printf("Hello, CRUST!\n");
	printf("This is a simple sample code that can be compiled by crust.\n");	
	return 0;
}
```

You can try command after you have build the crust compiler:
```bash
$ ./target/debug/crust sample_code/hello_crust.c hello_crust.s
$ gcc hello_crust.s -o a.out
$ ./a.out
```
You should get such texts printed in your terminal:
```
Hello, CRUST!
This is a simple sample code that can be compiled by crust.
```

Then you can also print some more interesting data now:
Code: `sample_code/bubble_sort.c`
```c
int a[15];

int main(void) {
        int tmp;

        for (int i = 0; i < 15; i = i + 1) {
                a[i] = 15 - i;
        }

        printf("Before bubble sort:\n");
        for (int i = 0; i < 15; i = i + 1) {
                printf("%d ", a[i]);
        }
        printf("\n");

        int len = 15;
        for (int i = 0; i < len - 1; i = i + 1) {
                for (int j = 0; j < len - 1 - i; j = j + 1)
                        if (a[j] > a[j + 1]) {
                                tmp = a[j];
                                a[j] = a[j + 1];
                                a[j + 1] = tmp;
                        }
        }

        printf("After bubble sort:\n");
        for (int i = 0; i < 15; i = i + 1) {
                printf("%d ", a[i]);
        }
        printf("\n");

        return 0;
}
```
Run:
```bash
$ ./target/debug/crust sample_code/bubble_sort.c bubble_sort.s
$ gcc bubble_sort.s -o a.out
$ ./a.out
```

You should see:
```
Before bubble sort:
15 14 13 12 11 10 9 8 7 6 5 4 3 2 1
After bubble sort:
1 2 3 4 5 6 7 8 9 10 11 12 13 14 15
```

And here is one more complex example from file `test/valid/combine_4.c`.
It defines a `fib` function and use it to calculate the 
10th [fibonacci number](https://en.wikipedia.org/wiki/Fibonacci_number),
then generate a fibonacci array,
and use [bubble sort](https://www.wikiwand.com/en/Bubble_sort) algorithm 
to sort an descend array `[99, 98, 97, ..., 1, 0]` to get an ascend array `[0, 1, ..., 98, 99]`, 
and also demonstrates a few other basic math functions.

```c
int fib(int a) {if (a == 0 || a == 1) {return a;} else {return fib(a - 1) + fib(a - 2);}}
int max(int a, int b) {return a > b ? a : b;}
int min(int a, int b) {return a < b ? a : b;}
int sum(int a, int b) {return a + b;}
int mul(int a, int b) {return a * b;}
int div(int a, int b) {return a / b;}

int EXIT_SUCCESS = 0;
int EXIT_FAILURE = 1;

int arr[100];

int main() {
        int a = 2;
        int b = 3;
        int n = 10;
        int len = 100;

        for (int i = 0; i < 30; i = i + 1) {
                if (i == 0 || i == 1) arr[i] = i;
                else arr[i] = arr[i-1] + arr[i-2];
        }

        for (int i = 0; i < 30; i = i + 1) {
                if (arr[i] != fib(i)) return EXIT_FAILURE;
        }

        for (int i = 0; i < len; i = i + 1) {
                arr[i] = len - 1 - i;
        }

        for (int i = 0; i < len; i = i + 1) {
                if (arr[i] != len - 1 - i) return EXIT_FAILURE;
        }

        int tmp = 0;
        for (int i = 0; i < len - 1; i = i + 1) {
                for (int j = 0; j < len - 1 - i; j = j + 1)
                        if (arr[j] > arr[j + 1]) {
                                tmp = arr[j];
                                arr[j] = arr[j + 1];
                                arr[j + 1] = tmp;
                        }
        }

        for (int i = 0; i < 100; i = i + 1) {
                if (arr[i] != i) return EXIT_FAILURE;
        }


        if (fib(n) != 55) return EXIT_FAILURE;

        if (min(a, b) != 2) return EXIT_FAILURE;

        if (max(a, b) != 3) return EXIT_FAILURE;

        if (sum(a, b) != 5) return EXIT_FAILURE;

        if (mul(a, b) != 6) return EXIT_FAILURE;

        if (div(a, b) != 0) return EXIT_FAILURE;

        return arr[len-1];
}
```
This can actually be an unit test, it can test every function works correctly,
if everything goes fine, this program should retrun `arr[99]` which should be 99, 
if there's something wrong, it will return 1,
which is `EXIT_FAILURE`.

If we use the `crust` compiler to compile this program and run the final executable file,
the program will return 99, which is correct.

The generated code would be some thing like this (The assmebly file contains too many lines, so I just paste a snippet of it)

```assembly
        .file "test/valid/combine_4.c"
        .text
        .global fib
        .type fib, @function
fib:
.LFB0:
        .cfi_startproc
        pushq	%rbp
        .cfi_def_cfa_offset 16
        .cfi_offset 6, -16
        movq	%rsp, %rbp
        .cfi_def_cfa_register 6
.LBB1:
        movq 16(%rbp), %rax
        pushq %rax
        movq $0, %rax
        popq %rcx
        cmpq %rax, %rcx # set ZF on if %rax == %rcx, set it off otherwise
        movq $0, %rax   # zero out EAX, does not change flag
        sete %al
        cmpq $0, %rax
        je .LCLAUSE3
        movq $1, %rax
        jmp .LEND4
.LCLAUSE3:
        movq 16(%rbp), %rax
        pushq %rax
        movq $1, %rax
        popq %rcx
        cmpq %rax, %rcx # set ZF on if %rax == %rcx, set it off otherwise
        movq $0, %rax   # zero out EAX, does not change flag
        sete %al
        cmpq $0, %rax
        movq $0, %rax
        setne %al
.LEND4: # end of clause here
        cmpq $0, %rax
        je .LS29
.LBB5:
        movq 16(%rbp), %rax
        movq %rbp, %rsp
        popq	%rbp
        .cfi_def_cfa 7, 8
        ret
.LEB6:
        addq $0, %rsp # block out
        jmp .LENDIF10
.LS29:
.LBB7:
        movq $1, %rax
        pushq %rax
        movq 16(%rbp), %rax
        popq %rcx
        subq %rcx, %rax
        pushq %rax
        call fib
        addq $8, %rsp # remove the arguments
        pushq %rax
        movq $2, %rax
        pushq %rax
        movq 16(%rbp), %rax
        popq %rcx
        subq %rcx, %rax
        pushq %rax
        call fib
        addq $8, %rsp # remove the arguments
        popq %rcx
        addq %rcx, %rax
        movq %rbp, %rsp
        popq	%rbp
        .cfi_def_cfa 7, 8
        ret
.LEB8:
        addq $0, %rsp # block out
.LENDIF10:
.LEB2:
        addq $0, %rsp # block out
        movq %rbp, %rsp
        popq	%rbp
        .cfi_def_cfa 7, 8
        .cfi_endproc
.LFE11:
        .size   fib, .-fib
        .text
        .global max
        .type max, @function
max:
.LFB12:
        .cfi_startproc
        pushq	%rbp
        .cfi_def_cfa_offset 16
        .cfi_offset 6, -16
        movq	%rsp, %rbp
        .cfi_def_cfa_register 6
.LBB13:
        movq 16(%rbp), %rax
        pushq %rax
        movq 24(%rbp), %rax
        popq %rcx
        cmpq %rax, %rcx # set ZF on if %rax == %rcx, set it off otherwise
        movq $0, %rax   # zero out EAX, does not change flag
        setg %al
        cmpq $0, %rax
        je .LE315
        movq 16(%rbp), %rax
        jmp .LENDCOND16
.LE315:
        movq 24(%rbp), %rax
.LENDCOND16:
        movq %rbp, %rsp
        popq	%rbp
        .cfi_def_cfa 7, 8
        ret
.LEB14:
        addq $0, %rsp # block out
        movq %rbp, %rsp
        popq	%rbp
        .cfi_def_cfa 7, 8
        .cfi_endproc
.LFE17:
        .size   max, .-max
        .text
        .global min
        .type min, @function
min:
.LFB18:
        .cfi_startproc
        pushq	%rbp
        .cfi_def_cfa_offset 16
        .cfi_offset 6, -16
        movq	%rsp, %rbp
        .cfi_def_cfa_register 6
.LBB19:
        movq 16(%rbp), %rax
        pushq %rax
        movq 24(%rbp), %rax
        popq %rcx
        cmpq %rax, %rcx # set ZF on if %rax == %rcx, set it off otherwise
        movq $0, %rax   # zero out EAX, does not change flag
        setl %al
        cmpq $0, %rax
        je .LE321
        movq 16(%rbp), %rax
        jmp .LENDCOND22
.LE321:
        movq 24(%rbp), %rax
.LENDCOND22:
        movq %rbp, %rsp
        popq	%rbp
        .cfi_def_cfa 7, 8
        ret
.LEB20:
        addq $0, %rsp # block out
        movq %rbp, %rsp
        popq	%rbp
        .cfi_def_cfa 7, 8
        .cfi_endproc
.LFE23:
        ;;
        ;; SKIP LOTS OF LINES
        ;;
.LFB24:
        movq -16(%rbp), %rax
        pushq %rax
        movq -8(%rbp), %rax
        pushq %rax
        call div
        addq $16, %rsp # remove the arguments
        pushq %rax
        movq $0, %rax
        popq %rcx
        cmpq %rax, %rcx # set ZF on if %rax == %rcx, set it off otherwise
        movq $0, %rax   # zero out EAX, does not change flag
        setne %al
        cmpq $0, %rax
        je .LS2103
        movq EXIT_FAILURE(%rip), %rax
        movq %rbp, %rsp
        popq	%rbp
        .cfi_def_cfa 7, 8
        ret
        jmp .LENDIF104
.LS2103:
.LENDIF104:
        movq $1, %rax
        pushq %rax
        movq -32(%rbp), %rax
        popq %rcx
        subq %rcx, %rax
        pushq %rdx
        pushq %rbx
        movq %rax, %rdx
        movq arr@GOTPCREL(%rip), %rbx
        movq (%rbx, %rdx, 8), %rax
        popq %rbx
        popq %rdx
        movq %rbp, %rsp
        popq	%rbp
        .cfi_def_cfa 7, 8
        ret
.LEB38:
        addq $40, %rsp # block out
        movq %rbp, %rsp
        popq	%rbp
        .cfi_def_cfa 7, 8
        .cfi_endproc
.LFE105:
        .size   main, .-main
        .ident	"crust: 0.1 (By Haoran Wang)"
        .section	.note.GNU-stack,"",@progbits
```

## Contact
If you are interested in this project, or have troubles with it, feel free to contact me at 
waharaxn@gmail.com, with a subject line containing [Crust-dev].
