# 参数传递与返回值

## constructor（ctor，构造函数）被放在private区

构造函数放在private区域的话，就不能直接通过该类进行对象的实例化。但是可以在该class的成员函数中进行调用。

常见的用法是使用Singleton（单例）模式。代码示例：

```cpp
class A {
public:
    static A& getInstance();
    setup() { ... }
private:
    A();
    A(const A& rhs);
    ...
};

A& A::getInstance() {
    static A a;
    return a;
}
```
```cpp
A::getInstance().setup();
```


## const member functions（常量成员函数）

常量成员函数表示该函数不会改变对象内部成员的值。

```cpp
class complex
{
public:
  complex (double r = 0, double i = 0): re (r), im (i) { }
  double real () const { return re; }
  double imag () const { return im; }
private:
  double re, im;
};
```

注意：const的位置在函数的小括号和大括号之间。

```cpp
double real () const { return re; }
```

**重要**：考虑这样一个场景：如果在函数声明的时候没有使用const关键字，但是使用者在实例化对象的时候却使用了const关键字；那么这个时候编译器就会报错。示例如下：

```cpp
double real () { return re; }
double imag () const { return im; }
```

```cpp
const complex c1(2, 1);
cout << c1.real();
cout << c1.imag();
```

```cpp
error: passing ‘const complex’ as ‘this’ argument of ‘double complex::real()’ discards qualifiers [-fpermissive]
```

因为使用者希望对象不被改变，但是函数定义却说函数内部可能会改变对象的值，这显然出现了矛盾。<font color="red">所以，在定义函数接口的时候，应该使用const的地方要果断加上const。</font>

调用关系如下：
1. 常量对象只能调用常量成员函数，不能调用普通成员函数；
2. 普通对象可以调用常量成员函数，也可以调用普通成员函数；
3. 常量成员函数，可以被常量对象调用，也可以被普通对象调用；
4. 普通成员函数，不能被常量对象调用，可以被普通对象调用；

<font color="red">总结一句话就是：常量对象不能调用普通成员函数。</font>


## 参数传递：pass by value vs. pass by reference (to const)

C++中建议在所有场景下均使用引用传递，而不使用值传递。

如果觉得传引用怕函数内部会修改传递的对象，则可以在函数定义的参数前使用关键字`const`。


## 返回值传递：return by value vs. return by reference (to const)

return by value 示例：

```cpp
double real () const { return re; }
```

return by reference 示例：

```cpp
complex& __doapl (complex *, const complex&);
```

与参数传递一样，返回值传递也尽量使用引用传递，而不是值传递。


## friend（友元）

代码示例：

```cpp
class complex
{
public:
  complex (double r = 0, double i = 0): re (r), im (i) { }
  complex& operator += (const complex&);
  double real () const { return re; }
  double imag () const { return im; }
private:
  double re, im;

  friend complex& __doapl (complex *, const complex&);
};


inline complex&
__doapl (complex* ths, const complex& r)
{
  ths->re += r.re;
  ths->im += r.im;
  return *ths;
}
```

友元函数可以直接使用类的成员变量，不过其破坏了类的封装性。


## 相同class的各个objects互为friends（友元）

代码示例：

```cpp
class complex
{
public:
    complex (double r = 0, double i = 0): re (r), im (i) { }
    int func(const complex& param)
    { return param.re + param.im; }
private:
    double re, im;
};
```
```cpp
{
    complex c1(2, 1);
    complex c2;
    c2.func(c1);
}
```


## class body外的各种定义（definitions）

1. 什么情况下可以pass by reference
2. 什么情况下可以return by reference

<font color="red">如果返回的变量是在函数内部创建的临时变量，则不能返回引用。其他情况均可以返回引用。</font>

