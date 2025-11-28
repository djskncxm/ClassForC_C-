---
title: 3.函数基础
type: docs
---

函数参数中的等价性
```c 
// 以下三种函数声明完全等价：
void func(int arr[]);
void func(int arr[10]);  // 编译器忽略数组大小
void func(int *arr);     // 最真实的本质

// 调用时：
int myArray[5];
func(myArray);  // 传递的是指针，不是整个数组
```

