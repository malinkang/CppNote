# 第12章 动态内存

除了自动和`static`对象外，`C++`还支持动态分配对象。动态分配的对象的生存期与它们在哪里创建是无关的，只有当显式地被释放时，这些对象才会销毁。

标准库定义了两个智能指针类型来管理动态分配的对象，当一个对象应该被释放时，指向它的智能指针可以确保自动地释放它。

我们的程序到目前为止只使用过静态内存和栈内存。静态内存用来保存局部`static`对象、类`static`数据成员以及定义在任何函数之外的变量。栈内存用来保存定义在函数内的非`static`对象。分配在静态或栈内存中的对象由编译器自动创建和销毁。对于栈对象，仅在其定义的程序块运行时才存在；`static`对象在使用之前分配，在程序结束时销毁。

除了静态内存和栈内存，每个程序还拥有一个内存池。这部分内存被称作**自由空间（free store）**或**堆（heap）**。程序用堆来存储**动态分配（dynamically allocate）**的对象。

## 12.1 动态内存与智能指针

在`C++`中，动态内存的管理是通过一对运算符来完成的：**new**，在动态内存中为对象分配空间并返回一个指向该对象的指针，我们可以选择对对象进行初始化；**delete**，接受一个动态对象的指针，销毁该对象，并释放与之关联的内存。

动态内存的使用很容易出问题，为了更容易地使用动态内存，新的标准库提供了两种**智能指针（smart pointer）**类型来管理动态对象。只能指针的行为类似常规指针，重要的区别是它负责自动释放所指向的对象。新标准库提供的这两种智能指针的区别在于管理底层指针的方式：**share\_ptr**允许多个指针指向同一个对象；**unique\_ptr**则“独占”所指向的对象。标准库还定义了一个名为`weak_ptr`的伴随类，它是一种弱引用，指向`shared_ptr`所管理的对象。这三种类型都定义在`memory`头文件中。

### 12.1.1 share\_ptr类

```cpp
shared_ptr<string> p1;
shared_ptr<list<int>> p2;
// 如果p1不为空，检查它是否指向一个空string
if( p1 && p1 -> empty())
    *p1 = "hi";
```

#### make\_shared函数

最安全的分配和使用动态内存的方法是调用一个名为`make_shared`的标准库函数。此函数在动态内存中分配一个对象并初始化它，返回指向此对象的`shared_ptr`。

```cpp
//指向一个值为42的int的shared_ptr
shared_ptr<int> p3 = make_shared<int>(42);
//p4指向一个值为"9999999999"的string
shared_ptr<string> p4 = make_shared<string>(10,'9');
//p5指向一个值初始化的int，即，值为0
shared_ptr<int> p5 = make_shared<int>();
```

我们通常用`auto`定义一个对象来保存`make_shared`的结果，这种方式较为简单：

```cpp
//p6指向一个动态分配的空vector<string>
auto p6 = make_shared<vector<string>>();
```

#### shared\_ptr的拷贝和赋值

当进行拷贝或赋值操作时，每个`shared_ptr`都会记录有多少个其他`shared_ptr`指向相同的对象：

```cpp
auto p = make_shared<int>(42); //p指向的对象只有p一个引用者
auto q(p); //p和q指向相同对象，此对象有两个引用者
```

我们可以认为每个`shared_ptr`都有一个关联的计数器，通常称其为**引用计数器（reference count）**。

```cpp
auto r = make_shared<int>(42);
r = q; 
//给r赋值，令它指向另一个地址
//递增q指向的对象的引用计数
//递减r原来指向的对象的引用计数
//r原来指向的对象已没有引用者，会自动释放
```

#### shared\_ptr自动销毁所管理的对象

当指向一个对象的最后一个`shared_ptr`被销毁时，`shared_ptr`类会自动销毁此对象。它是通过另一个特殊的成员函数`析构函数（destructor）`完成销毁工作的。

`shared_ptr`的析构函数会递减它所指向的对象的引用计数。如果引用计数变为0,`shared_ptr`的析构函数就会销毁对象，并释放它占用的内存。

#### shared\_ptr还会自动释放相关联的内存

### 12.1.2 直接管理内存

#### 使用new动态分配和初始化对象

在自由空间分配的内存是无名的，因此`new`无法为其分配的对象命名，而是返回一个指向该对象的指针：

```cpp
int *pi = new int; // pi指向一个动态分配的、未初始化的无名对象
```

默认情况下，动态分配的对象是**默认初始化**的，这意味着内置类型或组合类型的对象的值将是未定义的，而类类型对象将用默认构造函数进行初始化：

```cpp
string *ps = new string;// 初始化为空string
```

我们可以使用直接初始化方式来初始化一个动态分配的对象。我们可以使用传统的构造方式（使用圆括号），在新标准下，也可以使用列表初始化（使用花括号）：

```cpp
int *pi = new int(1024); //pi指向的对象的值为1024
string *ps = new string(10,'9');// *ps为"9999999999"
//vector有10个元素，值依次从0到9
vector<int> *pv = new vector<int>{0,1,2,3,4,5,6,7,8,9};
```

也可以对动态分配的对象进行**值初始化**，只需在类型名之后跟一对空括号即可：

```cpp
string *ps1 = new string; //默认初始化为空string
string *ps = new string(); //值初始化为空string
int *pi1 = new int; //默认初始化；*pi1的值未定义
int *pi2 = new int(); //值初始化为0；*pi2为0
```

#### 动态分配的const对象

#### 内存耗尽

#### 释放动态内存

#### 指针值和delete

我们传递给delete的指针必须指向动态分配的内存，或者是一个空指针。释放一块并非new分配的内存，或者将相同的指针值释放多次，其行为是未定义的：

```cpp
int i, *pi1 = &i, *pi2 = nullptr;
double *pd = new double(33), *pd2 = pd;
// delete i; // 错误：i不是一个指针
// delete pi1; // 未定义：pi1指向一个局部变量
delete pd;// 正确
// delete pd2; // 未定义：pd2指向的内存已经被释放了
delete pi2; // 正确：释放一个空指针总是没有错误的
```

#### 动态对象的生存期知道被释放时为止

### 12.1.3 shared\_pter和new结合使用

我们可以用`new`返回的指针来初始化智能指针：

```cpp
shared_ptr<double> p1; //shared_ptr可以指向一个double
shared_ptr<int> p2(new int(42)); //p2指向一个值为42的int
```

接受指针参数的智能指针构造函数是`explicit`的。因此我们不能将一个内置指针隐式转换为一个智能指针，必须使用直接初始化形式来初始化一个智能指针：

### 12.1.4 智能指针和异常

使用智能指针，即使程序块过早结束，智能指针类也能保存在内存不再需要时将其释放：

```cpp
void f()
{
    shared_ptr<int> sp(new int(42));//分配一个新对象
    //这段代码抛出一个异常，且在f中未被捕获
    //在函数结束时shared_ptr自动释放内存
}
```

与之相对的，当发生异常时，我们直接管理的内存是不会自动释放的。如果使用内置指针管理内存，且在`new`之后在对应的`delete`之前发生了异常，则内存不会被释放：

```cpp
void f()
{
    int *ip = new int(42);
    //这段代码抛出一个异常，且在f中未被捕获
    delete ip;//在退出之前释放内存。
}
```

#### 智能指针和哑类

#### 使用我们自己的释放操作

### 12.1.5 unique\_ptr

```cpp
unique_ptr<double> p1;//可以指向一个double的unique_ptr
unique_ptr<int> p2(new int(42)); //p2指向一个值为42的int
```

由于一个`unique_ptr`拥有它指向的对象，因此`unique_ptr`不支持普通的拷贝或赋值操作。

```cpp
unique_ptr<string> p1(new string("Stegosaurus"));
unique_ptr<string> p2(p1); //错误：unique_ptr不支持拷贝
unique_ptr<string> p3; 
p3 = p2; //错误：unique_ptr不支持赋值
```

### 12.1.6 weak\_pter

## 12.2 动态数组

### 12.2.1 new和数组

#### 分配一个数组会得到一个元素类型的指针

#### 初始化动态分配对象的数组

#### 动态分配一个空数组是合法的

#### 释放动态数组

#### 智能指针和动态数组

### 12.2.2 allocator类

#### allocator分配未构造的内存

#### 拷贝和填充未初始化内存的算法

