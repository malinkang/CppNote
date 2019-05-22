# 第15章 面向对象程序设计

## 15.1 OOP：概述

`面向对象程序设计（object-orented programming)`的核心思想是数据抽象、继承和动态绑定。通过使用数据抽象，我们可以将类的接口与实现分离；使用继承，可以定义相似的类型并对其相似关系建模：使用动态绑定，可以在一定程度上忽略相似类型的区别，而以统一的方式使用它们的对象。

#### 继承

通过`继承（inheritance）`联系在一起的类构成一种层次关系。通常在层次关系的根部有一个`基类（base class）`，其他类则直接或间接地从基类继承而来，而这些继承得到的类称为`派生类（derived class）`。基类负责定义在层次关系中所有类共同拥有的成员，而派生类定义各自特有的成员。

在`C++`语言中，基类将类型相关的函数与派生类不做改变直接继承的函数区别对待。对于某些函数，基类希望它的派生类和各自定义适合自身的版本，此时基类就将这些函数声明成`虚函数（virtual function）`。

```cpp
class Quote{
public:
    std::string isbn() const;
    virtual double net_price(std::size_t n)const;
};
```

派生类必须通过使用`类派生列表（class derivation list）`明确指出它是从哪个基类继承而来的。类派生列表的形式是：首先是一个冒号，后面紧跟以逗号分隔的基类列表，其中每个基类前面可以有访问说明符：

```cpp
class Bulk_quote : public Quote{ //Bulk_quote集成了Quote
public:
    double net_price(std::size_t) const override;
};
```

因为`Bulk_quote`在它的派生列表中使用`public`关键字，因此我们完全可以把`Bulk_quote`的对象当成`Quote`的对象来使用。

派生类必须在其内部对所有重新定义的虚函数进行声明。派生类可以在这样的函数之前加上`virtual`关键字，但是并不是非得这么做。`C++11`新标准允许派生类显式地注明它将使用哪个成员函数改写基类的虚函数，具体措施是在该函数的形参列表之后增加一个`override`关键字。

#### 动态绑定

通过使用`动态绑定（dynamic binding）`，我们能用同一段代码分别处理`Quote`和`Bulk_quote`的对象。

```cpp
double print_total(ostream &os,const Quote &item,size_t n)
{
    double ret = item.net_price(n);
    os << "ISBN: " << item.isbn() << " # sold: " << n << "total due: " << ret << endl;
    return ret;
}
```

```cpp
//basic的类型是Quote；bulk的类型是Bulk_quote
print_total(cout,basic,20);
print_total(cout,bulk,20);
```

第一条调用句`Quote`对象传入`print_total`，因此当`print_total`调用`net_price`时，执行的是`Quote`的版本；在第二条调用语句中，实参的类型是`Bulk_quote`,因此执行的是`Bulk_quote`的版本。因为在上述过程中函数的运行版本由实参决定，即在运行时选择函数的版本，所以动态绑定有时又被称为`运行时绑定（run-time binding）`。

## 15.2 定义基类和派生类

### 15.2.1 定义基类

```cpp
class Quote{
public:
    Quote() = default;
    Quote(const std::string &book,double sales_price):bookNo(book),price(sales_price){}
    std::string isbn() const {return bookNo;} ;
    //返回给定数量的书籍的销售总额
    //派生类负责改写并使用不同的折扣计算算法
    virtual double net_price(std::size_t n)const{ return n*price;}
    virtual ~Quote() = default; //对析构函数进行动态绑定
private:
    std::string bookNo; //书籍的ISBN编号
protected:
    double price = 0.0; //代表普通状态下不打折的价格
};
```

#### 成员函数与继承

#### 访问控制与继承

### 15.2.2 定义派生类

```cpp
class Bulk_quote : public Quote{ //Bulk_quote集成了Quote
public:
    Bulk_quote() = default;
    Bulk_quote(const std::string&,double,std::size_t,double);
    double net_price(std::size_t) const override;
private:
    std::size_t min_qty = 0;
    double discount = 0.0; 
};
```

#### 派生类中的虚函数

#### 派生类对象及派生类向基类的类型转换

一个派生类对象包含多个组成部分：一个含有派生类自己定义的成员子对象，以及一个与该派生类继承的基类对应的子对象，如果有多个基类，那么这样的子对象也有多个。

因为在派生类对象中含有与其基类对应的组成部分，所以我们能把派生类的对象当成基类对象来使用，而且我们也能将基类的指针或引用绑定到派生类对象中的基类部分上。

```cpp
Quote item; //基类对象
Bulk_quote bulk;//派生类对象
Quote *p = &item;//p指向Quote对象
p = &bulk; //p指向bulk的Quote部分
Quote &r = bulk;//r绑定到bulk的Quote部分
```

这种转换通常称为`派生类到基类（derived-to-base）`的类型转换。

#### 派生类构造函数

```cpp
 Bulk_quote(const std::string& book,double p,std::size_t qty,double disc):
                Quote(book,p),min_qty(qty),discount(disc){}
```

#### 派生类使用基类的成员

```cpp
double Bulk_quote::net_price(size_t cnt)const
{
    if(cnt >= min_qty)
        return cnt * (1-discount) * price;
    else
        return cnt * price;
}
```

#### 继承与静态成员

如果基类定义了一个静态成员，则在整个继承体系中只存在该成员的唯一定义。不论从基类中派生出来多少个派生类，对于每个静态成员来说都只存在唯一的实例。

```cpp
class Base {
public:
    static void statmem();
};
class Derived:public Base{
    void f(const Derived&);
}
```

```cpp
void Derived::f(const Derived &derived_obj)
{
    Base::statmem(); //正确：Base定义了statmem
    Derived::statmem();//正确；Derived继承了statmem
    //正确：派生类的对象能访问基类的静态成员
    derived_obj.statmem(); //通过Derived对象访问
    statmem();//通过this对象访问
}
```

#### 派生类的声明

派生类的声明与其他类差别不大，声明中包含类名但是不包含它的派生列表。

```cpp
class Bulk_quote:public Quote;//错误：派生列表不能出现在这里
class Bulk_quote;//正确：声明派生类的正确方式
```

#### 被用作基类的类

如果我们想将某个累用作基类，则该类必须已经定义而非仅仅声明：

```cpp
class Quote; //声明但未定义
//错误：Quote必须被定义
class Bulk_quote:public Quote{...};
```

一个类是基类，同时它也可以是一个派生类：

```cpp
class Base {/*...*/};
class D1:public Base {/*...*/};
class D2:public D1{/*...*/};
```

在这个继承关系中，`Base`是`D1`的`直接基类（direct base)`，同时是`D2`的`间接基类（indirect base)`。

#### 防止继承的发生

有时我们会定义这样一种类，我们不希望其他类继承它，或者不想考虑它是否适合作为一个基类。为了实现这一目的，`C++11`新标准提供了一种防止继承发生的方法，即在类名后跟一个关键字`final`：

```cpp
class NoDerived final {/* */}; //NoDerived不能作为基类
class Base{/* */};
class Last final : Base {/* */};//Last不能作为基类
class Bad:NoDerived{/* */};//错误：Noderived是final的
class Bad2：Last{/* */};//错误：Last是final的
```

### 15.2.3 类型转换与继承

#### 静态类型和动态类型

当我们使用存在继承关系的类型时，必须将一个变量或其他表达式的`静态类型（static type）`与该表达式表示对象的`动态类型（dynamic type）`区分开来。表达式的静态类型在编译时总是已知的，它是变量声明时的类型或表达式生成的类型：动态类型则是变量或表达式表示的内存中的对象的类型。动态类型知道运行时才知。

#### 不存在从基类向派生类的隐式类型转换

#### 在对象之间不存在类型转换

## 15.3 虚函数

#### 对虚函数的调用可能在运行时才被解析

#### 派生类中的虚函数

#### final和override说明符

#### 虚函数与默认实参

#### 回避虚函数的机制

在某些情况下，我们希望对虚函数的调用不要进行动态绑定，而是强迫其执行虚函数的某个特定版本。使用作用域运算符可以实现这一目的。

```cpp
//强制调用基类中定义的函数版本而不管baseP的动态类型到底是什么
double undiscounted = baseP->Quote::net_price(42);
```

## 15.4 抽象基类

#### 纯虚函数

我们通过在函数体的位置书写=0就可以将一个虚函数说明为纯虚函数。其中，=0只能出现在类内部的虚函数声明语句处：

```cpp
class Disc_quote:public Quote {
public:
    Disc_quote() = default;
    Disc_quote(const std::string& book,double price,std::size_t qty,double disc):
                Quote(book,price),quantity(qty),discount(disc){}
    double net_price(std::size_t) const = 0;
protected:
    std::size_t quantity = 0;  //折扣适用的购买量
    double discount = 0.0; //表示折扣的小数值
};
```

#### 含有春旭函数的类是抽象基类

含有纯虚函数的类是`抽象基类（abstract base class）`。

#### 派生类构造函数只初始化它的直接基类

## 15.5 访问控制与继承

#### 受保护的成员

```cpp
class Base{
protected:
    int prot_mem; //protected成员
};
class Sneaky：public Base {
    friend void clobber(Sneaky&); //能访问Sneaky::prot_mem
    friend void clobber(Base&);//不能访问Base::prot_mem
    int j;//j默认是private
};
//正确：clobber能访问Sneaky对象的private和protected成员
void clobber(Sneaky &s){s.j == s.prot_mem = 0};
//错误：clobber不能访问Base的protected成员
void clobber(Base &b){b.prot_mem = 0;}
```

#### 公有、私有和受保护继承

```cpp
class Base {
public:
    void pub_mem(); //public成员
protected:
    int prot_men; //protected成员
private：
    char priv_mem; //private成员
};
struct Pub_Derv:public Base {
    //正确：派生类能访问protected成员
    int f() {return prot_mem;}
    //错误：private成员对于派生类来说是不可访问的
    char g() {return priv_mem;}
}
struct Priv_Derv:private Base{
    //private不影响派生类的访问权限
    int f() {return prot_mem;}
}
```

派生访问说明符对于派生类的成员能否访问其直接基类的成员没什么影响。

派生访问说明符的目的是控制派生类用户（包括派生类的派生类在内）对于基类成员的访问权限：

```cpp
Pub_Derv d1; //继承自Base的成员是public的
Priv_Derv d2;//继承自Base的成员是private的
d1.pub_mem();//正确：pub_mem在派生类中是public的
d2.pub_mem();//错误：pub_mem在派生类中是private的
```

派生访问说明符还可以继承自派生类的新类的访问权限：

```cpp
struct Derived_from_Public : public Pub_Derv{
    //正确：Base::prot_mem 在Pub_Derv中仍然是protected的
    int use_base() { return prot_mem;}
}
struct Derived_from_Private : public Priv_Derv{
    //错误：Base::prot_mem 在Priv_Derv中是private的
    int use_base() {return prot_mem;}
}
```

#### 派生类向基类转换的可访问性

#### 友元与继承

就像友元关系不能传递一样，友元关系同样也不能继承。

```cpp
class Base{
    //添加friend声明
    friend class Pal; //Pal在访问Base的派生类时不具有特殊性
};
class Pal {
public:
    int f(Base b) {return b.prot_mem;}//正确：Pal是Base的友元
    int f2(Sneaky s) {return s.j;} //错误：Pal不是Sneaky的友元
    int f3(Sneaky s) {return s.prot_mem;}//正确：Pal是Base的友元 
}
```

当一个类将另一个类声明为友元时，这种友元关系只对做出声明的类有效。对于原来那个类来说，其友元的基类或者派生类不具有特殊的访问能力：

```cpp
//D2对Base的protected和private成员不具有特殊的访问能力
class D2:public Pal {
public:
    int mem(Base b)
        {retuurn b.prot_mem;} //友元关系不能继承
}
```

#### 改变个别成员的可访问性

有时我们需要改变派生类继承的某个名字的访问级别，通过使用`using`声明可以达到这一目的：

#### 默认的继承保护级别

## 15.6 继承中的类作用域

#### 在编译时进行名字查找

#### 名字冲突与继承

和其他作用域一样，派生类不能重新定义在其直接基类或间接基类中的名字，此时定义在内层作用域的名字将隐藏定义在外层作用域的名字：

```cpp
struct Base {
    Base():mem(0){}
protected:
    int mem;
};
struct Derived : Base {
    Derived(int i): mem(i){} 
    int get_mem() {return mem;}
protected:
    int mem; //隐藏基类中的mem
};

Derived d(42);
cout << d.get_mem() << endl;//打印42
```

#### 通过作用域运算符来使用隐藏的成员

```cpp
struct Derived:Base{
    int get_base_mem() { return Base::mem;}
};
```

#### 一如往常，名字查找先于类型检查

#### 虚函数与作用域

#### 通过基类调用隐藏的虚函数

#### 覆盖重载的函数

## 15.7 构造函数与拷贝控制

### 15.7.1 虚析构函数

#### 虚析构函数将组织合成移动操作

### 15.7.2 合成拷贝控制与继承

#### 派生类中删除的拷贝控制与基类的关系

#### 移动操作与继承

### 15.7.3 派生类的拷贝控制成员

#### 定义派生类的拷贝或移动构造函数

### 15.7.4 继承的构造函数

## 15.8 容器与继承

## 15.9 文本查询程序再探

