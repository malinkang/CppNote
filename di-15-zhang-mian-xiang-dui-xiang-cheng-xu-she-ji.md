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
