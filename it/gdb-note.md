# <center> gdb学习笔记

# 汇编相关
```
int main(int argc, char **argv)
  {
B     int begin = -1;
      int a = -1;
      printf("size: %d.\n", sizeof(mppc_comp_hdr));
  =>  if(a < sizeof(char)) {
          printf("1\n");
          return 1;
      }else {
          printf("2\n");
          return 2;
      }
  }
```

```
(gdb) disas
Dump of assembler code for function main:
   0x0000000000400504 <+0>:     push   %rbp
   0x0000000000400505 <+1>:     mov    %rsp,%rbp
   0x0000000000400508 <+4>:     sub    $0x20,%rsp
   0x000000000040050c <+8>:     mov    %edi,-0x14(%rbp)
   0x000000000040050f <+11>:    mov    %rsi,-0x20(%rbp)
   0x0000000000400513 <+15>:    movl   $0xffffffff,-0x8(%rbp)
   0x000000000040051a <+22>:    movl   $0xffffffff,-0x4(%rbp)
   0x0000000000400521 <+29>:    mov    $0x400658,%eax
   0x0000000000400526 <+34>:    mov    $0x2,%esi
   0x000000000040052b <+39>:    mov    %rax,%rdi
   0x000000000040052e <+42>:    mov    $0x0,%eax
   0x0000000000400533 <+47>:    callq  0x4003f0 <printf@plt>
=> 0x0000000000400538 <+52>:    cmpl   $0x0,-0x4(%rbp)
   0x000000000040053c <+56>:    jne    0x40054f <main+75>
   0x000000000040053e <+58>:    mov    $0x400663,%edi
   0x0000000000400543 <+63>:    callq  0x400400 <puts@plt>
   0x0000000000400548 <+68>:    mov    $0x1,%eax
   0x000000000040054d <+73>:    jmp    0x40055e <main+90>
   0x000000000040054f <+75>:    mov    $0x400665,%edi
   0x0000000000400554 <+80>:    callq  0x400400 <puts@plt>
   0x0000000000400559 <+85>:    mov    $0x2,%eax
   0x000000000040055e <+90>:    leaveq
   0x000000000040055f <+91>:    retq
End of assembler dump.
```


```
int main(int argc, char **argv)
  {
B     int begin = -1;
      int a = -1;
      printf("size: %d.\n", sizeof(mppc_comp_hdr));
  =>  if(a < 1) {
          printf("1\n");
          return 1;
      }else {
          printf("2\n");
          return 2;
      }
  }
```

```
(gdb) disas
Dump of assembler code for function main:
   0x0000000000400504 <+0>:     push   %rbp
   0x0000000000400505 <+1>:     mov    %rsp,%rbp
   0x0000000000400508 <+4>:     sub    $0x20,%rsp
   0x000000000040050c <+8>:     mov    %edi,-0x14(%rbp)
   0x000000000040050f <+11>:    mov    %rsi,-0x20(%rbp)
   0x0000000000400513 <+15>:    movl   $0xffffffff,-0x8(%rbp)
   0x000000000040051a <+22>:    movl   $0xffffffff,-0x4(%rbp)
   0x0000000000400521 <+29>:    mov    $0x400658,%eax
   0x0000000000400526 <+34>:    mov    $0x2,%esi
   0x000000000040052b <+39>:    mov    %rax,%rdi
   0x000000000040052e <+42>:    mov    $0x0,%eax
   0x0000000000400533 <+47>:    callq  0x4003f0 <printf@plt>
=> 0x0000000000400538 <+52>:    cmpl   $0x0,-0x4(%rbp)
   0x000000000040053c <+56>:    jg     0x40054f <main+75>
   0x000000000040053e <+58>:    mov    $0x400663,%edi
   0x0000000000400543 <+63>:    callq  0x400400 <puts@plt>
   0x0000000000400548 <+68>:    mov    $0x1,%eax
   0x000000000040054d <+73>:    jmp    0x40055e <main+90>
   0x000000000040054f <+75>:    mov    $0x400665,%edi
   0x0000000000400554 <+80>:    callq  0x400400 <puts@plt>
   0x0000000000400559 <+85>:    mov    $0x2,%eax
   0x000000000040055e <+90>:    leaveq
   0x000000000040055f <+91>:    retq
End of assembler dump.
```

 - 单步执行 
   stepi, nexti

 - 显示当前执行的汇编代码 
   disas(disassamble)
   可以指定函数，例如disas main
   另外可以通过命令objdump -D a.out，查看所有汇编代码

 - x /nfu 检查内存地址
   n是一个数字，表示内存的长度
   f指明格式，可以是i（代表指令地址），d（默认值，表示是10进制整数），x（16进制值），f（浮点数），s(字符串)
   u指明单位，可以是b（1个字节），h（2个字节），w（4个字节，默认值），g（8个字节）
   例如：
     x/32x $esp
     x/32d 0x123456

 - 查看寄存器、内存值
   info registers                    #检查所有的寄存器
   info register eip                 #指定寄存器
   info addr x                       #查看变量的内存地址

 - 修改寄存器、内存值
   set $epc = 0x1234                 #修改寄存器的值
   set *(long *)0x1234 = 0x1234567   #修改指定地址上的内存的值

 - 设置断点、监测点
   b a.c:1234 if index == 0
   b *0x123456
   watch p->addr.sp
   watch *(long *)0x123456 == 0x123454567

 - 断点后自动执行
   command breaknum
   c
   end

 - 列出gdb框架
    layout

 - def nn
next
refresh
end 

