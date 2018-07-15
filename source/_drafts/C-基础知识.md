---
title: C++基础知识
date: 2018-07-15 22:06:24
updated: 2018-07-15 22:06:24
description: 继承，多态
categories: C++
tags:
- C++
- 继承
- 多态
---

# 继承

## 基类成员变量
有public, protected, private三种继承方式，它们相应地改变了『基类成员变量』的访问属性。
派生类对『基类成员变量』的访问属性如下：

|| 基类 public 成员|基类 protected 成员| 基类 private 成员|
|:--|:--|:--|:--|
|public 继承|public|protected|private|
|protected 继承|protected|protected|private|
|private 继承|private|private|private|

但无论哪种继承方式，以下两点都没有改变：
1. private 成员只能被本类成员（类内）和友元访问，不能被派生类访问；
2. protected 成员可以被派生类访问。

## 基类成员函数
只有 public 继承，才能访问基类的 public 和 protected 成员函数。
其他类型的继承，都不能访问。

# 友元函数
友元函数并**不是**成员函数，但有权访问类的所有私有（private）成员和保护（protected）成员。

友元可以是一个函数，该函数被称为友元函数；友元也可以是一个类，该类被称为『友元类』，在这种情况下，整个类及其所有成员都是友元。
```C++
class Box
{
   double width;
public:
   double length;
   // 友元函数
   friend void printWidth( Box box );   
   void setWidth( double wid );
};

// 请注意：printWidth() 不是任何类的成员函数
// 因为不是成员函数，所以函数名前没有 Box::
// 要访问非 static成员，需要对象作参数
void printWidth( Box box )
{
   // 因为 printWidth() 是 Box 的友元，它可以直接访问该类的任何成员
   
   cout << "Width of box : " << box.width <<endl;
}
```

因为友元函数没有this指针，则参数要有三种情况： 
1. 要访问非static成员时，需要对象做参数；
2. 要访问static成员或全局变量时，则不需要对象做参数；
3. 如果做参数的对象是全局对象，则不需要对象做参数.

# 内联函数
在编译时，编译器会把内联函数的调用表达式用内联函数的函数体进行替换。

对内联函数进行任何修改，都需要重新编译函数的所有客户端。

```C++
inline int Max(int x, int y)
{
   return (x > y)? x : y;
}
```

# 构造函数与析构函数

## 构造函数

在每次创建类的新对象时执行，没有返回值，可用于为某些成员变量设置初始值。

使用初始化列表来初始化字段：
```C++
Rectangle::Rectangle( int width, int height): width(w), height(h)
{
    cout << "Object is being created, length = " << len << endl;
}
```

## 析构函数

在每次删除所创建的对象时执行，不会返回任何值，也不能带有任何参数。
析构函数有助于在跳出程序（比如关闭文件、释放内存等）前释放资源。

```C++
Line::~Line(void)
{
    cout << "Object is being deleted" << endl;
}
```

# 拷贝构造函数

# 静态成员

静态成员属于类本身，被所有对象共享，不属于任何一个对象。

## 静态成员变量
静态成员变量必须初始化。初始化操作必须放在类的外面。

```C++
class Box
{
public:
    static int objectCount; // 仅仅是声明，没有定义
};
 
// 初始化类 Box 的静态成员
// 在类的外面定义，实际上是给静态成员变量分配内存。
int Box::objectCount = 0;
 
int main(void)
{
   cout << "Total objects: " << Box::objectCount << endl;
   return 0;
}
```

## 静态成员函数

静态成员函数没有 this 指针，只能访问静态成员（包括静态成员变量和静态成员函数）和 类外部的其他函数。

# 多态
多态意味着调用成员函数时，会根据调用函数的对象的类型来执行不同的函数。

多态通过虚函数(virtual)来实现。

## 早绑定

```C++
#include <iostream>

using namespace std;

class Shape {
protected:
    int width, height;
public:
    Shape(int a = 0, int b = 0) {
        width = a;
        height = b;
    }

    void area() {
        cout << "Parent class area : 0" << endl;
    }
};

class Rectangle : public Shape {
public:
    Rectangle(int a = 0, int b = 0) : Shape(a, b) {}

    void area() {
        cout << "Rectangle class area : " << (width * height) << endl;
    }
};

class Triangle : public Shape {
public:
    Triangle(int a = 0, int b = 0) : Shape(a, b) {}

    void area() {
        cout << "Triangle class area : " << (width * height / 2 )<< endl;
    }
};

// 程序的主函数
int main() {
    Shape *shape;
    Rectangle rec(10, 5);
    Triangle tri(10, 5);

    // 存储矩形的地址
    shape = &rec;
    // 调用矩形的求面积函数 area
    // shape.area();    // !错误
    shape->area();

    // 存储三角形的地址
    shape = &tri;
    // 调用三角形的求面积函数 area
    shape->area();

    return 0;
}
```
输出：
```
Parent class area : 0
Parent class area : 0
```
shape调用函数 area() 时，被编译器设置为基类Shape中的版本，这就是所谓的『静态多态』或『静态链接』 - 函数调用在程序执行前就准备好了。
也称为早绑定，因为 area() 函数在程序编译期间就已经设置好了。

## 运行时绑定
对上述程序稍作修改，将基类Shape 的 area 前添加 `virtual`关键字，变成虚函数。

此时，编译器看的是指针的内容，而不是它的类型。
由于 tri 和 rec 类的对象的地址存储在 *shape 中，所以会调用各自的 area() 函数。

```C++
virtual void area() {
    cout << "Parent class area : 0" << endl;
}
```
输出：
```
Rectangle class area : 50
Triangle class area : 25
```

## 虚函数
虚函数 是在基类中使用关键字 virtual 声明的函数。

在派生类中重新定义基类中定义的虚函数时，会告诉编译器不要静态链接到该函数。

我们想要的是在程序中任意点可以根据所调用的对象类型来选择调用的函数，这种操作被称为『动态链接』，或『后期绑定』。

## 纯虚函数
如果在基类中又不能对虚函数给出有意义的实现，就要用到『纯虚函数』。
```C++
// 纯虚函数，告诉编译器，函数没有主体
virtual int area() = 0;
```

**小结**
1. 纯虚函数声明如下： virtual void funtion1()=0; 纯虚函数一定没有定义，纯虚函数用来规范派生类的行为，即接口。包含纯虚函数的类是抽象类，抽象类不能定义实例，但可以声明指向实现该抽象类的具体类的指针或引用。

2. 虚函数声明如下：virtual ReturnType FunctionName(Parameter) 虚函数必须实现，如果不实现，编译器将报错，错误提示为：

> error LNK****: unresolved external symbol "public: virtual void __thiscall ClassName::virtualFunctionName(void)"

3. 对于虚函数来说，父类和子类都有各自的版本。由多态方式调用的时候动态绑定。

4. 实现了纯虚函数的子类，该纯虚函数在子类中就编程了虚函数，子类的子类即孙子类可以覆盖该虚函数，由多态方式调用的时候动态绑定。

5. 虚函数是C++中用于实现多态(polymorphism)的机制。核心理念就是通过基类访问派生类定义的函数。

6. 在有动态分配堆上内存的时候，析构函数必须是虚函数，但没有必要是纯虚的。

7. 友元不是成员函数，只有成员函数才可以是虚拟的，因此友元不能是虚拟函数。但可以通过让友元函数调用虚拟成员函数来解决友元的虚拟问题。

8. 析构函数应当是虚函数，将调用相应对象类型的析构函数，因此，如果指针指向的是子类对象，将调用子类的析构函数，然后自动调用基类的析构函数。


C++多态意味着调用成员函数时，会根据调用函数的对象的类型来执行不同的函数；
形成多态必须具备三个条件：
1. 必须存在继承关系;

2. 继承关系必须有同名虚函数（其中虚函数是在基类中使用关键字Virtual声明的函数，在派生类中重新定义基类中定义的虚函数时，会告诉编译器不要静态链接到该函数）;

3. 存在基类类型的指针或者引用，通过该指针或引用调用虚函数.