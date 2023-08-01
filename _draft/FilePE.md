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
call/jmp指令实际跳转的地址 = E8的下一条地址 + X
E8 E9实际指令长度是5
call/jmp指令实际跳转的地址 = E8当前的地址 + 5 + x
