---
title: "🚀 C++学习笔记：基础语法与面向对象编程"
published: 2025-06-26 13:37:00
pinned: true
description: "C++学习笔记，涵盖基础语法、面向对象编程及标准模板库使用。"
tags: ["C++", "Algorithm"]
category: "Algorithm"
author: "🔥Flame"
draft: false
date: 2025-06-26 13:37:00
image: "./images/cppLearningCover.png"
pubDate: 2025-06-26 13:37:00
---



# 1、基础部分

## 1.1 Bool变量

```c++
#include<iostream>
int main () {
    bool a = true;

}
```

> 取值范围：true   false
>
> “非0即真”

## 1.2 内联函数

**内联函数是一个函数，通过“内存膨胀”的方式以空间换时间，目的是提高程序运行速度。**

```c++
inline 返回值类型  函数名（参数表）{
	函数体
}
```

> 1、函数体里面有循环不建议用内联函数
>
> 

## 1.3 函数重载

1、在同一个项目中定义的函数名字可以重复

2、函数名必须一致

3、函数的参数列表不同

## 1.4 函数参数缺省

1、声明函数某个参数的时候指定一个默认值

2、调用该函数时如果采用默认值，无须指定该参数

举例：

```c++
#include<iostream>
void func (int a, float b = 0.2f){
    printf("%d\n%f", a, b);
}
int main () {
    func(5);
}
/*
5
0.200000
*/
```

## 1.5 引用

给一个变量或者对象取的别名

```c++
#include<iostream>

int main () {
    int num = 10;
    printf("%d\n", num);
    int& nn = num;
    nn = 55;
    printf("%d\n", nn);
}
/*
10
55
*/
```

## 1.9 命名空间

1、命名空间是用来组织和重用代码的编译单元

2、通过命名空间来避免冲突， 解决重名现象

3、作用域运算符 ==：：==，可以读作“里面的”

```c++
#include <iostream>
namespace NAME_1{
	int num = 10;
}
int main () {
	int num = 8;
	printf("%d\n", NAME_1::num);
	printf("%d", num);
	return 0;
}
/*
10
8
*/
```

## 1.10 cin 和 cout

cin的作用类似 scanf，cout的作用类似printf，他们在具体使用的时候有些区别

```c++
#include <iostream>
using namespace std;
int main () {
	int a, b, c, d;
	cin >> a >> b >> c >> d;
	cout << a << b << c << d << endl;

}
/*
cin cout 等都是在 命名空间 std下面
*/
```

## 1.11 new 和 delete

`new`的作用类似`malloc`，`delete`的作用类似`free`

```c++
#include<iostream>
using namespace std;
int main() {
	// 1 申请单个内存
	int* p1 = new int;
	*p1 = 0;
	// 2 申请单个内存且初始化
	int* p2 = new int(656);
	cout << "*p2 = " << *p2;
	// 3 批量申请
	int* p3 = new int[10];
	for (size_t i = 0; i < 10; i++)
	{
		p3[i] = i;
		cout << "p3[" << i << "] = " << p3[i] << endl;

	}

	// 释放内存
	delete p1;
	delete p2;
	delete[] p3;

}
```

# 2、面向对象基础

对象在程序中抽象为两个部分：属性（数据）、方法（函数）

## 2.1 类与对象

类是一种用户自定义的数据类型 （函数， 数据） ，类是具有相同的属性和行为的对象的集合，类是对象的抽象， 对象是类的具体

语法：

```c++
// 语法
class 类名{
    // 默认是私有的
    	// 成员： 1 数据  2 函数
    // 访问权限修饰符
    public:		// 公有的
    	// 成员： 1 数据  2 函数
    private: 	// 私有的
        // 成员： 1 数据  2 函数
    protected: 	// 被保护的
    	// 成员： 1 数据  2 函数
    
};
```

代码示例：

```c++
#include<iostream>
using namespace std;
//#include<stdiio.h>
class Sheep {
public:
	char name[32];
private:
	int age;
public:
	void setAge(int age) {
		this->age = age;
	}
	void eat() {
		cout << "Eat grass!!" << endl;
	}
	void speak();
};
void Sheep::speak() {
	cout << "I'm a sheep; age = " << age << endl;
}

int main() {
	Sheep xiYY;
	strcpy(xiYY.name, "吸氧羊");
	xiYY.setAge(5);
	xiYY.speak();
	return 0;

}
```

## 2.2 String类

String是 C ++中 的字符串，类似于c语言中的字符数组

头文件：

```c++
#include<string>
```

举例：

```c++
#include<iostream>
//#include<string.h>
using namespace std;
#include<string>


int main() {
	string str;
	//str = "das465";
	cin >> str;
	cout << str.length() << endl;
	cout << str.empty() << endl;
	str.clear();
	cout << str.length() << endl;
	return 0;

}
```

# 3 构造和析构函数

## 3.1 普通构造函数

构造函数名和类名相同，构造函数没有返回值类型， 也没有返回值，构造函数可以重载， 需要满足函数重载的条件

代码示例：

```c++
#include<iostream>
using namespace std;

class MyClass {
private:
	int num;
	int val;
public:
	
	MyClass(int num, int val){
		this->num = num;
		this->val = val;
		cout << "MyClass(int num, int val)" << endl;
	}
	MyClass() {
		cout << "MyClass ()" << endl;
	}
};

int main() {
	MyClass obj;
	MyClass Obj_1(5, 6);
	MyClass* p1 = new MyClass;
	MyClass* p2 = new MyClass(5, 9);
}
/*
MyClass ()
MyClass(int num, int val)
MyClass ()
MyClass(int num, int val)
*/
```

> const常量初始化赋值
>
> ```c++
> #include<iostream>
> using namespace std;
> 
> class MyClass {
> public:
> 	const int num;
> 	const int val;
> public:
> 	
> 	MyClass(int i, int j): num(i), val(j)
> 	{
> 		cout << "MyClass(int num, int val)" << endl;
> 	}
> };
> 
> int main() {
> 	
> 	MyClass Obj_1(5, 6);
> 	cout << Obj_1.num << endl;
> 	cout << Obj_1.val << endl;
> }
> /*
> MyClass(int num, int val)
> 5
> 6
> */
> ```

## 3.2 析构函数

析构函数是一种特殊的函数，主要作用是在对象生命周期结束时进行清理，系统可以自动调用析构函数

说明：

> [!note]
>
> 1、函数名与类名相同， 在前面加上一个`~`
>
> 2、没有返回值类型和返回值，也没有参数
>
> 3、有低保： 没写系统给个默认的
>
> 4、析构函数可以主动通过对象调用
>
> 5、析构函数必须是公有属性下
>
> 6、在对象生命周期结束时， 会自动调用析构函数
>
> 7、不是因为调用了析构函数导致生命周期结束，是生命周期结束时， 会自动调用析构函数，注意因果关系！ ！
>
> **8、对于同个类别的对象，首先构造出来的后删除、析构，后构造出来的先删除、析构**
>
> **9、对于指针类型声明定义是在系统堆区域，结束时不会自动调用析构函数**

```c++
#include<iostream>
using namespace std;

class MyClass {
public:
	const int num;
	const int val;
public:
	
	MyClass(int i, int j): num(i), val(j)
	{
		cout << "MyClass(int num, int val)" << endl;
	}
	~MyClass() {
		cout << "析构函数执行" << endl;
	}
};

int main() {
	
	MyClass Obj_1(5, 6);
	cout << Obj_1.num << endl;
	cout << Obj_1.val << endl;
}
/*
MyClass(int num, int val)
5
6
析构函数执行
*/
```

## 3.3 拷贝构造函数

> 1、拷贝构造是一中特殊的构造函数
>
> 2、通过拷贝构造函数完成一个复制的过程
>
> 3、特殊 ： 第一个参数是本类的对象的引用
>
> 4、先是构造函数，才可能是拷贝构造函数
>
> 5、有低保 ： 没写系统给， 将成员一一对应赋值
>
> 6、可以自定义
>
> >调用时机：
> >
> >1、使用一个对象给另一个对象进行初始化
> >
> >2、使用一个对象构造另一个对象
> >
> >3、函数的参数是类的对象
> >
> >4、函数的返回值是类的对象

示例

```c++
#include<iostream>
using namespace std;
class MyClass {
public:
	// 构造函数
	MyClass(){}
	// 析构函数
	~MyClass(){}
	//拷贝构造函数
	MyClass(MyClass& obj){}
	MyClass(MyClass& obj, int n) {}
};
int main() {

	return 0;
}
```

# 4、C++标准模板库

## 4.1 vector 容器

可以理解为变长数组

头文件：`#include<vector>`

### 4.1.1 迭代器访问

```c++
#include<cstdio>
#include<vector>
using namespace std;
int main() {
	vector< int > temp;
	int length = 10;
	for (int i = 0; i < length; i++){
		temp.push_back(i);
	}
	vector<int>::iterator it;
	for (it = temp.begin(); it != temp.end(); it++) {
		printf("%d ", *it);
	}
	return 0;
}
// 0 1 2 3 4 5 6 7 8 9
```

### 4.1.2 常用函数

（1）`push_back()`函数

在vector后面添加一个元素`x`，时间复杂度为`O(1)`

用法示例：

```c++
#include<cstdio>
#include<vector>
using namespace std;
int main() {
	vector< int > temp;
	int length = 10;
	for (int i = 0; i < length; i++){
		temp.push_back(i);
	}
	vector<int>::iterator it;
	for (it = temp.begin(); it != temp.end(); it++) {
		printf("%d ", *it);
	}
	return 0;
}
// 0 1 2 3 4 5 6 7 8 9
```

（2）`pop_back()`函数

用于删除vector的尾元素，时间复杂度为`O(1)`

用法示例：

```c++
#include<cstdio>
#include<vector>
using namespace std;
int main() {
	vector< int > vi;
	int length = 10;
	for (int i = 0; i < length; i++){
		vi.push_back(i);
	}
	vi.pop_back();
	vector<int>::iterator it;
	for (it = vi.begin(); it != vi.end(); it++) {
		printf("%d ", *it);
	}
	return 0;
}
// 0 1 2 3 4 5 6 7 8
```

（3）`size()`函数

用于获得vector中的元素个数，时间复杂度为`O(1)`

示例用法：

```c++
#include<cstdio>
#include<vector>
using namespace std;
int main() {
	vector< int > vi;
	int length = 10;
	for (int i = 0; i < length; i++){
		vi.push_back(i);
	}
	vi.pop_back();
	printf("lentgh = %d", vi.size());
	
}
// lentgh = 9
```

（4）`clear()`函数

用于清空vector中的所有元素，时间复杂度为`O(N)`，其中`N`为vector中元素的个数

示例用法：

```c++
#include<cstdio>
#include<vector>
using namespace std;
int main() {
	vector< int > vi;
	int length = 10;
	for (int i = 0; i < length; i++){
		vi.push_back(i);
	}
	vi.clear();
	printf("lentgh = %d", vi.size());
	
}
// lentgh = 0
```

（5）`insert()`函数

`insert(it, x)`用来向 vector 的任意迭代器it处插入一个元素`x`，时间复杂度为`O(N)`

示例代码：

```c++
#include<cstdio>
#include<vector>
using namespace std;
int main() {
	vector< int > vi;
	int length = 10;
	for (int i = 0; i < length; i++){
		vi.push_back(i);
	}
	vi.insert(vi.begin() + 2, -5); // 向位置为2的地方插入-5
	vector<int>::iterator it;
	for (it = vi.begin(); it != vi.end(); it++) {
		printf("%d ", *it);
	}
	return 0;
}
// 0 1 -5 2 3 4 5 6 7 8 9
```

（6）`erase()`函数

两种用法：删除单个元素、删除一个区间内的所有元素。时间复杂度为`O(N)`

示例用法：

1、删除单个元素：`erase(it)`即为删除迭代器it处的元素

```c++
#include<cstdio>
#include<vector>
using namespace std;
int main() {
	vector< int > vi;
	int length = 10;
	for (int i = 0; i < length; i++){
		vi.push_back(i);
	}
	vi.erase(vi.begin() + 1); // 删除 vi[1]
	vector<int>::iterator it;
	for (it = vi.begin(); it != vi.end(); it++) {
		printf("%d ", *it);
	}
	return 0;
}
// 0 2 3 4 5 6 7 8 9
```

2、删除一个区间内的所有元素：`erase(first, last)`用于删除`[fist, last)`内的所有元素

示例代码：

```c++
#include<cstdio>
#include<vector>
using namespace std;
int main() {
	vector< int > vi;
	int length = 10;
	for (int i = 0; i < length; i++){
		vi.push_back(i);
	}
	vi.erase(vi.begin() + 1, vi.begin() + 4); // 删除 vi[1] vi[2] vi[3]
	vector<int>::iterator it;
	for (it = vi.begin(); it != vi.end(); it++) {
		printf("%d ", *it);
	}
	return 0;
}
// 0 4 5 6 7 8 9
```

## 4.2 set 集合

set翻译为集合，是一个**内部自动有序且不含重复元素**的容器

头文件：`#include<set>`

### 4.2.1 迭代器访问

```c++
#include<cstdio>
#include<set>
using namespace std;
int main() {
	set<int> st;
	st.insert(3);
	st.insert(2);
	st.insert(5);
	st.insert(3);
	for (set<int>::iterator it = st.begin(); it != st.end(); it++) {
		printf("%d ", *(it));
	}
	return 0;
}
// 2 3 5
```

### 4.2.2 常用函数

（1）`insert()`函数

`insert(x)`实现将`x`插入`set`容器中，并且自动递增排序和去重，时间复杂度为`O(logN)`，其中`N`为`set`内的元素个数

（2）`find()`函数

`find(value)`函数返回`set`中对应值为`value`的迭代器，时间复杂度为`O(logN)`，其中`N`为`set`内的元素个数

示例用法：

```c++
#include<cstdio>
#include<set>
using namespace std;
int main() {
	set<int> st;
	st.insert(3);
	st.insert(2);
	st.insert(5);
	st.insert(3);
	set<int> ::iterator it = st.find(2); // 在 set 中查找2，返回其迭代器
	printf("%d\n", *it);
	return 0;
}
// 2
```

（3）`erase()`函数

两种用法：删除单个元素、删除一个区间内的所有元素

1、删除单个元素，两种方法

- st.erase(it)，其中 it 为所需要删除元素的迭代器，时间复杂度为`O(1)`，可以结合`find()`来使用

```c++
#include<cstdio>
#include<set>
using namespace std;
int main() {
	set<int> st;
	st.insert(100);
	st.insert(200);
	st.insert(100);
	st.insert(300);
	st.erase(st.find(100));
	for (set<int>::iterator it = st.begin(); it != st.end(); it++) {
		printf("%d ", *(it));
	}
	return 0;
}
// 200 300
```

- st.erase(value)，value 为所需要删除元素的值，时间复杂度为`O(logN)`，`N`为`set`内的元素个数

```c++
#include<cstdio>
#include<set>
using namespace std;
int main() {
	set<int> st;
	st.insert(100);
	st.insert(200);
	st.insert(100);
	st.insert(300);
	st.erase(100);
	for (set<int>::iterator it = st.begin(); it != st.end(); it++) {
		printf("%d ", *(it));
	}
	return 0;
}
// 200 300
```

2、删除一个区间内的所有元素：`erase(first, last)`用于删除`[fist, last)`内的所有元素，时间复杂度为`O(last - first)`

```c++
#include<cstdio>
#include<set>
using namespace std;
int main() {
	set<int> st;
	st.insert(20);
	st.insert(10);
	st.insert(40);
	st.insert(30);
	st.erase(st.find(30), st.end());
	for (set<int>::iterator it = st.begin(); it != st.end(); it++) {
		printf("%d ", *(it));
	}
	return 0;
}
// 10 20
```

（4）`size()`函数

用于获取set内的元素个数，时间复杂度为`O(1)`

（5）`clear()`函数

用于清空set中的所有元素，时间复杂度为`O(N)`，`N`为`set`内的元素个数



> [!important]
>
> ==set最主要的作用是自动去重并且升序排序==

## 4.3 String 字符串

### KMP算法：

```c++
void getNext(int* next, const string& s){
    int j = -1;
    next[0] = j;
    for(int i = 1; i < s.size(); i++){
        while(j>=0 && s[i] == s[j+1]){
            j = next[j];
		}
        if(s[i] == s[j+1]){
            j++;
		}
        next[i] = j;
	}
}
```

KMP优化版本，不算官方，自己测试出来的：

```c++
    void getNext(int* next, string& s) {
        int j = -1;
        next[0] = j;
        for (int i = 1; i < s.size(); i++) {
            while (j >= 0 && s[j + 1] != s[i]) {
                j = next[j];
            }
            if (s[j + 1] == s[i]) {
                j++;
            }
            next[i] = j;
            //  next[i] = (j >= 0 && s[i] == s[j]) ? next[j] : j;
        }
        for(int i = 0; i <s.size();i++){
            if(i > 0 && s[next[i-1] + 1] == s[i] && next[i-1] >= 0){
                next[i-1] = next[next[i-1]];
            }
        }
    }
```

## 4.4 list 

list 容器，又称双向链表容器，即该容器的底层是以双向链表的形式实现的。这意味着，list 容器中的元素可以分散存储在内存空间里，而不是必须存储在一整块连续的内存空间中。



## 4.5 stack







## 4.6 queue



### 4.6.1 优先级队列

#### （1）基本用法

```c++
#include<iostream>
#include<vector>
#include<algorithm>
#include<queue>
using namespace std;
int main() {
	vector<int> a = { -12, 5, -2, 9, 8, -8 };
	priority_queue<int, vector<int>, greater<int>> que(a.begin(), a.end()); // 小顶堆
	//priority_queue<int, vector<int>, less<int>> que(a.begin(), a.end()); // 大顶堆
	while (!que.empty()) {
		cout << que.top() << " ";
		que.pop();
	}
	return 0;
}
// 小顶堆 -12 -8 -2 5 8 9
// 大顶堆 9 8 5 -2 -8 -12
```

> 说明：
>
> 1、`less<int>`表示数字大的优先级越大，而`greater<int>`表示数字小的优先级越大。
>
> 2、顶堆插入一个新元素时，就是插入到最后一个叶子。然后这时候整理堆内元素让堆重新满足大小顶堆。关键**让新插入的结点和它的父结点进行比较，`comp(新插入，它的父结点)`**。
>
> 3、大顶堆就是让父比子大，即符合`less`让新插入的比父结点更小；小顶堆就是父比子小，即符合`greater`让新插入的比父结点更大。
>
> 4、优先队列的这个函数与sort中的cmp函数的效果是相反的

#### （2）自定义比较顺序

优先级队列中的自定义顺序，举例，已知有如下结构体：

```c++
struct fruit {
    string name;
    int price;
    fruit() {}
    fruit(string name, int price) : name(name), price(price) {}
};
```

1、重载 `operator<`

通过在 `fruit` 结构体中重载 `operator<`，让 `priority_queue` 默认使用该操作符进行比较。

**目标**：价格高的水果优先级高（大顶堆）。

```c++
#include<iostream>
#include<vector>
#include<algorithm>
#include<queue>
using namespace std;
struct fruit {
	string name;
	int price;
	fruit() {}
	fruit(string name, int price) : name(name), price(price) {}
	// 重载 < 操作符：定义“小于”关系
	// priority_queue 默认是最大堆，所以返回 a < b 时，大的在前
	bool operator<(const fruit& other) const {
		return price < other.price; // 价格高的优先级高
	}
};
int main() {
	priority_queue<fruit> pq;
	pq.push(fruit("Apple", 10));
	pq.push(fruit("Banana", 20));
	pq.push(fruit("Orange", 15));
	pq.push(fruit("Grape", 25));
	cout << "按价格从高到低输出（重载 operator<）：" << endl;
	while (!pq.empty()) {
		fruit f = pq.top();
		cout << f.name << " : " << f.price << "元" << endl;
		pq.pop();
	}
	return 0;
}
/*
按价格从高到低输出（重载 operator<）：
Grape : 25元
Banana : 20元
Orange : 15元
Apple : 10元
*/
```

2、使用仿函数（函数对象）

定义一个结构体或类，重载 `operator()`，作为比较器类型传入 `priority_queue`。

**目标**：价格低的水果优先级高（小顶堆）。

```c++
#include <iostream>
#include <vector>
#include <queue>
using namespace std;
struct fruit {
    string name;
    int price;
    fruit() {}
    fruit(string name, int price) : name(name), price(price) {}
};
// 仿函数：定义比较逻辑
struct Compare {
    bool operator()(const fruit& a, const fruit& b) const {
        return a.price > b.price; // 小顶堆：价格小的优先
    }
};
int main() {
    // 第三个模板参数是仿函数类型
    priority_queue<fruit, vector<fruit>, Compare> pq;
    pq.push(fruit("Apple", 10));
    pq.push(fruit("Banana", 20));
    pq.push(fruit("Orange", 15));
    pq.push(fruit("Grape", 25));
    cout << "按价格从低到高输出（仿函数）：" << endl;
    while (!pq.empty()) {
        fruit f = pq.top();
        cout << f.name << " : " << f.price << "元" << endl;
        pq.pop();
    }
    return 0;
}
/*
按价格从低到高输出（仿函数）：
Apple : 10元
Orange : 15元
Banana : 20元
Grape : 25元
*/
```

3、使用 `Lambda` 表达式

```c++
#include <iostream>
#include <vector>
#include <queue>
using namespace std;
struct fruit {
	string name;
	int price;
	fruit() {}
	fruit(string name, int price) : name(name), price(price) {}
};
int main() {
	// 定义 lambda
	auto cmp = [](const fruit& a, const fruit& b) {
		return a.price < b.price; // 大顶堆：价格大的优先
		};
	// 使用 lambda 初始化 priority_queue
	// 注意：必须指定容器类型和比较器类型（decltype）
	priority_queue<fruit, vector<fruit>, decltype(cmp)> pq(cmp);
	pq.push(fruit("Apple", 10));
	pq.push(fruit("Banana", 20));
	pq.push(fruit("Orange", 15));
	pq.push(fruit("Grape", 25));
	cout << "按价格从高到低输出（Lambda）：" << endl;
	while (!pq.empty()) {
		fruit f = pq.top();
		cout << f.name << " : " << f.price << "元" << endl;
		pq.pop();
	}
	return 0;
}
/*
按价格从高到低输出（Lambda）：
Grape : 25元
Banana : 20元
Orange : 15元
Apple : 10元
*/
```



