---
title: 《C++ Primer》读书笔记 第7章 类
date: '2016-05-21T13:58:42.000Z'
tags: null
---

# 第7章 类

## 目录

## 7.1 定义抽象数据类型

### 7.1.1 设计Sales\_data类

#### 使用改进的Sales\_data类

```cpp
#include <iostream>
#include <string>
#include "Sales_data.h"
using std::cin;
using std::endl;
using std::cerr;
int main()
{
    Sales_data total; //保存当前求和结果的变量
    if(read(cin,total)){ //读取第一笔交易
        Sales_data trans;// 保存下一条交易数据的变量
        while(read(cin,trans)){ //读入剩余的交易
            if(total.isbn() == trans.isbn()) //检查isbn
                total.combine(trans);//更新变量total当前的值
            else{
                print(cout,total) << endl;
            }
        }
        print(cout,total) << endl;
    }else{
        cerr << "No data?!" << endl;
    }
}
```

### 7.1.2 定义改进的Sales\_data类

```cpp
struct Sales_data
{
    //新成员：关于Sales_data对象的操作
    std::string isbn() const {return bookNo;}
    Sales_data& combine(const Sales_data&);
    double avg_price() const;// 成员函数用于返回售出书籍的平均价格
    //数据成员和2.6.1节相比没有改变
    std::string bookNo; //编号
    unsigned units_sold = 0; //销量
    double revenue = 0.0; //收入
};
Sales_data add(const Sales_data&,const Sales_data&);
std::ostream &print(std::ostream&,const Sales_data&);
std::istream &read(std::istream&,Sales_data&);
```

#### 定义成员函数

尽管所有成员必须在类的内部声明，但是成员函数体可以定义在类内也可以定义在类外。对于`Sales_data`类来说，`isbn`函数定义在了类内，而`combine`和`avg_price`定义在了类外。

#### 引入this

#### 引入const成员函数

`isbn`函数的另一个关键之处是紧随参数列表之后的const关键字，这里，const的作用是修改隐式this指针的类型。

默认情况下，this的类型是指向类类型非常量版本的常量指针。例如在`Sales_data`成员函数中，`this`的类型是`Sales_data *const`。尽管`this`是隐式的，但它仍然需要遵循初始化规则，意味着我们不能把`this`绑定到一个常量对象上。这一情况也就使得我们不能在一个常量对象上调用普通的成员函数。

如果`isbn`是一个普通函数而且`this`是一个普通指针参数，则我们应该把`this`声明成`const Sales_data *const`。毕竟，在`isbn`的函数体内不会改变 `this`所指的对象，所以把`this`设置为指向常量的指针有助于提高函数的灵活性。

然而，`this`是隐式的并且不会出现在参数列表中，所以在哪儿将`this`声明成指向常量的指针就成为我们必须面对的问题。`C++`语言的做法是允许把`const`关键字放在成员函数的参数列表之后，此时，紧跟在参数列表后面的`const`表示`this`是一个指向常量的指针。像这样使用`const`的成员函数被称作`常量成员函数（const member function）`。

#### 类作用域和成员函数

#### 在类的外部定义成员函数

```cpp
double Sales_data::avg_price() const{
    if(units_sold)
        return revenue/units_sold;
    else 
        return 0;
}
```

函数名`Sales_data::avg_price`使用作用域运算符来说明如下的事实：我们定义了一个名为`avg_price`的函数，并且该函数被声明在类`Sales_data`的作用域内。一旦编译器看到这个函数名，就能理解剩余的代码是位于类的作用域内的。因此，当`avg_price`使用`revenue`和`units_sold`时，实际上它隐式地使用了`Sales_data`的成员。

#### 定义一个返回this对象的函数

```cpp
Sales_data& Sales_data::combine(const Sales_data &rhs)
{
    units_sold + = rhs.units_sold; //把rhs的成员加到this对象的成员上
    revenue += rhs.revenue;
    return *this; //返回调用该函数的对象
}
```

### 7.1.3 定义类相关的非成员函数

#### 定义read和print函数

```cpp
std::istream &read(std::istream &is,Sales_data &item)
{
    double price = 0;
    is >> item.bookNo >> item.units_sold >> price;
    item.revenue = price * item.units_sold;
    return is;
}

std::ostream &print(std::ostream &os,const Sales_data &item)
{
    os << item.isbn() << " " << item.units_sold << " "
    << item.revenue << " " << item.avg_price();
    return os;
}
```

#### 定义add函数

```cpp
Sales_data add(const Sales_data &lhs,const Sales_data &rhs)
{
    Sales_data sum = lhs; //把lhs的数据成员拷贝给sum
    sum.combine(rhs); //把rhs的数据成员加到sum当中
    return sum;
}
```

### 7.1.4构造函数

#### 合成的默认构造函数

#### 某些类不能依赖于合成的默认构造函数

#### 定义Sale\_data的构造函数

```cpp
struct Sales_data
{
    //新增的构造函数
    Sales_data() = default;
    Sales_data(const std::string &s):bookNo(s){}
    Sales_data(const std::string &s,unsigned n,double p):
                bookNo(s),units_sold(n),revenue(p*n){}
    Sales_data(std::istream &);

    //新成员：关于Sales_data对象的操作
    std::string isbn() const {return bookNo;}
    Sales_data& combine(const Sales_data&);
    double avg_price() const;// 成员函数用于返回售出书籍的平均价格
    //数据成员和2.6.1节相比没有改变
    std::string bookNo; //编号
    unsigned units_sold = 0; //销量
    double revenue = 0.0; //收入
};
Sales_data add(const Sales_data&,const Sales_data&);
std::ostream &print(std::ostream&,const Sales_data&);
std::istream &read(std::istream&,Sales_data&);
```

#### = default 的含义

#### 构造函数初始值列表

接下来我们介绍类中定义的另外两个构造函数：

```cpp
Sales_data(const std::string &s):bookNo(s){}
Sales_data(const std::string &s,unsigned n,double p):
            bookNo(s),units_sold(n),revenue(p*n){}
```

这两个定义中出现了新的部分，即冒号以及冒号和花括号之间的代码，其中花括号定义了函数体。我们把新出现的部分称为`构造函数初始值列表（constructor initialize list）`，它负责为新创建的对象的一个或几个数据成员赋初值。构造函数初始值是成员名字的一个列表，每个名字后面紧跟括号括起来的成员初始值。不同成员的初始化通过逗号分割开来。

#### 在类的外部定义构造函数

```cpp
Sales_data::Sales_data(std::istream &is)
{
    read(is,*this);
}
```

### 7.1.5 拷贝、赋值和析构

#### 某些类不能依赖于合成的版本

## 7.2 访问控制与封装

```cpp
#include <iostream>
#include <string>
using std::cin; 
using std::cout; 
using std::endl; 
using std::string;
class Sales_data
{
public:
    Sales_data() = default;
    Sales_data(const std::string &s):bookNo(s){}
    Sales_data(const std::string &s,unsigned n,double p):
    bookNo(s),units_sold(n),revenue(p*n){}
    Sales_data(std::istream &);
    //新成员：关于Sales_data对象的操作
    std::string isbn() const {return bookNo;}
    Sales_data &combine(const Sales_data&);
private:
    double avg_price() const
        {return units_sold?revenue/units_sold:0;}
    std::string bookNo; //编号
    unsigned units_sold = 0; //销量
    double revenue = 0.0; //收入
};
Sales_data add(const Sales_data&,const Sales_data&);
std::ostream &print(std::ostream&,const Sales_data&);
std::istream &read(std::istream&,Sales_data&);
```

#### 使用class或struct关键字

`struct`和`class`的默认访问权限不太一样。

类可以在它的第一个访问说明符之前定义成员，对这种成员的访问权限依赖于类定义的方式。如果我们使用`struct`关键字，则定义在第一个访问说明符之前的成员是`public`的；相反，如果我们使用`class`关键字，则这些成员是`private`的。

出于统一编程风格的考虑，当我们希望定义的类的所有成员是`public`的时，使用`struct`；反之，如果希望成员是`private`的，使用`class`。

### 7.2.1 友元

既然`Sales_data`的数据成员是`private`的，我们的`read`、`print`和`add`函数也就无法正常编译了，这是因为尽管这几个函数是类的接口的一部分，但它们不是类的成员。

类可以允许其他类或者函数访问它的非公有成员，方法是令其他类或者函数称为它的`友元（friend）`。如果类想把一个函数作为它的友元，只需要增加一条以`friend`关键字开始的函数声明语句即可：

```cpp
class Sales_data
{
// 为Sales_data的非成员函数所做的友元声明
friend Sales_data add(const Sales_data&,const Sales_data&);
friend std::istream &read(std::istream&,Sales_data&);
friend std::ostream &print(std::ostream&,const Sales_data&);
public:
    Sales_data() = default;
    Sales_data(const std::string &s):bookNo(s){}
    Sales_data(const std::string &s,unsigned n,double p):
    bookNo(s),units_sold(n),revenue(p*n){}
    Sales_data(std::istream &);
    //新成员：关于Sales_data对象的操作
    std::string isbn() const {return bookNo;}
    Sales_data &combine(const Sales_data&);
private:
    double avg_price() const
        {return units_sold?revenue/units_sold:0;}
    std::string bookNo; //编号
    unsigned units_sold = 0; //销量
    double revenue = 0.0; //收入
};
Sales_data add(const Sales_data&,const Sales_data&);
std::ostream &print(std::ostream&,const Sales_data&);
std::istream &read(std::istream&,Sales_data&);
```

#### 友元的声明

## 7.3 类的其他特性

### 7.3.1 类成员再探

#### 定义一个类型成员

```cpp
#include <string.h>

class Screen {
public:
    typedef std::string::size_type pos;
    Screen() = default; //因为Screen有另一个构造函数，所以本函数是必需的
    Screen(pos ht, pos wd, char c):height(ht),width(wd),contents(ht*wd,c){}
    char get() const 
        {return contents[cursor];} //隐式内联
    inline char get(pos ht,pos wd) const;//显式内联
    Screen &move(pos r,pos c); //能在之后被设为内联
private:
    pos cursor = 0;
    pos height = 0, width = 0;
    std::string contents;
};
```

#### 令成员作为内联函数

在类中，常有一些规模较小的函数适合于被声明为内联函数。如我们之前所见的，定义在类内部的成员函数是自动`inline`的。因此，`Screen`的构造函数和返回光标所指字符的`get`函数默认是`inline`函数。

我们可以在类的内部把`inline`作为声明的一部分显式地声明成员函数，同样的，也能在类的外部用`inline`关键字修饰函数的定义：

```cpp
#include "Screen.h"

inline Screen &Screen::move(pos r,pos c) //可以在函数的定义处指定inline
{
    pos row = r * width; //计算行的位置
    cursor = row + c; //在行内将光标移动到指定的列
    return *this; //以左值的形式返回对象
}
char Screen::get(pos r,pos c) const //在类的内部声明成inline
{
    pos row = r * width; //计算行的位置
    return contents[row + c]; //返回给定列的字符
}
```

#### 重载成员函数

#### 可变数据成员

有时会发生这样一种情况，我们希望能修改类的某个数据成员，即使是在一个`const`成员函数内。可以通过在变量的声明中加入`mutable`关键字做到这一点。

一个`可变数据成员（mutable data member）`永远不会是`const`，即使它是`const`对象的成员。因此，一个`const`成员函数可以改变一个可变成员的值。举个🌰，我们将给`Screen`添加一个名为`access_ctr`的可变成员，通过它我们可以追踪每个`Screen`的成员函数被调用了多少次。

```cpp
class Screen {
public:
    void some_member() const;
private:
    mutable size_t access_ctr;// 即使在一个const对象内也能被修改
    //其他成员与之前的版本一致
};
void Screen::some_member() const
{
    ++access_ctr;
}
```

尽管`some_member`是一个`const`成员函数，她仍然能够改变`access_ctr`的值。该成员是个可变成员，因此任何成员函数，包括`const`函数在内都能改变它的值。

#### 类成员的初始值

```cpp
class Window_mgr {
private:
    //这个Window_mgr追踪的Screen
    //默认情况下，一个Window_mgr包含一个标准尺寸的空白Screen
    std::vector<Screen> screens{Screen(24,80,'')};
};
```

如我们之前所知的，类内初始值必须使用=的初始化形式（初始化Screen的数据成员时所用的）或者花括号括起来的直接初始化形式（初始化screens所用的）。

### 7.3.2 返回\*this的成员

#### 从const成员函数返回\*this

#### 基于const的重载

### 7.3.3 类类型

每个类定义了唯一的类型。对于两个类来说，即使它们的成员完全一样，这两个类也是两个不同的类型。

```cpp
struct First {
    int memi;
    int getMem();
};

struct Second {
    int memi;
    int getMem();
};
First obj1;
Second obj2 = obj1;
```

我们可以把类名作为类型的名字使用，从而直接指向类类型。或者，我们也可以把类名跟在关键字`class`或`struct`后面：

```cpp
Sales_data item1; //默认初始化Sales_data类型的对象
class Sales_data item1; //一条等价的声明
```

#### 类的声明

就像可以把函数的声明和定义分离开来一样，我们也能仅仅声明类而暂时不定义它：

```cpp
class Screen; //Screen类的声明
```

这种声明有时被称作`前向声明（forward declaration），`它向程序中引入了名字`Screen`并且指明`Screen`是一种类类型。对于类型`Screen`来说，在它声明之后定义之前是一个`不完全类型（incomplete type）`，也就是说，此时我们已知`Screen`是一个类类型，但是不清楚它到底包含哪些成员。

### 7.3.4 友元

类还可以把其他的类定义成友元，也可以把其他类的成员函数定义成友元。此外，友元函数能定义在类的内部，这样的函数是隐式内联的。

#### 类之间的友元关系

```cpp
class Screen {
    //Window_mgr的成员可以访问Screen类的私有部分
    friend class Window_mgr;
    // Screen类的剩余部分
}
```

```cpp
class Window_mgr {
public:
    using ScreenIndex = std::vector<Screen>::size_type;
    void clear(ScreenIndex);
private:
    std::vector<Screen> screens{Screen(24,80,'')};
};
void Window_mgr::clear(ScreenIndex i)
{
    //s是一个Screen的引用，指向我们想清空的那个屏幕
    Screen &s = screens[i];
    //将那个选定的Screen重置为空表
    s.contents = string(s.height*s.width,'');
}
```

必须要注意的一点事，友元关系不存在传递性。也就是说，如果`Window_mgr`有它自己的友元，则这些友元不能理所当然地具有访问`Screen`的特权。

#### 令成员函数作为友元

#### 函数重载和友元

## 7.4 类的作用域

```cpp
Screen::pos ht = 24, wd = 80; //使用Screen定义的pos类型
Screen scr(ht,wd,'')
Screen *p = &scr;
char c = scr.get(); //访问scr对象的get成员
c = p->get(); //访问p所指对象的get成员
```

#### 作用域和定义在类外部的成员

### 7.4.1 名字查找与类的作用域

#### 用于类成员声明的名字查找

#### 类型名要特殊处理

#### 成员定义中的普通块作用域的名字查找

#### 类作用域之后，在外围的作用域中查找

#### 在文件中名字的出现处对其进行解析

## 7.5 构造函数再探

### 7.5.1 构造函数初始值列表

#### 构造函数的初始值有时必不可少

#### 成员初始化顺序

#### 默认实参和构造函数

### 7.5.2 委托构造函数

### 7.5.3 默认构造函数的作用

### 7.5.4 隐式的类类型转换

### 7.5.5 聚合类

### 7.5.6 字面值常量类

## 7.6 类的静态成员

#### 声明静态成员

我们通过在成员的声明之前加上关键字`static`使得其与类关联在一起。和其他成员一样，静态成员可以是`public`的或`private`的。静态数据成员的类型可以是常量、引用、指针、类类型等。

举个🌰，我们定义一个类，用它表示银行的账户记录：

```cpp
class Account {
public:
    void calculate() { amount += amout * interestRate; }
    static double rate() { return interestRate; }
    static void rate(double);
private:
    std::string owner;
    double amount;
    static double interestRate;
    static double initRate();
}
```

#### 使用类的静态成员

我们使用作用域运算符直接访问静态成员：

```cpp
double r;
r = Account::rate(); //使用作用域运算符访问静态成员
```

虽然静态成员不属于类的某个对象，但是我们仍然可以使用类的对象、引用或者指针来访问静态成员：

```cpp
Account ac1;
Account *ac2 = &ac1;
//调用静态成员函数rate的等价形式
r = ac1.rate();  //通过Account的对象或引用
r = ac2.rate(); //通过指向Account对象的指针
```

成员函数不用通过作用域运算符就能直接使用静态成员：

```cpp
class Account {
public:
    void calculate() {amount += amount*interestRate;}
private:
    static double interestRate;
    //其他成员与之前的版本一致
}
```

#### 定义静态成员

#### 静态成员的类内初始化

#### 静态成员能用于某些场景，而普通成员不能

