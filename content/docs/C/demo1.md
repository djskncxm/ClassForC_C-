---
title: demo案例1
type: docs
---
![bg](../../../dominik-mayer-1.jpg)

这篇文来讲一个小小的案例，来讲一下前面的东西

## 定义结构体
```c
#include <stdio.h>

typedef struct Student {
    char name[20];
    int age;
    char phone[20];
} Student;
```

## 我们使用这个结构体来存一些东西
```c
Student students[3] = {
    {"Alice", 18, "123456"},
    {"Bob",   19, "987654"},
    {"Cathy", 20, "555666"}
};
```
## 函数
这个函数的功能是  
接收一个结构体数组和数组的长度，通过循环来进行打印
```c
void print_students(Student *p, int count) {
    for (int i = 0; i < count; i++) {
        printf("Name: %s, Age: %d, Phone: %s\n",
               p[i].name, p[i].age, p[i].phone);
    }
}
```
这里面我们传进来的是students的首地址指针，需要长度来标记是否会过界   
> Student *p 既可以指向一个结构体，也可以指向结构体数组的“第一个结构体”   
> 在 C 语言里，“数组名”本质上就是“首元素的地址”，传进函数时自动退化为指针   

我们传递进来的结构体并不是指针，所以我们还是用`.`来进行访问

## 调用函数
接下来我们调用一下上面写的函数
```c
int main() {
    print_students(students, 3);
    return 0;
}
```

最终得到的结果就是输出出来数据内容
```text
Name: Alice, Age: 18, Phone: 123456
Name: Bob, Age: 19, Phone: 987654
Name: Cathy, Age: 20, Phone: 555666
```

## 使用指针
我们还可以把这个数组结构体内的东西拿出来
```c
Student *p = &students[0];
```
这时候我们就吧 "`Alice", 18, "123456"`单独拿出来了  
我们想通过p来访问里面的值就变成  
```c
printf("%s\n", p->name);
```

## 结构体指针函数

这点对初学来说也算比较进阶了，在结构体内加下面这一句，这就很像我们在类里面声明方法
```c
void (*print)(struct Student *self);
```
这个方法的实际执行是
```c
void print_student(Student *self) {
    printf("Name: %s, Age: %d\n", self->name, self->age);
}
```
我们在初始化结构体的时候直接传入这个函数
```c
Student stu = {"Alice", 18, print_student};
```
然后我们就可以
```c
stu.print(&stu); 
```
很想面向对象的写法
> 对于这点要谨记函数返回值，函数传参

各位可以读一下
```c
int *(*p)(int,int);
```
这个可以分析一下看看

### 全部代码为
```c
#include <stdio.h>

typedef struct Student
{
	char name[20];
	int age;
	char phone[20];
	void (*print)(struct Student *self);
} Student;

void print_students(Student *p, int count)
{
	for (int i = 0; i < count; i++) {
		printf("Name: %s, Age: %d, Phone: %s\n",
			p[i].name,
			p[i].age,
			p[i].phone);
	}
}

void print_student(Student *self)
{
	printf("Name: %s, Age: %d\n", self->name, self->age);
}

int main()
{
	Student students[3] = {
		{ "Alice", 18, "123456" },
		{ "Bob", 19, "987654" },
		{ "Cathy", 20, "555666" }
	};
	print_students(students, 3);

	Student *p = &students[0];
	p->print = print_student;
	p->print(p);
	return 0;
}
```
最终全部输出为
```text
Name: Alice, Age: 18, Phone: 123456
Name: Bob, Age: 19, Phone: 987654
Name: Cathy, Age: 20, Phone: 555666
Name: Alice, Age: 18
```
