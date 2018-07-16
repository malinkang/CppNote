# 第3章 字符串、向量和数组


## 3.1 命名空间的using声明

有了`using`声明就无须专门的前缀也能使用所需的名字了。

```text
using namespace::name;
```

```cpp
#include <iostream>
using std::cin;
int main()
{
    int i;
    cin >> i;
    //cout << i; //错误：没有对应的using声明
    std::cout << i;
    return 0;
}
```

#### 每个名字都需要独立的using声明

```cpp
#include <iostream>

using std::cin;
using std::cout;using std::endl;
int main()
{
    cout << "Enter two numbers:" << endl;
    int v1, v2;
    cin >> v1 >> v2;
    cout << "The sum of " << v1 << " and " << v2
         << " is " << v1 + v2 << endl;
    return 0;
}
```

#### 头文件不应包含using声明

## 3.2 标准库类型string

标准库类型`string`表示可变长的字符序列。

### 3.2.1 定义和初始化string对象

```cpp
#include <iostream>
#include <string>
using std::string;
using std::cout;
using std::endl;
int main()
{
    string s1;
    string s2 = s1;
    string s3 = "hiya";
    string s4(10,'c');
    cout << "s4 = " << s4 << endl; //s4 = cccccccccc
    return 0;
}
```

#### 直接初始化和拷贝初始化

如果使用等号初始化一个变量，实际上执行的是拷贝初始化，与之相反，如果不适用等号，则执行的是直接初始化。

```cpp
#include <iostream>
#include <string>
using std::string;
using std::cout;
using std::endl;
int main()
{   
    string s5 = "hiya";
    string s6("hiya");
    string s7(10,'c');
    string s8 = string(10,'c');//拷贝初始化处理多值
    cout << "s8 = " << s8 << endl; //s8 = cccccccccc

}
```

### 3.2.2 string对象上的操作

![image.png](https://upload-images.jianshu.io/upload_images/25383-47d4ac151f5a3f02.png?imageMogr2/auto-orient/strip|imageView2/2/w/1240)

#### 读写string对象

```cpp
#include <iostream>
#include <string>
using std::string;
using std::cout;
using std::cin;
using std::endl;
int main()
{   
    string s;
    cin >> s;
    cout << s << endl;
    return 0;

}
```

#### 读取未知数量的string对象

```cpp
#include <iostream>
#include <string>
using std::string;
using std::cout;
using std::cin;
using std::endl;
int main()
{   
    string word;
    while(cin >> word)
        cout << word << endl;
    return 0;
}
```

#### 使用getline读取一整行

```cpp
#include <iostream>
#include <string>
using std::string;
using std::cout;
using std::cin;
using std::endl;
int main()
{   
    string line;
    while(getline(cin,line))
        cout << line << endl;
    return 0;
}
```

#### string的empty和size操作

```cpp
#include <iostream>
#include <string>
using std::string;
using std::cout;
using std::cin;
using std::endl;
int main()
{   //每次读入一整行，遇到空行直接跳过
    string line;
    while(getline(cin,line))
        if(!line.empty())
            cout << line << endl;
    return 0;
}
```

```cpp
#include <iostream>
#include <string>
using std::string;
using std::cout;
using std::cin;
using std::endl;
int main()
{   //输出超过10个字符的行
    string line;
    while(getline(cin,line))
        if(line.size()>10)
            cout << line << endl;
    return 0;
}
```

#### string::size\_type类型

`size`函数返回的并非`int`，而是返回一个`string::size_type`类型的值。

#### 比较string对象

1. 如果两个`string`对象的长度不同，而且较短`string`对象的每个字符都与较长`string`对象对应位置上的字符相同，就说较短`string`对象小于较长`string`对象。
2. 如果两个`string`对象在某些对应的位置上不一致，则`string`对象比较的结果其实是`string`对象中第一对相异字符比较的结果。

```cpp
#include <iostream>
#include <string>
using std::string;
using std::cout;
using std::cin;
using std::endl;
int main()
{   
    string str = "Hello";
    string phrase = "Hello world";
    string slang = "Hiya";
    bool b1 = str < phrase;
    bool b2 = phrase < slang;
    cout << b1 << endl; // 1
    cout << b2 << endl; // 1
    return 0;
}
```

#### 两个string对象相加

```cpp
#include <iostream>
#include <string>
using std::string;
using std::cout;
using std::cin;
using std::endl;
int main()
{   
    string s1 = "hello, ", s2 = "world\n";
    string s3 = s1 + s2;
    s1 += s2;
    cout << s3 << endl; // 1
    cout << s1 << endl; // 1
    return 0;
}
/*
hello, world

hello, world

*/
```

#### 字面值和string对象相加

即使一种类型并非所需，我们也可以使用它，不过前提是这种类型可以自动转换成所需的类型。标准库允许把字符字面值和字符串字面值转换成`string`对象。

当把`string`对象和字符字面值及字符串字面值混在一条语句中使用时，必须确保每个加法运算符的两侧的运算对象至少有一个是`string`：

```cpp
#include <iostream>
#include <string>
using std::string;
using std::cout;
using std::cin;
using std::endl;
int main()
{   
    string s1 = "hello", s2 = "world";
    string s3 = s1 + "," + s2;
    count << "s3 = " << s3 << endl;
    string s4 = s1 + ",";
    // string s5 = "hello" + ",";//错误：两个运算对象都不是string
    string s6 = s1 + "," + "world"; 
    // string s7 = "hello" + ","+ s2; //错误：不能把两个字面值相加

}
```

### 3.2.3 处理string对象中的字符

#### 使用基于范围的for语句处理每个字符

```cpp
#include <iostream>
#include <string>
using std::string;
using std::cout;
using std::cin;
using std::endl;
int main()
{   
    string str("some string");
    for(auto c:str)
        cout << c << endl;
}
/*
s
o
m
e

s
t
r
i
n
g
*/
```

统计标点符号的个数

```cpp
#include <iostream>
#include <string>
using std::string;
using std::cout;
using std::cin;
using std::endl;
int main()
{   
    string s("Hello World!!!");
    decltype(s.size()) punct_cnt = 0;
    for (auto c : s)
        if(ispunct(c))
            ++punct_cnt;
    cout << punct_cnt << " punctuation characters in " << s << endl;
    return 0;
}
```

#### 使用范围for语句改变字符串中的字符

```cpp
#include <iostream>
#include <string>
using std::string;
using std::cout;
using std::cin;
using std::endl;
int main()
{   
    string s("Hello World!!!");
    for (auto &c : s) //对于s中的每个字符
        c = toupper(c); //c是一个引用，因此赋值语句将改变s中字符的值
    cout << s << endl;
    return 0;
}
/*
HELLO WORLD!!!
*/
```

#### 值处理一部分字符

要想访问`string`对象中的单个字符有两种方式：一种是使用下标，另外一种是使用迭代器。

`下标运算符[]`接收的输入参数是`string::size_type`类型的值，这个参数表示要访问的字符的位置；返回值是该位置上字符的引用。

```cpp
#include <iostream>
#include <string>
using std::string;
using std::cout;
using std::cin;
using std::endl;
int main()
{   
    string s("some string");
    if(!s.empty())
        s[0] = toupper(s[0]);
    cout << s << endl;
    return 0;
}
/*
Some string
*/
```

使用下标执行迭代

```cpp
#include <iostream>
#include <string>
using std::string;
using std::cout;
using std::cin;
using std::endl;
int main()
{   
    string s("some string");
    for(decltype(s.size()) index = 0;
        index != s.size() && !isspace(s[index]); ++index)
        s[index] = toupper(s[index]);
    cout << s << endl;
}
/*
SOME string
*/
```

#### 使用下标执行随机访问

```cpp
#include <iostream>
#include <string>
using std::string;
using std::cout;
using std::cin;
using std::endl;
int main()
{   
    const string hexdigits = "0123456789ABCDEF";
    cout << "Enter a series of numbers between 0 and 15" << endl;
    string result;
    string::size_type n;
    while(cin >> n)
        if(n<hexdigits.size())
            result +=hexdigits[n];
    cout << "Your hex number is: " << result << endl;
    return 0;
}
/*
Enter a series of numbers between 0 and 15
12 0 5 15 8 15
Your hex number is: C05F8F
*/
```

## 3.3 标准库类型vector

`C++`语言既有`类模板（class template）`，也有函数模板，其中`vector`是一个类模板。模板本身不是类或函数，相反可以将模板看作为编译器生成类或函数编写的一份说明。编译器根据模板创建类或函数的过程称为`实例化（instantiation）`，当使用模板时，需要指出编译器应把类或函数实例化成何种类型。

```cpp
#include <vector>
#include <string>
using std::vector;
using std::string;
int main()
{   
    vector<int> ivec;
    vector<vector<string>> file;
    return 0;
}
```

### 3.3.1 定义和初始化vector

![image.png](https://upload-images.jianshu.io/upload_images/25383-bb0b3c6ef272ccb9.png?imageMogr2/auto-orient/strip|imageView2/2/w/1240)

```cpp
#include <vector>
#include <string>
using std::vector;
using std::string;
int main()
{   
    vector<int> ivec; //初始状态为空
    vector<int> ivec2(ivec);
    vector<int> ivec3 = ivec;
    //vector<string> svec(ivec2);//错误：
    return 0;
}
```

#### 列表初始化vector对象

```cpp
#include <vector>
#include <string>
using std::vector;
using std::string;
int main()
{   
    vector<string> articles = {"a","an","the"};
    vector<string> v1{"a","an","the"};
    //vector<string> v2("a","an","the"); //错误 只能放在花括号里不能放在圆括号里
    return 0;
}
```

#### 创建指定数量的元素

```cpp
#include <vector>
#include <string>
using std::vector;
using std::string;
int main()
{   
    vector<int> ivec(10,-1); //10个int类型的元素，每个都被初始化为-1
    vector<string> svec(10,"hi!");
    return 0;
}
```

#### 值初始化

通常情况下，可以只提供`vector`对象容纳的元素数量，此时库会创建一个`值初始化的`元素初值，并把它赋给容器中的所有元素。

```cpp
#include <vector>
#include <string>
using std::vector;
using std::string;
int main()
{   
    vector<int> ivec(10); //10个元素，每个都初始化为0
    vector<string> svec(10); //10个元素，每个都是空string对象
    return 0;
}
```

#### 列表初始值还是元素数量

```cpp
#include <vector>
#include <string>
using std::vector;
using std::string;
int main()
{   
    vector<int> v1(10); //v1有10个元素，每个值都是0
    vector<int> v2{10}; //v2有1个元素，该元素的值是10
    vector<int> v3(10,1);//v3有10个元素，每个的值都是1
    vector<int> v4{10,1};//v4有2个元素，值分别是10和1

    vector<string> v5{"hi"}; //列表初始化：v5有一个元素

    // vector<string> v6("hi"); //错误：不能使用字符串字面值构建vector对象

    vector<string> v7{10};// v7有10个默认初始化的元素
    vector<string> v8{10,"hi"}; //v8有10个值为"hi"的元素。
    return 0;
}
```

如果初始化时使用了花括号的形式但是提供的值又不能用来列表初始化，就要考虑用这样的值来构造`vector`对象了。例如，要想列表初始化一个含有string对象的vector对象，应该提供能赋给string对象的初值。

显然不能用`int`初始化`string`对象，所以`v7`和`v8`提供的值不能作为元素的初始值。确认无法执行列表初始化后，编译器会尝试用默认值初始化`vector`对象。

### 3.3.2 向vector对象中添加元素

```cpp
#include <vector>
#include <string>
#include <iostream>
using std::vector;
using std::string;
using std::cin;
int main()
{   
    vector<int> v2;
    for (int i = 0;i !=100; ++i)
        v2.push_back(i);
    string word;
    vector<string> text;
    while(cin >> word){
        text.push_back(word);
    }
    return 0;
}
```

### 3.3.3 其他vector操作

![image.png](https://upload-images.jianshu.io/upload_images/25383-c34e59e3ea4031d7.png?imageMogr2/auto-orient/strip|imageView2/2/w/1240)

```cpp
#include <vector>
#include <string>
#include <iostream>
using std::vector;
using std::string;
using std::cout;
using std::endl;
int main()
{   
    vector<int> v{1,2,3,4,5,6,7,8,9};
    for (auto &i : v)
        i *= i;
    for (auto i : v)
        cout << i << " ";
    cout << endl;
    return 0;
}
/*
1 4 9 16 25 36 49 64 81
*/
```

`size`返回`vector`对象中元素的个数，返回值类型是由`vector`定义的`size_type`类型。

#### 计算vector内对象的索引

```cpp
#include <vector>
#include <string>
#include <iostream>
using std::vector;
using std::string;
using std::cout;
using std::cin;
using std::endl;
int main()
{   
    //以10分为一个分段统计成绩的数量：0~9，10~19，...90~99，100
    vector<unsigned> scores(11,0);
    unsigned grade;
    while(cin >> grade)
        if (grade <=100)
            ++scores[grade/10]; //将对应分数段的计数值加1
}
```

#### 不能用下标形式添加元素

## 3.4 迭代器介绍

类似于指针类型，迭代器也提供了对对象的间接访问。就迭代器而言，其对象是容器中的元素或者`string`对象中的字符。使用迭代器可以访问某个元素，迭代器也能从一个元素移动到另外一个元素。迭代器有有效和无效之分，这一点和指针差不多。有效的迭代器或者指向某个元素，或者指向容器中尾元素的下一位置；其他所有情况都属于无效。

### 3.4.1 使用迭代器

和指针不一样的是，获取迭代器不是使用取地址符，有迭代器的类型（比如string）同时拥有返回迭代器的成员。比如，这些类型都拥有名为`begin`和`end`的成员，其中`begin`成员负责返回指向第一个元素（或第一个字符）的迭代器。`end`成员则负责返回指向容器（或string对象）“尾元素的下一位置（one past the end）”的迭代器，也就是说，该迭代器指示的是容器的一个本不存在的“尾后（off the end）”元素。这样的迭代器没有实际含义，仅是个标记而已，表示我们已经处理完了容器中的所有元素。`end`成员返回的迭代器常被称作`尾后迭代器（off-the-end iterator）`或者简称为`尾迭代器（end iterator）`。特殊情况下如果容器为空，则`begin`和`end`返回的是同一个迭代器。

#### 迭代器运算符

![image.png](https://i.imgur.com/PuN5KHL.png)

和指针类似，也能通过解引用迭代器来获取它所指示的元素，执行解引用的迭代器必须合法并确实指示着某个元素。试图解引用一个非法迭代器或者尾后迭代器都是违背定义的行为。

```cpp
#include <string>
#include <iostream>
using std::string;
using std::cout;
using std::endl;
int main()
{   //首字母修改为大写
    string s("some string");
    //确保s非空
    if(s.begin() != s.end()){
        auto it = s.begin();
        *it = toupper(*it); 
    }
    cout << s << endl;
    return 0;
}
/*
Some string
*/
```

#### 将迭代器从一个元素移动到另外一个元素

迭代器使用递增运算符来从一个元素移动到下一个元素。

```cpp
#include <vector>
#include <string>
#include <iostream>
using std::vector;
using std::string;
using std::cout;
using std::cin;
using std::endl;
int main()
{   //首字母修改为大写
    string s("some string");
    for(auto it = s.begin(); it != s.end()&&!isspace(*it);++it)
        *it = toupper(*it);
    cout << s << endl;
}
/*
SOME string
*/
```

#### 迭代器类型

拥有迭代器的标准库类型使用`iterator`和`const_iterator`来标识迭代器的类型：

```cpp
#include <vector>
#include <string>
#include <iostream>
using std::vector;
using std::string;
using std::cout;
using std::cin;
using std::endl;
int main()
{   
    vector<int>::iterator it;//it能读写vector<int>的元素
    string::iterator it2;// it2能读写string对象中的字符
    vector<int>::const_iterator it3;
    string::iterator it4;
}
```

#### begin和end运算符

`begin`和`end`返回的具体类型由对象是否是常量决定，如果对象是常量，`begin`和`end`返回`const_iterator`；如果对象不是常量，返回`iterator`:

有时候这种默认的行为并非我们所要。如果对象只需读操作而无需写操作的话最好使用常量类型。为了便于专门得到`const_iterator`类型的返回值，`C++11`新标准引入了两个新函数，分别是`cbegin`和`cend`。

```cpp
#include <vector>
#include <string>
#include <iostream>
using std::vector;
using std::string;
using std::cout;
using std::cin;
using std::endl;
int main()
{   
    vector<int> v;
    const vector<int> cv;
    auto it1 = v.begin(); // it1的类型是vector<int>::iterator
    auto it2 = cv.begin();// it2的类型是vector<int>::const_iterator
    auto it3 = v.cbegin(); // it3的类型是vector<int>::const_iterator
}
```

#### 结合解引用和成员访问操作

#### 某些对vector对象的操作会使迭代器失效

### 3.4.2 迭代器运算

![image.png](https://upload-images.jianshu.io/upload_images/25383-76ead2ebb73b4c6a.png?imageMogr2/auto-orient/strip|imageView2/2/w/1240)

#### 迭代器的算术运算

#### 使用迭代器运算

## 3.5 数组

### 3.5.1 定义和初始化内置数组

维度必须是一个常量表达式

```cpp
#include <vector>
#include <string>
#include <iostream>
using std::vector;
using std::string;
int main()
{  
    unsigned cnt = 42;
    constexpr unsigned sz = 42;
    int arr[10];
    int *parr[sz];
    string bad[cnt]; //错误：cnt不是常量表达式 注：编译并没有报错
    //string strs[get_size()];当get_size是constexpr时正确；否则错误
}
```

定义数组的时候必须指定数组的类型，不允许用`auto`关键字由初始值的列表推断类型，另外和`vector`一样，数组的元素应为对象，因此不存在引用的数组。

#### 显式初始化数组元素

```cpp
#include <string>
using std::string;
int main()
{  
    const unsigned sz = 3;
    int a1[sz] = {0,1,2};
    int a2[] = {0,1,2}; //纬度是3的数组
    int a3[5] = {0,1,2};//等价于 a3={0,1,2,0,0}
    string a4[3] = {"hi","bye"}; //等价于 a4 = {"hi","bye",""}
    // int a5[2] = {0,1,2}; //错误：初始值过多
}
```

#### 字符数组的特殊性

字符数组有一种额外的初始化形式，我们可以用字符串字面量对此类数组初始化。当使用这种方式时，一定要注意字符串字面值的结尾处还有一个空字符，这个空字符也会像字符串的其他字符一样被拷贝到字符数组中去：

```cpp
int main()
{  
    char a1[] = {'C','+','+'};
    char a2[] = {'C','+','+','\0'};
    char a3[] = "C++";
    // const char a4[6] = "Daniel"; 没有空间可存放空字符！
    return 0;
}
```

#### 不允许拷贝和赋值

不能将数组的内容拷贝给其他数组作为其初始值，也不能用数组为其他数组赋值：

```cpp
int main()
{  
    int a[] = {0,1,2};
    // int a2[] = a;
    // a2 = a; 
    return 0;
}
```

#### 理解复杂的数组声明

```cpp
int main()
{   int arr[10];
    int *ptrs[10]; //ptrs是含有10个整型指针的数组
    // int &refs[10]; 不存在引用的数组
    int (*Parray)[10] = &arr;//指向一个含有10个整数的数组
    int (&arrRef)[10] = arr; //引用一个含有10个整数的数组
    int *(&array)[10] = ptrs; //array是数组的引用，该数组含有10个指针
    return 0;
}
```

### 3.5.2 访问数组元素

### 3.5.3 指针和数组

```cpp
#include <string>
#include <iostream>
using std::cout;
using std::endl;
using std::string;

int main()
{   
    string nums[] = {"one","two","three"};
    string *p = &nums[0]; //p指向nums的第一个元素
    //在很多用到数组名字的地方，编译器都会自动将其替换为一个指向数组首元素的指针：
    string *p2 = nums;
    cout << *p << endl; //输出 one
    //当使用数组作为一个auto变量的初始值时，推断得到的类型是指针而非数组
    int ia[] = {0,1,2,3,4,5,6,7,8,9};
    auto ia2(ia);
    *ia2 = 42;
    cout << *ia2 << endl;  //输出 42
    //当使用decltype关键字时上述转换不会发生，
    decltype(ia) ia3 = {0,1,2,3,4,5,6,7,8,9};
    cout << ia3[4] << endl;  //输出 4
    return 0;
}
```

#### 指针也是迭代器

使用指针也能遍历数组中的元素。这样做的前提是先得获取到指向数组第一个元素的指针和指向数组尾元素的下一位置的指针。通过数组名字或者数组中首元素的地址都能得到指向首元素的指针；不过获取尾部指针就要用到数组的另外一个特殊性质了。

```cpp
#include <iostream>
using std::cout;
using std::endl;

int main()
{   
    int arr[] = {0,1,2,3,4,5,6,7,8,9};
    int *e = &arr[10];
    for ( int *b = arr; b != e; ++b){
        cout<< *b << endl;
    }
    return 0;
}
```

#### 标准库函数begin和end

尽管计算得到尾后指针，但这种用法极易出错。为了让指针的使用更简单，更安全，`C++11`新标准引入了两个名为`begin`和`end`的函数。这两个函数定义在`iterator`头文件中。

```cpp
#include <iostream>
#include <iterator>
using std::cout;
using std::endl;
using std::begin;
using std::end;
int main()
{   
    int arr[] = {0,1,2,3,4,5,6,7,8,9};
    int *pbeg = begin(arr), *pend = end(arr);
    while(pbeg != pend && *pbeg >=0){
        cout<< *pbeg << endl;
        ++pbeg;
    }
    return 0;
}
```

#### 指针运算

```cpp
#include <iostream>
#include <iterator>
using std::cout;
using std::endl;
using std::begin;
using std::end;

int main()
{   
    constexpr size_t sz = 5;
    int arr[sz] = {1,2,3,4,5};
    int *ip = arr;
    int *ip2 = ip + 4;
    cout << *ip2 << endl; // 5
    int *p = arr +sz;  //p指向arr尾元素的下一位置。
    //两个指针相减的结果是它们之间的距离，
    //参与运算的两个指针必须指向同一个数组的元素
    auto n = end(arr)-begin(arr); // 
    cout << n << endl; // 5
    //只要两个指针指向同一个数组的元素，或者指向该数组的尾元素
    //的下一位置，就能利用关系运算符对其进行比较。
    int *b = arr, *e = arr + sz;
    while (b<e){
        cout << "b = " << *b << endl;
        ++b;
    }
    //如果两个指针分别指向不相关的对象，则不能比较它们
    int i = 0, sz = 42;
    int *p = &i,*e = &sz;
    // cout << (p < e) << endl; //未定义的：p和e无关，因此比较毫无意义。
    return 0;
}
```

两个指针相减的结果的类型是一种名为`ptrdiff_t`的标准类型，和`size_t`一样，`ptrdiff_t`也是一种定义在`cstddef`头文件中的机器相关的类型。因为差值可能为负值，所以`ptrdiff_t`是一种带符号类型。

#### 解引用和指针运算的交互

指针加上一个整数所得的结果还是一个指针，假设结果指针指向一个元素，则允许解引用该结果指针：

```cpp
#include <iostream>
#include <iterator>
using std::cout;
using std::endl;

int main()
{   
    int ia[] = {0,2,4,6,8};
    int last = *(ia+4);
    cout << last << endl; // 8
    return 0;
}
```

#### 下标和指针

在很多情况下使用数组的名字其实用的是一个指向数组首元素的指针。`ia[0]`是一个使用了数组名字的表达式，对数组执行`下标运算`其实是对数组元素的指针执行下标运算：

```cpp
#include <string>
#include <iostream>
#include <iterator>
using std::cout;
using std::endl;
using std::string;
using std::begin;
using std::end;

int main()
{   
    int ia[] = {0,2,4,6,8};
    int i = ia[2]; 
    // ia转换成指向数组首元素的指针 
    // ia[2]得到(ia+2)所指的元素
    int *p = ia;
    i = *(p+2); // 等价于 i = ia[2]
    cout << "i = " << i << endl; // i = 4
    //只要指针指向的是数组元素，都可以执行下标运算：
    int *p2 = &ia[2];
    int j = p2[1]; //p2[1]等价于*(p2+1)，就是ia[3]表示的那个元素
    int k = p2[-2]; // p2[-2]是ia[0]表示的那个元素
    cout << "j = " << j << endl; // j = 6
    cout << "k = " << k << endl; // k = 0
    return 0;
}
```

### 3.5.4 C风格字符串

字符串字面值是一种通用结构的实例，这种结构即是`C++`由C继承而来的`C风格字符串`。C风格字符串不是一种类型，而是为了表达和使用字符串而形成的一种约定俗称的写法。按此习惯书写的字符串放在字符数组中以`空字符结束`。以空字符结束的意思是在字符串最后一个字符后面跟着一个空字符\('\0'\)。一般利用指针来操作这些字符串。

#### C标准库String函数

下表列举了C语言标准库提供的一组函数，这些函数可用于操作C风格字符串，它们定义在`cstring`头文件中，`cstring`是C语言头文件`string.h`的`C++`版本。

![Imgur](https://i.imgur.com/FWLORBA.png)

传入此类函数的指针必须指向以空字符串作为结束的数组：

```cpp
char ca[] = {'C','+','+'}; // 不以空字符结束
cout << strlen(ca) << endl;// 错误：ca没有以空字符结束
```

#### 比较字符串

比较标准库`string`对象的时候，用的是普通的关系运算符和相等性运算符；如果把这些运算符用在两个C风格字符串上，实际比较的将是指针而非字符串本身；如果想要比较两个C风格字符串需要调用`strcmp`函数。

#### 目标字符串的大小由调用者指定

### 3.5.5 与旧代码的接口

#### 混用string对象和C风格字符串

* 允许使用以空字符结束的字符数组来初始化`string`对象或为`string`对象赋值。
* 在`string`对象的加法运算中允许使用以空字符结束的字符数组作为其中一个运算对象；在`string`对象的复合赋值运算中允许使用以空字符结束的字符数组作为右侧的运算对象。

上述性质反过来就不成立了：如果程序的某处需要一个C风格字符串，无法直接使用`string`对象来替代它。例如，不能用`string`对象直接初始化指向字符的指针。为了完成该功能，`string`专门提供了一个名为`c_str`的成员函数：

```cpp
#include <string>
#include <iostream>
using std::cout;
using std::endl;
using std::string;

int main()
{   
    string s("Hello World");
    const char *str = s.c_str();
    return 0;
}
```

#### 使用数组初始化vector对象。

`3.5.1节`介绍过不允许使用一个数组为另一个内置类型的数组赋初值，也不允许使用`vector`对象初始化数组。相反的，允许使用数组来初始化`vector`对象。要实现这一目的，只需指明要拷贝区域的首元素地址和尾元素地址就可以了：

```cpp
#include <vector>
#include <iostream>
using std::vector;
using std::cout;
using std::endl;
using std::begin;
using std::end;

int main()
{   
    int int_arr[] = {0,1,2,3,4,5};
    vector<int> ivec(begin(int_arr),end(int_arr));
    for (auto i : ivec)
        cout << i << endl;
    //用于初始化vector对象的值也可能仅是数组的一部分
    vector<int> subVec(int_arr+1,int_arr+4);
    for (auto i : subVec)
        cout << i << endl; // 1,2,3
    return 0;
}
```

## 3.6多维数组

当一个数组的元素仍单是数组时，通常使用两个维度来定义它：一个维度表示数组本身大小，另外一个维度表示其元素大小：

```cpp
#include <iostream>
using std::cout;
using std::endl;

int main()
{   
    int ia[3][4];// 大小为3的数组，每个元素时含有4个整数的数组
    //大小为10的数组，它的每个元素都是打下为20的数组，
    //这些数组的元素时含有30个整数的数组
    int arr[10][20][30] = {0};//将所有元素初始化为0
    return 0;
}
```

对于二维数组来说，常把第一个维度称作行，第二个维度称作列。

#### 多维数组的初始化

```cpp
int main()
{   
    int ia[3][4] = {
        {0,1,2,3},
        {4,5,6,7},
        {8,9,10,11}
    };
    return 0;
}
```

内层嵌套的花括号并非必需的。

```cpp
int ia[3][4] = {0,1,2,3,4,5,6,7,8,9,10,11};
```

类似于一维数组，在初始化多维数组时也并非所有元素的值都必须包含在初始化列表之内。如果仅仅想初始化每一行的第一个元素，通过如下的语句即可：

```cpp
int ia[3][4] = {{0},{4},{8}};
```

在这种情况下如果再省略掉内层的花括号，结果就大不一样了。

```cpp
int ia[3][4] = {0,3,6,9};
```

含义发生了变化，它初始化的第一行的4个元素，其他元素被初始化为0。

#### 多维数组的下标引用

如果表达式含有的下标运算符数量和数组的维度一样多，该表达式的结果将是给定类型的元素；反之，如果表达式含有的下标运算符数量比数组的维度小，则表达式的结果将是给定索引出的一个内层数组：

```cpp
#include <iostream>
using std::cout;
using std::endl;

int main()
{   
    constexpr size_t rowCnt = 3, colCnt = 4;
    int ia[rowCnt][colCnt];
    for (size_t i = 0;i != rowCnt; ++i) {
        for(size_t j = 0;j !=colCnt; ++j){
            ia[i][j] = i*colCnt +j;
        }
    }
    return 0;
}
```

#### 使用范围for语句处理多维数组

```cpp
#include <iostream>
using std::cout;
using std::endl;

int main()
{   
    constexpr size_t rowCnt = 3, colCnt = 4;
    int ia[rowCnt][colCnt];
    for (size_t i = 0;i != rowCnt; ++i) {
        for(size_t j = 0;j !=colCnt; ++j){
            ia[i][j] = i*colCnt +j;
        }
    }
    size_t cnt = 0;
    for(auto &row : ia){
        for(auto &col :row){
            col = cnt;
            ++cnt;
        }
    }
    for (size_t i = 0;i != rowCnt; ++i) {
        for(size_t j = 0;j !=colCnt; ++j){
            cout << "ia[" << i << "][" << j << "]=" << ia[i][j] << endl; 
        }
    }
    return 0;
}
```

#### 指针和多维数组

