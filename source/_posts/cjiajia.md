---
title: C++ 11的10大革新特性简析
date: 2023-10-29 19:28:00
categories:
  - 教程指南
tags:
  - C++11
  - 现代C++
description: 这是一篇介绍 C++11 的重要新特性的文章,增加了许多新特性和功能。文章详细介绍了C++11的一些主要新特性
cover: https://th.bing.com/th/id/OIP.wgrM5T2m3bFR73-Qevg3PQHaDz?pid=ImgDet&rs=1
---



> C++11,自1998年C++初次标准化以来的第二个重要标准,引入了大量重要的改变。与C++98/03相比,C++11增加了超过140项新特性和600多项缺陷修复,为系统和库开发带来了革命性的变化。本文旨在探讨C++11中最有价值和最常用的新特性和功能。

## auto关键字:简化类型推断

C++11中最显著的新增功能之一是auto关键字,它充当类型说明符。使用auto声明变量时,必须初始化它。在编译期间,编译器根据auto声明右侧表达式的实际类型来推断变量的实际类型。本质上,auto充当变量的实际类型的占位符,该类型由编译器确定。这允许用auto替换冗长的类型声明。但是,在使用auto时需要注意一些准则:

- 声明指针类型时,auto和auto*之间没有区别,但引用必须使用auto&。

- 在同一行上使用auto声明多个变量时,它们必须都是同一类型。编译器根据第一个变量推断类型,并将其应用于其余变量。

- auto不能用作函数参数。

- auto不能用来声明数组。

## decltype关键字:根据表达式指定类型

与auto相反,decltype关键字允许使用由表达式指定的类型声明变量。而auto充当类型推断的占位符,decltype明确声明基于表达式类型的变量。考虑以下示例:

```c++
template<class T1, class T2>
void func(T1 x, T2 y) {
  decltype(x * y) ret = x * y;
  cout << typeid(ret).name() << endl; 
}
```

在上面的代码中,decltype(x * y)声明变量ret与x * y的结果具有相同的类型。当需要表达式的确切类型进行进一步操作时,此特性尤其有用。

## nullptr关键字:增强的空指针处理

在C中,NULL是一个在stddef.h头文件中定义的宏,可用于表示空指针。但是,为了确保类型安全性和改进函数重载的支持,C++引入了nullptr关键字。nullptr是一个指针类型,用作NULL的替代品。事实上,nullptr被定义为((void*)0)。使用nullptr的好处可以在涉及函数重载的场景中看到:

```c++
void func(int x) {
  cout << "void func(int x)" << endl; 
}

void func(int* x) {
  cout << "void func(int* x)" << endl;
}

void test() {
  func(NULL);     // void func(int x)
  func(nullptr);  // void func(int* x)
  cout << typeid(NULL).name() << endl;      // int
  cout << typeid(nullptr).name() << endl;   // std::nullptr
}
```

通过使用nullptr,编译器可以准确确定要调用的重载函数,消除潜在的歧义。

## explicit关键字:防止隐式类型转换

explicit关键字主要用于防止自动隐式类型转换,特别是在构造函数中。通过将explicit应用于单参数或多参数构造函数,可以禁止通过隐式类型转换直接构造对象。考虑以下示例:

```c++ 
class demo {
public:
  explicit demo(int a) : _a(a) { } 
private:
  int _a;
};

void test() {
  // 隐式类型转换,调用单参数构造函数
  // demo d = 10;

  // 当使用explicit禁止此类转换时,编译将失败
}
```

explicit关键字提供了编译时检查,以确保仅允许显式类型转换,从而促进更安全、更谨慎的代码。

## final关键字:限制继承和重写

final关键字在C++11中具有两个目的:限制类继承和防止虚函数的重写。通过将类标记为final,它变为不可继承。类似地,将final应用于基类中的虚函数可防止派生类重写它。考虑以下示例:

```c++
class A final {
public:
  virtual void func() {
    cout << "A::func()" << endl;
  }
};

// 错误:类B无法继承final类A
// class B : public A {
// public:
//   virtual void func() {
//     cout << "B::func()" << endl;  
//   }
// };

class B {
public:
  virtual void func() final {
    cout << "A::func()" << endl;
  } 
};

class C : public B {  
public:
  // 错误:无法重写final函数B::func()
  // virtual void func() {
  //   cout << "B::func()" << endl;
  // }
};
```

final和override关键字充当编译时检查,以强制执行预期行为。如果发生违规,代码将无法编译。

## initializer_list容器:简化初始化

C++11引入了initializer_list容器,它允许使用花括号{}简化对象的初始化。此功能适用于所有类型,而不像C++98中花括号只能用于数组初始化。编译器在遇到如object = {arg1, arg2, arg3}的代码时会自动构造initializer_list容器。C++11容器中的各种构造函数利用了此初始化机制。考虑以下针对vector的示例:

```c++
class point {
public:
  point(int x, int y) : _x(x), _y(y) {
    cout << "point" << endl;
  }
  point(const point& p) : _x(p._x), _y(p._y) {
    cout << "point(const point& p)" << endl; 
  }
private:
  int _x;
  int _y;  
};

void test() {
  point p1(1, 2);                         // 调用构造函数
  point p2 = { 3, 4 };                    // 通过多参数构造函数进行隐式类型转换
  point p3{ 5, 6 };                       // 构造+复制构造函数=直接构造
  vector<point> v{ {1, 2}, {3, 4}, {5, 6} }; // 构造+列表初始化
}
```

initializer_list容器允许各种容器进行简洁、一致的对象初始化。

## unordered_map和unordered_set容器:高效的基于散列表的数据结构

C++11中引入的unordered_map和unordered_set容器提供了高效的基于散列表的数据结构。这些容器是标准模板库(STL)的一部分,为插入、删除和检索等操作提供了常数时间复杂度。通过利用散列函数,unordered_map和unordered_set比它们对应的map和set提供了更快的元素访问。当处理大数据集或需要快速查找的场景时,这些容器特别有用。unordered_map容器存储键值对,而unordered_set容器存储唯一元素。两个容器都提供与有序对应物类似的接口,这使得根据性能要求在两者之间进行转换变得很容易。

## 右值引用和移动语义:优化对象构造和赋值

C++11引入了右值引用和移动语义,以优化对象构造和赋值操作。在C++11之前,在将对象作为函数参数传递时,通常使用左值引用以最小化复制并提高效率。但是,这种方法仅适用于左值。对于需要深拷贝的对象,通过值传递和返回值仍然会产生多个副本。为了解决这个问题,C++11引入了右值引用和移动语义。

右值引用允许直接将临时对象(也称为右值)绑定到引用。这可实现从临时对象到其他对象的资源高效传输,无论是通过移动构造还是移动赋值。移动构造涉及将右值的资源传输到新对象,而移动赋值涉及将右值的资源传输到现有对象。

考虑以下示例:

```c++
class demo {  
public:
  demo(const demo& d) : _a(d._a), _b(d._b) {
    cout << "demo(const demo& d),深拷贝" << endl;
  }
  demo& operator=(const demo& d) {
    /* 资源拷贝 */
    cout << "demo& operator=(const demo& d),深拷贝" << endl;
    return *this;
  }
private:
  /* 资源 */
};

demo getTmpObj() {
  demo d;
  return d; 
}

void test() {
  /* 场景1 */
  demo ret_1 = getTmpObj();   // 深拷贝

  /* 场景2 */ 
  demo ret_2;
  ret_2 = getTmpObj();        // 深拷贝,深拷贝
}
```

在场景1中,当getTmpObj()返回右值时,将执行分配给ret_1的深拷贝。但是,在场景2中,会进行两次深拷贝:一次在分配给ret_2时,另一次在随后的赋值操作期间。对于大对象,这种方式非常低效。

为了解决这种低效问题,引入了移动语义。通过移动语义,第一个深拷贝可以被资源传输替换,从而显着提高性能。

```c++
demo(demo&& d) {
  /* 资源传输 */
  cout << "demo(demo&& d),移动构造" << endl;
}

demo& operator=(demo&& d) {
  /* 资源传输 */ 
  cout << "demo& operator=(demo&& d),移动赋值" << endl;
  return *this;
}  

void test() {
  /* 场景1 */
  demo ret_1 = getTmpObj();   // 移动构造

  /* 场景2 */
  demo ret_2; 
  ret_2 = getTmpObj();        // 移动构造,移动赋值
}
```

通过实现移动构造和移动赋值,不必要的深拷贝被消除,特别是对较大对象,这极大地提高了性能。

## 完美转发:保留引用属性

完美转发是一种保留右值引用的引用属性的技术。它允许按照接收到的原样转发参数,而不会失去其引用特性。完美转发通常用于函数模板中,其中参数的确切类型在转发过程中得以保留。

考虑以下示例:

```c++
void Func(int& x) {
  cout << "左值引用" << endl;
}

void Func(const int& x) {
  cout << "常量左值引用" << endl;
}

void Func(int&& x) {
  cout << "右值引用" << endl;  
}

void Func(const int&& x) {
  cout << "常量右值引用" << endl;
}

template<class T>  
void referenceTransmit(T&& t) {
  Func(t);
}

template<class T>
void perfectForward(T&& t) {
  Func(forward<T>(t)); 
}

void test() {
  int a = 10;
  int b = a;
  referenceTransmit(a);           // "左值引用"
  referenceTransmit(a + b);       // "左值引用" 
  perfectForward(a);              // "左值引用"
  perfectForward(a + b);          // "右值引用"
}
```

在上面的代码中,referenceTransmit和perfectForward演示了引用属性的保留。当将参数传递给referenceTransmit或perfectForward时,第一个场景将产生左值引用,而第二个场景将产生右值引用。这种行为允许转发参数而保持其原始引用特性。

## 可变参数模板:处理可变参数

C++11引入了可变参数模板,它允许处理数量和类型可变的参数。可变参数模板支持创建可以接受任意数量参数的函数和类。这种灵活性是通过使用参数包定义模板类或函数实现的,表示为class... Args或typename... Args。然后可以使用Args... args语法展开参数包Args。在参数数量或类型未知或可能变化的场景中,可变参数模板特别有用。

考虑以下示例:

```c++
void cppPrint() {
  cout << "end";
}

template<class T, class... Args>  
void cppPrint(T argu, Args... args) {
  cout << argu << " ";
  cppPrint(args...);
}

void test() {
  cppPrint(1, "hello", 3, 'a'); // 1 hello 3 a end
}
```

cppPrint函数演示了可变参数模板的强大之处,它可以接受并打印任意数量的参数。通过递归和参数包的展开,会处理每个参数,直到处理完所有参数为止。

## 使用列表初始化进行初始化:简化对象初始化

在C++11中,使用花括号{}的列表初始化被扩展为支持所有类型的初始化,而不仅仅是数组。这一增强提供了一种一致简洁的对象初始化方式。列表初始化利用initializer_list容器,后者在遇到初始化值时会自动构造对象。此特性简化和统一了初始化过程,使代码更可读、更易维护。

## 结论

C++11带来了大量新的特性和功能,极大地增强了该语言。auto关键字简化了类型推断,而decltype允许根据表达式指定类型。nullptr关键字改进了空指针处理,explicit关键字防止了隐式类型转换。final关键字限制了继承和重写,确保了代码完整性。initializer_list容器实现了简化和一致的对象初始化,而unordered_map和unordered_set容器提供了高效的基于散列表的数据结构。右值引用和移动语义优化了对象构造和赋值,而完美转发保留了引用属性。可变参数模板允许处理可变参数,列表初始化简化了对象初始化。

通过这些新特性和功能,C++11开创了C++编程的新时代,提供了更高的效率、安全性和灵活性。拥抱这些进步可以显着提高开发生产力和代码质量。随着C++的不断发展,开发人员需要与时俱进,利用现代C++特性的强大功能极为重要。

