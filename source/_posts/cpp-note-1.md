---
title: C++ 学习笔记(一):命名空间、输入输出与缺省参数
date: 2026-09-22
tags: [C++, 入门, 基础语法]
categories: C++
---

## 前言

写 C 的时候,你一定碰到过这种尴尬:变量名跟库函数撞车,编译报「重定义」;`printf` 要自己记 `%d`、`%f` 一堆格式符,一不留神就写错。这些「坑」,C++ 都有对应的解法。

今天这篇笔记,带你搞清楚三件事:

1. **命名空间**:怎么让同名变量 / 函数各过各的,互不打架;
2. **输入输出**:`cout` / `cin` 为什么不用写格式符就能自动识别类型;
3. **缺省参数**:函数参数怎么能「不传也有默认值」。

每个知识点都配了能直接跑的代码和运行结果,照着敲一遍就能懂。

## 一、命名空间 namespace

### 1. 问题:名字撞车了

在 C 里,如果全局定义了一个叫 `rand` 的变量,而 `<stdlib.h>` 里已经有一个 `rand()` 函数,编译器直接报「重定义」:

```cpp
#include <stdio.h>
#include <stdlib.h> // 里面有 rand() 函数

int rand = 10; // 撞名了!

int main()
{
    printf("%d\n", rand);
    return 0;
}
```

运行结果(编译失败):

```text
error C2365: "rand": 重定义;以前的定义是"函数"
```

**根因**:C 里所有名字都在同一个全局「大房间」里,重名就冲突。

### 2. 解决:用 namespace 隔开

`namespace` 相当于给代码开了个独立的「房间」,房间里可以放变量、函数、类型,访问时用 `命名空间::成员`:

```cpp
#include <stdio.h>
#include <stdlib.h>

namespace bit
{
    int rand = 10;               // 变量
    int Add(int left, int right) // 函数
    {
        return left + right;
    }
    struct Node                  // 类型
    {
        struct Node* next;
        int val;
    };
}

int main()
{
    printf("%d\n", bit::rand);       // 访问 bit 房间里的 rand
    printf("%d\n", bit::Add(1, 2));  // 调用 bit 房间里的 Add
    struct bit::Node n;              // 用 bit 房间里的类型
    return 0;
}
```

运行结果:

```text
10
3
```

现在 `bit::rand` 和库里的 `rand()` 各在各的房间,不再冲突。

### 3. 同名也不怕:两个命名空间各放一个

同一个名字,放在不同的命名空间里就是两个不同的东西:

```cpp
#include <iostream>
using namespace std;

namespace first
{
    int var = 5;
}

namespace second
{
    double var = 3.1416;
}

int main()
{
    cout << first::var << endl;   // 5
    cout << second::var << endl;  // 3.1416
    return 0;
}
```

运行结果:

```text
5
3.1416
```

两个 `var` 同名,但因为住在不同的「房间」,互不干扰。

### 4. 作用域查找规则与 `::`

编译时查找名字的规则:**先在局部找,再到全局找**。`::` 是域作用限定符,左边不写命名空间时表示全局作用域:

```cpp
#include <stdio.h>

int rand = 10; // 全局变量

int main()
{
    int rand = 1;           // 局部变量
    printf("%d\n", rand);   // 局部优先,输出 1
    printf("%d\n", ::rand); // :: 指定全局,输出 10
    return 0;
}
```

运行结果:

```text
1
10
```

### 5. 嵌套 & 多文件合并

命名空间可以嵌套;多个文件里定义的**同名** namespace 在编译时会自动合并成一个。但同一个命名空间内不能有同名成员,否则照样报重定义:

```cpp
namespace bit
{
    namespace bit1 { struct Node { struct Node* next; int val; }; }
    namespace bit2 { struct Node { struct Node* next; struct Node* prev; int val; }; }
}

int main()
{
    struct bit::bit1::Node n1;
    struct bit::bit2::Node n2;
    return 0;
}
```

## 二、C++ 的输入输出

C 用 `printf` / `scanf`,要手写 `%d`、`%f`、`%s` 这些格式符;C++ 用 `cout` / `cin`,**自动识别类型**,不用记格式符:

```cpp
#include <iostream>

int main()
{
    int i = 10;
    double j = 11.11;

    std::cout << "hello world" << std::endl; // endl 是换行
    std::cout << i << std::endl;   // 整型直接输出
    std::cout << j << std::endl;   // 浮点直接输出
    std::cout << &i << std::endl;  // 连地址都能直接输出

    std::cin >> i;                 // 从键盘读一个数到 i
    return 0;
}
```

运行结果(前四行):

```text
hello world
10
11.11
0000002B1B4FF9B4
```

`&i` 是地址,每次运行会变。注意:用 `cout` / `cin` 要引入头文件 `<iostream>`(不再是 `<stdio.h>`)。

### using namespace std 的三种姿势

`std` 是 C++ 标准库的命名空间,`cout`、`cin`、`string` 都在里面。访问它们有三种写法:

| 写法 | 示例 | 优缺点 |
| --- | --- | --- |
| 全限定名 | `std::cout << x;` | 最安全,但每次都要写 `std::` |
| using 声明(推荐) | `using std::cout;` 后直接 `cout << x;` | 只引入要用的几个,兼顾方便与安全 |
| using 指令 | `using namespace std;` | 全展开最省事,但容易撞名,项目里不推荐 |

折中写法(第二种)长这样:

```cpp
#include <iostream>
using std::cout;
using std::endl;
using std::cin;

int main()
{
    int i = 10;
    cout << "hello world" << endl;
    cin >> i;
    return 0;
}
```

> 一句话记:头文件里**别**用 `using namespace`,源文件里**少用**,真要方便就用 `using std::cout` 这种折中。

## 三、缺省参数

缺省参数 = 给形参一个默认值。调用时**不传就用默认值,传了就用实参**:

```cpp
#include <iostream>
using namespace std;

void Func(int a = 0)
{
    cout << a << endl;
}

int main()
{
    Func();    // 不传参,用默认值
    Func(10);  // 传参,用实参
    return 0;
}
```

运行结果:

```text
0
10
```

### 全缺省与半缺省

- **全缺省**:所有参数都有默认值;
- **半缺省**:只有靠右的一部分参数有默认值。

```cpp
// 全缺省:三个参数都有默认值
void Func1(int a = 10, int b = 20, int c = 30) { ... }

// 半缺省:只有 b、c 有默认值,a 必须传
void Func2(int a, int b = 20, int c = 30) { ... }
```

**铁律**:缺省参数必须**从右往左连续**给,不能跳着给。`void Func(int a = 1, int b, int c = 3)` 就是错的——`a` 有默认值而 `b` 没有,中间断了,会有歧义。

## 四、踩坑记录

**坑 1:`rand` 重定义**

- 现象:全局写 `int rand = 10;` → 编译报 `error C2365: "rand": 重定义`
- 原因:和 `<stdlib.h>` 里的 `rand()` 函数撞名
- 解决:放进 `namespace` 隔离开,或换个不撞的名字

**坑 2:`Func2` 注释和定义对不上**

- 现象:我注释标的是「半缺省」,但定义写的是 `void Func2(int a = 10, int b = 20, int c = 30)`
- 原因:三个参数都有默认值,这其实是**全缺省**;真正的半缺省应该是 `void Func2(int a, int b = 20, int c = 30)`
- 教训:注释和定义要对得上,不然会误导自己(和看代码的人)

## 五、小结与下一步

| 知识点 | 一句话要点 |
| --- | --- |
| 命名空间 | 给代码开独立「房间」,`命名空间::成员` 访问;可嵌套、多文件同名自动合并 |
| 作用域查找 | 局部优先于全局;`::` 指定全局 |
| 输入输出 | `cout` / `cin` 自动识别类型,免去格式符 |
| using 三姿势 | 全限定最安全;`using std::cout` 折中;`using namespace std` 有冲突风险 |
| 缺省参数 | 不传用默认值;必须从右往左连续;分全缺省 / 半缺省 |

**下一步计划**:函数重载、引用、内联函数。这几个和缺省参数经常一起考,下一篇接着聊。

---

*如果有哪里写错了,欢迎在评论区指出,感谢阅读!*
