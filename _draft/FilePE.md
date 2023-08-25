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
### 重定位表
```
数据目录的第六个结构 重定位表
```
