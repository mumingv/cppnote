# 模块接口

## User input

### 

#### 示例：接收用户输入的不定个参数

```
$ cat sample.cpp 
#include <string>
#include <stdio.h>
#include <iostream>
int main(int argc, char** argv) {
    std::cout << "参数个数：" << argc << std::endl;
    for (int i = 0; i < argc; ++i) {
        std::cout << "第" << i << "个参数值为：" << argv[i] << std::endl;
    }
}
```
```c
$ ./a.out param1 param2 param3
参数个数：4
第0个参数值为：./a.out
第1个参数值为：param1
第2个参数值为：param2
第3个参数值为：param3
```

