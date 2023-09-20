### PE相关
#### PE结构大小
```
#include <stdio.h>
#include <winnt.h>

int main(int argc, char const *argv[])
{   
    IMAGE_DOS_HEADER pd;
    IMAGE_NT_HEADERS pn;
    IMAGE_NT_HEADERS32 pn32;
    IMAGE_NT_HEADERS64 pn64;
    IMAGE_FILE_HEADER pf;
    IMAGE_OPTIONAL_HEADER po;
    IMAGE_OPTIONAL_HEADER32 po32;
    IMAGE_OPTIONAL_HEADER64 po64;

    printf("SIZEOF IMAGE_DOS_HEADER: %d\n",sizeof pd);
    printf("SIZEOF IMAGE_NT_HEADERS: %d\n",sizeof pn);
    printf("SIZEOF IMAGE_NT_HEADERS32: %d\n",sizeof pn32);
    printf("SIZEOF IMAGE_NT_HEADERS64: %d\n",sizeof pn64);
    printf("SIZEOF IMAGE_FILE_HEADER: %d\n",sizeof pf);
    printf("SIZEOF IMAGE_OPTIONAL_HEADER: %d\n",sizeof po);
    printf("SIZEOF IMAGE_OPTIONAL_HEADER32: %d\n",sizeof po32);
    printf("SIZEOF IMAGE_OPTIONAL_HEADER64: %d\n",sizeof po64);

    return 0;
}

SIZEOF IMAGE_DOS_HEADER: 64
SIZEOF IMAGE_NT_HEADERS: 248
SIZEOF IMAGE_NT_HEADERS32: 248
SIZEOF IMAGE_NT_HEADERS64: 264
SIZEOF IMAGE_FILE_HEADER: 20
SIZEOF IMAGE_OPTIONAL_HEADER: 224
SIZEOF IMAGE_OPTIONAL_HEADER32: 224
SIZEOF IMAGE_OPTIONAL_HEADER64: 240
```
### 代码空白区添加代码

使用反汇编工具打开一个程序 下断点 记录断点的位置  
E8 E9  
E8 E9实际指令长度是5  
call/jmp指令实际跳转的地址 = E8的下一条地址 + X  



```
SizeOfRawData 		在文件中对齐后的大小
VirtualSize     	是在内存中对齐前的大小 内存中真实的大小
PointerToRawData 	节在文件中的开始
VirtualAddress   	节在内存中的开始

复制SizeOfRawData大小


VirtualSize 如果在内存中拉伸后比在文件中对齐后的值大



imagebuffer to newbuffer

计算从内存到硬盘需要的大小
节在文件中的开始(PointerToRawData) + 最后一个节在文件中对齐后的大小(SizeOfRawData)

RAV=VA-ImageBase

FOA= PointerToRawData + (RAV-VirtualAddress)


计算需要添加代码的位置
先判断节的空间够不够存下ShellCode
(SizeOfRawData - VirtualSize) < ShellCode

添加代码的位置
ImageBuffer + VirtualAddress + VirtualSize
修正E8
需要执行的代码地址 - （ImageBase + （（E8 xx xx xx xx E9的地址） - pImageBuffer)）
需要执行的代码地址 - （ImageBase + （E8的下一条指令E9的地址相对偏移地址)）
需要执行的代码地址 - （ImageBase + （E8的下一条地址（需要自己清楚写的shellcode里面E8 E9的位置))）
修正E9
ImageBase + AddressOfEntryPoint - ImageBase + ShllCode
修正OEP

移动头和节表
删掉没有的dos_stub

扩大最后一个节
```
### 新增节
```
需要节+40字节的空间（也可以不为0 看情况）  
sizeofheader - 前面的所有已有数据的空间 > 80字节  
修改NumberOfSections  
修改SizeOfImage  
扩大的位置需要是对齐后的整数倍  
```
### 在64位软件中怎么添加shellcode？

### 合并节
```
变成一个节表
文件变大
改节表
```
### 导出表
```
打印导出表
16个目录结构
第一个是导出表在哪里
rva - foa
addressoffunctions 导出函数地址表 rva->真正的地址的值
addressofnames    导出函数的名称表 rav->真正的名字的值
addressofNameordinals 导出函数序号的表 真正的值需要加上Base
addressofnames和addressofNaneordinals的数量一样
```
两个函数 用函数名字找地址和用序号找地址
#### 按照名字导出
```
函数先在addressofnames表里面比对
比对一样的名字的索引直接找addressofNameordinals里面对应索引对应的值
再用值找addressoffunctions里面的函数地址
```
#### 按照序号导出
```
序号减去Base的值
得到的值做addressoffunctions的索引找函数对应的地址
```
#### 更直观的输出
```
按照函数地址表排序 并且过滤掉为0的项目
按照函数地址表的索引去找序号表里面值为X的项目 如果能找到的话 表示当前的这个函数是按照名字导出的 如果没有 则表示一定不是以名字导出的
再使用值为X的索引去函数名称表里面索引为X的名字
```
### 重定位表
```
数据目录的第六个结构 重定位表
通过将RVA转成FOA 获得重定位表再文件中的偏移
VirtualAddress    当前数据块 每一个低12为的值加上VirtualAddress才是真正需要修复的数据的RVA
SezeofBlock       当前块的总大小
Block(虚拟的属性)  所有重定位的值 单个为两字节 每个值高四位的值为3表示这个值需要修改 否则就是被填充的数据
```
### IAT表

### 导入表
#### 加载前
INT表和IAT表存的值完全一样 都以0作为结束符 存的都是RVA 指向IMAGE_IMPORT_BY_NAME
#### 加载后
加载后 操作系统会调用GetGrocAddr()函数 用INT表找到的函数地址写入到IAT表中 IAT表存的是真实的函数地址  
使用目录结构修复exe的导入表  
IMAGE_IMPORT_DESCRIPTOR -> OriginalFirstTunk ->IMAGE_THUNK_DATA32
判断值是不是1 是1则根据后31为编号去导出表查找函数名得到地址 添加到IAT表的相同位置

### ??!!
句柄就是内存中文件的地址  
GetGrocAddr(in HMODULE hModule,in LPCSTR lpProcName)函数  
用句柄和函数名或者函数导出序号去对应dll里面查找导出表里面存的对应函数的地址  
加载对应的dll获取句柄  

!!程序的虚拟地址空间：每个程序都有自己的虚拟地址空间，通常在32位系统中是4GB。  
这个4GB的虚拟地址空间包括了程序的代码、数据以及任何加载的DLL的代码和数据。程序可以使用这个虚拟地址空间来访问各种资源。  

!!DLL的虚拟地址空间：每个DLL也有自己的虚拟地址空间，但它并不是独立的4GB空间。  
DLL的虚拟地址空间是相对于加载它的程序的虚拟地址空间的一部分。  
当程序加载DLL时，DLL的代码和数据会被映射到程序的虚拟地址空间中的适当位置。  
这意味着DLL的虚拟地址空间的大小与它所属的程序的虚拟地址空间大小相同。  
DLL不能单独拥有独立的4GB虚拟地址空间，它与加载它的程序共享同一个地址空间。
