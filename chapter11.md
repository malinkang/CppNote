---
title: 《C++ Primer》读书笔记 第11章 关联容器
date: 2016-07-11 13:38:00
tags:
---

关联容器和顺序容器有着根本的不同：关联容器中的元素时按关键字来保存和访问的。与之相对，顺序容器中的元素是按它们在容器中的位置顺序保存和访问的。

虽然关联容器的很多行为与顺序容器相同，但其不同之处反映了关键字的作用。

标准库提供了8个关联容器，如表11.1所示。这8个容器间的不同体现在三个维度上：每个容器（1）或者是一个`set`，或者是一个`map`；（2）或者要求不重复的关键字，或者允许重复关键字；（3）按顺序保存元素，或无序保存。允许重复关键字的容器的名字中都包含单词`multi`；不保持关键字按顺序存储的容器的名字都以单词`unordered`开头。因此一个`unordered_multi_set`是一个允许重复关键字，元素无序保存的集合，而一个`set`则是一个要求不重复关键字，有序存储的集合。无序容器使用哈希函数来组织元素。


![](/images/cpp-primer-plus/chapter11/1.png)

### 11.1 使用关联容器

#### 使用map

一个经典的使用关联数组的例子是单词计数程序：

```cpp
map<string,size_t> word_count;
string word;
while(cin >> word)
	++word_count[word]; //提取word的计数器并将其加1
for (const auto &w : word_count) //对map中的每个元素打印结果
	cout << w.first << " occurs " << w.second << ((w.second>1)?" times":" time") << endl;
```

#### 使用set

上一个示例程序的一个合理扩展是：忽略常见单词，如“the”、“and”、“or”等。我们可以使用`set`保存想忽略的单词，只对不在集合中的单词统计出现次数：

```cpp
map<string,size_t> word_count;
set<string> exclude = {"The","But","And","Or","An","A",
						"the","but","and","or","an","a"};
string word;
while(cin >> word)
	//只统计不在exclude中的单词
	if(exclude.find(word) == exclude.end())
		++word_count[word];
for (const auto &w : word_count)
	cout << w.first << " occurs " << w.second << ((w.second>1)?" times":" time") << endl;
```

### 11.2 关联容器概述

#### 11.2.1 定义关联容器

```cpp
map<string,size_t> word_count; //空容器
//列表初始化
set<string> exclude = {"The","But","And","Or","An","A",
						"the","but","and","or","an","a"};
//三个元素：
map<string,string> authors = {{"Joyce","James"},{"Austen","Jane"},{"Dickens","Charles"}}; 
```

##### 初始化multimap或multiset

```cpp
vector<int> ivec;
for (vector<int>::size_type i = 0;i !=10; ++i){
	ivec.push_back(i);
	ivec.push_back(i); //每个数重复保存一次
}
//iset包含来自ivec的不重复的元素；miset包含所有20个元素
set<int> iset(ivec.cbegin(),ivec.cend());
multiset<int> miset(ivec.cbegin(),ivec.cend());
cout << ivec.size() << endl;  // 20
cout << iset.size() << endl;  // 10
cout << miset.size() << endl; //20
```

#### 11.2.2 关键字类型的要求

##### 有序容器的关键字类型

##### 使用关键字类型的比较函数

#### 11.2.3 pair

```cpp
pair<string,string> anon; //保存两个string
pair<string,size_t> word_count;
pair<string,vector<int>> line; 
pair<string,string> author{"James","Joyce"};
cout << author.first << " " << author.second << endl;
```

![](/images/cpp-primer-plus/chapter11/2.png)


##### 创建pair对象的函数

```cpp
pair<string,int> process(vector<string> &v)
{
	if(!v.empty())
		return {v.back(),v.back().size()}; //列表初始化
	else 
		return pair<string,int>(); //隐式构造返回值
}
```

### 11.3 关联容器操作

除了表9.2中列出的类型，关联容器还定义了表11.3中累出的类型。这些类型表示容器关键字和值的类型。
 

![](/images/cpp-primer-plus/chapter11/3.png)

#### 11.3.1 关联容器迭代器


```cpp
map<string,size_t> word_count;
string word;
while(cin >> word)
	++word_count[word]; 
auto map_it = word_count.begin();
cout << map_it->first;
cout << " " << map_it->second; //
// map_it->first = "new key"; //错误：关键字是const的
++map_it->second; 
```

##### set的迭代器是const的

```cpp
set<int> iset = {0,1,2,3,4,5,6,7,8,9};
set<int>::iterator set_it = iset.begin();
if(set_it != iset.end()){
	// *set_it = 42; //错误：set中的关键字是只读的
	cout << *set_it << endl; //
}
```

##### 遍历关联容器


```cpp
map<string,size_t> word_count;
string word;
while(cin >> word)
	++word_count[word]; 
auto map_it = word_count.cbegin();
while (map_it != word_count.cend()){
	cout << map_it->first << " occurs "
		 << map_it->second << " times" <<endl;
	++map_it;
}
```

##### 关联容器和算法

#### 11.3.2 添加元素

```cpp
vector<int> ivec = {2,4,6,8,2,4,6,8};
set<int> set2; //空集合
set2.insert(ivec.cbegin(),ivec.cend()); //set2有4个元素
set2.insert({1,3,5,7,1,3,5,7}); //set2现在有8个元素
```
