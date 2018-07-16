---
title: 《C++ Primer》读书笔记 第10章 泛型算法
date: '2016-05-25T16:17:43.000Z'
tags: null
---

# 第10章 泛型算法

## 目录

顺序容器只定义了很少的操作：在多数情况下，我们可以添加和删除元素、访问首尾元素、确定容器是否为空以及获得指向首元素或尾元素之后位置的迭代器。

我们可以想象用户可能还希望做其他很多有用的操作：查找特定元素、替换或删除一个特定值、重排元素顺序等。

标准库并未给每个容器都定义成员函数来实现这些操作，而是定义了一组`泛型算法（generic algorithm)`：称它们为`“算法”`，是因为它们实现了一些经典算法的公共接口，如排序和搜索；称它们是`“泛型的”`，是因为它们可以用于不同类型的元素和多种容器类型（不仅包括标准库类型，如`vector`或`list`，还包括内置的数组类型），以及我们将看到的，还能用于其他类型的序列。

## 10.1 概述

大都数算法都定义在头文件`algorithm`中，标准库还在头文件`numeric`中定义了一组数值泛型算法。

一般情况下，这些算法并不直接操作容器，而是遍历由两个迭代器指定的一个元素范围来进行操作。通常情况下，算法遍历范围，对其中每个元素进行一些处理。例如，假定我们有一个`int`的`vector`，希望知道`vector`中是否包含一个特定值。回答这个问题最方便的方法是调用标准库算法`find`：

```cpp
vector<int> vec = {1,2,3,4,5,42,6,7,8,9};
int val = 42;
auto result = find(vec.begin(),vec.end(),val);
cout << "The value " << (result == vec.end() ? "is not present": "is present") << endl;
```

传递给`find`的前两个参数是表示元素范围的迭代器，第三个参数是一个值。`find`将范围中每个元素与给定值进行比较。它返回指向第一个等于给定值的元素的迭代器。如果范围中无匹配元素，则`find`返回第二个参数来表示搜索失败。因此，我们可以通过比较返回值和第二个参数来判断搜索是否成功。我们在输出语句中执行这个检测，其中使用了条件运算符来报告搜索是否成功。

由于`find`操作的是迭代器，因此我们可以用同样的`find`函数在任何容器中查找值。例如，可以用`find`在一个`string`的`list`中查找一个给定值：

```cpp
string val = "a value"; //我们要查找的值
auto result = find(lst.cbegin(),lst.cend(),val)
```

类似的，由于指针就像内置数组上的迭代器一样，我们可以用`find`在数组中查找值：

```cpp
int ia[] = {27,210,12,47,109,83};
int val = 83;
int* result = find(begin(ia),end(ia),val);
```

#### 迭代器令算法不依赖于容器，……

#### ……，但算法依赖于元素类型的操作

## 10.2 初识泛型算法

标准库提供了超过100个算法。幸运的是，与容器类似，这些算法有一致的结构。比起死记硬背全部100多个算法，理解此结构可以帮助我们更容易地学习和使用这些算法。

除了少数例外，标准库算法都对一个范围内的元素进行操作。我们将此元素范围称为“输入范围”。接受输入范围的算法总是使用前两个参数来表示此范围，两个参数分别是指向要处理的第一个元素和尾元素之后位置的迭代器。

虽然大多数算法遍历输入范围的方式相似，但它们使用范围中元素的方式不同。理解算法的最基本的方法就是了解它们是否读取元素、改变元素或是重排元素顺序。

### 10.2.1 只读算法

只读算法`accumulate`定义在头文件`numeric`中。`accumulate`函数接受三个参数，前两个指出了需要求和的元素的范围，第三个参数是和的初值。

```cpp
vector<int> vec = {1,2,3,4,5,6,7,8,9};
int sum = accumulate(vec.cbegin(),vec.cend(),10); //和的初值被设置为10
cout << sum << endl; //55
```

#### 算法和元素类型

```cpp
vector<string> v = {"1","2","3","4","5","6","7","8","9"};
string sum = accumulate(v.cbegin(),v.cend(),string("number:"));
cout << sum << endl; //number:123456789
```

#### 操作两个序列的算法

```cpp
string rester1("123456789"),rester2("345");
if(equal(rester1.cbegin()+2,rester1.cend()-4,rester2.cbegin())){
    cout << "true" << endl;
}
```

### 10.2.2 写容器元素的算法

```cpp
vector<int> vec = {1,2,3,4,5,6,7,8,9};
fill(vec.begin(),vec.begin() + vec.size()/2,10); //将前4个元素设置为10
```

#### 算法不检查写操作

#### 介绍back\_inserter

```cpp
vector<int> vec;// 空向量
auto it = back_inserter(vec); //通过它赋值会将元素添加到vec中
*it = 42; //vec中现在有一个元素，值为42
```

```cpp
vector<int> vec;// 空向量
fill_n(back_inserter(vec),10,0);//添加10个元素到vec
```

#### 拷贝算法

```cpp
int a1[] = {0,1,2,3,4,5,6,7,8,9};
int a2[sizeof(a1)/sizeof(*a1)];
cout << sizeof(a1) << endl;
cout << sizeof(*a1) << endl;
//ret指向拷贝到a2的尾元素之后的位置
auto ret = copy(begin(a1),end(a1),a2); //把a1的内容拷贝给a2
```

### 10.2.3 重排容器元素的算法

```cpp
void elimDups(vector<string> &words)
{
    //排序words，以便查找重复单词
    sort(words.begin(),words.end());
    //fox jumps over quick red red slow the the turtle
    for(auto word:words){
        cout << word <<" "<<flush; //
    }
    //unique重排插入范围，使得每个单词只出现一次
    //排列在范围的前部，返回指向不重复区域之后一个位置的迭代器
    auto end_unique = unique(words.begin(),words.end());
    cout << endl;
    //fox jumps over quick red slow the turtle the
    for(auto word:words){
        cout << word <<" "<<flush;
    }
    //使用向量操作erase删除重复单词
    words.erase(end_unique,words.end());
    cout << endl;
    //fox jumps over quick red slow the turtle
    for(auto word:words){
        cout << word <<" "<<flush;
    }
}

int main()
{
    vector<string> svec= {"the","quick","red","fox","jumps","over","the","slow","red","turtle"};
    elimDups(svec);
}
```

## 10.3 定制操作

```cpp
bool isShorter(const string &s1,const string&s2)
{
    return s1.size() < s2.size();
}

sort(words.begin(),words.end(),isShorter);
```

### 10.3.1 向算法传递函数

#### 谓词

#### 排序算法

通过调用`stable_sort`，可以保持等长元素间的字典顺序。

```cpp
elimDups(svec); 
stable_sort(svec.begin(),svec.end(),isShorter);
for(const auto &word : svec)
    cout << word <<" ";
cout << endl;
```

输出

```cpp
fox jumps over quick red red slow the the turtle
fox jumps over quick red slow the turtle the
fox jumps over quick red slow the turtle
fox red the over slow jumps quick turtle
```

### 10.3.2 lambda表达式

一个`lambda`表达式表示一个可调用的代码单元。我们可以将其理解为一个未命名的内联函数。与任何函数类似，一个`lambda`具有一个返回类型、一个参数列表和一个函数体。但与函数不同，`lambad`可能定义在函数内部。一个`lambda`表达式具有如下形式：

```cpp
[capture list](parameter list) -> return type {function body}
```

我们可以忽略参数列表和返回类型，但必须永远包含捕获列表和函数体

```cpp
auto f = []{ return 42;};
cout << f() << endl;
```

#### 向lambda传递参数

使用`lambda`来调用`stable_sort`

```cpp
stable_sort(svec.begin(),svec.end(),
    [](const string &a,const string &b)
        {return a.size() < b.size();});
```

#### 使用捕获列表

#### 调用find\_if

#### for\_each算法

#### 完整的biggies

### 10.3.3 lambda捕获和返回

#### 值捕获

```cpp
void fcn1()
{
    size_t v1 = 42;//局部变量
    auto f = [v1] { return v1;};
    v1 = 0;
    auto j = f();
    cout << j << endl; // j = 42
}
```

#### 引用捕获

```cpp
void fcn2()
{
    size_t v1 = 42;//局部变量
    auto f2 = [&v1] { return v1;};
    v1 = 0;
    auto j = f2();
    cout << j << endl; // j = 0
}
```

#### 隐式捕获

#### 可变lambda表达式

#### 指定lambda返回类型

### 10.3.4 参数绑定

#### 标准库bind函数

#### 绑定check\_size的sz参数

#### 使用placeholders名字

#### bind的参数

#### 用bind重排参数顺序

#### 绑定引用参数

## 10.4 再探迭代器

除了为每个容器定义的迭代器之外，标准库在头文件`iterator`中还定义了额外几种迭代器。这些迭代器包括以下几种。

* 插入迭代器（insert iterator\)：这些迭代器被绑定到一个容器上，可用来向容器插入元素。
* 流迭代器（stream iterator）：这些迭代器被绑定到输入或输出流上，可用来遍历所关联的IO流。
* 反向迭代器（reverse iterator\)：这些迭代器向后而不向前移动。除了`forward_list`之外的标准容器都有反向迭代器。
* 移动迭代器（move iterator\)：这些专用的迭代器不是拷贝其中的元素，而是移动它们。

### 10.4.1 插入迭代器

插入器是一种迭代器适配器，它接受一个容器，生成一个迭代器，能实现向给定容器添加元素。当我们通过一个插入迭代器进行赋值时，该迭代器调用容器操作来向给定容器添加元素。

* back\_inserter：创建一个使用push\_back的迭代器
* front\_inserter：创建一个使用push\_front的迭代器
* inserter:创建一个使用insert的迭代器。此函数接受第二个参数，这个参数必须是一个指向给定容器的迭代器。元素将被插入到给定迭代器所表示的元素之前。

```cpp
list<int> lst = {1,2,3,4};
list<int> lst2,lst3;
copy(lst.cbegin(),lst.cend(),front_inserter(lst2));
for(const auto &i : lst2)
    cout << i <<" "; //4 3 2 1
cout << endl;
copy(lst.cbegin(),lst.cend(),inserter(lst3,lst3.begin()));
for(const auto &i : lst3)
    cout << i <<" "; //1 2 3 4
cout << endl;
```

### 10.4.2 iostream迭代器

虽然`iostream`类型不是容器，但标准库定义了可以用于这些`IO`类型对象的迭代器。`istream_iterator`读取输入流，`ostream_iterator`向一个输出流写数据。这些迭代器将它们对应的流当做一个特定类型的元素序列来处理。通过使用流迭代器，我们可以用泛型算法从流对象读取数据以及向其写入数据。

#### istream\_iterator操作

```cpp
istream_iterator<int> in_iter(cin);//从cin读取int
istream_iterator<int> eof; //尾后迭代器
vector<int> vec; 
while( in_iter != eof )
    vec.push_back(*in_iter++);
for( auto &i : vec )
    cout << i << " ";
cout << endl;
```

我们可以将程序重写为如下形式。

```cpp
istream_iterator<int> in_iter(cin),eof;
vector<int> vec(in_iter,eof);
for( auto &i : vec )
    cout << i << " ";
cout << endl;
```

#### 使用算法操作流迭代器

```cpp
istream_iterator<int> in(cin),eof;
cout << accumulate(in,eof,0) << endl;
```

```text
23 109 45 89 6 34 12 90 34 23 56 23 8 89 23 //输入
664 //输出结果
```

#### istream\_iterator允许使用懒惰求值

#### ostream\_iterator操作

```cpp
istream_iterator<int> in_iter(cin),eof;
vector<int> vec(in_iter,eof);
ostream_iterator<int> out_iter(cout," ");
for (auto e : vec)
    *out_iter++ = e; //将元素写到cout
cout << endl;
```

值得注意的是，当我们向`out_iter`赋值时，可以忽略解引用和递增运算。即，循环可以重写成下面的样子：

```cpp
for (auto e : vec)
    out_iter = e;
cout << endl;
```

可以通过调用`copy`来打印`vec`中的元素，这比编写循环更为简单：

```cpp
istream_iterator<int> in_iter(cin),eof;
vector<int> vec(in_iter,eof);
ostream_iterator<int> out_iter(cout," ");
copy(vec.begin(),vec.end(),out_iter);
cout << endl;
```

#### 使用流迭代器处理类类型

### 10.4.3 反向迭代器

```cpp
vector<int> vec = {0,1,2,3,4,5,6,7,8,9};
for (auto r_iter = vec.rbegin();
    r_iter != vec.rend();
    ++r_iter)
    cout << *r_iter << " ";
cout << endl;
```

#### 反向迭代器需要递减运算符

#### 反向迭代器和其他迭代器间的关系

## 10.5 泛型算法结构

任何算法的最基本的特性是它要求其迭代器提供哪些操作。某些算法，如`find`，值要求通过迭代器访问元素、递增迭代器以及比较两个迭代器是否相等这些能力。其他一些算法，如`sort`，还要求读、写和随机访问元素的能力。算法所要求的迭代器操作可以分为5个`迭代器类别（iterator category）`。每个算法都会对它的每个迭代器参数指明须提供哪类迭代器。

![image.png](https://upload-images.jianshu.io/upload_images/25383-46a93acb161b44b6.png?imageMogr2/auto-orient/strip|imageView2/2/w/1240)

### 10.5.1 5类迭代器

### 10.5.2 算法形参模式

### 10.5.3 算法命名规范

## 10.6 特定容器算法

