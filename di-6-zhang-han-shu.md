# 第6章 函数

## 6.1 函数基础

```cpp
int fact(int val)
{
    int ret = 1;
    while (val >1)
        ret *=val--;
    return ret;
}
```

```cpp
int main()
{   
    int j = fact(5);
    cout << "5! is " << j << endl; //5! is 120
    return 0;
}
```

函数的形参列表可以为空，但是不能省略。要想定义一个不带形参的函数，最常用的办法是书写一个空的形参列表。不过为了与C语言兼容，也可以使用关键字void表示函数没有形参：

```cpp
void f1(){/*...*/}
void f2(void){/*...*/}
```

### 6.1.1 局部对象

#### 自动对象

#### 局部静态对象

```cpp
size_t count_calls()
{
    static size_t ctr = 0;//调用结束后，这个值仍然有效
    return ++ctr;
}
int main()
{   
    for(size_t i = 0;i!=10;++i)
        cout << count_calls() <<endl;
    return 0;
}
```

### 6.1.2 函数声明

#### 在头文件中进行函数声明

### 6.1.3 分离式编译

#### 编译和链接多个源文件

fact函数的定义位于一个名为`fact.cc`的文件中，它的声明位于名为`Chapter6.h`的头文件中。我们在名为`factMain.cc`的文件中创建main函数，main函数将调用`fact`函数。

Chapter6.h

```cpp
int fact(int val);
```

fact.cc

```cpp
#include "Chapter6.h"

int fact(int val)
{
    int ret = 1;
    while (val >1)
        ret *=val--;
    return ret;
}
```

factMain.cc

```cpp
#include "Chapter6.h"
#include <iostream>
using std::cout;
using std::endl;
int main()
{   
    int j = fact(5);
    cout << "5! is " << j << endl; //5! is 120
    return 0;
}
```

编译的过程如下所示：

```text
clang++ -std=c++11 factMain.cc fact.cc -o main
```

## 6.2 参数传递

### 6.2.1 传值参数

当初始化一个非引用类型的变量时，初始值被拷贝给变量。此时，对变量的改动不会影响初始值：

```cpp
int n = 0;
int i = n;
i = 42;// i的值改变；n的值不变
```

#### 指针形参

指针的行为和其他非引用类型一样。当执行指针拷贝操作时，拷贝的是指针的值。拷贝之后，两个指针是不同的指针。因为指针使我们可以间接地访问它所指的对象，所以通过指针可以修改它所指对象的值：

```cpp
int n = 0, i = 42;
int *p = &n, *q = &i; 
*p = 42; //n的值改变；p不变
p = q; //p现在指向了i；但是i和n的值都不变
```

```cpp
#include <iostream>
using std::cout;
using std::cin;
using std::endl;
void reset(int *ip)
{
    *ip = 0;
    ip = 0;
}
int main()
{   
    int i = 42;
    int *p = &i;
    cout << "p = " << p << endl;
    reset(p);
    cout << "p = " << p << endl;
    cout << "i = " << i << endl;
}
/*
p = 0x7ffee73da95c
p = 0x7ffee73da95c
i = 0
*/
```

### 6.2.2 传引用参数

#### 使用引用避免拷贝

#### 使用引用形参返回额外信息

### 6.2.3 const形参和实参

#### 指针或引用形参与const

#### 尽量使用常量引用

### 6.2.4 数组形参

#### 使用标记指定数组长度

#### 使用标准库规范

#### 显式传递一个表示数组大小的形参

#### 数组形参和const

#### 数组引用形参

#### 传递多维数组

### 6.2.5 main：处理命令行选项

### 6.2.6 含有可变形参的函数

#### 省略符形参

## 6.3 返回类型和return语句

### 6.3.1 无返回值函数

### 6.3.2 有返回值函数

#### 值是如何被返回的

#### 不要返回局部对象的引用或指针

函数完成后，它所占用的存储空间也随之被释放掉。因此，函数终止意味着局部变量的引用将指向不再有效的内存区域：

```cpp
const string manip()
{
    string ret;
    if(!ret.empty())
        return ret;
    else 
        return "Empty";
}
```

#### 返回类类型的函数和调用运算符

#### 引用返回左值

```cpp
#include <iostream>
#include <string>
using std::string;
using std::cout;
using std::endl;

char &get_val(string &str,string::size_type ix)
{
    return str[ix];
}
int main()
{
    string s("a value"); 
    cout << s << endl; //a value
    get_val(s,0) = 'A';
    cout << s << endl; //A value
}
```

#### 列表初始化返回值

`C++11`新标准规定，函数可以返回花括号包围的值的列表。类似于其他返回结果，此处的列表也用来对表示函数返回的临时量进行初始化。如果列表为空，临时量执行初始化；否则返回的值由函数返回类型决定。

```cpp
#include <iostream>
#include <string>
#include <vector>
using std::string;
using std::cout;
using std::endl;
using std::vector;

vector<string> process(string expected,string actual)
{
    if(expected.empty())
        return {};
    else if(expected == actual)
        return {"functionX","okay"};
    else 
        return {"functionX",expected,actual};
}
int main()
{
    for(auto s:process("a","b"))
        cout << s << endl;
}
/*
functionX
a
b
*/
```

#### 主函数main的返回值

如果函数的返回类型不是`void`，那么它必须返回一个值，但是这条规则有个例外：我们允许`main`函数没有`return`语句直接结束。如果控制到达了`main`函数的结尾处而且没有`return`语句，编译器将隐式地插入一条返回0的`return`语句。

`main`函数的返回值可以看做是状态指示器，返回0表示执行成功，返回其他值表示执行失败，其中非0值的具体含义依机器而定。为了使返回值与机器无关，`cstdlib`头文件定义了两个预处理变量，我们可以使用这两个变量分别表示成功与失败：

```cpp
#include <cstdlib>

int main()
{
    return EXIT_SUCCESS;

}
```

#### 递归

```cpp
#include <iostream>
#include <string>
#include <cstdlib>
using std::cout;
using std::endl;
int factorial(int val)
{
    if(val > 1)
        return factorial(val - 1) * val;
    return 1;
}
int main()
{
    cout <<factorial(5) << endl; // 120
}
```

### 6.3.3 返回数组指针

因为数组不能被拷贝，所以函数不能返回数组。不过，函数可以返回数组的指针或引用。虽然从语法上来说，要想定义一个返回数组的指针或引用的函数比较烦琐，但是有一些方法可以简化这一任务，其中最直接的方法是使用类型别名。

```cpp
typedef int arrT[10]; // arrT是一个类型别名，它表示的类型是含有10个整数的数组
using arrT = int[10]; // arrT的等价声明
arrT* func(int i); //func返回一个指向含有10个整数的数组的指针
```

#### 声明一个返回数组指针的函数

#### 使用尾置返回类型

#### 使用decltype

## 6.4 函数重载

#### 定义重载函数

#### 判断两个形参的类型是否相异

#### 重载和const形参

#### const\_cast和重载

#### 调用重载的函数

### 6.4.1 重载与作用域

## 6.5 特殊用途语言特性

### 6.5.1 默认实参

#### 使用默认实参调用函数

#### 默认实参声明

#### 默认实参初始化

### 6.5.2 内联函数和constexpr函数

#### 内联函数可避免调用的开销

#### constexpr函数

### 6.5.3 调试帮助

#### assert预处理宏

#### NDEBUG预处理变量

## 6.6 函数匹配

#### 确定候选函数和可行函数

#### 寻找最佳匹配

#### 含有多个形参的函数匹配

### 6.6.1 实参类型转换

#### 函数匹配和const实参

## 6.7 函数指针

#### 使用函数指针

#### 重载函数指针

#### 函数指针形参

#### 返回指向函数的指针

#### 将auto和decltype用于函数指针类型

