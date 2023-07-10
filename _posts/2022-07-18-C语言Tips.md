---
title: "C语言Tips"
last_modified_at: 2023-07-10T15:42:02-10:00
toc: true
categories:
  - Blog
tags:
  - Basics
  - C
  - Computer Science(CS)
---

## C语言Tips
### C语言五个区  
* 栈区：存放函数的形参和局部变量
* 堆区：malloc、calloc、new所申请的内存
* 静态存储区：存放全局变量和静态(包括静态全局变量与静态局部变量)，初始化的全局变量和静态局部变量放在一块，未初始化的放在另一块
* 常量区：常量统一在运行是被创建，常量是只读的，常量区也在静态存储区
* 代码区：存放程序的二进制代码

```c
int*a = 0;//全局初始化区
char* p1;//全局未初始化区
main(){
	int b;				//栈
	char s[] = "abc";		//栈
	char* p2;			//栈
	char* p3 = "123456";		//123456\0在常量区,p3在栈上
	static int c = 0;		//全局静态初始化区
	p1 = (char*)malloc(10);
	p2 = (char*)malloc(20);		//分配的10和20字节区域在堆区
	strcpy(p1,"123456");		//123456\0放在常量区,编译器可能会将p1与p3所指向的常量字符串优化成一个地方
}
```
### CONST
#### const修饰局部变量
* 经过const修饰的变量，在定义的时候，就要进行初始化。const修饰之后只能以右值使用。
* const表示将修饰的变量变为"只读"
```c
const int i = 0;
int const x = 1;
```
表示变量`i`和`x`的值不能被改变

```c
char* str ="Hello World";
```
此时,"Hello World"处于常量区,常量区不允许修改,如果尝试修改字符串的值,例如:`str[0] = 'c';`  
将会导致程序异常,可以加上const修饰符:`const char* str ="Hello World";`,防止`str`变量被修改

#### const修饰指针
* const修饰的指针类型,在忽略变量类型之后,看`const`右边紧跟的是`*`还是变量名,  
	* 是`*`表示`*p`不能被修改,`p`能修改  
	* 是`p`表示`p`不能被修改,`*p`能修改  

##### 常量指针:指针指向的内容是常量
```c
const int* p;	//const *p
int const* p;	//const *p
//能改变p的值(p = &a/&b/&c)，不能改变p所指向的地址的值,即*p的值。
```
##### 指针常量:指针本身是个常量,不能再指向其他地址
```c
int* const p = &x;	//* const p
//不能改变p的值，能改变p所指向的地址的值,即*p的值。
//C++中的引用类型，可以类比成指针常量
```
##### 指向常量的指针常量
```c
const int* const p = &x;    //const * const p
//既不能改变p的值，也不能改变p所指向的地址的值,即*p的值。
```
