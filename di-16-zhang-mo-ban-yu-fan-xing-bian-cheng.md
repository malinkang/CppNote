# 第16章 模版与泛型编程

## 16.1 定义模版

### 16.1.1 函数模版

模版定义以关键字`template`开始，后跟一个`模版参数列表（template parameter list）`，这是一个逗号分隔的一个或多个`模版参数（template parameter）`的列表。

```cpp
template <typename T>
int compare(const T &v1,const T &v2)
{
    if(v1 < v2) return -1;
    if(v2 < v1) return 1;
    return 0;
}
```

#### 实例化函数模版

```cpp
cout << compare(1,2) << endl; //-1
vector<int> vec1{1,2,3},vec2{4,5,6};
cout << compare(vec1,vec2) << endl; //-1
```

#### 模版类型参数

我们的`compare`函数有一个模版`类型参数（type parameter）`。一般来说，我们可以将类型参数看做类型说明符，就像内置类型或类类型说明符一样使用。特别是，类型参数可以用来指定返回类型或函数的参数类型，以及在函数体内用于变量声明或类型转换。

```cpp
template <typename T> T foo(T* p)
{
    T tmp = *p;
    //...
    return tmp;
}
```

类型参数前必须使用关键字`class`或`typename`；

```cpp
//错误：U之前必须加上class或typename
template <typename T,U> T calc(const T&,const U&);
```

#### 非类型模版参数

除了定义类型参数，还可以在模版中定义`非类型参数（nontype parameter)`。

```cpp
template <unsigned N,unsigned M>
int compare(const char (&p1)[N],const char (&p2)[M])
{
    return strcmp(p1,p2);
}
cout << compare("hi","mom") << endl; //-5
```

#### inline和constexpr的函数模版

#### 编写类型无关的代码

#### 模版编译

#### 大多数编译错误在实例化期间报告

