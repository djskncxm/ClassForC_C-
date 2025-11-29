---
title: 3.函数基础
type: docs
---

![bg](../../../cabin-2.jpg)

### 1. 什么是函数？
> [!NOTE]
> 函数就是一段可以重复调用的代码，用来完成一个独立的功能。

- 代码可复用
- 结构更清晰
- 易于调试
- 方便协作
- 可以给别人使用你自己写的函数，函数多了也就成库了

### 2. 函数的基本结构
```text
返回类型 函数名(参数列表) {
    // 函数体（要执行的代码）
}
```
比如:
```c
int add(int a, int b) {
    return a + b;
}
```
- 返回类型：int
    > 返回类型为 void 表示函数不返回值，此时不能写 return 表达式，只能写 return;    
    > 也可以考虑不写
- 函数名：add
- 参数列表：int a, int b
- 函数体：return a + b;

### 3. 函数声明
在C语言中我们可以先声明这个函数的原型结构,再实现这个函数具体内容,也可以直接在main前面实现出来这个函数   
声明操作我们一般会在头文件内进行声明,在源代码内实现,在主要C文件里面引入这个头文件,使用函数,最后一起链接编译
> 声明的目的是让编译器提前知道函数的参数类型，避免隐式声明导致类型错误
```c
int add(int, int);  // 声明

int main() {
    add(2, 3);
}

int add(int a, int b) {  // 定义
    return a + b;
}
```
### 4. 参数传递
```c
void test(int a) {
    a = 10;
}
```
> a 是形参，等于实参的“拷贝”，修改它不会影响外部变量

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
> 函数参数无法按值传递整个数组，参数永远会退化为指针   
> 在我们日常使用的时候我们通常会提前把这个数组的长度或者大小来进行计算,然后传递进入一个具体的值,这样函数内部就不用再判断是不是空了

### 5. 局部变量 vs 全局变量
- 局部变量
> 函数内部定义，只在函数内部可见  
```c
void test() {
    int a = 0;
}
```
- 全局变量
> 定义在函数外，任何地方能访问：
```c
int g = 0;

void test() {
    g++;
}
```

- static 函数局部变量
> static 局部变量会保持值，函数退出也不会消失
```c
void count() {
    static int c = 0;
    c++;
    printf("%d\n", c);
}
```
> 每次调用，计数会递增。

### 6. 函数指针
> 函数名其实是一个地址，可以用指针保存
```c
int add(int a, int b) { return a + b; }

int (*fptr)(int, int) = add;

int r = fptr(1, 2);  // 调用函数
```
> 函数是存放在内存某个地址上的一段代码  
> 函数名本质上就是这段代码的起始地址   
> 调用函数时，CPU 会把“下一条指令地址”压栈，然后跳到函数地址执行，执行完返回指令会回到原来的地方继续跑   

这个怎么理解 ?
- 声明一个指针 fptr，它指向一个返回值是 int、参数是两个 int 的函数  
- 把 add 这个函数的地址装进 fptr
> 函数指针在被放在调用语句里时，会被编译器自动当成 *fptr 使用  
> 所以 fptr(1,2) 和 (*fptr)(1,2) 完全一样  

所以直接使用fptr就可以

### 7. 内联函数 inline
> 提高性能，减少调用开销
```c
inline int add(int a, int b) {
    return a + b;
}
```
在我们的标准库`ctype.h`中就有这样的例子
```c
#if !defined(__BIONIC_CTYPE_INLINE)
#define __BIONIC_CTYPE_INLINE static __inline
#endif

__BIONIC_CTYPE_INLINE int isalpha(int __ch) {
  return (__ch >= 'A' && __ch <= 'Z') || (__ch >= 'a' && __ch <= 'z');
}

__BIONIC_CTYPE_INLINE int islower(int __ch) {
  return (__ch >= 'a' && __ch <= 'z');
}
```
在我们的ida里面反出来的是
```c
__int64 __fastcall caesar_cipher(__int64 result, int a2)
{
  unsigned __int8 v2; // w8
  unsigned __int8 v3; // [xsp+Fh] [xbp-11h]
  int i; // [xsp+10h] [xbp-10h]
  __int64 v6; // [xsp+18h] [xbp-8h]

  v6 = result;
  for ( i = 0; *(_BYTE *)(v6 + i); ++i )
  {
    v3 = *(_BYTE *)(v6 + i);
    result = sub_6C0BC(v3);
    if ( (_DWORD)result )
    {
      result = sub_6C134(v3);
      if ( (_DWORD)result )
        v2 = 97;
      else
        v2 = 65;
      *(_BYTE *)(v6 + i) = (v3 - v2 + a2) % 26;
      *(_BYTE *)(v6 + i) += v2;
    }
  }
  return result;
}
```
里面两个函数调用就是这两个函数,内联函数就是把这一段函数加到你的程序里面,不再跳转到对应的库内调用,这种写法多用于简单函数使用,上面这两个函数内容就非常非常的简单
```c
__int64 __fastcall sub_6C0BC(int a1)
{
  bool v2; // [xsp+4h] [xbp-Ch]
  char v3; // [xsp+8h] [xbp-8h]

  if ( a1 < 65 || (v3 = 1, a1 > 90) )
  {
    v2 = 0;
    if ( a1 >= 97 )
      v2 = a1 <= 122;
    v3 = v2;
  }
  return v3 & 1;
}
bool __fastcall sub_6C134(int a1)
{
  bool v2; // [xsp+8h] [xbp-8h]

  v2 = 0;
  if ( a1 >= 97 )
    return a1 <= 122;
  return v2;
}
```
各位可以对照一下,这两个的含义是完全一样的
> inline 只是给编译器的“建议”，最终是否真的内联由编译器决定

### 8. 递归函数
> 函数自己调用自己
```c
int fact(int n) {
    if (n <= 1) return 1;
    return n * fact(n - 1);
}
```
对于递归我们在写的时候要写好停止操作,避免无限递归下去,导致程序出错


## 总结
函数是程序中完成独立功能的一段可复用代码，本质上就是一段存放在内存里的指令，调用函数就是让 CPU 跳转到这段代码执行，再返回原来的位置继续运行。通过函数，我们可以把程序拆分成清晰的模块，便于调试、协作和复用；参数传递在 C 语言中默认是传值，数组会退化为指针，而函数指针则允许我们保存函数地址，实现回调或动态调用。内联函数可以把简单的函数直接嵌入调用点，提高性能，而递归函数则让函数自己调用自己，完成复杂的分治逻辑。局部变量、全局变量和 static 变量各自有不同的作用域和生命周期，保证程序结构清晰、状态可控。总之，理解函数的本质和使用方式，是掌握 C 语言程序设计的关键，也是理解底层程序执行流程的基础
