---
title: 4.结构体基础
type: docs
---
![bg](../../../car-wreck.png)

在我们逆向或者开发的过程中，结构体是不可缺少的一部分

### 1. struct 是什么？
`struct` 就是一块自定义的复合数据类型，里面可以放多个不同类型的变量，让你把数据组织在一起  
它有点像 C 把“类”拆成最基本的形态：没有方法，只有字段  
```text
struct StructName {
    type Name;
};
```

```c
struct Student {
    int id;
    char name[20];
    float score;
};
```
这个就是一个简单的结构体类型
### 2. 结构体变量的声明
```c
struct Student s1;
```
也可以直接定义的同时声明变量
```c
struct Student {
    int id;
    char name[20];
} s1, s2;
```
### 3. 访问结构体成员
结构体变量访问成员用`.`号，这点跟类里面一样
```c
s1.id = 10;
s1.name = "Alice";
s1.score = 98.5;
```

### 4. 结构体指针
```c
struct Student *p = &s1;

p->id = 20;       // 等价于 (*p).id
```
在结构体被声明成为函数的时候我们不用再使用`.`转而使用`->`，来进行访问
> 箭头运算符本质上就是 (*p).成员 的语法糖

### 5. typedef 简化结构体写法
你不想每次都写 struct Student？  
用 typedef:  
```c
typedef struct Student {
    int id;
    char name[20];
    float score;
} Student;
```
这时候我们使用就可以直接
```c
Student s;
```

### 6. 结构体的内存布局(***)
结构体内部成员在内存中是按定义顺序排列的，但会有对齐补齐(padding)
```c
struct A {
    char a;    // 1 byte
    int b;     // 4 bytes
    short c;   // 2 bytes
};
```
虽然你写成这样了，但是编译器会进行优化，时间情况可能是
```text
a (1) + padding(3) + b(4) + c(2) + padding(2) = 12 字节
```

### 7. 结构体嵌套
可以把一个结构体放进另一个:
```c
struct Point {
    int x;
    int y;
};

struct Rect {
    struct Point lt;   // 左上角
    struct Point rb;   // 右下角
};
```
使用时候我们可以
```c
struct Rect r;
r.lt.x = 10;
r.rb.y = 20;
```

结构体内的结构体也就是一个正常的属性字段，我们直接访问就行了

### 8. 结构体数组
结构体也能做数组
```c
struct Student arr[100];
arr[0].score = 99;
```
使用的时候和正常数组一样的，每个结构体的名字就是数组索引

### 9. 含指针的结构体
```c
struct Node {
    int value;
    struct Node *next;
};
```
这种写法在链表上非常非常的常见，自己包含自己，然后可以一直叠下去

### 10. 结构体传参
结构体可以按值传递(复制整块内容)
```c
void print(Student s);
```
但更常见的是传指针(避免复制)
```c
void print(Student *s);
```

### 11. 结构体初始化
```c
struct Student s = {1, "Bob", 95.5};
```
也可以部分初始化
> 初始化定值，不固定的可以到后面再进行初始化
```c
struct Student s = {.name="Tom"};
```

### 12. 结构体与内存（逆向常用）
```text
+0x00 id: int
+0x04 score: float
+0x08 name: char[20]
```
就是结构体内存布局。结构体本质上就是   
但是上面也说了，可能不一定是这样进行编译的，编译器可能会进行优化处理，所以实际逆向情况自己还是仔细阅读ida或者trace日志
```text
连续内存块 + 各成员偏移量
```


## 小结
C语言的结构体是一种把不同类型的数据组织在一起的复合类型，本质是连续的内存布局，通过成员偏移访问。它支持嵌套、数组、指针，用 . 和 -> 操作成员，可结合 typedef 简化类型名。结构体可能因对齐产生 padding，也可用 packed 控制无对齐。结构体广泛用于模块通信、二进制协议、系统编程与逆向分析，是理解内存与数据布局的重要基础工具
