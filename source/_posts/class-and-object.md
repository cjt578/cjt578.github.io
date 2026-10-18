---
title: C++ 学习笔记(三):类和对象
date: 2026-10-14
tags: [C++]
categories: C++
---

## 前言

今天我们来学习C++的主要知识点：类和对象



### 一、类和对象的基本概念

#### 1.1 类的定义

**1.**

class为定义类的关键字，Stack为类的名字，{}中为类的主体，注意类定义结束时后面分号不能省

略。类体中内容称为类的成员：类中的变量称为类的属性或成员变量; 类中的函数称为类的方法或

者成员函数。

**2.**

为了区分成员变量，一般习惯上成员变量会加一个特殊标识，如成员变量前面或者后面加_ 或者 m

开头，注意C++中这个并不是强制的，只是一些惯例，具体看公司的要求。

**3.**

C++中struct也可以定义类，C++兼容C中struct的用法，同时struct升级成了类，明显的变化是

struct中可以定义函数，一般情况下我们还是推荐用class定义类。

**4.**

定义在类面的成员函数默认为inline。

下面我们来看具体的代码演示

```c++
#include<iostream>
using namespace std;
class Stack1
{
	void Init(int n = 4); //成员函数（此处我们把之前学习的缺省函数也用上了）
	void Push(int x);


	int* _a; //成员变量
	int _top;
	int _capacity;
};// 分号不能省略
```

但是我们的类还不是一个完整的类，因为我们还缺少访问限定符，下面我们就来了解一下

#### 1.2 访问限定符

**1.**

C++一种实现封装的方式，用类将对象的属性与方法结合在一块，让对象更加完善，通过访问权限

选择性的将其接口提供给外部的用户使用。

**2.**

public修饰的成员在类外可以直接被访问；protected和private修饰的成员在类外不能直接被访

问，protected和private是一样的，以后继承章节才能体现出他们的区别。

**3.**

访问权限作用域从该访问限定符出现的位置开始直到下一个访问限定符出现时为止，如果后面没有

访问限定符，作用域就到 }即类结束。

**4.**

class定义成员没有被访问限定符修饰时默认为private，struct默认为public。

**5.**

一般成员变量都会被限制为private/protected，需要给别人使用的成员函数会放为public



为了清晰的解释这些概念我们用一个简单的类来实现日常生活的年月日

```c++
 class Date
 {
 public:  //在类外可以直接被访问Init函数，通过传参来改变年月日
	void Init(int year, int month, int day)
	 {
	   _year = year;
	  _month = month;
	  _day = day;
	 }
     
     
	void print() //打印函数 
	{
		cout << _year << "/" << _month << "/" << _day << endl;
	}
     
 private: //在类外不可以访问
	 int _year; 
	 int _month;
	 int _day;
	 };

 int main()
 {
	 Date d; //与结构体类似
	 d.Init(2024, 3, 31);
     d.print();
	 return 0;
 }
```



```c++
 class Date
 {
 public:
	void Init(int year=2026, int month=10, int day=14)
	 {
	 _year = year;
	_month = month;
	_day = day;
	}

	void print()
	{
		cout << _year << "/" << _month << "/" << _day << endl;
	}

 private:
	 int _year; 
	 int _month;
	 int _day;
	 };

 int main()
 {
	 Date d;
	 d.Init();
	 d.print();
	 return 0;
 }
```

**输出结果：**

![](/img/cpp3-01.png)



下面我们增加一些难度，用类来模拟实现栈，栈(Stack)是一种数据结构,特点叫 **"后进先出"(LIFO)**,就像一摞盘子:

只能从**顶部**放东西(`Push`)和取东西(`Top`/弹出)   最后放进去的最先被拿出来

```c++
class Stack
{
public:
	// 成员函数
	void Init(int n = 4) //可以提前通过缺省参数设置栈的大小
	{
		array = (int*)malloc(sizeof(int) * n);
		if (nullptr == array)
		{
			perror("malloc申请空间失败");
			return;
		}
		capacity = n;
		top = 0;
	}
	void Push(int x) //插入函数
	{
		array[top++] = x; //x插入前array[top]位置后，使top++这些下一个位置
	}
	int Top() //返回栈顶元素
    {
	 return array[top - 1]; //因为top在栈顶元素的下一个位置，需要-1移动到栈顶元素位置
     }
    
	//void Destroy()此处是有关析构函数，会在后面解释
	// {
	  // free(array);
	  // array = nullptr;
	   //top = capacity = 0;
	// }

 private:
	 // 成员变量
	 int* array;      // 指向堆上申请的内存,用来存数据
	 size_t capacity; // 容量:最多能存几个元素
	 size_t top;      // 栈顶位置:当前有几个元素(也是下一个元素要放的下标)
}; // 分号不能省略

int main()
{
	Stack st;
	st.Init();
	st.Push(1);
	st.Push(2);
	cout << st.Top() << endl;
	//st.Destroy();
	return 0;
}
```

**输出结果：(遵循后进先出规则)**

![](/img/cpp3-02.png)



#### 1.3 类域

**1.**

类定义了一个新的作用域，类的所有成员都在类的作用域中，在类体外定义成员时，需要使用 :: 作

用域操作符指明成员属于哪个类域。(以上面的main 函数为例，在 `Stack` 类域**外面**,所以看不到 `top`、`Push` 这些名字,必须借助对象 `st` 用 `.` 才能访问。)

**2.**

类域影响的是编译的查找规则，下面程序中Init如果不指定类域Stack，那么编译器就把Init当成全

局函数，那么编译时，找不到array等成员的声明/定义在哪里，就会报错。指定类域Stack，就是知

道Init是成员函数，当前域找不到的array等成员，就会到类域中去查找

**3.**

类域的作用主要是两点:

1. **封装/隐藏**:类内部的 `private` 成员,类域外面看不到、碰不到,保护了数据。
2. 防止命名冲突:不同类里可以有同名的成员,互不干扰。

比如：

```c++
class A { public: int x; };
class B { public: int x; };

A a;
B b;
a.x = 1;   // 是 A 类域里的 x
b.x = 2;   // 是 B 类域里的 x,互不影响
```





**重点：声明和定义分离时，需要指定类域**

```c++
class Stack
{
public:
	// 成员函数
	void Init(int n = 4); //声明
	void Push(int x)
	{
		// ...扩容
		array[top++] = x;
	}
	int Top()
    {
	 return array[top - 1];
     }
	void Destroy()
	 {
	   free(array);
	   array = nullptr;
	   top = capacity = 0;
	 }

 private:
	 // 成员变量
	 int* array;
	 size_t capacity;
	 size_t top;
}; // 分号不能省略

void Stack::Init(int n) //定义(在类外面定义要标注类域)
{
	array = (int*)malloc(sizeof(int) * n);
	if (nullptr == array)
	{
		perror("malloc申请空间失败");
		return;
	}
	capacity = n;
	top = 0;
}

int main()
{
	Stack st;
	st.Init();
	st.Push(1);
	st.Push(2);
	cout << st.Top() << endl;
	st.Destroy();
	return 0;
}
```



如果声明和定义分离时，没有指定类域，则无法访问类里面的成员

![](/img/cpp3-03.png)



2. 实例化

#### 1.4 实例化概念

**1.**

用类类型在物理内存中创建对象的过程，称为类实例化出对象。

**2.**

类是对象进行一种抽象描述，是一个模型一样的东西，限定了类有哪些成员变量，这些成员变量只

是声明，没有分配空间，用类实例化出对象时，才会分配空间。(以上面的为例，我们定义的成员变量(  _year; 
	  _month;  _day;)只是声明，没有开空间)

**3.**

一个类可以实例化出多个对象，实例化出的对象 占用实际的物理空间，存储类成员变量。打个比

方：类实例化出对象就像现实中使用建筑设计图建造出房子，类就像是设计图，设计图规划了有多

少个房间，房间大小功能等，但是并没有实体的建筑存在，也不能住人，用设计图修建出房子，房

子才能住人。同样类就像设计图一样，不能存储数据，实例化出的对象分配物理内存存储数据。

(Date类实例化出对象d1和d2  ( Date d1;Date d2;)



#### 1.5 对象大小

**1.**

类实例化出的每个对象，都有独立的数据空间，所以对象中肯定包含成员变量，那么成员函数是否包含呢？

**2.**

首先函数被编译后是一段指令，对象中没办法存储，这些指令存储在一个单独的区域(代码段)，那么对象中非要存储的话，只能是成员函数的指针。再分析一下，对象中是否有存储指针的必要呢？

**3.**

Date实例化d1和d2两个对象，d1和d2都有各自独立的成员变量，year/_month/_day存储各自的数据，但是d1和d2的成员函数Init/Print指针却是一样的，存储在对象中就浪费了。如果用Date实例化100个对象，那么成员函数指针就重复存储100次，太浪费了。

**4.**

这里需要再额外哆嗦一下，其实函数指针是不需要存储的，函数指针是一个地址，调用函数被编译成汇编指

令[call 地址]， 其实编译器在编译链接时，就要找到函数的地址，不是在运行时找，只有动态多态是在

运行时找，就需要存储函数地址，这个我们以后会讲解。

上面我们分析了对象中只存储成员变量，而C++规定类实例化的对象也要符合内存对齐的规则。

**内存对齐规则：**

1.第一个成员在与结构体偏移量为0的地址处。

2.其他成员变量要对齐到某个数字（对齐数）的整数倍的地址处。(注意：对齐数 = 编译器默认的一个对齐数 与 该成员大小的较小值。VS中默认的对齐数为8)

3.结构体总大小为：最大对齐数（所有变量类型最大者与默认对齐参数取最小）的整数倍，如果嵌套了结构体的情况，嵌套的结构体对齐到自己的最大对齐数的整数倍处，结构体的整体大小就是所有最大对齐数（含嵌套结构体的对齐数）的整数倍。



```c++
class A
{
public:
	void Print()
	{
		cout << _ch << endl;
	}
private:
	char _ch;
	int _i;
};
class B
{
public:
	void Print()
	{
		//...
	}
};
class C
{
};
int main()
{
	A a;
	B b;
	C c;
	cout <<"A类实例化的大小:"<< sizeof(a) << endl;
	cout <<"B类实例化的大小:"<<sizeof(b) << endl;
	cout <<"C类实例化的大小:"<< sizeof(c) << endl;
	return 0;
}
```



**输出结果：**

 ![](/img/cpp3-04.png)



解析：

`char _ch`(1 字节)+ `int _i`(4 字节),加起来 5 字节,但结果是 **8**。原因是**内存对齐**:_ch放在偏移量为0的地址处，__i与编译器默认对其树8比较取较小值4，所以放在偏移量为0的地址处，中间空出三个位置，1+3+4=8.

`B` 和 `C` 都没有成员变量,但 `sizeof` 是 **1**,不是 0。原因:**如果空类大小是 0,那创建两个对象时,它们的地址会一模一样,无法区分**。所以 C++ 规定空类至少占 1 字节,保证每个对象有独立的地址。



#### 1.6 this 指针

**1.**

Date类中有 Init 与 Print 两个成员函数，函数体中没有关于不同对象的区分，那当d1调用Init和

Print函数时，该函数是如何知道应该访问的是d1对象还是d2对象呢？那么这里就要看到C++给了

一个隐含的this指针解决这里的问题

**2.**

编译器编译后，类的成员函数默认都会在形参第一个位置，增加一个当前类类型的指针，叫做this

指针。比如Date类的Init的真实原型为， void Init(Date* const this, int year,int month, int day)

**3.**

类的成员函数中访问成员变量，本质都是通过this指针访问的，如Init函数中给_year赋值， this-

\>_year = year;

**4.**

C++规定不能在实参和形参的位置显示的写this指针(编译时编译器会处理)，但是可以在函数体内显

示使用this指针。



### 二、类的默认成员函数

#### 2.1 默认成员函数概述

默认成员函数就是用户没有显式实现，编译器会自动生成的成员函数称为默认成员函数。一个类，我

们不写的情况下编译器会默认生成以下6个默认成员函数，需要注意的是这6个中最重要的是前4个，最

后两个取地址重载不重要，我们稍微了解一下即可。其次就是C++11以后还会增加两个默认成员函数，

移动构造和移动赋值，这个我们后面再讲解。默认成员函数很重要，也比较复杂，我们要从两个方面

去学习：

第一：我们不写时，编译器默认生成的函数行为是什么，是否满足我们的需求。

第二：编译器默认生成的函数不满足我们的需求，我们需要自己实现，那么如何自己实现？



#### 2.2 构造函数

构造函数是特殊的成员函数，主要任务是对象实例化时初始化对象。构造函数的本质是要替代我们以前Stack和Date类中写的Init函数的功能，构造函数自动调用的特点就完美的替代的了Init。

构造函数的特点：

1. 函数名与类名相同。
2. 无返回值。
3. 对象实例化时系统会自动调用对应的构造函数。
4. 构造函数可以重载。
5. 如果类中没有显式定义构造函数，则C++编译器会自动生成一个无参的默认构造函数，一旦用户显式定义编译器将不再生成。
6. 无参构造函数、全缺省构造函数、我们不写构造时编译器默认生成的构造函数，都叫做默认构造函

数。但是这三个函数有且只有一个存在，不能同时存在。无参构造函数和全缺省构造函数虽然构成

函数重载，但是调用时会存在歧义。要注意很多同学会认为默认构造函数是编译器默认生成那个叫

默认构造，实际上无参构造函数、全缺省构造函数也是默认构造，总结一下就是"不传实参就可以调

用的构造就叫默认构造"。

7. 我们不写，编译器默认生成的构造，对内置类型成员变量的初始化没有要求，也就是说是是否初始

化是不确定的，看编译器。对于自定义类型成员变量，要求调用这个成员变量的默认构造函数初始

化。如果这个成员变量，没有默认构造函数，那么就会报错，我们要初始化这个成员变量，需要用

初始化列表才能解决，初始化列表，我们下个章节再细细讲解。

```c++
class Date
{
public:
	// 无参构造函数
	 Date()  //函数名与类名相同，无返回值
	 {
		_year = 1;
		_month = 1;
		_day = 1;
	 }
	// 带参构造函数
	 Date(int year, int month, int day)  ////函数名与类名相同，无返回值
	{
		_year = year;
		 _month = month;
		 _day = day;
	 }
    void Print()
    {
	 cout << _year << "/" << _month << "/" << _day << endl;
    }
 private:
	 int _year;
	 int _month;
	 int _day;
	 };

 int main()
 {
	 
	 Date d1；  // 调用默认构造函数
	 Date d2(2025, 1, 1); // 调用带参的构造函数
     
	 // 注意：如果通过无参构造函数创建对象时，对象后面不用跟括号，否则编译器无法
     // 区分这里是函数声明还是实例化对象
	 d1.Print();
	 d2.Print();
	 return 0;
	}

```

// 无参构造函数和全缺省构造函数不能同时存在，要不就会报错“Date::Date”: 对重载函数的调用不明确“

**输出结果：**

![](/img/cpp3-05.png)

```c++
class Date
{
public:
	
     //全缺省构造函数 （可以同时满足上面两种函数的作用）
    Date(int year = 1, int month = 1, int day = 1)
	 {
	 _year = year;
	 _month = month;
	 _day = day;
	 }
	
    void Print()
    {
	 cout << _year << "/" << _month << "/" << _day << endl;
    }
 private:
	 int _year;
	 int _month;
	 int _day;

};

 int main()
 {
	 Date d3(2026,10,15);
     Date d4; //不传参就使用设定好的缺省参数
	 d3.Print(); 
     d4.Print(); 
	 return 0;
}

```

**输出结果:**

![](/img/cpp3-06.png)





 两个Stack实现队列

```c++
class Stack
{
public:
	Stack(int n = 10) //
	{
		_arr = (int*)malloc(sizeof(int) * n);
		if (_arr == nullptr)
		{
			perror("申请失败");
		}
		_capacity = n;
		_top = 0;

	}
	

private:
	int* _arr;
	int _capacity;
	int _top;

};

class MyQueue
{
public:
//..................
    
//编译器默认生成MyQueue的构造函数调用了Stack的构造，完成了两个成员的初始化
private:
	Stack push;
	Stack pop;

};

int main()
{
	MyQueue mq;
}
```





#### 2.3 析构函数

析构函数与构造函数功能相反，析构函数不是完成对对象本身的销毁，比如局部对象是存在栈帧的，

函数结束栈帧销毁，他就释放了，不需要我们管，C++规定对象在销毁时会自动调用析构函数，完成对

象中资源的清理释放工作。析构函数的功能类比我们之前Stack实现的Destroy功能，而像Date没有

Destroy，其实就是没有资源需要释放，所以严格说Date是不需要析构函数的。

析构函数的特点：

1. 析构函数名是在类名前加上字符 ~。
2. 无参数无返回值。 (这里跟构造类似，也不需要加void)
3. 一个类只能有一个析构函数。若未显式定义，系统会自动生成默认的析构函数。
4. 对象生命周期结束时，系统会自动调用析构函数。
5. 跟构造函数类似，我们不写编译器自动生成的析构函数对内置类型成员不做处理，自定类型成员会

调用他的析构函数。

6. 还需要注意的是我们显示写析构函数，对于自定义类型成员也会调用他的析构，也就是说自定义类

型成员无论什么情况都会自动调用析构函数。7. 如果类中没有申请资源时，析构函数可以不写，直接使用编译器生成的默认析构函数，如Date；如果默认生成的析构就可以用，也就不需要显示写析构，如MyQueue；但是有资源申请时，一定要自己写析构，否则会造成资源泄漏，如Stack。

8. 一个局部域的多个对象，C++规定后定义的先析构。



```c++
class Date
{
public:
	Date(int year = 1, int month = 1, int day = 1)
	{
		_year = year;
		_month = month;
		_day = day;
	}


	Date(const Date& d)
	{
		_year = d._year;
		_month = d._month;
		_day = d._day;
	}

	void print()
	{
		cout << _year << "/" << _month << "/" << _day << endl;
	}

	~Date()
	{
		cout << this << "~Date()" << endl;
		//后定义的会先析构
	}

public:
	int _year;
	int _month;
	int _day;

};
int main()
{
	Date d1;
	Date d2(2026, 10, 13);
	//const Date d2(2026, 10, 13);
	d1.print();
	d2.print();
}
```



![](/img/cpp3-07.png)






















#### 2.4 拷贝构造函数

拷贝构造函数：只有单个形参，该形参是对本类类型对象的引用（一般用 const 修饰），在用已存在的类类型对象创建新对象时由编译器自动调用。

它也是特殊的成员函数，特征如下：

1. 拷贝构造函数是构造函数的一个重载形式。
2. 参数只有一个且**必须是类类型对象的引用**。如果写成传值 `Date(Date d)`，编译器直接报错——因为传值本身就要调用拷贝构造，会无限递归下去。
3. 若未显式定义，编译器会生成默认的拷贝构造函数。默认版本对内置类型成员完成按字节的值拷贝（浅拷贝），对自定义类型成员则调用它自己的拷贝构造函数。
4. C++ 规定：自定义类型对象的拷贝行为必须调用拷贝构造。所以传值传参、传值返回都会调用拷贝构造——实参传给形参时调一次，返回时用返回值拷贝构造一个临时对象时再调一次。这也是传值传参效率低的原因。
5. 典型调用场景：用已存在对象创建新对象、函数参数为类类型对象（传值）、函数返回值为类类型对象（传值）。

拿我们的 Date 类写一遍：

```c++
class Date
{
public:
	Date(int year = 1, int month = 1, int day = 1)
	{
		_year = year;
		_month = month;
		_day = day;
	}

	// 拷贝构造：参数必须是本类对象的引用
	Date(const Date& d)
	{
		_year = d._year;
		_month = d._month;
		_day = d._day;
	}

	void print()
	{
		cout << _year << "/" << _month << "/" << _day << endl;
	}

private:
	int _year;
	int _month;
	int _day;
};

int main()
{
	Date d1(2026, 8, 3);
	Date d2(d1);      // 拷贝构造：用已存在对象 d1 创建新对象 d2
	Date d3 = d1;     // 同样是拷贝构造，不是赋值！

	d1.print();
	d2.print();
	d3.print();
	return 0;
}
```

这里有个最容易混的点：`Date d3 = d1;` 虽然写成了 `=`，但 d3 是**新创建**的对象，所以走的是拷贝构造函数，不是赋值运算符重载。判断依据很简单——**看左边那个对象是不是刚创建的**。

那什么时候必须自己写拷贝构造？看这个坑：

```c++
class Stack
{
public:
	Stack(int n = 4)
	{
		_a = (int*)malloc(sizeof(int) * n);
		_top = 0;
		_capacity = n;
	}

	~Stack()
	{
		free(_a);
	}

private:
	int* _a;
	int _top;
	int _capacity;
};

int main()
{
	Stack s1;
	Stack s2(s1);   // 用编译器默认生成的拷贝构造
	return 0;
}
```

默认拷贝构造把 `s1._a` 这个**指针值**原样抄给了 `s2._a`，于是两个对象指向同一块堆内存。函数结束析构时，s2 先 `free` 一次，s1 再 `free` 一次，同一块内存被释放两次，程序直接崩溃（double free）。

所以我们得到一个结论：**类里只要申请了资源（堆内存、文件句柄等），就必须自己写拷贝构造，做深拷贝**；像 Date 这种成员全是 int 的类，用编译器默认的完全够用。

那自己写的深拷贝长什么样？把上面 Stack 的拷贝构造函数补齐就是：

```c++
typedef int STDataType;
class Stack
{
public:
	Stack(int n = 10)
	{
		_a = (int*)malloc(sizeof(int) * n);
		if (_a == nullptr)
		{
			perror("申请失败");
		}
		_capacity = n;
		_top = 0;
	}

	Stack(const Stack& s) //深拷贝
	{
		_a = (int*)malloc(sizeof(int) * s._capacity);
		if (_a == nullptr)
		{
			perror("申请失败");
			return;
		}
		memcpy(_a, s._a, sizeof(int) * s._top);
		_top = s._top;
		_capacity = s._capacity;
	}

	void push(STDataType x)
	{
		if (_top == _capacity)
		{
			//如果满了就扩容
			int newcapacity = _capacity * 2;
			STDataType* tmp = (STDataType*)realloc(_a, newcapacity* sizeof(STDataType));
			if (tmp == nullptr)
			{
				perror("relloc失败");
				return;
			}
			_a = tmp;
			_capacity = newcapacity;
		}
		_a[_top++] = x;
	}


	~Stack()
	{

		cout << this << "~Stack" << endl;
		if (_a)
		{
			free(_a);
			_a = nullptr;
			_top = _capacity = 0;
		}
	}


private:

	int* _a;
	int _top;
	int _capacity;
};

int main()
{
Stack s1;
Stack s2(s1);   // 调用深拷贝：两个对象的 _a 指向各自的内存
}
```



![](/img/cpp3-08.png)


#### 2.5 赋值运算符重载

运算符重载是具有特殊名字的函数，名字是 `operator` 后面接要定义的运算符，同样有返回值类型、参数列表和函数体。

几个需要注意的点：

1. 不能通过连接其他符号创造新的运算符，比如 `operator@` 就不行。
2. 重载的运算符必须有一个类类型参数，不能改变内置类型运算符的含义（内置的 `int +` 永远是把两个数相加）。
3. 作为类成员函数重载时，形参看起来比操作数少一个，因为成员函数有一个隐藏的 `this`。
4. 有 5 个运算符不能重载：`. `、`.*`、`::`、`sizeof`、`?:`（这是选择题常考点）。

赋值运算符重载的特征：

1. 参数建议写成 `const T&`，既能避免传值拷贝，又能接住 const 对象。
2. 返回值建议写成 `T&`，返回 `*this`。如果返回类型写成 `T`（传值返回），返回值是个临时对象，连续赋值 `d3 = d1 = d2` 之后，临时对象销毁，拿到的就成了野引用；返回引用才能串起来。
3. 赋值前先判断 `this != &d`，防止自己给自己赋值。自己赋值本身不会出错，但养成检查的习惯能避开很多隐藏问题。
4. 如果不显式实现，编译器会生成一个默认的赋值运算符重载，同样是按字节的值拷贝（浅拷贝）。和拷贝构造一样，**申请了资源的类必须自己实现深拷贝**。





```c++
class Date  ///以下代码放在Date.h
{
public:
	Date(const Date& d)  //拷贝构造
	{	
		_year = d._year;
		_month = d._month;
		_day = d._day;
	}

	//类里面直接定义的，默认是inline的，编译器会把函数体直接替换到调用的地方
	void print()
	{
		cout << _year << "/" << _month << "/" << _day << endl;
	}

	bool operator==(const Date& d);
	bool operator>(const Date& d);

	bool operator>=(const Date& d);
	bool operator<(const Date& d);

	bool operator<=(const Date& d);
	bool operator!=(const Date& d);

	Date&  operator+=(int day);
	Date  operator+(int day);

	Date& operator-=(int day);
	Date  operator-(int day);

	//++d
	Date&  operator++();
	//d++
	Date  operator++(int);

	//--d
	Date  operator--();
	//d--
	Date  operator--(int);

	int operator - (const Date& d);

	//d1=d2  this就是d1，d就是d2
	Date&  operator=(const Date& d)
	{
		//赋值重载
		if (this != &d)
		{
			_year = d._year;
			_month = d._month;
			_day = d._day;
		}
		return *this;
	}
	//不写，用编译器默认生成的赋值运算符重载函数，编译器会自动生成一个赋值运算符重载函数(浅拷贝)

	//Date*this
	//取地址运算符重载
	Date* operator&()
	{
		return this;
	}

	//const Date*this  //构成函数重载
	const Date* operator&()const
	{
		return this;
	}


private:
	int _year;
	int _month;
	int _day;

};


```

```c++
#include"Date.h"  //以下代码放在Date.cpp

Date::Date(int year, int month, int day)
{
	_year = year;
	_month = month;
	_day = day;
}

	bool Date::operator==(const Date & d) //需要指明类域，d1=d2,d就是d2
	{
		return _year == d._year && _month == d._month && _day == d._day;
	}

	bool Date::operator>(const Date & d)
	{
		if (_year > d._year)
		{
			return true;
		}
		else if (_year == d._year)
		{
			if (_month > d._month)
			{
				return true;
			}
			else if (_month == d._month)
			{
				return _day > d._day;
			}
		}
		return false;
	}
   bool Date::operator>=(const Date& d) //this指针
   {
    	return *this > d || *this == d;
    }

	bool Date::operator<(const Date& d)
	{
		//不是大于等于就是小于
		return !(*this >= d);
	}

	bool Date::operator<=(const Date& d)
	{
        //不是小于等于就是大于
		return!(*this > d);
	}
	bool Date::operator!=(const Date& d)
	{
        //不是等于就是不等于
		return!(*this == d);
	}



void TestDate()
{
	Date d1(2026, 8, 3);
	Date d2(2026, 10, 13);

	d1.print();
	d2.print();

	Date d3(d1);

	cout << (d1 < d2) << endl;
	cout << (d1 > d2) << endl;
	cout << (d3 == d1) << endl;
	cout << (d3 != d1) << endl;
}
int main()
{
    TestDate();
}

```



![](/img/cpp3-09.png)







```c++
int GetMonthDay(int year,int month )
{
    //获取具体的年月日
	int monthDayArr[13] = { 0, 31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31 };
	if (month == 2 && (year % 400 == 0 || year % 4 == 0 && year % 100 != 0))
	{
		return 29;
	}
	return  monthDayArr[month];
}

Date& Date::operator+=(int day)
{
	if (day < 0)
	{
		return *this -= -day;
	}

	_day += day;
	while (_day> GetMonthDay(_year, _month))
	{
		_day -= GetMonthDay(_year, _month);
		++_month;
		if (_month >= 13)
		{
			++_year;
			_month = 1;
		}
	}
	return *this;
}


Date Date::operator+(int day)
{
	Date tmp(*this);
	tmp += day;
	return tmp;
}
 

Date& Date::operator-=(int day)
{

	if (day < 0)
	{
		return *this += -day;
	}

	 _day-=day;
	 while (_day < 1)
	 {
		 //向上个月借,不够就向上一年借
		 --_month;
		 if (_month == 0)
		 {
			 --_year;
			_month = 12;
		 }
	_day += GetMonthDay(_year,_month);
	 }
	return *this;
}


Date  Date::operator-(int day)
{
	Date tmp(*this);
	tmp -= day;
	return tmp;
}

Date& Date::operator++()
{
	*this += 1;
	return *this;
}


Date Date:: operator++(int)
{
	Date tmp(*this);
	*this += 1;
	return tmp;
}


Date  Date::operator--()
{
	*this -= 1;
	return *this;
}

Date  Date::operator--(int)
{   
	Date tmp(*this);
	*this -= 1;
	return tmp;
}


//d1-d2
int Date::operator-(const Date& d)
{
    //相差的天数
	//假设d1>d2
	Date max = *this;
	Date min = d;
	int flag = 1;
	if (*this < d)
	{
		max = d;
		min = *this;
		flag = -1;
	}
	int n = 0;
	while (min != max)
	{
		//差了多少天就循环多少次
		++min;
		++n;
	}
	return n*flag;
}



void TestDate2()
{
	Date d1(2026, 8, 3);
	Date d2(2026, 8, 7);

	Date d3 = d2 - 100;
	d3.print();

	d2 -= 100;
	d2.print();

	Date d4 = d2 + 100;
	d4.print();

	d2 += 100;
	d2.print();


	Date d5 = d2 + 2000;
	d5.print();

	Date d6 = d5 - 2000;
	d6.print();

	Date d7 = d6 + -2000;
	d7.print();

	cout << (d2 - d1) << endl;
	cout << (d1 - d2) << endl;

}

int main()
{
    TestDate2();
}
```



![](/img/cpp3-10.png)

| | 拷贝构造 | 赋值运算符重载 |
|---|---|---|
| 左边对象 | **新创建**的对象 | **已存在**的对象 |
| 写法 | `Date d2(d1);` 或 `Date d2 = d1;` | `d1 = d2;` |
| 本质 | 用一个对象初始化另一个对象 | 两个已有对象之间拷贝数据 |

#### 2.6 取地址运算符重载

这两个默认成员函数最简单，一般情况下完全不需要自己写，编译器会默认生成。只有一种场景要自己写：你不想让外部函数拿到对象的真实地址（比如单例、调试用具，或者某些刻意隐藏实现的设计），才会重载 `&` 返回一个假地址。

```c++
// 普通对象取地址
Date* operator&()
{
	return this;
}

// const 对象取地址，与上面构成函数重载
const Date* operator&() const
{
	return this;
}
```

测试代码就是我们的 TestDate4：

```c++
void TestDate4()
{
	const Date d1(2026, 8, 3);
	Date d2(2026, 10, 13);

	cout << &d1 << endl;   // const 对象，调用带 const 的版本
	cout << &d2 << endl;   // 普通对象，调用不带 const 的版本
}
```

注意 `operator&() const` 末尾那个 const——它修饰的是隐藏的 `this` 指针的类型，表示 this 指向的对象不能被修改。带 const 和不带 const 的两个 `&` 重载构成函数重载，const 对象会自动匹配带 const 的那一个。

（这里的 const 成员函数机制，后面讲 const 成员变量和 const 成员函数那一节会展开。）

#### 2.7 本节总结

六个默认成员函数到此过完一遍，串起来记：

| 默认成员函数 | 需不需要自己写 | 一句话记住 |
|---|---|---|
| 1. 构造函数 | 需要定制初始化时写 | 全缺省构造也算默认构造 |
| 2. 析构函数 | 有资源要释放时写 | 一个类只能有一个 |
| 3. 拷贝构造函数 | 类里申请了资源时必须写 | 参数必须是引用，否则无限递归 |
| 4. 赋值运算符重载 | 类里申请了资源时必须写 | 返回 `*this` 的引用，先判自赋值 |
| 5. 取地址重载 | 基本不写 | `operator&()` |
| 6. const 取地址重载 | 基本不写 | `operator&() const`，与前者重载 |

两条贯穿全篇的规律：

1. **编译器默认生成的版本，对内置类型一律是浅拷贝**。所以只要类里出现了 `new`、`malloc`、文件句柄这类资源，构造函数、析构函数、拷贝构造、赋值重载这"四大金刚"就要成套地自己写。
2. **构造/析构负责生命周期，拷贝构造/赋值负责数据搬运**。前者自动被调用，后者只在特定语句里被调用，两者不能互相替代。
