---
title: 《C++ Primer》读书笔记 第9章 顺序容器
date: '2016-05-23T15:37:30.000Z'
tags: null
---

# 第9章 顺序容器

## 目录

## 9.1 顺序容器概述

![image.png](https://upload-images.jianshu.io/upload_images/25383-224c085f3c81ed7e.png?imageMogr2/auto-orient/strip|imageView2/2/w/1240)

#### 确定使用哪种顺序容器

## 9.2 容器库概览

一般来说，每个容器都定义在一个头文件中，文件名与类型名相同。即，`deque`定义在头文件`deque`中，`list`定义在头文件`list`中，以此类推。容器均定义为模板类。

#### 对容器可以保存的元素类型的限制

### 9.2.1 迭代器

#### 迭代器范围

#### 使用左闭合范围蕴含的编程假定

### 9.2.2 容器类型成员

### 9.2.3 begin和end成员

### 9.2.4 容器定义和初始化

![image.png](https://upload-images.jianshu.io/upload_images/25383-326156b551f03cfd.png?imageMogr2/auto-orient/strip|imageView2/2/w/1240)

#### 将一个容器初始化为另一个容器的拷贝

为了创建一个容器为另一个容器的拷贝，两个容器的类型及其元素类型必须匹配。不过，当传递迭代器参数来拷贝一个范围时，就不要求容器类型是相同的了。而且，新容器和原容器中的元素类型也可以不同，只要能将要拷贝的元素转换为要初始化的容器的元素类型即可。

```cpp
#include <iostream>
#include <string>
#include <list>
#include <vector>
#include <deque>
#include <forward_list>
using std::cout;
using std::endl;
using std::string;
using std::list;
using std::vector;
using std::deque;
using std::forward_list;

int main(int argc,char *argv[])
{   
    list<string> authors = {"Milton","Shakespeare","Austen"};
    vector<const char*> articles = {"a","an","the"};
    list<string> list2(authors); //正确：类型匹配
    //deque<string> authList(authors);//错误：容器类型不匹配
    // vector<string> words(articles);//错误

    //正确：可以将const char*元素转换为string
    forward_list<string> words(articles.begin(),articles.end());

    for(auto it = words.begin(); it != words.end();++it)
        cout << *it << endl; 

}
/*
a
an
the
*/
```

#### 列表初始化

#### 与顺序容器大小相关的构造函数

```cpp
vector<int> ivec(10,-1); // 10个int元素，每个都初始化为-1
list<string> svec(10,"hi!"); //10个strings;每个都初始化为"hi!"
forward_list<int> ivec(10); //10个元素，每个都初始化为0
deque<string> svec(10); //10个元素，每个都是空string
```

#### 标准库array具有固定大小

与内置数组一样，标准库`array`的大小也是类型的一部分。当定义一个`array`时，除了指定元素类型，还要指定容器大小：

```cpp
array<int,42> //类型为：保存42个int的数组
array<string,10> //类型为：保存10个string的数组
```

```cpp
array<int,10> ia1; // 10个默认初始化的int
array<int,10> ia2 = {0,1,2,3,4,5,6,7,8,9}; //列表初始化
array<int,10> ia3 = {42};// ia3[0]为42，剩余元素为0
```

值得注意的是，虽然我们不能对内置数组类型进行拷贝或对象赋值操作，但`array`并无此限制：

```cpp
int digs[10] = {0,1,2,3,4,5,6,7,8,9};
// int cpy[10] = digs; //错误：内置数组不支持拷贝或赋值
array<int,10> digits = {0,1,2,3,4,5,6,7,9};
array<int,10> copy = digits; //正确：只要数组类型匹配即合法
```

### 9.2.5 赋值和swap

与内置数组不同，标准库`array`类型允许赋值。赋值号左右两边的运算对象必须具有相同的类型：

```cpp
array<int,10> a1 = {0,1,2,3,4,5,6,7,8,9};
array<int,10> a2 = {0};
a1 = a2;
a2 = {0}; //书中写的说会报错，但是实际运行并没有异常
```

#### 使用assign

顺序容器（`array`除外）还定义了一个名为`assign`的成员，允许我们从一个不同但相容的类型赋值，或者从容器的一个子序列赋值。`assign`操作用参数所指定的元素替换左边容器中的所有元素。例如，我们可以用`assign`实现将一个`vector`中的一段`char*`值赋予一个`list`中的`string`:

```cpp
list<string> names;
vector<const char*> oldstyle;
// names = oldstyle; 错误：容器类型不匹配
names.assign(oldstyle.cbegin(),oldstyle.cend());
```

这段代码中对`assign`的调用将`names`中的元素替代为迭代器指定的范围中的元素的拷贝。`assign`的参数决定了容器中将由多少个元素以及它们的值都是什么。

`assign`的第二个版本接受一个整数值和一个元素值。它用指定数目且具有相同给定值的元素替换容器中原有的元素：

```cpp
list<string> slist1(1);// 1个元素，为空string
slist1.assign(10,"Hiya!"); // 10个元素，每个都是"Hiya!"
```

#### 使用swap

```cpp
vector<string> svec1(10);
vector<string> svec2(24);
swap(svec1,svec2);
```

调用`swap`后，`svec1`将包含24个`string`元素，`svec2`将包含10个`string`。除`array`外，交换两个容器内容的操作保证会很快，元素本身并未交换，`swap`只是交换了两个容器的内部数据结构。

### 9.2.7 关系运算符

```cpp
vector<int> v1 = {1,3,5,7,9,12};
vector<int> v2 = {1,3,9};
vector<int> v3 = {1,3,5,7};
vector<int> v4 = {1,3,5,7,9,12};
v1 < v2; // true v1和v2在元素[2]处不同：v1[2]小于等于v2[2]
v1 < v3; // false 所有元素都相等，但v3中元素数目更少
v1 == v4; // true
v1 == v2; //false v2元素数目比v1少
```

#### 容器的关系运算符使用元素的关系运算符完成比较

## 9.3 顺序容器操作

### 9.3.1 向顺序容器添加元素

![image.png](https://upload-images.jianshu.io/upload_images/25383-5f105285dccfc594.png?imageMogr2/auto-orient/strip|imageView2/2/w/1240)

#### 使用push\_back

#### 使用push\_font

#### 在容器中的特定位置添加元素

`insert`函数将元素插入到迭代器所指的元素之前的位置。

#### 插入范围内元素

#### 使用insert的返回值

#### 使用emplace操作

当调用`push`或`insert`成员函数时，我们将元素类型的对象传递给它们，这些对象被拷贝到容器中。而当我们调用一个`emplace`成员函数时，则是将参数传递给元素类型的构造函数。`emplace`成员使用这些参数在容器管理的内存空间中直接构造元素。例如，假定`c`保存`Sales_data`元素。

```cpp
//在c的末尾构造一个Sales_data对象
//使用三个参数的Sales_data构造函数
c.emplace_back("978-0590353403",25,15.99);

c.push_back(Sales_data("978-0590353403",25,15.99))

c.emplace_back();//使用Sales_data的默认构造函数

c.emplace(iter,"999-999999999"); //使用Sales_data(string)

c.emplace_front("978-0590353403",25,15.99); //
```

### 9.3.2 访问元素

```cpp
if(!c.empty()){
    //val和val2是c中第一个元素值的拷贝
    auto val = *c.begin(),val2 = c.front();
    //val3和val4是c中最后一个元素值的拷贝
    auto last = c.end();
    auto val3 = *(--last); //不能递减forward_list迭代器
    auto val4 = c.back(); //forward_list不支持
}
```

#### 访问成员函数返回的是引用

```cpp
if(!c.empty()){
    c.front() = 42; //将42赋予c中的第一个元素
    auto &v = c.back(); //获得指向最后一个元素的引用
    v = 1024; //改变c中的元素
    auto v2 = c.back(); //v2不是一个引用，它是c.back()的一个拷贝
    v2 = 0; //未改变c中的元素
}
```

#### 下标操作和安全的随机访问

如果我们希望确保下标是合法的，可以使用at成员函数。at成员函数类似下标运算符，但如果下标越界，at会抛出一个`auto_of_range`异常。

```cpp
vector<string> svec; //空vector
cout << svec[0]; //
cout << svec.at(0); //抛出一个 auto_of_range异常
```

### 9.3.3 删除元素

![image.png](https://upload-images.jianshu.io/upload_images/25383-7313e31b285cc1e1.png?imageMogr2/auto-orient/strip|imageView2/2/w/1240)

#### pop\_front和pop\_back成员函数

#### 从容器内部删除一个元素

```cpp
list<int> lst = {0,1,2,3,4,5,6,7,8,9};
auto it = lst.begin();
while (it != lst.end())
    if(*it % 2)
        it = lst.erase(it);
    else
        ++it;
for(auto i : lst)
    cout << i << endl;
```

#### 删除多个元素

接受一对迭代器的`erase`版本允许我们删除一个范围内的元素：

```cpp
//删除两个迭代器表示的范围内的元素
//返回指向最后一个被删元素之后位置的迭代器
elem1 = slist1.erase(elem1,elem2); //调用后，elem1 == elem2
```

为了删除一个容器中的所有元素，我们既可以调用clear，也可以用begin和end获得的迭代器作为参数调用erase:

```cpp
slist.clear(); //删除容器中所有元素
slist.erase(slist.begin(),slist.end()); //等价调用
```

#### 9.3.4 特殊的forward\_list操作

`forward_list`是单向链表。在一个单向链表中，没有简单的方法来获取一个元素的前驱。出于这个原因，在一个`forward_list`中添加或删除元素的操作时通过改变给定元素之后的元素来完成的。这样，我们总是可以访问到被添加或删除操作所影响的元素。

由于这些操作与其他容器上的操作的实现方式不同，`forward_list`并未定义`insert`、`emplace`和`erase`，而是定义了名为`insert_after`、`emplace_after`和`erase_after`的操作。`forward_list`也定义了`before_begin`，它返回一个`首前（off-the-beginning）`迭代器。

![image.png](https://upload-images.jianshu.io/upload_images/25383-780be3e84321b423.png?imageMogr2/auto-orient/strip|imageView2/2/w/1240)

```cpp
forward_list<int> flst = {0,1,2,3,4,5,6,7,8,9};
auto prev = flst.before_begin(); //表示flst的首前元素
auto curr = flst.begin(); //表示flst中的第一个元素
while(curr != flst.end()){ //仍有元素要处理
    if(*curr %2) //若元素为奇数
        curr = flst.erase_after(prev); //删除它并移动curr
    else{
        prev = curr; //移动迭代器curr
        ++curr; //curr之前的元素
    }
}
for(auto i : flst)
    cout << i << endl;
```

### 9.3.5 改变容器大小

```cpp
list<int> ilist(10,42);//10个int：每个的值都是42
ilist.resize(15); //将5个值为0的元素添加到ilist的末尾
ilist.resize(25,-1);//将10个值为-1的元素添加到ilist的末尾
ilist.resize(5);//从ilist末尾删除20个元素
```

### 9.3.6 容器操作可能使迭代器失效

#### 编写改变容器的循环程序

添加/删除`vector`、`string`或`deque`元素的循环程序必须考虑迭代器、引用和指针可能失效的问题。程序必须保证每个循环步中都更新迭代器、引用或指针。如果循环中调用的是`insert`或`erase`，那么更新迭代器很容易。这些操作都返回迭代器，我们可以用来更新：

```cpp
//删除偶数元素，复制每个奇数元素
vector<int> vi = {0,1,2,3,4,5,6,7,8,9};
auto iter = vi.begin();  //调用begin而不是cbegin，因为我们要改变vi
while (iter != vi.end()){ 
    if(*iter%2) {
        iter = vi.insert(iter,*iter); // 复制当前元素
        iter += 2; //向前移动迭代器，跳过当前元素以及插入到它之前的元素
    }else{
        iter = vi.erase(iter); //删除偶数元素
        //不应向前移动迭代器，iter指向我们删除的元素之后的元素
    }
}
```

#### 不要保存end返回的迭代器

当我们添加/删除`vector`或`string`的元素后，或在`deque`中首元素之外任何位置添加/删除元素后，原来`end`返回的迭代器总是会失效。因此，添加或删除元素的循环程序必须反复调用`end`，而不能再循环之前保存`end`返回的迭代器，一直当作容器末尾使用。

## 9.4 vector对象是如何增长的

#### 管理容量的成员函数

![image.png](https://upload-images.jianshu.io/upload_images/25383-baf686e54ffdd792.png?imageMogr2/auto-orient/strip|imageView2/2/w/1240)

#### capacity和size

```cpp
vector<int> ivec;
cout << "ivec: size: " << ivec.size()
    << " capacity: " << ivec.capacity() << endl;

//向ivec添加24个元素

for (vector<int>::size_type ix = 0;ix !=24; ++ix)
    ivec.push_back(ix);

cout << "ivec: size: " << ivec.size()
    << " capacity: " << ivec.capacity() << endl;


ivec.reserve(50); //将capacity至少设定为50，可能会更大

cout << "ivec: size: " << ivec.size()
    << " capacity: " << ivec.capacity() << endl;
```

输出

```bash
ivec: size: 0 capacity: 0
ivec: size: 24 capacity: 32
ivec: size: 24 capacity: 50
```

```cpp
//添加元素用光多余容量
while(ivec.size() != ivec.capacity())
    ivec.push_back(0);

cout << "ivec: size: " << ivec.size()
    << " capacity: " << ivec.capacity() << endl;
```

输出

```bash
ivec: size: 50 capacity: 50
```

我们现在再添加一个新元素，`vector`就不得不重新分配空间：

```cpp
ivec.push_back(42); //在添加一个元素

cout << "ivec: size: " << ivec.size()
    << " capacity: " << ivec.capacity() << endl;
```

输出

```cpp
ivec: size: 51 capacity: 100
```

这表明`vector`的实现采用的策略似乎是在每次需要分配新内存空间时将当前容量翻倍。

可以调用`shrink_to_fit`来要求`vector`将超出当前大小的多余内存退回给系统：

```cpp
ivec.shrink_to_fit(); //要求归还内存

cout << "ivec: size: " << ivec.size()
    << " capacity: " << ivec.capacity() << endl;
```

输出

```bash
ivec: size: 51 capacity: 51
```

## 9.5 额外的string操作

### 9.5.1 构造string的其他方法

![image.png](https://upload-images.jianshu.io/upload_images/25383-4ada8b90e78fd00f.png?imageMogr2/auto-orient/strip|imageView2/2/w/1240)

```cpp
const char *cp = "Hello World!!!"; //以空字符结束的数组
char noNull[] = {'H','i'}; //不是以空字符结束
string s1(cp);  //拷贝cp中的字符直到遇到空字符；s1 == "Hello World!!!"
string s2(noNull,2); //从noNull拷贝两个字符; s2 == "Hi"
string s3(noNull); //未定义：noNull不是以空字符结束
string s4(cp + 6,5); //从cp[6]开始拷贝5个字符；s4 == "World"
string s5(s1,6,4); //从s1[6]开始拷贝5个字符；s5 == "World"
string s6(s1,6); //从s1[6]开始拷贝，直至s1末尾；s6 == "World!!!"
string s7(s1,6,20); //正确，只拷贝到s1末尾；s7 == "World!!!"
// string s8(s1,16); //抛出一个out_of_range异常
```

#### substr操作

substr操作返回一个string，它是原始string的一部分或全部的拷贝。

```cpp
string s("hello world");
string s2 = s.substr(0,5); // s2 = hello
string s3 = s.substr(6); // s3 = world
string s4 = s.substr(6,100); // s3 = world
// string s5 = s.substr(12); //抛出一个out_of_range异常
```

### 9.5.2 改变string的其他方法

```cpp
s.insert(s.size(),5,'!'); // 在s末尾插入5个感叹号
s.erase(s.size()-5,5); //从s删除最后5个字符
```

标准库`string`类型还提供了接受C风格字符数组的`insert`和`assign`版本。例如，我们可以将以空字符结尾的字符数组`insert`到或`assign`给一个`string`：

```cpp
const char *cp = "Stately, plump Buck";
s.assign(cp,7); // s == "Stately"
s.insert(s.size(),cp + 7); // 将cp开始的7个字符拷贝到s中 s == "Stately, plump Buck"
```

```cpp
string s = "some string", s2 = "some other string";
s.insert(0,s2); //在s中位置0之前插入s2的拷贝 some other stringsome string
//在s[0]之前插入s2中s2[0]开始的s2.size()个字符
s.insert(0,s2,0,s2.size()); //some other stringsome other stringsome string
```

#### append和replace函数

```cpp
string s("C++ Primer"), s2 = s; //将s和s2初始化为"C++ Primer"
s.insert(s.size()," 4th Ed."); // s == "C++ Primer 4th Ed."
s2.append(" 4th Ed."); //s2 == "C++ Primer 4th Ed."
```

`replace`操作时调用`erase`和`insert`的一种简写形式：

```cpp
s.erase(11,3); // s == "C++ Primer Ed."
s.insert(11,"5th"); // s == "C++ Primer 5th Ed."
s2.replace(11,3,"5th"); // s2 == "C++ Primer 5th Ed."
```

此例中调用`replace`时，插入的文本恰好与删除的文本一样长。这不是必须的，可以插入一个更长或更短的string:

```cpp
s.replace(11,3,"Fifth"); //C++ Primer Fifth Ed.
```

#### 改变string的多种重载函数

### 9.5.3 string搜索操作

```cpp
string name("AnnaBelle");
auto pos1 = name.find("Anna"); // pos1 == 0
string lowercase("annabelle"); //搜索是大小写敏感的
pos1 = lowercase.find("Anna"); // pos1 == npos
```

```cpp
//定位name中的第一个数字
string numbers("0123456789"),name("r2d2");
auto pos = name.find_first_of(numbers); // pos == 1
//搜索一个string中第一个非数字字符
string dept("03714p3");
pos = dept.find_first_not_of(numbers); //5
```

#### 指定在哪里开始搜索

#### 逆向搜索

### 9.5.4 compare函数

### 9.5.5 数值转换

```cpp
int i = 42;
string s = to_string(i); //将整数i转换为字符表示形式
double d = stod(s);  //将字符串s转换为浮点数
```

![image.png](https://upload-images.jianshu.io/upload_images/25383-735a39fbee8b573b.png?imageMogr2/auto-orient/strip|imageView2/2/w/1240)

## 9.6 容器适配器

#### 定义一个适配器

#### 栈适配器

#### 队列适配器

