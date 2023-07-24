## 通用寄存器
64位寄存器|低32位|低16位|高/低8位
|--|--|--|--|
rax|eax|ax|ah/al|
rbx|ebx|bx|bh/bl|
rcx|ecx|cx|ch/cl|
rdx|edx|Dx|dh/Dl|
rsi|esi|si|sil|
rdi|edi|di|dil|
rbp|ebp|bp|bpl|
rsp|esp|sp|Spl|
### 汇编指令
语法构成：command(指令) dst(目的操作数),src(源操作数)
* dst和src单独可以为8、16、32、64位的寄存器、立即数、内存地址
* 需要保证dst和src的数据宽度一致
* 但是dst和src不能同时为内存地址 
#### mov
* 将src的值给dst,值保存在dst中
#### sub
* dst减去src,值保存在dst中
#### add
* src加上dst,值保存在dst中
#### movsd movsw movsb
* 将esi指定的内存地址的值传送给edi指定的内存地址,指令执行后,df位为0则两个寄存器根据数据宽度自增,为1则寄存器根据数据宽度自减.
#### stosd stosw stosb
* 将al/ax/eax的值存储到edi指定的内存地址中
#### rep
* 循环执行movs/stos命令,执行次数为ecx的值
#### push
* 向堆栈中压入数据,esp指针提升(sub),提升多少取决于压入的数据的数据宽度
#### pop
* 将堆栈中的数据释放,esp指针下降(add),下降多少取决于释放的数据的数据宽度
#### jmp
* 修改eip的的值为指定的指令地址
#### call
* call指令会修改eip的值,并将其下一条指令压入栈中,esp指针提升(sub)
####  ret
* 将当前栈顶指针的值给eip,esp指针下降(add)
#### jcc
* 指令太多且与标志寄存器挂钩,随用随查
#### lea
* 取地址指令,将src的内存地址给dst
#### leave	
* leave指令等于恢复堆栈的指令
```asm
mov esp,ebp
pop ebp
```
#### enter
```asm
push ebp
mov ebp,esp
```
### 参数的传递
C语言实参的传递其实是值的传递  
C语言函数调用约定方式  
调用约定|参数压栈顺序|平衡堆栈
|---|---|---|
__cdecl|从右至左入栈|调用者清理堆栈|
__stdcall|从右至左入栈|自身清理堆栈|
__fastcall|ecx/edx传送前两个|自身清理堆栈|

剩下:从右至左入栈	

### 编译器
#### gcc
##### gcc编译器流程
```
//常用命令
-E         Preprocess only; do not compile, assemble or link.
-S         Compile only; do not assemble or link.
-c         Compile and assemble, but do not link.
-o <file>  Place the output into <file>.
```
###### 预处理阶段
在预处理阶段 预处理器`cpp`根据以字符`#`开头的命令 修改原始的C程序  
定义的头文件将会以文本的形式 直接插入到文件中 定义的宏将会展开
```
gcc -E hello.c
```
###### 编译阶段
将预处理过的C程序hello.i翻译成一个可读的汇编文本文件hello.s
```
gcc -S hello.c
```
###### 汇编
将hello.s翻译成机器语言指令 将指令打包成可重定位目标程序(relocatable object program)
```
gcc -c hello.c
```
###### 链接
链接在程序执行中所需的各种动、静态库文件与程序合并 得到可执行程序
```
gcc hello.c
```
#### llvm
##### llvm编译器流程
使用clang命令输出程序的编译过程
```cmd
 clang -ccc-print-phases hello.c
```
```cmd
+- 0: input, "hello.c", c
+- 1: preprocessor, {0}, cpp-output
+- 2: compiler, {1}, ir
+- 3: backend, {2}, assembler
+- 4: assembler, {3}, object
5: linker, {4}, image
```
使用clang命令输出程序生产过程中的文件
```cmd
clang -save-temps hello.c
```
```
a.out hello.bc  hello.c  hello.i  hello.o  hello.s
```
###### 预处理阶段
```cmd
clang -E hello.c -o hello.i
//生成 hello.i文件
```
###### 词汇分析
生成token
###### 语法分析
AST语法树
###### IR代码
```cmd
clang -S -emit-llvm hello.c
//生成hello.ll文件
.ll -> llvm-as -> .bc
.bc -> llvm-dis -> .ll
.ll -> llc -> .s
```
###### 编译阶段
```cmd
clang -S hello.c
```
###### 汇编
```cmd
clang -c hello.c
```
###### 链接
```
clang hello
```
