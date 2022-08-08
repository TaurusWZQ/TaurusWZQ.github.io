---
title: C++如何实现一个不能被继承的类
date: 2022-08-08 18:00:00 +0800
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

# C++如何实现一个不能被继承的类～ 



今天我们来讨论一下在C++中 如何设计一个类，使得该类不能被继承？

这是一个在面试过程过很容易被问到的一个问题。



C++什么类不能被继承，首先思考派生类继承基类将会发生什么默认操作？派生类在调用自身的构造函数之前需要先调用基类的构造函数。那么我们就让这个不想被别人继承的类的构造函数无法被其派生类调用，从而能达到无法被继承的功能。



最直观的解决方案显然是就是将其基类的构造函数声明为私有的，这样就可以阻止子类构造对象了。

进一步思考，但是这样的话，就无法构造本身的对象了，就无法利用了。既然这样，我们又可以想定义一个静态方法来构造类和释放类。
对，就这样实现它。

```c++
#include<iostream>
using namespace std;
class A {
public:
    static A * Construct(int n) {
          A *pa = new A;
          pa->num = n;
          cout<<"num is:"<<pa->num<<endl;
          return pa;
    }
    static void Destruct(A * pIntance) {
         delete pIntance;
         pIntance = NULL;
    }
private:
	A(){}
	~A(){}
public:
    int num;
};
int  main() {
     A *f = A::Construct(9);
     cout<<f->num<<endl;
     A::Destruct(f);
     return 0;
}
```





