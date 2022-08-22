---
title: C++中关于继承与访问权限的那些事
date: 2022-08-12 18:00:00 +0800
categories: [技术]
tags: [CPP]
pin: true
author: TaurusWZQ

toc: true
comments: true

math: false
mermaid: true

typora-root-url: ../../TaurusWZQ.github.io
---

# C++中关于继承与访问权限的那些事



今天我们来讨论一下在C++中关于继承与访问权限的那些事？



希望能够帮助大家真正的理解基类与派生类之间访问权限的问题。当然这也是一个在面试过程过很容易被问到的一个问题。

我们都知道继承（inheritance）是C++的三大特性之一。在继承的过程中会涉及很多的问题，访问权限就是其中之一。

注意：在具体说明访问权限如何变化之前，首先需要明确“派生类”与“派生类对象”不能混为一谈，前者是一个type，而后者是一个实例，是一个object。



接下来我们来讨论一下访问权限

C++通过public，protected，private 三个关键字来控制成员变量和成员函数的访问权限，它们分别表示公有的、受保护的、私有的，这三个keyword被称为成员访问限定符。

在类的内部(定义类的代码内部)，无论成员被声明为public. protected还是private,都是可以互相访问的，没有访问权限的限制。
在类的外部(定义类的代码之外)，只能通过**对象**访问成员，并且通过对象只能访问public属性的成员，不能访问private、protected 属性的成员。
无论公有继承、私有和保护继承，私有成员不能被“派生类”访问，基类中的公有和保护成员能被**“派生类”**访问。
对于公有继承，只有基类中的公有成员能被**“派生类对象”**访问，保护和私有成员不能被**“派生类对象”**访问。对于私有和保护继承，基类中的所有成员不能被‘派生类对象"访问。



下面附上demo信息 （具体代码见

```c++
#include<iostream>
using namespace std;
class Base{
public:
    int a;
protected:
    int b; 
private:
    int c;
public:
    Base();
    ~Base();
};
Base::Base() {
}
Base::~Base() {
}

class Derived1 : public Base { // public继承
public:
    void xfunc1() {
        x = a; // 基类中的公有成员能被“派生类”访问。
    }
    void xfunc2() {
        x = b; // 基类中的保护成员能被“派生类”访问。
    }
    void xfunc3() {
        //x = c; // 私有成员不能被“派生类”访问
    }
private:
    int x;
public:
    Derived1();
    ~Derived1();
};

Derived1::Derived1() {
}
Derived1::~Derived1() {
}

class Derived2 : protected Base { // protected继承
private:
    int y;
    void yfunc1() {
        y = a; // 基类中的公有成员能被“派生类”访问。
    }
    void yfunc2() {
        y = b; // 基类中的保护成员能被“派生类”访问。
    }
    void yfunc3() {
        //y = c; // 私有成员不能被“派生类”访问
    }
public:
    Derived2();
    ~Derived2();
};

Derived2::Derived2() {
}
Derived2::~Derived2() {
}

class Derived3 : private Base { // private继承
private:
    int z;
    void zfunc1() {
        z = a; // 基类中的公有成员能被“派生类”访问。
    }
    void zfunc2() {
        z = b; // 基类中的保护成员能被“派生类”访问。
    }
    void zfunc3() {
        //z = c; // 私有成员不能被“派生类”访问
    }
public:
    Derived3();
    ~Derived3();
};

Derived3::Derived3() {
}
Derived3::~Derived3() {
}

int main() {
    Base parent1;
    parent1.a = 1;
    //parent1.b = 2; // 对象不能访问类中protected 属性的成员。
    //parent1.c = 3; // 对象不能访问类中private 属性的成员。
    Derived1 child1;
    child1.a = 1;
    //child1.b = 2; // public继承下的 派生类对象 不能访问基类 protected 属性的成员
    //child1.c = 3; // public继承下的 派生类对象 不能访问基类 private 属性的成员
    Derived2 child2;
    //child2.a = 1; // protected继承下的 派生类对象 不能访问基类 public 属性的成员
    //child2.b = 2; // protected继承下的 派生类对象 不能访问基类 protected 属性的成员
    //child2.c = 3; // protected继承下的 派生类对象 不能访问基类 private 属性的成员
    Derived3 child3;
    //child3.a = 1; // private继承下的 派生类对象 不能访问基类 public 属性的成员
    //child3.b = 2; // private继承下的 派生类对象 不能访问基类 protected 属性的成员
    //child3.c = 3; // private继承下的 派生类对象 不能访问基类 private 属性的成员
    return 0;
}
```



最重要的就是分清是“派生类”还是“派生类对象”
