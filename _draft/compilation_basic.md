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
