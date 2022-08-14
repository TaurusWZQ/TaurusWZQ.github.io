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

按照理论分析，这样做确实可以做到防止被继承。
**注意：又有一个新的问题，对，就是只能在堆上创建，无法再栈上实现这个类**
这就是私有的构造函数的局限性。



 第二个方法

利用友元不能被继承的特性，可以实现这样的类。
主要思想，设计一个模板辅助类Base，将构造函数声明为私有的；再设计一个不能继承的类FinalClass,,将FinalClass 作为Base的友元类。将FinalClass虚继承Base。

```c++
include <iostream>
using namespace std;

template <typename T>
class Base{
    friend T;
private:
    Base(){
        cout << "base" << endl;
    }
    ~Base(){}
};

class FinalClass : virtual public Base<FinalClass>{  
 //一定注意 必须是虚继承
public:
    FinalClass(){
        cout << "FinalClass()" << endl;
    }
};

class C:public FinalClass{
public:
    C(){}     //继承时报错，无法通过编译
};


int main(){
    FinalClass b;      //B类无法被继承
    //C c;
    return 0;
}
```

类Base的构造函数和析构函数因为是私有的，只有Base类的友元可以访问，FinalClass类在继承时将模板的参数设置为了FinalClass类，所以构造FinalClass类对象时们可以直接访问父类（Base）的构造函数。

C 在调用构造函数时，不会先调用FinalClass的构造函数，而是直接调用Base的构造函数，C不是Base的友元类，所以无法访问。这样的话C就不能继承FinalClass。



**当然在C++11以后，C++引入了新的关键词 new keyword final ，直接在类后面加上final关键字，就可以防止该类被继承**

