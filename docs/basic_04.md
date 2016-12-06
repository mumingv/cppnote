# 程序的链接

## 编译、汇编和静态链接

将高级语言源程序文件转换为可执行文件分为四个步骤，分别是：预处理、编译、汇编和链接。

其中，汇编生成的.o文件称为可重定位目标文件（Relocatable Object File，简称为可重定位文件）；连接生成的文件称为可执行目标文件（Executable Object File，简称为可执行文件）。

整个过程中各阶段使用的工具和编译选项如下：

```
.c  --预编译(cpp/gcc -E)-->  .i  --编译(cc1/gcc -S)-->  .s
--汇编(as/gcc -c)-->  .o  --链接(ld/gcc)-->  a.out
```

### 编译和汇编

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

### 可执行目标文件的生成









