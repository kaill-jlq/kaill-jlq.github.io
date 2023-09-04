## 计组~~复习~~补天整理 --JLQ
> 期末考试允许带一张A4纸手写笔记入场，笔记署名，笔记试卷草稿纸都要交，考试范围以ppt为主,ppt中的计算题都得熟悉

### Chapter 01

 RISC: Reduced Instruction Set Computer
+ merit:指令执行用尽量少的时钟周期、指令编码长度定长等->提高编译效率
What is a Computer?
+ 电子化的实现方式
+ 有指令集
+ 可执行指令
+ 可存储指令与数据
+ 计算能力上是图灵完全的
**计算机结构**
![](pic/1.png)

Memory(存储)
+ **Main memory(主存)**:volatile(易失的),used to hold programs while they are running(e.g.DRAM)
+ **Second memory**: nonvolatile(非易失)used to store programs and data between runs(e.g. magnetic disks)
+ DRAM和SRAM是易失的
  

Throughput(吞吐率)
+ 单位时间能够处理的任务数量(e.g., tasks/transactions/... per hour)

**Relative Performance**
Define `Performance = 1/Execution Time`
X 比 y 快n倍指的是两者性能之比为n，即$Performance_x$/$Performance_y$ = n

Elapsed time(经过时间):
+ 总的响应时间，包括Processing,I/O,OS overhead,idle time

**CPU time(CPU时间)：**
+ 处理一个指定任务所花的时间(忽略I/O等)
+ 包括用户CPU time和系统CPU time

clock period(时钟周期):一个周期的时间,也就是Clock Cycle Time
clock frequency/rate(时钟频率): cycles per second= $1/clock period$
**单位换算** :$4.0GHz = 4000MHz = 4.0×10^9Hz$

#### 重点1
CPU time的计算：
$CPUTime=ClockCycle*ClockCycleTime = \frac{ClockCycle}{Clock Rate} $

##### $CPUTime = Instruction Count*CPI*ClockCycleTime = \frac{Instruction Count*CPI}{Clock Rate}$

>Instruction Count*CPI结果就是总共有多少clock
+ 一个程序的IC即指令数量,由程序本身、ISA和编译器所决定(ISA是计算机体系结构中的指令集架构，如x86,ARM,MIPS,risc等)
+ clock cycle time由CPU硬件决定，且不同的指令也有不同的CPI

![](pic/9.png)
若不同的指令IC不同，则：
+ $Clock Cycles = \sum_{i=1}^{n}(CPI_i*IC_i) $
或者算出平均CPI：
+ $AVG.CPI = \sum_{i=1}^{n}(CPI_i*\frac{IC_i}{IC_总})$
一个计算CPI的例子：
![](pic/10.png)

Performance的总结(~~有点没看懂这个公式。。但是好像也不重要~~)：

$CPU Time = \frac{Instructions}{Program}*\frac{ClockCycles}{Instruction}*\frac{Seconds}{Clock Cycle}$

影响性能的因素：

1. 算法：影响IC，可能影响CPI
2. 程序语言：影响IC，CPI
3. 编译器：影响IC，CPI
4. 指令集架构(ISA):影响IC，CPI，$T_c$

Power Trends:
计算Power:
$Power = Capacitive load* Voltage^2*Frequency$
例题见ppt75页，~~应该没那么重要~~

#### 重点2
**阿姆达尔定律(Amdahl’s Law):**
$Speedup = 1 / [(1 - p) + (p / n)]$或

$time\_after\_improvement = \frac{time\_affected\_by\_improvement}{amount\_of\_improvement} + time\_unaffected$

+ Speedup 表示整体性能的提升倍数（加速比）。
+ p是可以并行执行的部分在整个任务中所占的比例。
+ n是并行处理的处理器数量。
> 1-p的部分为串行执行的时间，而p/n的部分表示并行执行部分花费的时间

#### 重点3
**八大设计思想：**
1. Design for Moore’s Law （设计紧跟摩尔定律）
+ 更高性能、更小尺寸、更低功耗的芯片设计
2. Use Abstraction to Simplify Design (采用抽象简化设计)
3. Make the Common Case Fast (加速大概率事件)
4. Performance via Parallelism (通过并行提高性能)
5. Performance via Pipelining (通过流水线提高性能)
+ 例：自动化生产的流水线
6. Performance via Prediction (通过预测提高性能)
+ 包括风力信息的航海导航系统
7. Hierarchy of Memories (存储器层次)
8.  Dependability via Redundancy (通过冗余提高可靠性)
+ 悬索桥(用多个悬索，提高桥的牢固性)


-----------------------------------

### Chapter 02指令
>我们所选择的指令集：RISC-V

在RISC-V中，共有32个寄存器每个寄存器宽度为64位，且32个寄存器按照约定的编号和用途进行命名，如x0、x1、x2等，具体见下图：
![](pic/11.png)
在**RISC-V**架构中，32位数据被称为一个字(word),64位的数据被称为双字(doubleword)
>对齐（alignment）是指数据在内存中存储时的位置要求。在某些计算机体系结构中，要求某些数据类型（如字）必须以特定的边界地址开始存储，即数据的地址必须是其大小的倍数。例如，对于要求字对齐的架构，一个字必须从内存地址的偶数位置开始存储。
然而，在RISC-V架构中，字在内存中的存储位置**没有对齐要求**。这意味着字可以以任意地址开始存储，而不必满足特定的边界要求。这种非对齐存储的特性提供了更大的灵活性，允许程序在处理数据时更自由地选择内存地址，无需考虑对齐限制,但同时，非对齐存储也可能导致一些性能损失，因为处理器可能需要额外的指令或处理来处理非对齐数据。

RISC-V架构采用的是 **小端序（Little Endian）** 字节顺序：
较低地址的字节存储着多字节值的最低有效字节，而较高地址的字节存储着多字节值的最高有效字节

#### 重点!!：翻译汇编码：
**R-Format Instructions**
```
add x9,x20,x21
>21是rs2，20是rs1

十进制版本的机器码：
0_21_20_0_9_51
二进制版本的机器码：
0000000_10101_10100_000_01001_0110011
```
funct7(7位)_rs2(5位)_rs1(5位)_funct3(3位)_rd(5位)_opcode(7位)

**I-Format Instructions**
```
ld x9,64(x22)
二进制版本的机器码：
000001000000_10110_011_01001_0000011
```
immediate(12位)\_rs1(5位)\_funct3(3位)_rd(5位)_opcode(7位)

**S-Format Instructions**
```
sd x9 64(x22)

要注意的是9是rs2，22是rs1
二进制版本的机器码：
0000010_01001_10110_011_00000_0100011
```
imm\[11:5](7位)\_rs2(5位)\_rs1(5位)\_funct3(3位)\_imm\[4:0](5位)\_opcode(7位)

**SB-Format Instructions**
```
bne x10 x11 2000 //2000 = 00111 1101 0000
机器码：0_111110_01011_01010_001_1000_0_1100011

目标地址 = PC + Branch offset = PC + immediate ×2
```
imm\[12,10:5](7位)\_rs2(5位)\_rs1(5位)\_funct3(3位)\_imm\[4:1,11](5位)\_opcode(7位)
**要注意**：这里的imm[0]之所以不存，是因为默认了为0，相当于你将机器码翻译为汇编码的时候已经✖️2了（jal同理）
**UJ-Format Instructions**
```
jal x0 2000 #2000 = 0 00000000 0 1111101000 0
机器码：0_1111101000_0_00000000_00000_1101111
```
imm\[20,10:1,11,19:12](20位)_rd(5位)_opcode(7位)

**U-Format Instructions**
```
lui s3,976 //加载立即数到上半字寄存器,立即数是20位的立即数
机器码：0000 0000 0011 1101 0000_10011_0110111
该指令可用于Loading a 32-bit constant，方法如下：
要load：0000 0000 0011 1101 0000_1001 0000 0000
则
lui s3, 976
addi s3, s3, 2304  #  2304d = 1001 0000 0000b
```
imm\[31:12](20位)_rd(5位)_opcode(7位)

**各个指令对应机器码大全：**
![](pic/12.png)
![](pic/15.png)
![](pic/16.png)
![](pic/17.png)
```{.line-numbers}
C code:
    A[30]  =  h + A[30] + 1 ;
    (Assume:h----x21,base address of A----x10)
RiSC-V:
    ld x9, 240(x10) // temporary reg x9 gets A[30]
    add x9, x21, x9  // temporary reg x9 gets  h+A[30]
    addi x9, x9, 1  // temporary reg x9 gets  h+A[30]+1
    sd x9, 240(x10) // stores h+A[30]+1  back into A[30]
```

```{.line-numbers}
C code:
        if (i==j) goto L1;
        f = g + h;
L1:     f = f - i;
RISC-V assembly code:
    beq x21,x22,L1 #go to L1 if i equals j
    add x19,x20,x21 #f=g+h(skipped if  i equals j)
L1: sub x19,x19,x22 #f=f-i(always executed)
```

```{.line-numbers}
( Assume: i~ k----x22 and x24, base of save ----x25)
C code:
    while( save[i]  = =  k ) 
        i+= i;
RISCV assembly code:
Loop:   slli x10,x22,3 #temp regt1 = 8*i
        add x10,x10,x25 #x10 = address of save[i]
        ld x9, 0(x10)  # x9 gets save[i]
        bne x9, x24, Exit # go to Exit if save[i] != k
        addi x22, x22, 1  # i+= 1
        beq x0, x0, Loop   # go to Loop
Exit:
```

```{.line-numbers}
( Assume: g,h,i,j in x10, x11,x12,x13 and f in x20)
C code:
long long int leaf_example(long long int g, long long int h, 
    long long int i, long long int j) {
    long long int f;
    f = (g + h) - (i + j);
    return f;
}
RISCV assembly code:
    addi sp, sp, -24 #adjust stack to make room for 3 items
    sd x5, 16(sp) #These three instructions save three
    sd x6, 8(sp)  # register x5,x6,x20
    sd x20, 0(sp) #Let’s consider why it need to be done.
    add x5,x10,x11 #register x5 contains g+h
    add x6,x12,x1 #register x6 contains i+j
    sub x20,x5,x6 #f = x5-x6, which is (g+h)–(i+j)
    addi x10,x20,0 # copy f to return register (x10 = x20 + 0)
    ld x20, 0(sp) # restore register x20 for caller
    ld x6, 8(sp) # restore register x6 for caller
    ld x5, 16(sp)# restore register x5 for caller
    addi sp,  sp, +24# adjust stack to delete 3 items
    jalr x0,0(x1) # jump backto calling routine
```

```{.line-numbers}
递归求阶乘：
C code for n!
    int fact (int n){
        if (n < 1) return 1;
        else return n*fact(n-1) ;
    }
RISCV assembly code:
fact:  addi sp, sp, -16  # adjust stack for 2 items
       sd ra, 8(sp)      # save the return address：x1
       sd a0, 0(sp)      # save the argument  n:x10
       addi t0, a0, -1   # x5 = n -1
       bge t0, zero, L1  # if  n >= 1, go to L1(else)
       addi a0, zero, 1  # return 1 if n<1
       addi sp, sp, 16   # Recover sp(Why not recover x1 and x10 ?)
       jalr zero, 0(ra)  # return to caller
L1:    addi a0, a0, -1   # n>=1: argument gets (n-1)
       jal ra, fact      # call fact with (n-1)
       add t1, a0, zero  # move result of fact(n -1) to x6(t1)
       ld a0, 0(sp)      # return from jal: restore argument n
       ld ra, 8(sp)      # restore the return address
       add sp, sp, 16    # adjust stack pointer to pop 2 items
       mul a0, a0, t1    # return  n*fact(n-1)
       jalr zero, 0(ra)  # return to the  caller
```

过程调用中保存和不保存的对象：
![](pic/13.png)
以下是过程调用之前，之中，之后栈的分配情况。fp指针指向帧的第一个double word，通常是保存的参数寄存器，通过将栈指针向下移动来实现为函数分配栈空间
![](pic/14.jpg)

内存布局(Memory Layout):
![](pic/14.png)
上图是运行Linux操作系统时RISC-V分配内存的约定。栈从用户地址空间高端开始并向下扩展，低端内存的第一部分是保留的，之后是RISC-V的机器代码，称为代码段，再往上是静态数据段(如constant array和string)，再往上是动态分配的数据。堆和栈相向而长，这两个段的此消彼长达到内存高效使用。

#### 人机交互
当前大多数计算机使用字节来表示字符，也就是ASCLL码
人机交互方式：
1. 字节编码字符集(Byte-encoded character sets)
+ Latin-1
+ ASCLL
2. 32位字符集(32-bit character set)
+ Unicode
+ UTF-8, UTF-16

RISC-V指令集体系结构中提供了用于字节（byte）、半字（halfword）和字（word）的加载（load）和存储（store）指令：
```c
//从内存中加载一个字节（8位）/半字(16位)/一字(32位)数据到寄存器中
//并将其符号扩展为 64 位存储到目标寄存器 rd
lb rd, offset(rs1)
lh rd, offset(rs1)
lw rd, offset(rs1)
//Store byte/halfword/word: Store rightmost 8/16/32 bits
sb rs2, offset(rs1)
sh rs2, offset(rs1)
sw rs2, offset(rs1)
```

```c
strcpy example:
Assume: base addresses for i--x19, x’s base --x10,  y’s base --x11
c code:
    void strcpy(char x[], char y[]){            
        size_t i = 0;
        while ((x[i] = y[i])!= ‘\0’ )       
            i+=1 ;
    } 
RISC-V assembly code:
strcpy: addi sp, sp, -8 // adjust stack for 1 doubleword
        sd x19, 0(sp)   // save x19
        add x19, x0, x0 // i=  0 
L1:     add x5, x19, x11//x5 = address of y[i]
        lbu x6, 0(x5)   // x6=y[i],因为是char所以用lbu
        add x7,x19,x10  //x7 = address of x[i] 
        sb  x6, 0(x7)   // x[i] = y[i]
        beq x6, x0, L2  //if y[i] == 0 then exit
        addi x19, x19,1 // i= i+1
        jal x0, L1      //next iteration of loop
L2:     ld x19,0(sp)    //restore saved old s3
        addi sp, sp, 8  //pop 1 double word from stack
        jalr zero 0(x1) // return
```

#### RISC-V的同步操作：
+ 原子加载和原子存储
```
lr.d rd, (rs1) //从rs1加载到rd
sc.d rd,(rs1),rs2 //将rs2的内容存到rs1的地址上去，rd返回0代表成功，否则失败
```
以下是两个例子：
![](pic/18.png)

#### 链接器
是一个系统程序，将独立汇编的机器语言程序组合起来，并解析所有未定义的标签，最终生成可执行文件。
链接器工作的三个步骤：
1. 将代码和数据模块按符号特征放入内存
2. 决定数据和指令标签的地址
3. 修正内部和外部引用

将磁盘上的图像文件加载到内存中的过程：
1. 读取头部信息以确定段的大小：从图像文件的头部中读取相关信息，例如代码段（text segment）和初始化数据段（initialized data segment）的大小
2. 创建虚拟地址空间：为图像文件的代码和数据段创建虚拟地址空间，即为它们在内存中分配合适的地址空间。
3. 将代码和初始化数据复制到内存中：将图像文件中的代码段和初始化数据段复制到先前创建的虚拟地址空间中，或者设置页面表项以便在需要时进行缺页中断（page fault）
4. 在堆栈上设置参数：在堆栈上设置需要传递给程序的参数，这些参数将在程序执行时被访问和使用。
5. 初始化寄存器（包括sp、fp、gp等）：设置程序执行所需的寄存器的初始值，例如堆栈指针（sp）、帧指针（fp）、全局指针（gp）等。
6. 跳转到启动程序（startup routine）

##### 动态链接(Dynamic Linking)
动态链接（dynamic linking）是一种在程序执行时将外部库（或模块）与程序进行连接的技术。与静态链接（static linking）相对应，静态链接在编译时将库的代码和数据复制到可执行文件中，而**动态链接则推迟了链接过程**，**直到程序运行时才进行**。

##### 懒惰链接（lazy linkage）
延迟加载外部库的链接方式。在传统的动态链接中，所有的外部库都会在程序启动时被加载和链接，而懒惰链接则将这个过程**推迟到实际需要使用外部库**的时候。
优点：
+ 灵活性、模块性
+ 节省内存、快速启动
缺点：
+ 频繁使用外部库会导致额外的延迟

------------------------
### Chapter 03算术运算


-------------------

### Chapter 04处理器

RISC-V中的指令执行：
1. Fetch(取指)
+ Take instructions from the instruction memory 
+ Modify PC to point the next instruction
2. instruction decoding & Read Operand(解码、读指令)
+ Will be translated into machine control command 
+ Reading Register Operands, whether or not to use
+ Reading Register Operands, whether or not to use
3. Executive Control
+ Control the implementation of the corresponding ALU operation
4. Memory access
+ Write or Read data from memory 
+ Only ld/sd
5. Write results to register:
+ If it is R-type instructions, ALU results are written to rd
+ If it is I-type instructions, memory data are written to rd
6. Modify PC for branch instructions

寄存器堆：
如下图，对于一个R型指令，需要从寄存器堆中读出两个数据字，再写入一个数据字。为了读出两个数据字，需要输入两个**给定的要读的寄存器号**，输出读出的数据，为写入寄存器的数据，需要输入给定的寄存器号，以及要写入的数据`write data`。寄存器号为5位宽，用于指定32个寄存器中的一个，而数据输入总线和数据输出总线均为64位宽。
![](pic/19.png)

数据存储单元（load/store指令会用）：
数据存储单元是一个状态单元，有**地址输入**和**写数据的输入**，**输出为读取结果**。读和写控制信号相互独立，但仅有一个可以在任意给定的时钟上有效。
![](pic/20.png)

立即数生成单元：
立即数生成单元有一个32位的指令输入，如果是load，store，和branch成立时的分支指令，他将12位字段符号扩展为64位结果输出（使用`sign-extent`但也不是简单的符号位扩展，因为不同指令(如SB指令)的立即数结构是不同的，还要考虑针对特定的指令类型将其恢复成正常的立即数）。
![](pic/21.png)
> beq有三个操作数其中两个为寄存器，比较是否相等，第三个是一个12位的偏移量，**用于计算目标地址**（规定基地址是beq指令所在的地址）
> 计算目标地址时
> 1. 符号位扩展。
> 2. 偏移量左移一位以表示半字为单位的偏移量，使得偏移量有效范围扩大到两倍。
> 3. 传入PC

如下图所示
![](pic/22.png)
ALU做什么？
![](pic/23.png)
#### 完整控制通路：

![](pic/25.png)
#### 控制信号（重点）：
![](pic/24.png)

根据指令中的两位ALUOp控制字段、funct7和funct3字段生成`ALU control`信号。
首先定义的四种控制为
|ALU控制线|功能|
|--|--|
|0000|AND|
|0001|OR|
|0010|add|
|0110|subtract|
随后根据`ALUOp`生成的`ALU control`信号如下，其中`ALUOp`为00和01时不关心`func3`和`func7`信号，因为ALU已经确定了做什么操作。
![](pic/26.png)

#### PipeLine
执行时间最长的指令类型决定了周期：
（对应习题4.7）
ld指令：
Instruction memory ->register file ->ALU ->data memory ->register file
分为IX -> ID -> EX -> MEM -> WB五个阶段(五级流水线)


#### 流水线寄存器：
    如果指令只是从左往右执行，不会产生问题，但是write back和PC更新会在图中往左走，会影响后续指令，所以需要引入流水线寄存器的概念。
下图为流水线寄存器的例子（包含完整的控制信号，~~但是好像没有管jal指令~~）
![](pic/30.png)


#### Hazard
1. Structure Hazrads
   即硬件不支持多条指令在同一时钟周期执行（容易避免）
2. Data Hazards
   一个步骤必须等待另一个步骤完成的数据
   如
   ```assembly
   add x19, x0, x1
   sub x2, x19, x3
   ```
   解决方法：**Forwarding（bypassing）**
   + 不用等到上一条指令存回寄存器
   + 需要在数据通路中额外的连线
    ![](pic/27.png)
     一直特殊的data hazard：无法避免stall：**load-use**冒险

    ![](pic/28.png)
   分析以下指令执行共需花几个周期：
   ![](pic/29.png)
   左图有两处需要stall，右图不需要stall
3. Control Hazards
   需要根据一条指令做出决定，而其他指令正在执行
    分支预测技术：静态分支预测，动态分支预测（常见的方法是记录最近的分支跳转情况来预测下一次是否跳转）

#### Hazard Detect:
1. 检测是否需要进行`forwarding`:

##### 检测是否发生data hazard：
条件1:
+ **1.a** EX/MEM.RegisterRd= ID/EX.RegisterRs1
+ **1.b** EX/MEM.RegisterRd= ID/EX.RegisterRs2
+ **2.a** MEM/WB.RegisterRd= ID/EX.RegisterRs1(load-use类型，在mem/wb寄存器中才刚刚获得+ load的值)
+ **2.b** MEM/WB.RegisterRd= ID/EX.RegisterRs2(load-use类型)

条件2:
+ 需要写回寄存器：EX/MEM.RegWrite, MEM/WB.RegWrite

条件3:
+ 写回的目的地址不是x0:EX/MEM.RegisterRd≠ 0,MEM/WB.RegisterRd≠ 0
+ 因为如果是0的话，就是一个常量0。

如下图，添加了forward unit对于传入ALU的两个寄存器rs1和rs2，都有可能发生data hazard，数据可能从EX/MEM寄存器来，也可能从MEM/WB寄存器来。
其中：
+ forwardA=00代表从register file中读出。
+ forwardA=10代表从EX/MEM寄存器读(发生forward)
+ forwardA=01代表从MEM/WB寄存器读(发生forward)
+ forwardB同理，只不过forwardA是rs1，forwardB是rs2
+ 注意rs1和rs2传入forward unit是**用来做判断的**
![](pic/31.png)

但是会发生double data hazard，如：
```{.line-numbers}
add x1,x1,x2
add x1,x1,x3
add x1,x1,x4
```
这种情况发生时,会检测到两张hazard情况，即在执行第三行到时候：
+ 对于第一行指令和第三行指令有MEM/WB.RegisterRd= ID/EX.RegisterRs1
+ 对于第二行指令和第三行指令有EX/MEM.RegisterRd= ID/EX.RegisterRs1
当这两种冲突的时候**选择EX/MEM的寄存器值返回**，故mem hazard判断语句：
![](pic/32.png)

##### Load - Use Hazard detection
在指令在**ID阶段**的时候就去检测（但是最后还是要从mem/wb寄存器中取，得插入一条stall）
检测发生的条件：
+ ID/EX.MemRead and 
 ((ID/EX.RegisterRd= IF/ID.RegisterRs1) or(ID/EX.RegisterRd= IF/ID.RegisterRs2))

一旦检测到，就插入一条stall。
**如何stall：**
1. 让ID/EX寄存器中的值全变0
2. EX, MEM and WB 阶段做 nop(no-operation)
3. 阻止PC 和 IF/ID 寄存器的更新

![](pic/33.png)
**加入hazard detect和forward unit的图**
![](pic/34.png)

##### Branch Hazard
减小branch delay：
+ 移动硬件在ID阶段就计算出结果

**动态分支预测：**
使用**分支预测缓存**或**分支历史表**，包含一个或多个比特以表明一个分支最近是否发生了跳转
**1位的预测机制**：用一个bit来表明一个分支最近是否发生了跳转
+ 优：当分支预测规律性较强正确率较高，如一直是跳转的情况。
+ 缺：一旦其发生不跳转，会造成两次预测错误。如连续9次跳转，一次不跳转，会第一次以及最后一次预测错误。

**2位预测机制**：只有在发生两次连续预测错误时，预测结果才会发生改变
![](pic/35.png)


#### Exceptions and Interrupts
Exception：CPU造成，如undefined opcode, syscall,hardware malfunction
Interrupt：外部的I/O控制器造成

解决Exception：
方法一、
**SEPC**：保存中断时候的**PC**（64位）
**SCAUSE**：保存造成Exception的**原因**（64bits，但大部分用不到）如未定义指令编码为2，硬件故障编码为12

方法二、
**向量式中断**：该方法用**基址寄存器**加上**例外原因**（作为偏移）作为目标地址来完成控制流转换。基址寄存器保存了向量式中断内存区域的起始地址。
+ Undefined opcode错误为00 0100 0000
+ Hardware malfunction错误为01 1000 0000

设置**0000 0000 1C09 0000**为统一入口地址实现例外处理，并在RISC-V的实现中添加两个额外的寄存器SEPC和SCAUSE

流水线实现中，将例外处理看作另一种控制冒险。例如在`add`指令执行时产生硬件故障，我们需要清除掉add之后的指令，并从新地址开始取址。和处理分支指令不同的是，例外会引起系统状态的变化。
检测到例外时，先在SEPC中保存发生例外的指令地址，随后让**flush**信号进行重置，发生例外的指令所有控制信号为无效（数值设为0）。**下一个周期开始**，插入bubble，并重新从例程入口地址0000 0000 1C09 0000开始取第一条指令。
![](pic/36.png)

如果在执行的时候，同一周期内有多种例外发生，一种简单的处理方法是先处理早发生的例外
非精确中断：流水线中的例外并未和引发该例外的指令精确对应。

#### Instruction-Level Parallelism (ILP) 指令级并行
提高指令级并行度的方法主要有两种：
1. 增加流水线级数，如让五级流水线变成六级流水线
2. 增加流水线内部的功能部件数量，，使得每个周期可以发射出多条指令，称为**多发射**

IPC = 1/CPI

##### Speculation
推测是另一种非常重要的指令级并行的方法。允许编译器或处理器“猜测”指令的行为，以尽早消除该指令与其他指令之间的依赖关系。
>例如：可以对**分支指令结果进行推测**，这样分支指令之后的指令可以提早执行。再如，先store再load的指令序列，可以推测两条指令的访存地址不同，允许load先于store执行。

推测机制必须包含推测结果的正确性检验和预测之后的恢复机制。

##### 多发射：

**静态多发射**：由**编译器**完成发射相关判断，编译器支持指令打包成**issue packets**，并将打包的指令看成一条需要进行多种操作的“大指令”，符合VLIW（Very Long Instruction Word）的设计思路。
下图为`Two-issue packets`
![](pic/37.png)

多发射代码的调度(主要就是调换一些指令顺序，以及并行执行一些指令来提高IPC)：
![](pic/38.png)

Loop Unroll的方法展开循环，进一步提高IPC，下图的例子中一次执行4条原来一个循环的内容。
在循环展开的方法中，编译器使用了额外的寄存器（如下图的x28，x29，x30），这个称为**register rename**（寄存器重命名）
![](pic/39.png)

**动态多发射**：超标量处理器，CPU来决定指令执行的顺序
例子：
```
ld x31, 20(x21)
add x1, x31, x2
sub x23, x23, x3
andi x5, x6, 20
```
可以在add等待ld的过程中先执行sub


---------------------------------

### Chapter 05

**Appendix**
chapter04习题：
![](pic/2.png)
![](pic/3.png)
![](pic/4.png)
![](pic/5.png)
![](pic/6.png)
![](pic/7.png)
![](pic/8.png)
