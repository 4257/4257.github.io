---
title: "CLanguage Basics"
last_modified_at: 2023-07-10T09:42:02-10:00
categories:
  - Blog
tags:
  - Basics
  - CLanguage
  - Computer Science(CS)
---

## 数据类型
### 整数类型
类型(type)|存储大小|值范围
|--|--|--|
char|	1 字节|	-128 到 127 或 0 到 255
unsigned char	|1 字节|	0 到 255
signed char	|1 字节|	-128 到 127
int|	4 字节|	-32,768 到 32,767 或 -2,147,483,648 到 2,147,483,647
unsigned int	|4 字节	|0 到 65,535 或 0 到 4,294,967,295
short	|2 字节	|-32,768 到 32,767
unsigned short	|2 字节	|0 到 65,535
long|	4 字节	|-2,147,483,648 到 2,147,483,647
unsigned long	|4 字节|	0 到 4,294,967,295

在c语言中可以使用`sizeof(type)`函数来获得当前系统中不同类型正确的数据宽度  
在64位系统中，long类型和`char*`类型为8字节。

### 数据宽度
在计算机中，因为受到硬件的制约，数据是有长度限制的，我们一般称之为**数据宽度**，超出最多宽度的数据会被丢弃掉。  
|名称|图示|大小|
|:--:|:--:|:--:|
|位(BIT)|▋|1位|
|字节(BYTE)|▋▋▋▋▋▋▋▋|8位|
|字(WORD)|▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋|16位,2个字节|
|双字(DWORD)|▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋|32位,4字节,2字|

### 浮点类型  
类型|	存储大小|	值范围|	精度
|--|--|--|--|
float|	4 字节|	1.2E-38 到 3.4E+38	|6 位有效位
double|	8 字节|	2.3E-308 到 1.7E+308	|15 位有效位
long double|	16 字节|	3.4E-4932 到 1.1E+4932	|19 位有效位

下表中每一个`▋`表示1Bit,float和double在存储方式上遵从IEEE的规范  

|type|符号位|指数部分|尾数部分
|--|--|:--:|:--:|
float|▋(1Bit)|▋▋▋▋▋▋▋▋(8Bit)|▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋(23Bit)
double|▋(1Bit)|▋▋▋▋▋▋▋▋▋▋▋(11Bit)|▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋▋(52Bit)

**上述类型赋值**
精简写法|完整写法
|--|--|
char x  = 1;|char x  = (char)1
short x = 2;|short x = (short)2
int x = 3;|int x = (int)3;
double x = 3.5;|double x = (double)3.5;

### 指针类型

#### 宽度
>**带`*`类型的变量宽度永远是4字节、无论类型是什么，无论有几个`*`**  

`sizof(char*)`  `sizeof(short****)`  `sizeof(int*********)`  `sizeof(void****)`
#### 声明
>**带有`*`的变量类型的标准写法：变量类型`*`变量名**  
>**任何类型都可以带`*`加上`*`以后是新的类型**  
>**`*`可以是任意多个**  

`struct A* x;`  `int** x;`  `char*** x;`  `double**** x;`  
此时表示`x`为`sturcu A*`/`int**`/`char***`/`double****` 类型的变量
#### 赋值
带`*`类型赋值  
完整写法|
|--|
char* x = (char*)1;|
short* x = (short*)2;|	
int* x = (int*)3;	|
struct A* x = (struct A*)4|


#### 运算
**++运算**  
>**不带`*`类型的变量,`++`或者`--`,都是加1,或者减1**  
>**带`*`类型的变量,可以进行`++`,或者`--`的操作**  
>**带`*`类型的变量,`++`或者`--`新增(减少)的数量是去掉一个`*`后变量的宽度**  

```c
int main(){
  char* x = (char*)100;
  printf("初始值:%d\n",x);
  x++;
  printf("++运算:%d\n",x);
  x = (char*)100;//恢复初值
  x--;
  printf("--运算:%d\n",x);
}
//初始值:100
//++运算:101
//--运算:99
//如以上为`char**`类型结果则为
//初始值:100
//++运算:104
//--运算:96
```
**--运算**  
```c
int main(){
  int* x = (int*)100;
  printf("初始值:%d\n",x);
  x++;
  printf("++运算:%d\n",x);
  x = (int*)100;//恢复初值
  x--;
  printf("--运算:%d\n",x);
}
//初始值:100
//++运算:104
//--运算:96
//如以上为`int**`类型结果则为
//初始值:100
//++运算:104
//--运算:96
```
**+ -运算**  

> **带`*`类型的变量可以加、减一个整数，但不能乘或者除**  
> **带`*`类型变量与其他整数相加或者相减时:**  
>> **带`*`类型变量`+ N  =  `带`*`类型变量 + N`*`(去掉一个`*`后类型的宽度)**  
>> **带`*`类型变量`- N  =  `带`*`类型变量 - N`*`(去掉一个`*`后类型的宽度)**

```c
int main(){
  char* a = (char*)100;		
  short* b = (short*)100;		
  int* c = (int*)100;		

  a = a + 5;		
  b = b + 5;		
  c = c + 5;
  printf("A:%d B:%d c:%d",a,b,c);	
  
  a = (char*)100;//恢复初值
  b = (short*)100;//恢复初值
  c = (int*)100;//恢复初值
  
  a = a - 5;		
  b = b - 5;		
  c = c - 5;
  printf("A:%d B:%d c:%d",a,b,c);	
}
//A:105 B:110 C:120
//A:95 B:90 C:80
```
**求差值**
>**两个类型相同的带`*`类型的变量可以进行减法操作**  
>**想减的结果要除以去掉一个`*`的数据的宽度**  		
```c
int main(){
  char* a ;
  char* b ;

  a = (char*)200;
  b = (char*)100;

  int x = a - b;

  printf("X:%d\n",x);
}
//X:100
```
**比较**
>**带`*`的变量，如果类型相同，可以做大小的比较。**  
```c
int main(){
  char**** a ;	
  char**** b ;	

  a = (char****)200;	
  b = (char****)100;	

  if(a>b)	
    printf("1");
  else	
    printf("2");
}
```
**取地址**  
>**`&`是取地址符，类型是其后面的类型加一个`*`，任何变量都可以使用`&`来获取地址，但不能用在常量上。**
```c
char a = 10;
short b = 20;
int c = 30;

//&a 是char*类型
//&b 是short*类型
//&c 是int*类型

char* cp = &a;
short* sp = &b;
int* ip = &c;

```
**解引用**  
>**`*`在使用变量名时是解引用**  
* 带`*`类型的变量，可以通过在其变量前加`*`来获取其指向内存中存储的值
* 在带`*`类型的变量前面加`*`，类型是其原来的类型减去一个`*`
```c
int* px;
int** px2;
int*** px3;
int**** px4;

//*(px)  是int类型
//*(px2) 是int*类型
//*(px3) 是int**类型
//*(px4) 是int***类型
```
指针数组：数组元素为指针的数组

数组指针：指向数组地址的指针

**数组名是一个指向数组中第一个元素的常量指针。**  
例:`int arr[10];`  
`arr`是一个指向`&arr[0]`的常量指针

### 空类型
void类型  

## C语言理解
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

**常量指针:指针指向的内容是常量**  
```c
const int* p;	//const *p
int const* p;	//const *p
//能改变p的值(p = &a/&b/&c)，不能改变p所指向的地址的值,即*p的值。
```
**指针常量:指针本身是个常量,不能再指向其他地址**  
```c
int* const p = &x;	//* const p
//不能改变p的值，能改变p所指向的地址的值,即*p的值。
//C++中的引用类型，可以类比成指针常量
```
**指向常量的指针常量**  
```c
const int* const p = &x;    //const * const p
//既不能改变p的值，也不能改变p所指向的地址的值,即*p的值。
```