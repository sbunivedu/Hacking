# Machine Instructions
* Compile and run `firstprog.c`
```
$ gcc -m 32 firstprog.c
$ ls -l a.out
-rwxr-xr-x 1 ubuntu ubuntu 8523 Feb 11 21:19 a.out*
$ ./a.out
Hello, world!

Hello, world!

Hello, world!

Hello, world!

Hello, world!

Hello, world!

Hello, world!

Hello, world!

Hello, world!

Hello, world!

```

* Examine the binary
```
$ objdump -D a.out | grep -A20 main.:
0804841d <main>:
 804841d:       55                      push   %ebp
 804841e:       89 e5                   mov    %esp,%ebp
 8048420:       83 e4 f0                and    $0xfffffff0,%esp
 8048423:       83 ec 20                sub    $0x20,%esp
 8048426:       c7 44 24 1c 00 00 00    movl   $0x0,0x1c(%esp)
 804842d:       00
 804842e:       eb 11                   jmp    8048441 <main+0x24>
 8048430:       c7 04 24 e0 84 04 08    movl   $0x80484e0,(%esp)
 8048437:       e8 b4 fe ff ff          call   80482f0 <puts@plt>
 804843c:       83 44 24 1c 01          addl   $0x1,0x1c(%esp)
 8048441:       83 7c 24 1c 09          cmpl   $0x9,0x1c(%esp)
 8048446:       7e e8                   jle    8048430 <main+0x13>
 8048448:       b8 00 00 00 00          mov    $0x0,%eax
 804844d:       c9                      leave
 804844e:       c3                      ret
 804844f:       90                      nop

08048450 <__libc_csu_init>:
 8048450:       55                      push   %ebp
 8048451:       57                      push   %edi
```
Use Intel syntax for assembly language representation
```
$ objdump -M intel -D a.out | grep -A20 main.:
0804841d <main>:
 804841d:       55                      push   ebp
 804841e:       89 e5                   mov    ebp,esp
 8048420:       83 e4 f0                and    esp,0xfffffff0
 8048423:       83 ec 20                sub    esp,0x20
 8048426:       c7 44 24 1c 00 00 00    mov    DWORD PTR [esp+0x1c],0x0
 804842d:       00
 804842e:       eb 11                   jmp    8048441 <main+0x24>
 8048430:       c7 04 24 e0 84 04 08    mov    DWORD PTR [esp],0x80484e0
 8048437:       e8 b4 fe ff ff          call   80482f0 <puts@plt>
 804843c:       83 44 24 1c 01          add    DWORD PTR [esp+0x1c],0x1
 8048441:       83 7c 24 1c 09          cmp    DWORD PTR [esp+0x1c],0x9
 8048446:       7e e8                   jle    8048430 <main+0x13>
 8048448:       b8 00 00 00 00          mov    eax,0x0
 804844d:       c9                      leave
 804844e:       c3                      ret
 804844f:       90                      nop

08048450 <__libc_csu_init>:
 8048450:       55                      push   ebp
 8048451:       57                      push   edi
```
Use GDB to view processor registers
```
$ gdb -q ./a.out
Reading symbols from ./a.out...done.
(gdb) break main
Breakpoint 1 at 0x8048426: file firstprog.c, line 5.
(gdb) run
Starting program: /home/ubuntu/workspace/Hacking/a.out

Breakpoint 1, main () at firstprog.c:5
5         for(i=0; i < 10; i++) {     // Loop 10 times.
(gdb) info registers
eax            0x1      1
ecx            0x78e4033d       2028208957
edx            0xffffd344       -11452
ebx            0xf7fc3000       -134467584
esp            0xffffd2f0       0xffffd2f0
ebp            0xffffd318       0xffffd318
esi            0x0      0
edi            0x0      0
eip            0x8048426        0x8048426 <main+9>
eflags         0x286    [ PF SF IF ]
cs             0x23     35
ss             0x2b     43
ds             0x2b     43
es             0x2b     43
fs             0x0      0
gs             0x63     99
(gdb) quit
A debugging session is active.

        Inferior 1 [process 26506] will be killed.

Quit anyway? (y or n) y
```
Set GDB to use Intel syntax
```
$ echo "set disassembly intel" > ~/.gdbinit
$ cat ~/.gdbinit
set dis intel
```
Map assembly code to source code: `-g` option adds extra dubugging information.
```
$ gcc -g -m32 firstprog.c
$ gdb -q ./a.out
Reading symbols from ./a.out...done.
(gdb) list
1       #include <stdio.h>
2
3       int main() {
4         int i;
5         for(i=0; i < 10; i++) {     // Loop 10 times.
6           puts("Hello, world!\n");  // put the string to the output.
7         }
8         return 0;                   // Tell OS the program exited without errors.
9       }
(gdb) disassemble main
Dump of assembler code for function main:
   0x0804841d <+0>:     push   ebp
   0x0804841e <+1>:     mov    ebp,esp
   0x08048420 <+3>:     and    esp,0xfffffff0
   0x08048423 <+6>:     sub    esp,0x20
   0x08048426 <+9>:     mov    DWORD PTR [esp+0x1c],0x0
   0x0804842e <+17>:    jmp    0x8048441 <main+36>
   0x08048430 <+19>:    mov    DWORD PTR [esp],0x80484e0
   0x08048437 <+26>:    call   0x80482f0 <puts@plt>
   0x0804843c <+31>:    add    DWORD PTR [esp+0x1c],0x1
   0x08048441 <+36>:    cmp    DWORD PTR [esp+0x1c],0x9
   0x08048446 <+41>:    jle    0x8048430 <main+19>
   0x08048448 <+43>:    mov    eax,0x0
   0x0804844d <+48>:    leave
   0x0804844e <+49>:    ret
End of assembler dump.
(gdb) break main
Breakpoint 1 at 0x8048426: file firstprog.c, line 5.
(gdb) run
Starting program: /home/ubuntu/workspace/Hacking/a.out

Breakpoint 1, main () at firstprog.c:5
5         for(i=0; i < 10; i++) {     // Loop 10 times.
(gdb) info register eip
eip            0x8048426        0x8048426 <main+9>
```
The instructions before `0x8048426` are collectively known as the function
prologue and are generated by the compiler.

* Examine memory
<pre>
(gdb) x/o $eip
0x8048426 <main+9>:     03411042307
(gdb) x/x $eip
0x8048426 <main+9>:     0x1c2444c7
(gdb) x/u $eip
0x8048426 <main+9>:     472138951
(gdb) x/t $eip
0x8048426 <main+9>:     00011100001001000100010011000111
(gdb) x/2x $eip
0x8048426 <main+9>:     0x1c2444c7      0x00000000
(gdb) x/12x $eip
0x8048426 <main+9>:     0x1c2444c7      0x00000000      0x04c711eb      0x0484e024
0x8048436 <main+25>:    0xfeb4e808      0x4483ffff      0x83011c24      0x091c247c
0x8048446 <main+41>:    0x00b8e87e      0xc9000000      0x575590c3      0x5356ff31
(gdb) x/4xb $eip
0x8048426 <main+9>:     0xc7    0x44    0x24    0x1c
(gdb) x/4ub $eip
0x8048426 <main+9>:     199     68      36      28
(gdb) x/iuw $eip
0x8048426 <main+9>:     472138951
(gdb) x/i $eip
=> <mark>0x8048426 <main+9>:  mov    DWORD PTR [esp+0x1c],0x0</mark>
(gdb) x/3i $eip
=> 0x8048426 <main+9>:  mov    DWORD PTR [esp+0x1c],0x0
   0x804842e <main+17>: jmp    0x8048441 <main+36>
   0x8048430 <main+19>: mov    DWORD PTR [esp],0x80484e0
(gdb) x/7xb $eip
0x8048426 <main+9>:     0xc7    0x44    0x24    0x1c    0x00    0x00    0x00
</pre>
See local variable initialization
<pre>
(gdb) i r esp
esp            0xffffd2f0       0xffffd2f0
(gdb)  x/4xb $esp+0x1c
0xffffd30c:     0x00    0x30    0xfc    0xf7
(gdb) x/4xb 0xffffd30c
0xffffd30c:     0x00    0x30    0xfc    0xf7
(gdb) print $esp+0x1c
$1 = (void *) 0xffffd30c
(gdb) x/4xb $1
<mark>0xffffd30c:     0x00    0x30    0xfc    0xf7</mark>
(gdb) x/xw $1
0xffffd30c:     0xf7fc3000
(gdb) nexti
0x0804842e      5         for(i=0; i < 10; i++) {     // Loop 10 times.
(gdb) x/4xb $1
<mark>0xffffd30c:     0x00    0x00    0x00    0x00</mark>
(gdb) x/dw $1
0xffffd30c:     0
(gdb) i r eip
eip            0x804842e        0x804842e <main+17>
(gdb) x/i $eip
=> 0x804842e <main+17>: jmp    0x8048441 <main+36>
</pre>
As expected, the instruction `mov DWORD PTR [esp+0x1c],0x0` zeroes out the 4
bytes found at `esp+0x1c`, which is memory set aside for the C variable i.
<pre>
(gdb) x/10i $eip
=> 0x804842e <main+17>: jmp    <mark>0x8048441</mark> <main+36>
   <mark>0x8048430</mark> <main+19>: mov    DWORD PTR [esp],0x80484e0
   0x8048437 <main+26>: call   0x80482f0 <puts@plt>
   0x804843c <main+31>: add    DWORD PTR [esp+0x1c],0x1
   <mark>0x8048441 <main+36>: cmp    DWORD PTR [esp+0x1c],0x9</mark>
   0x8048446 <main+41>: jle    <mark>0x8048430</mark> <main+19>
   0x8048448 <main+43>: mov    eax,0x0
   0x804844d <main+48>: leave
   0x804844e <main+49>: ret
   0x804844f:   nop
</pre>
The `cmp` and `jle` instructions checks the loop condition.
<pre>
(gdb) nexti
0x08048441      5         for(i=0; i < 10; i++) {     // Loop 10 times.
(gdb) x/i $eip
=> 0x8048441 <main+36>: cmp    DWORD PTR [esp+0x1c],0x9
(gdb) nexti
0x08048446      5         for(i=0; i < 10; i++) {     // Loop 10 times.
(gdb) nexti
6           puts("Hello, world!\n");  // put the string to the output.
(gdb) i r eip
eip            0x8048430        0x8048430 <main+19>
(gdb) x/2i $eip
=> 0x8048430 <main+19>: mov    DWORD PTR [esp],<mark>0x80484e0</mark>
   0x8048437 <main+26>: call   0x80482f0 <puts@plt>
</pre>

The `mov` instruction moves `0x80484e0` (an address) to `esp`, but what is
stored at the memory location?

<pre>
(gdb) i r esp
esp            0xffffd2f0       0xffffd2f0
(gdb) x/2xw 0xffffd2f0
0xffffd2f0:     0x00000001      0xffffd3b4
(gdb) nexti
0x08048437      6           puts("Hello, world!\n");  // put the string to the output.
(gdb) i r esp
esp            0xffffd2f0       0xffffd2f0
(gdb) x/2xw 0xffffd2f0
0xffffd2f0:     <mark>0x080484e0</mark>      0xffffd3b4
(gdb) x/6xb <mark>0x080484e0</mark>
0x80484e0:      0x48    0x65    0x6c    0x6c    0x6f    0x2c
(gdb) x/6ub <mark>0x080484e0</mark>
0x80484e0:      72      101     108     108     111     44
(gdb) x/6cb <mark>0x080484e0</mark>
0x80484e0:      72 'H'  101 'e' 108 'l' 108 'l' 111 'o' 44 ','
(gdb) x/s <mark>0x080484e0</mark>
0x80484e0:      "Hello, world!\n"
</pre>

These commands reveal that the data string "Hello, world!\n" is stored at
memory address `0x080484e0`.

<pre>
(gdb) x/2i $eip
=> 0x8048437 <main+26>: call   0x80482f0 <puts@plt>
   0x804843c <main+31>: add    DWORD PTR [esp+0x1c],0x1
(gdb) nexti
Hello, world!

5         for(i=0; i < 10; i++) {     // Loop 10 times.
</pre>

The next instruction increments the variable `i` by `1`, the second instruction
check the loop termination condition, and the third instruction conditionally
jumps to the beginning of the loop body by testing the values in and put
`0x8048430` in `eip`.

<pre>
(gdb) x/3i $eip
=> 0x804843c <main+31>: add    DWORD PTR [esp+0x1c],0x1
   0x8048441 <main+36>: cmp    DWORD PTR [esp+0x1c],0x9
   0x8048446 <main+41>: jle    0x8048430 <main+19>
(gdb) info register eflags
eflags         0x282    [ SF IF ]
(gdb) nexti
0x08048441      5         for(i=0; i < 10; i++) {     // Loop 10 times.
(gdb) x/3i $eip
=> 0x8048441 <main+36>: cmp    DWORD PTR [esp+0x1c],0x9
   0x8048446 <main+41>: jle    0x8048430 <main+19>
   0x8048448 <main+43>: mov    eax,0x0
(gdb) nexti
0x08048446      5         for(i=0; i < 10; i++) {     // Loop 10 times.
(gdb) x/3i $eip
=> 0x8048446 <main+41>: jle    0x8048430 <main+19>
   0x8048448 <main+43>: mov    eax,0x0
   0x804844d <main+48>: leave
(gdb) info register eflags
eflags         0x293    [ CF AF SF IF ]
</pre>

The [`eflags`](https://en.wikipedia.org/wiki/FLAGS_register) register is set
accordingly after the result of `cmp` instruction. `jle` will jump to `0x8048430`
because the sign flage `SF` is on.
