# 编译FAQ

## error: request for member ... in ..., which is of non-class type ...

#### 示例代码

链接：[Github](https://github.com/mumingv/cpp/tree/master/netease/major/05_dp/template_method/old)。


#### 问题描述

首先声明一个类

```cpp
class Library{
public:
    Library() {};
    void step1();
    void step3();
    void step5();
};
```

然后使用该类实例化一个对象

```cpp
Library lib();
```

编译出现的详细错误如下

```cpp
$ make
g++ template1_lib.cpp template1_app.cpp -o main
template1_app.cpp: In function ‘int main()’:
template1_app.cpp:33:7: error: request for member ‘step1’ in ‘lib’, which is of non-class type ‘Library()’
  lib->step1();
       ^
make: *** [all] Error 1
```


#### 问题分析

根据提示的错误可以得知，`lib`并未被当作一个类对象(non-class type)。原因是`Library`类的构造函数没有参数，而此时用`Library lib();`的话，就会将`lib`当作一个函数声明而不是对象。

#### 问题总结

想要实例化一个对象的话，主要分下面两种情况。

1.类没有构造函数 或者 构造函数没有参数

```cpp
Library lib;
```

<font color="red">说明：在这种情况下如果使用括号的话，即：`Library lib();` 其就会被当作一个函数的声明，也就是本例中出现的错误。</font>

2.构造函数有参数

```cpp
Library lib(param1, param2, ...);
```


## error: ‘virtual’ outside class declaration

#### 示例代码

链接：[Github](https://github.com/mumingv/cpp/tree/master/netease/major/05_dp/strategy/new)。


#### 问题描述

类的声明USTax.h

```cpp
class USTax : public TaxStrategy {
public:
    virtual double Calculate();
};
```

类中的函数定义USTax.cpp

```cpp
virtual double USTax::Calculate() {
    cout << "Calculate called in USTax" << endl;
}
```

编译该类文件，提示如下错误

```cpp
$ g++ -c USTax.cpp
USTax.cpp:7:33: error: ‘virtual’ outside class declaration
```


#### 问题总结

关键字`virtual`只能用在类的声明当中，而不能用在类的定义当中。







