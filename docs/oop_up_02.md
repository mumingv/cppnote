# 头文件与类的声明

## C++关于数据和函数

- 面向过程：数据和函数是分离的。
- 面向对象：过程和数据是一起放在类/对象中的。


## Output, C++ vs. C

- C语言中，头文件使用`<stdio.h>`，可以演化为`<cstdio>`。
- C++语言中，头文件使用`<iostream.h>`，可以演化为`<iostream>`。


## 头文件中的防卫式声明

```cpp
#ifndef __COMPLEX__
#define __COMPLEX__
...
#endif
```

## class template(模板)简介

```cpp
template<typename T>
class complex {
    public:
        ...
    private:
        T re, im;
}
complex<double> c1(2.5, 1.5);
complex<int> c2(2, 6);
```


