## 侯捷C++学习
## 防御式编程

include：记得ifdef endif 防御式编程，防止头文件重复引用。

## 类构造

类构造函数，防止调用时候无法区分，如有默认值的构造和不带参数的构造。

## const

`const A *getPointer()`

此函数的返回类型为`const A * `类型。【const加在前】: 表示此函数的return类型是const类型。

`A *getPointer() const`

【const写在后】表示此函数是一个常函数，函数中只能作读取操作、不能作修改操作.

C++规定，对于一个常量的任何操作不能修改此常量，不管这个常量是什么类型，不管是否用函数修改，所以出于安全性的考虑，C++直接规定作用于常量的函数一定要是常函数，即大括号中不含修改操作，只能“get”一些东西。

表示此函数为常函数的方法是在函数大括号前加const关键字。

参数或返回值是否加上const取决于会不会被改变。

## 引用

reference：int& r=x，x和r的，sizeof，取地址，都一样，其实都是假象，内部实现都是指针。很少用做参数定义，多半用在参数传递

在此附上将指针转为引用的小技巧：`int *p = &a`, 我们将引用符号移到左边将 \*替换即可：`int &p = a`。

引用基本等于指针，修改会影响源值，不希望修改这加上const。尽量用引用，传参和返回值。考虑如果返回是局部的，则返回不能为引用。


## 运算符重载

运算符重载，设计成成员函数或者非成员函数都可以，比如+=写在成员函数里面，+写在非成员函数里。但比如<<不能作为成员函数，因为他的左边是ostream而不是自己定义的类，如果作为成员函数会有左值报错。

## friend

friend：可以访问类的私有成员

## Big 3 : 拷贝，虚构，赋值函数

拷贝赋值函数：需要检测自我赋值

## delete/new

`new array[],delete []`, `new, delete`

## 静态成员函数

static： 静态成员函数，不会有默认this。调用可以使用  **类名::函数名** 方式，也可以**具体对象.函数名**

## 面向对象3个重要点

### Composition复合

has-a 关系，一般通过实体引入，作为私有成员，用实心棱形表示，棱形指向引入者。等于组合关系。

### Delegation委托

一般通过指针引入，作为私有成员，用空心棱形表示，棱形指向引入者。指针引入 创建过程与实体引入不同步，指针在需要的时候才会使用（进行委托）。等于聚合关系。

pimp：point to impelecation。比如相同字符串，都指向一块空间，当需要修改时，可以copy on write, 即拷贝一份供修改

## Inheritance继承

is-a关系，空心三角形指向父类

委托+继承：如观察者模式，组合模式，都是这样

## virtual

virtual：虚函数，父类有自身定义，子类可以不必定义；

Pure virtual：纯虚函数，父类没有定义，子类必须定义

虚机制（动态绑定），多态：虚函数，虚表实现。1）调用指针，2）向上转型（猪到动物），3）调用虚函数

## 转换函数

operator double() const();

## Explicit

Explicit:一般用在类构造函数前，防止编译器自动转换成这个类

## 模板

`template<typename T>`

类模板

函数模板 

成员模板

不保证使用能够编译通过，因为只有使用时才知道具体类型

熟悉可以看 seastar源码

## 模板偏特化

参数个数偏特化：模板有多个参数，偏特化让某个或几个参数固定，其余动态

参数范围偏特化：模板是`T`，偏特化版本为`*T`

数量不定的模板参数variadic template，模板参数有…arg


## C++ 启动

CRT，heap init ，io init等


## RAII

RAII是Resource Acquisition is Initialization(资源获取即初始化)的缩写，是C++语言的一种管理资源，避免泄漏的用法。

利用的就是C++构造的对象最终会被销毁的原则。利用C++对象生命周期的概念来控制程序的资源,比如内存,文件句柄,网络连接等。

RAII的做法是使用一个对象，在其构造时获取对应的资源，在对象生命周期内控制对资源的访问，使之始终保持有效，最后在对象析构的时候，释放构造时获取的资源。

简单地说，就是把资源的使用限制在对象的生命周期之中，自动释放。

举个简单的例子，通常在多线程编程的时候，都会用到std::mutex，如下代码：

```cpp
std::mutex mutex_;

void fun() {
  mutex_.lock();
  
  if (...) {
    mutex_.unlock();
    return;
  }
  
  mutex_.unlock()
}
```

在上述代码中，如果if分支多的话，每个if分支里面都要释放锁，如果一不小心忘记释放，那么就会造成故障，为了解决这个问题，我们使用RAII技术，代码如下：
```cpp
std::mutex mutex_;

void fun() {
  std::lock_guard<std::mutex> guard(mutex_);

  if (...) {
    return;
  }
}
```

在guard出了fun作用域的时候，会自动调用mutex_.lock()进行释放，避免了很多不必要的问题。


## cpp测试工具链

用现代C++测试工具链：doctest+FakeIt+nanobench, 可以完美地替代gtest/gmock和google bench，没有任何依赖，无需安装，直接包含头文件就可以用，非常容易集成和使用，是时候抛弃google test和google bench了!

## constexpr

constexpr即常量表达式，重点在表达式字段，用于指定变量或函数可以在常量表达式中使用，可以(或者说一定)在编译时求值的表达式，而const则为了约束变量的访问控制，表示运行时不可以直接被修改，其往往可以在编译期和运行时进行初始化。

前面提到了constexpr是在编译阶段进行求值，那么也就是说在程序运行之前，就已经计算完成，这种无疑大大提升了程序的运行效率。因此提升运行效率就是C++11引入constexpr说明符的目的，也就是说能在编译阶段做的事情就绝不放在运行期做。

```cpp
constexpr int val = 1 + 2;
// 等价与
const int val = 1 + 2;
// 等价于
constexpr int Add(const int a, const int b) {
    return a + b;
}

int main() {
    const int val = Add(1, 2);
    return 0;
}
```

C++17起，引入了if constexpr语句