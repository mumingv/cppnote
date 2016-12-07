# 程序的链接

## 预处理、编译、汇编和静态链接

将高级语言源程序文件转换为可执行文件分为四个步骤，分别是：预处理、编译、汇编和链接。

其中，汇编生成的.o文件称为可重定位目标文件（Relocatable Object File，简称为可重定位文件）；连接生成的文件称为可执行目标文件（Executable Object File，简称为可执行文件）。

整个过程中各阶段使用的工具和编译选项如下：

```
.c  --预编译(cpp/gcc -E)-->  .i  --编译(cc1/gcc -S)-->  .s
--汇编(as/gcc -c)-->  .o  --链接(ld/gcc)-->  a.out
```

### 预处理、编译和汇编

说明：下面的命令使用的代码，请：[点击](https://github.com/mumingv/c/tree/master/books/computer_system_basic/04_link/4.1.2.main)。

#### 预处理

示例：将`main.c`转换成`main.i`

```c
$ gcc -E main.c -o main.i
```

```c
$ cpp main.c -o main.i
```

说明：如果不使用`-o`参数的话，将不会自动生成`main.i`文件，而是会将预处理后的结果输出到标准输出。

```c
$ gcc -E main.c
# 1 "main.c"
# 1 "<command-line>"
# 1 "/usr/include/stdc-predef.h" 1 3 4
# 1 "<command-line>" 2
# 1 "main.c"
int add(int, int);
int main()
{
    return add(20, 13);
}
```


#### 编译

示例：将`main.i`转换成`main.s`

```c
$ gcc -S main.i -o main.s
```
```c
$ cc1 main.i -o main.s
 main
Analyzing compilation unit
Performing interprocedural optimizations
 <*free_lang_data> <visibility> <early_local_cleanups> <*free_inline_summary> <whole-program>Assembling functions:
 main
Execution times (seconds)
 phase setup             :   0.00 ( 0%) usr   0.00 ( 0%) sys   0.01 (100%) wall    1093 kB (88%) ggc
 TOTAL                 :   0.00             0.00             0.01               1243 kB
```

Q：对于gcc4.8.2版本，使用cc1编译时可能提示如下错误：
```
$ cc1 main.i -o main.s
-bash: cc1: command not found
```
A：解决方法是将cc1命令文件所在目录加入到PATH环境变量中(对应配置文件：~/.bash_profile)。
```
PATH=$PATH:/usr/libexec/gcc/x86_64-redhat-linux/4.8.2
export PATH
```

当然，也可以直接通过`.c`文件生成`.s`文件。

```c
$ gcc -S main.c -o main.s
```
```c
$ cc1 main.c -o main.s  
 main
Analyzing compilation unit
Performing interprocedural optimizations
 <*free_lang_data> <visibility> <early_local_cleanups> <*free_inline_summary> <whole-program>Assembling functions:
 main
Execution times (seconds)
 phase setup             :   0.00 ( 0%) usr   0.00 ( 0%) sys   0.01 (100%) wall    1093 kB (88%) ggc
 phase opt and generate  :   0.01 (100%) usr   0.00 ( 0%) sys   0.00 ( 0%) wall      30 kB ( 2%) ggc
 final                   :   0.01 (100%) usr   0.00 ( 0%) sys   0.00 ( 0%) wall       1 kB ( 0%) ggc
 TOTAL                 :   0.01             0.00             0.01               1243 kB
```

说明：如果不使用`-o`参数的话，会自动生成`main.s`文件。

```c
$ gcc -S main.i
```
```c
$ cc1 main.i
```

```c
$ gcc -S main.c
```
```c
$ cc1 main.c
```


#### 汇编

示例：将`main.s`转换成`main.o`

```c
$ gcc -c main.s -o main.o
```c
$ as main.s -o main.o
```

当然，也可以直接通过`.c`文件生成`.o`文件。

```c
$ gcc -c main.c -o main.o
```

<font color="red">但是不能使用as命令直接将.c文件生成.o文件，会直接报错。</font>

```c
$ as main.c -o main.o          
main.c: Assembler messages:
main.c:1: Error: junk `(int,int)' after expression
main.c:1: Error: operand size mismatch for `int'
main.c:2: Error: junk `()' after expression
main.c:2: Error: operand size mismatch for `int'
main.c:3: Error: junk at end of line, first unrecognized character is `{'
main.c:4: Error: no such instruction: `return add(20,13)'
main.c:5: Error: junk at end of line, first unrecognized character is `}'
```

说明：如果不使用`-o`参数的话，会自动生成`main.o`文件。

```c
$ gcc -c main.s
```

<font color="red">但是不能使用as命令自动生成main.o文件，不会报错，但会生成一个不能执行的a.out文件，且该文件的内容和main.o的内容完全一样，仅仅是文件名称不同。</font>

```c
$ as main.s
$ ls
a.out  main.c  main.s  test.c
```

```c
$ gcc -c main.c
```

<font color="red">同样，不能使用as命令直接将.c文件生成.o文件，会直接报错。</font>

```c
$ as main.c
main.c: Assembler messages:
main.c:1: Error: junk `(int,int)' after expression
main.c:1: Error: operand size mismatch for `int'
main.c:2: Error: junk `()' after expression
main.c:2: Error: operand size mismatch for `int'
main.c:3: Error: junk at end of line, first unrecognized character is `{'
main.c:4: Error: no such instruction: `return add(20,13)'
main.c:5: Error: junk at end of line, first unrecognized character is `}'
```

总结：`as`命令只能处理`.s`文件且必须使用`-o`参数指定输出目标文件的名称。


### 可执行目标文件的生成

#### 链接

示例：将`main.o`、`test.o`转换成可执行文件`main`

```c
$ gcc main.o test.o -o main
```

Q: 直接根据教材使用`ld`命令进行链接操作的话，会产生如下的错误。
```c
$ ld main.o test.o -o main
ld: warning: cannot find entry symbol _start; defaulting to 00000000004000b0
```
A: 在x86-64的gcc环境下，使用`ld`命令的时候需要增加一些参数和选项，如下所示：
```c
$ ld --build-id --no-add-needed --eh-frame-hdr --hash-style=gnu -m elf_x86_64 -dynamic-linker /lib64/ld-linux-x86-64.so.2 -o main /usr/lib/gcc/x86_64-redhat-linux/4.8.2/../../../../lib64/crt1.o /usr/lib/gcc/x86_64-redhat-linux/4.8.2/../../../../lib64/crti.o /usr/lib/gcc/x86_64-redhat-linux/4.8.2/crtbegin.o -L/usr/lib/gcc/x86_64-redhat-linux/4.8.2 -L/usr/lib/gcc/x86_64-redhat-linux/4.8.2/../../../../lib64 -L/lib/../lib64 -L/usr/lib/../lib64 -L/usr/lib/gcc/x86_64-redhat-linux/4.8.2/../../.. main.o test.o -lgcc --as-needed -lgcc_s --no-as-needed -lc -lgcc --as-needed -lgcc_s --no-as-needed /usr/lib/gcc/x86_64-redhat-linux/4.8.2/crtend.o /usr/lib/gcc/x86_64-redhat-linux/4.8.2/../../../../lib64/crtn.o
```
注：上面的链接选项根据命令`gcc -v main.o test.o -o main`查询编译过程详情得到，GCC用的链接器是`collect2`，其实基于`ld`命令的一层封装，它们的参数是一致的。

参考资料：[The True Story of Hello World](http://www.lisha.ufsc.br/teaching/os/exercise/hello.html)。

当然，也可以直接通过`.c`文件生成可执行文件。

```c
$ gcc main.c test.c -o main
```

<font color="red">但是不能使用ld命令自动生成可执行文件，会报直接报错。</font>

```c
$ ld --build-id --no-add-needed --eh-frame-hdr --hash-style=gnu -m elf_x86_64 -dynamic-linker /lib64/ld-linux-x86-64.so.2 -o main /usr/lib/gcc/x86_64-redhat-linux/4.8.2/../../../../lib64/crt1.o /usr/lib/gcc/x86_64-redhat-linux/4.8.2/../../../../lib64/crti.o /usr/lib/gcc/x86_64-redhat-linux/4.8.2/crtbegin.o -L/usr/lib/gcc/x86_64-redhat-linux/4.8.2 -L/usr/lib/gcc/x86_64-redhat-linux/4.8.2/../../../../lib64 -L/lib/../lib64 -L/usr/lib/../lib64 -L/usr/lib/gcc/x86_64-redhat-linux/4.8.2/../../.. main.c test.c -lgcc --as-needed -lgcc_s --no-as-needed -lc -lgcc --as-needed -lgcc_s --no-as-needed /usr/lib/gcc/x86_64-redhat-linux/4.8.2/crtend.o /usr/lib/gcc/x86_64-redhat-linux/4.8.2/../../../../lib64/crtn.o
ld:main.c: file format not recognized; treating as linker script
ld:main.c:1: syntax error
```

说明：如果不使用-o参数的话，会自动生成可执行文件，不过文件名称固定为`a.out`。

```c
$ gcc main.c test.c
```

<font color="red">同样，不能使用ld命令自动生成可执行文件，会报直接报错。</font>

```c
$ ld --build-id --no-add-needed --eh-frame-hdr --hash-style=gnu -m elf_x86_64 -dynamic-linker /lib64/ld-linux-x86-64.so.2 /usr/lib/gcc/x86_64-redhat-linux/4.8.2/../../../../lib64/crt1.o /usr/lib/gcc/x86_64-redhat-linux/4.8.2/../../../../lib64/crti.o /usr/lib/gcc/x86_64-redhat-linux/4.8.2/crtbegin.o -L/usr/lib/gcc/x86_64-redhat-linux/4.8.2 -L/usr/lib/gcc/x86_64-redhat-linux/4.8.2/../../../../lib64 -L/lib/../lib64 -L/usr/lib/../lib64 -L/usr/lib/gcc/x86_64-redhat-linux/4.8.2/../../.. main.c test.c -lgcc --as-needed -lgcc_s --no-as-needed -lc -lgcc --as-needed -lgcc_s --no-as-needed /usr/lib/gcc/x86_64-redhat-linux/4.8.2/crtend.o /usr/lib/gcc/x86_64-redhat-linux/4.8.2/../../../../lib64/crtn.o        
ld:main.c: file format not recognized; treating as linker script
ld:main.c:1: syntax error
```


#### 可重定位目标文件 vs 可执行目标文件

相同点：都是机器语言目标文件

不同点：
- 可重定位目标文件是由单个模块生成的；可执行目标文件是由多个模块组合而成的；
- 可重定位目标文件的代码地址总是从0开始；可执行目标文件的代码地址不是从0开始的，而是在操作系统规定的虚拟地址空间中产生的；

关于目标文件的代码地址可以使用反汇编命令进行查询：

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

目标文件中包含如下一些信息区（也叫做节/段，section）
- .init 程序初始化代码段
- .fini 程序终结代码段
- .text 代码段
- .rodata 只读数据段
- .data 已初始化的全局数据段
- .bss 未初始化的全局数据段
- .plt 动态链接的跳转表
- ...

*CentOS 7的x86-64位环境中编译32为程序，会报如下错误。*

```c
$ gcc main.c test.c -m32
/usr/bin/ld: cannot find crt1.o: No such file or directory
/usr/bin/ld: cannot find crti.o: No such file or directory
/usr/bin/ld: skipping incompatible /usr/lib/gcc/x86_64-redhat-linux/4.8.2/libgcc_s.so when searching for -lgcc_s
/usr/bin/ld: cannot find -lgcc_s
/usr/bin/ld: skipping incompatible /usr/lib64/libc.so when searching for -lc
/usr/bin/ld: cannot find -lc
/usr/bin/ld: skipping incompatible /usr/lib/gcc/x86_64-redhat-linux/4.8.2/libgcc_s.so when searching for -lgcc_s
/usr/bin/ld: cannot find -lgcc_s
/usr/bin/ld: cannot find crtn.o: No such file or directory
collect2: error: ld returned 1 exit status
```


### 链接器的作用

1.符号解析(symbol resolution)

符号解析的目的就是将每个符号的引用与一个确定的符号定义建立关联。程序中有被定义的符号和被引用的符号，这些符号包括变量名和函数名。

编译器将所有符号存放在可重定位目标文件的符号表(symbol table)中。符号表是一个结构数组，每个表项包含符号名、长度和位置信息等。

2.重定位(relocation)

重定位是指重新确定代码和数据的地址并更新指令中被引用符号地址的工作。

可重定位目标文件中的代码区和数据区都是从地址0开始的，链接器需要将不同模块中相同的段合并起来生成一个新的单独的段，并将合并后的代码区和数据区按照操作系统确定的虚拟地址空间划分（也叫存储器映像）来重新确定位置。

*对于32位Linux系统存储器映像，其代码段总是从地址0x08048000开始，而数据段总是在代码段后面的第一个4KB对齐的地址处开始。*


## 目标文件格式


## 符号表和符号解析


## 重定位


## 可执行文件的加载


## 动态链接


## 小节













