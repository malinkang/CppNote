# 第1章 开始

## 1.2 输入输出

C++的`iostream`库来提供输入和输出。`iostream`包含`istream`和`ostream`两个类，分别代表输入流和输出流。 标注库定义了4个io**对象**。为了处理输入，我们使用一个名为`cin`的`istream`对象。这个对象被称为**标准输入**。对于输出，我们使用一个名为`cout`的`ostream`类型的对象。此对象也被称为标准输出。标准库还定义了其他两个`ostream`对象，名为`cerr`和`clog`。通常用`cerr`来输出警告和错误信息，因此也被称为标准错误。而`clog`用来输出程序运行时的一般性信息。

```cpp
#include <iostream>
int main()
{    
    std::cout << "enter tow numbers" << std::endl;
    int v1 = 0, v2 = 0;
    std::cin >> v1 >> v2;
    std::cout << "the sum of " << v1 << " and " << v2 << " is " << v1 + v2 << std::endl;
    return 0;
}
```

`<<`是输出运算符，接受两个运算对象：左侧的运算对象必须是一个`ostream`对象，右侧的运算对象是要打印的值。此运算符将给定的值写个给定的`ostream`对象中。输出运算符的**计算结果**就是我们写入给定值的`ostream`对象。

`endl`是一个被称为**操纵符**的特殊值。写入`endl`的效果是结束当前行，并将与设备关联的缓冲区中的内容刷到设备中。缓冲刷新操作可以保证目前为止程序所产生的所有输出都真正写入输出流中，而不是仅停留在内存中等待写入流。

`std::`指出名字`cout`和`endl`是定义在名为**std的命名空间**中的。

`>>`是输入运算符，同样接受两个运算对象：左侧运算对象是一个`istream`对象，右侧是一个对象。此运算符从给定的`istream`读取数据，并存入给定对象中。输入运算符的**计算结果**就是给定的`istream`对象。

## 1.3 注释

单行注释以双斜线（//）开始，以换行符结束。 多行注释以`/*`开始以`*/`结束。

## 1.4 控制流

### 1.4.1 While语句

```cpp
#include "stdafx.h"
#include <iostream>

int main()
{    
    int sum = 0, num = 1;
    while (num <= 10) {
        sum += num;
        num++;
    }
    std::cout << "Sum of 1 to 10 inclusive is " << sum << std::endl;
    return 0;
}
```

## 1.4.2 for循环

```cpp
#include "stdafx.h"
#include <iostream>
int main()
{    
    int sum = 0;
    for (int num = 1; num <= 10; num++) {
        sum += num;
    }
    std::cout << "Sum of 1 to 10 inclusive is " << sum << std::endl;
    return 0;
}
```

## 1.4.3 读取数量不定的输入数据

```cpp
#include <iostream>
int main()
{
    int sum = 0, value = 0;
    while(std::cin>>value)
        sum += value;
    std::cout << "Sum is: " << sum << std::endl;
    return 0;
}
```

从标准输入读取下一个数，保存在value中。输入运算符返回其左侧运算对象，在本例中是`std::cin`。因此，此循环条件实际上检测的是`std::cin`。

当我们使用一个`isstream`对象作为条件时，其效果是检测流的状态。如果流是有效的，即流未遇到错误，那么检测成功。当遇到`文件结束符（end-of-file）`，或遇到一个无效输入时（例如读入的值不是一个整数），`istream`对象的状态会变为无效。处于无效状态的`istream`对象会使条件变为假。

### 1.4.4 if语句

统计输入中每个值连续出现的次数：

```cpp
#include <iostream>
int main()
{
    int currVal = 0, val = 0;
    if(std::cin >> currVal){
        int cnt = 1;
        while(std::cin >> val){
            if(val == currVal)
                ++cnt;
            else{
                //打印前一个值的个数
                std::cout << currVal << " occurs "
                << cnt << " times" << std::endl;
                currVal = val;
                cnt = 1;
            }
        }
        //打印最后一个值的个数
        std::cout << currVal << " occurs "
                << cnt << " times" << std::endl;
    }
    return 0;
}
```

## 1.5类简介

### 1.5.1 Sales\_item类

`Sales_item`类的作用是表示一本书的总销售额、售出册数和平均售价。

