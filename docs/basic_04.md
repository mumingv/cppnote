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

## 目标文件格式


## 符号表和符号解析


## 重定位


## 可执行文件的加载


## 动态链接


## 小节













