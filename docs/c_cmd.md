# 相关命令

## gcc

*gcc - GNU project C and C++ compiler*

常用参数：
0. -fPIC  生成目标无关代码
0. -shared  生成共享代码

###  

#### 生成.so动态链接库

```c
g++ -fPIC -shared -o output/lib/libdict.so src/libdict.cpp
```


#### 同时编译.cpp和.a文件

```c
g++ -o ./output/test test.cpp ../output/lib/libdict.a
```


## cpp


## cc1


## as


## ld


## ar

*ar - create, modify, and extract from archives*

常用参数：
0. -c  创建
0. -r  将文件插入archive
0. -v  verbose

###  

#### 生成.a静态链接库

```c
g++ -c src/libdict.cpp -o output/lib/libdict.o
ar -crv output/lib/libdict.a output/lib/libdict.o
```

## gdb


## objdump

*objdump - display information from object files.*

常用参数：
0. -d --disassemble  反汇编

### 

#### 反汇编可重定位目标文件(.o)

```c
$ objdump -d test.o
test.o:     file format elf64-x86-64
Disassembly of section .text:
0000000000000000 <add>:
   0:   55                      push   %rbp
   1:   48 89 e5                mov    %rsp,%rbp
   4:   89 7d ec                mov    %edi,-0x14(%rbp)
   7:   89 75 e8                mov    %esi,-0x18(%rbp)
   a:   8b 45 e8                mov    -0x18(%rbp),%eax
   d:   8b 55 ec                mov    -0x14(%rbp),%edx
  10:   01 d0                   add    %edx,%eax
  12:   89 45 fc                mov    %eax,-0x4(%rbp)
  15:   8b 45 fc                mov    -0x4(%rbp),%eax
  18:   5d                      pop    %rbp
  19:   c3                      retq   
```

反汇编输出的结果中，包含的信息有：指令的地址、指令的机器代码、指令的汇编代码。


### 

#### 反汇编可执行文件(a.out)

```c
$ objdump -d a.out 
a.out:     file format elf64-x86-64
...
Disassembly of section .text:
...
0000000000400508 <add>:
  400508:       55                      push   %rbp
  400509:       48 89 e5                mov    %rsp,%rbp
  40050c:       89 7d ec                mov    %edi,-0x14(%rbp)
  40050f:       89 75 e8                mov    %esi,-0x18(%rbp)
  400512:       8b 45 e8                mov    -0x18(%rbp),%eax
  400515:       8b 55 ec                mov    -0x14(%rbp),%edx
  400518:       01 d0                   add    %edx,%eax
  40051a:       89 45 fc                mov    %eax,-0x4(%rbp)
  40051d:       8b 45 fc                mov    -0x4(%rbp),%eax
  400520:       5d                      pop    %rbp
  400521:       c3                      retq   
  400522:       66 2e 0f 1f 84 00 00    nopw   %cs:0x0(%rax,%rax,1)
  400529:       00 00 00 
  40052c:       0f 1f 40 00             nopl   0x0(%rax)
...
```







