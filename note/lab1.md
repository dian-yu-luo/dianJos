# boot.s 功能
+ 执行16进制代码,各种寄存器置零
+ 端口,开启物理的A20 ,
  需要 执行 lgdt指令,并调整cr0寄存器
+ 开gdt,现在就能用实模式了,地址映射到了2²⁰
+ 跳转到 32-bit mode的代码段
+ 还是配置寄存器,设置数据段位置,设置栈帧
  #+begin_src asm
    movw    $PROT_MODE_DSEG, %ax    # Our data segment selector
        ;; 这里 .set PROT_MODE_DSEG, 0x10
        ;; 0x10 对应 0b1010
        ;; 也就是说对应 gdt表的第二个位置,这个位置储存的地址就是段选择子
  #+end_src
# boot/main.c

+ 进入bootmain
+ bootmain有c语言进行编写,主要任务是把磁盘中的数据导入到内存的0x10000位置上(1MB)[fn:1]
  + 先读一页(8个扇区,一个扇区512)
    处理逻辑,因为已经读取了BootLoader所以需要对读取的第一个上去其实是偏移的第二个扇区
  + 根据读入的第一个扇区信息(elf头)把所需要的剩下内容放到磁盘上,本实验中就读取了4MB

# 进入entry之后的动作
1. 入口页表的位置放到cr3上,调整cr0,开页面映射
   这个elf文件的代码段VMA(virtual memory adderss) 从kernbase开始,我们需要把他映射到物理地址上面
   #define	KERNBASE	0xF0000000

2. entry_pgdir 的物理地址放到cr3上
3. 设置分页机制
	movl	%cr0, %eax
	orl	$(CR0_PE|CR0_PG|CR0_WP), %eax
	movl	%eax, %cr0
就是修改cr0    
，启用保护模式（PE），启用分页（PG）以及写保护标志（WP）。
在设置完了之后就是说访问虚拟地址,但是定位以后就会定位到物理地址上面了
4. 
5. 
6. 

