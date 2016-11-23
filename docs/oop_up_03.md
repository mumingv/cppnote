# 构造函数

## inline（内联）函数

两种情况：
1. 函数若在class body内完成定义，便自动成为inline候选人。具体会不会真正的成为inline函数，则由编译器决定。
2. 函数若在class外部完成定义，如果需要将其变成inline候选人，则需要在函数之前增加inline关键字。


## access level（访问级别）

三种：public、protected、private。默认为public。

一个类中，可以使用多个public、或者多个protected、又或者多个private。


## constructor（ctor，构造函数）

示例：

```cpp
class complex
{
public:
    complex (double r = 0, double i = 0): re (r), im (i) { }
private:
    double re, im;
    friend complex& __doapl (complex *, const complex&);
};
```

说明：
1. 构造函数名称一定和类的名称相同；
2. 创建对象时，如果没有携带实参，则使用默认实参（default argument）；
3. 构造函数不需要返回值；
4. 构造函数可以通过初值列（initializatin list）将参数值赋值给类的成员变量；

初始化 vs 赋值，对于构造函数，尽量通过初值列的方式初始化，而不是在构造函数体内进行赋值。

<font class="red">不带指针的类，多半不用析构函数。</font>


## ctor（构造函数）可以有很多个 - overloading（重载）

存在名称相同但参数不同的多个函数，称为函数的重载。

同名函数编译后的实际名称是不一样的，如：两个real函数编译后的名称。

```cpp
?real@Complex@@QBENXZ
?real@Complex@@QAENABN@Z
```

函数重载常常用在构造函数身上。

但是，下面这两个构造函数却是不能同时存在的。

```cpp
complex (double r = 0, double i = 0): re (r), im (i) { }
complex (): re (0), im (0) { }
```

因为当实例化对象的时候，编译器不知道要调用哪个构造函数。

```cpp
complex c1;
complex c2();
```

