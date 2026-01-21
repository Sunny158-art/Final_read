一、实验目的
    本实验旨在理解 xv6 操作系统从上电到进入内核的完整启动过程，掌握 BIOS、bootloader 以及内核初始化之间的衔接关系，重点分析 bootasm.S、bootmain.c 和 main.c 在系统启动中的作用。

二、实验环境
    操作系统：macOS（Apple Silicon M2）
    编译工具链：x86_64-elf-gcc、x86_64-elf-ld
    模拟器：QEMU
    xv6 版本：MIT xv6（x86，课程提供版本）

三、xv6 启动流程总体概述
    xv6 的启动过程可以划分为以下几个阶段：
    BIOS
    ↓
    bootasm.S   （实模式 → 保护模式）
    ↓
    bootmain.c （加载内核 ELF）
    ↓
    entry.S    （内核入口）
    ↓
    main.c     （内核初始化）

四、各阶段功能分析
4.1 BIOS 阶段
    计算机上电后，首先由 BIOS 执行
    BIOS 从磁盘第一个扇区（MBR）加载 512 字节到内存地址 0x7c00
    将控制权转交给 bootloader
4.2 bootasm.S（引导汇编代码）
    bootasm.S 是 xv6 的第一段代码，主要完成：
    关闭中断
    设置段寄存器
    打开 A20 地址线
    从 16 位实模式切换到 32 位保护模式
    跳转到 bootmain()（C 语言）
4.3 bootmain.c（加载内核）
    bootmain.c 的主要职责是：
    从磁盘读取内核 ELF 文件
    解析 ELF 头
    将内核各段加载到指定内存位置
    跳转到内核入口地址 entry
    在该阶段：
    尚未初始化 console / uart
    无法使用 printf / cprintf
    只能进行内存与磁盘操作
4.4 entry.S（内核入口）
    entry.S 是内核的第一段代码，完成：
    设置内核栈
    初始化分页机制
    跳转到 C 语言的 main() 函数
4.5 main.c（内核初始化）
    在 main.c 中，内核完成：
    物理内存管理初始化
    中断向量初始化
    进程管理初始化
    控制台（console）初始化

五、实验验证方法
    在 main.c 中加入如下代码：
    cprintf("[KERNEL] main started\n");
    重新编译并运行：
    make clean
    make qemu

六、实验结果
    xv6 成功从 BIOS 启动
    bootloader 正确加载内核
    内核成功进入 main() 函数
    控制台输出验证了启动流程的正确性

七、实验总结
    通过本实验，深入理解了操作系统从上电到内核启动的完整流程，明确了各阶段的职责划分。实验表明，bootloader 阶段功能受限，无法进行标准输出，而内核初始化完成后才具备完整的 I/O 能力。