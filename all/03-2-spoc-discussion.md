# lec6 SPOC思考题


NOTICE
- 有"w3l2"标记的题是助教要提交到学堂在线上的。
- 有"w3l2"和"spoc"标记的题是要求拿清华学分的同学要在实体课上完成，并按时提交到学生对应的git repo上。
- 有"hard"标记的题有一定难度，鼓励实现。
- 有"easy"标记的题很容易实现，鼓励实现。
- 有"midd"标记的题是一般水平，鼓励实现。

## 与视频相关思考题

### 6.1	非连续内存分配的需求背景
  1. 为什么要设计非连续内存分配机制？
     - 提高分配的灵活性
     - 提高内存的利用效率：方便共享、充分利用内存空间


  1. 非连续内存分配中内存分块大小有哪些可能的选择？大小与大小是否可变?
     - 大块易于管理和内存保护，小块更加灵活，易于优化转移。大块大小可以改变，小块大小固定


  1. 为什么在大块时要设计大小可变，而在小块时要设计成固定大小？小块时的固定大小可以提供多种选择吗？
     - 固定大小易于管理，多种大小比一中更加灵活
     - 可变大小可以通过对齐来减少管理难度

### 6.2	段式存储管理
  1. 什么是段、段基址和段内偏移？
     - 段表示访问方式和存储数据等属性相同的一段地址空间


  1. 段式存储管理机制的地址转换流程是什么？为什么在段式存储管理中，各段的存储位置可以不连续？这种做法有什么好处和麻烦？
     - 地址转换是段基址加段内偏移
     - 段反映的程序的存储逻辑结构，程序不会从一个段的基址去访问另一个段，于是不同的段可以不连续
     - 好处是可以不连续，方便内存管理；麻烦是地址转换稍微复杂了一些。


### 6.3	页式存储管理
 1. 什么是页（page）、帧（frame）、页表（page table）、存储管理单元（MMU）、快表（TLB, Translation Lookaside Buffer）和高速缓存（cache）？
 1. 页式存储管理机制的地址转换流程是什么？为什么在页式存储管理中，各页的存储位置可以不连续？这种做法有什么好处和麻烦？
    - 进程逻辑地址空间被划分为大小相等的页
    - 页式存储管理中，地址转换是页号加页内偏移
    - PU使用连续的逻辑地址，存储访问时，逻辑地址先分成逻辑页号和页内偏移，然后通过页表定义的对应关系，把逻辑页面转换成物理页号，最后再把物理页号加页内偏移得到物理地址；于是不同的段可以不连续。
    - 好处是可以不连续，方便内存管理中的存储分配和回收；麻烦是地址转换比较复杂（页表项访问开销和页表存储开销），并且频繁进行（每次存储访问会变成两次或更多）。


### 6.4	页表概述
 1. 每个页表项有些什么内容？有哪些标志位？它们起什么作用？

    - 页表结构

      - 每个进程都有一个页表
        - 每个页面对应一个页表项
        - 随进程运行状态而动态变化
        - 页表基址寄存器（PTBR）

      页表项组成

      - 帧号：f
      - 页表项标志
        - 存在位
        - 修改位
        - 引用位

 1. 页表大小受哪些因素影响？

    - 页大小、地址空间大小、进程数目


### 6.5	快表和多级页表
 1. 快表（TLB）与高速缓存（cache）有什么不同？
    - TLB是一个内存管理单元用于改进虚拟地址到物理地址转换速度的缓存.TLB是位于内存中的页表的cache，如果没有TLB，则每次取数据都需要两次访问内存,即查页表获得物理地址和取数据.
    - cache是为了解决处理器与慢速DRAM(慢速DRAM即内存)设备之间巨大的速度差异而出现的.
    - cache中的数据存在时间局域性和空间局域性。TLB中存放的是物理地址只有时间局域性。
    - cache存放数据，TLB存放物理地址
 1. 为什么快表中查找物理地址的速度非常快？它是如何实现的？为什么它的的容量很小？
    - 缓存近期访问的页表项
      - TLB使用关联存储实现，具备快速访问性能
      - TLB命中，物理页号快速获取
      - TLB未命中，对应的表项被更新到TLB中
 1. 什么是多级页表？多级页表中的地址转换流程是什么？多级页表有什么好处和麻烦？
    - 通过间接引用将页号分为k级
      - 建立页表树
      - 减少每一级页表长度


### 6.6	反置页表
 1. 页寄存器机制的地址转换流程是什么？
    - 逻辑地址进行hash，然后查相应页寄存器
 1. 反置页表机制的地址转换流程是什么？
    - 反置页表项的内容
    - 逻辑地址和进程号共同进行hash，然后查相应页寄存器
 1. 反置页表项有些什么内容？
    - PID、逻辑页号、标志位

### 6.7	段页式存储管理
 1. 段页式存储管理机制的地址转换流程是什么？这种做法有什么好处和麻烦？

    - 在段式存储管理基础上，给每个段加一级页表
    - 段式存储在内存保护方面有优势，页式存储在内存利用和优化转移到后备存储方面有优势，结合两者的优势

 1. 如何实现基于段式存储管理的内存共享？

    - 按段为单位进行共享

 1. 如何实现基于页式存储管理的内存共享？

    - 指向相同的页表基址，实现进程间的内存共享

    

## 个人思考题
（1） (w3l2) 请简要分析64bit CPU体系结构下的分页机制是如何实现的

- 64位寻址空间能够寻址16EB的内存大小，对于目前的硬件来说太大。在X64体系结构下，只实现了48位的虚拟寻址，不同于X86体系结构，每级页表寻址长度变成9位，由于在x64结构中，普通页大小仍为4KB，然而数据却表示64位长，因此一个4KB页在x64下只能包含512项内容，所以为了保证页对齐和以页为单位的页表内容换入换出，在x64下每级页表寻址部分长度定为9位。

## 小组思考题
（1）(spoc) 某系统使用请求分页存储管理，若页在内存中，满足一个内存请求需要150ns (10^-9s)。若缺页率是10%，为使有效访问时间达到0.5us(10^-6s),求不在内存的页面的平均访问时间。请给出计算步骤。

- ```
  0.9*150 + 0.1*x = 500
  x = 3650ns
  ```

（2）(spoc) 有一台假想的计算机，页大小（page size）为32 Bytes，支持32KB的虚拟地址空间（virtual address space）,有4KB的物理内存空间（physical memory），采用二级页表，一个页目录项（page directory entry ，PDE）大小为1 Byte,一个页表项（page-table entries
PTEs）大小为1 Byte，1个页目录表大小为32 Bytes，1个页表大小为32 Bytes。页目录基址寄存器（page directory base register，PDBR）保存了页目录表的物理地址（按页对齐）。

PTE格式（8 bit） :
```
  VALID | PFN6 ... PFN0
```
PDE格式（8 bit） :
```
  VALID | PT6 ... PT0
```
其
```
VALID==1表示，表示映射存在；VALID==0表示，表示映射不存在。
PFN6..0:页帧号
PT6..0:页表的物理基址>>5
```
在[物理内存模拟数据文件](./03-2-spoc-testdata.md)中，给出了4KB物理内存空间的值，请回答下列虚地址是否有合法对应的物理内存，请给出对应的pde index, pde contents, pte index, pte contents。
```
1) Virtual Address 6c74
   Virtual Address 6b22
2) Virtual Address 03df
   Virtual Address 69dc
3) Virtual Address 317a
   Virtual Address 4546
4) Virtual Address 2c03
   Virtual Address 7fd7
5) Virtual Address 390e
   Virtual Address 748b
```

比如答案可以如下表示： (注意：下面的结果是错的，你需要关注的是如何表示)
```
Virtual Address 7570:
  --> pde index:0x1d  pde contents:(valid 1, pfn 0x33)
    --> pte index:0xb  pte contents:(valid 0, pfn 0x7f)
      --> Fault (page table entry not valid)

Virtual Address 21e1:
  --> pde index:0x8  pde contents:(valid 0, pfn 0x7f)
      --> Fault (page directory entry not valid)

Virtual Address 7268:
  --> pde index:0x1c  pde contents:(valid 1, pfn 0x5e)
    --> pte index:0x13  pte contents:(valid 1, pfn 0x65)
      --> Translates to Physical Address 0xca8 --> Value: 16
```

[链接](https://piazza.com/class/i5j09fnsl7k5x0?cid=664)有上面链接的参考答案。请比较你的结果与参考答案是否一致。如果不一致，请说明原因。

```
Virtual Address 6c74:
  --> pde index:0x1b  pde contents:(valid 1, pfn 0x20)
    --> pte index:0x03  pte contents:(valid 1, pfn 0x61)
      --> Translates to Physical Address 0xc34 --> Value: 0x6
Virtual Address 6b22:
  --> pde index:0x1a  pde contents:(valid 1, pfn 0x52)
    --> pte index:0x19  pte contents:(valid 1, pfn 0x47)
      --> Translates to Physical Address 0x8e2 --> Value: 0x1a
Virtual Address 03df:
  --> pde index:0x00  pde contents:(valid 1, pfn 0x5a)
    --> pte index:0x1e  pte contents:(valid 1, pfn 0x05)
      --> Translates to Physical Address 0xbf --> Value: 0xf
```



（3）请基于你对原理课二级页表的理解，并参考Lab2建页表的过程，设计一个应用程序（可基于python、ruby、C、C++、LISP、JavaScript等）可模拟实现(2)题中描述的抽象OS，可正确完成二级页表转换。

[链接](https://piazza.com/class/i5j09fnsl7k5x0?cid=664)有上面链接的参考答案。请比较你的结果与参考答案是否一致。如果不一致，提交你的实现，并说明区别。

（4）假设你有一台支持[反置页表](http://en.wikipedia.org/wiki/Page_table#Inverted_page_table)的机器，请问你如何设计操作系统支持这种类型计算机？请给出设计方案。

 (5)[X86的页面结构](http://os.cs.tsinghua.edu.cn/oscourse/OS2019spring/lecture06)
---

## 扩展思考题

阅读64bit IBM Powerpc CPU架构是如何实现[反置页表](http://en.wikipedia.org/wiki/Page_table#Inverted_page_table)，给出分析报告。


## interactive　understand VM

[Virtual Memory with 256 Bytes of RAM](http://blog.robertelder.org/virtual-memory-with-256-bytes-of-ram/)：这是一个只有256字节内存的一个极小计算机系统。按作者的[特征描述](https://github.com/RobertElderSoftware/recc#what-can-this-project-do)，它具备如下的功能。
 - CPU的实现代码不多于500行；
 - 支持14条指令、进程切换、虚拟存储和中断；
 - 用C实现了一个小的操作系统微内核可以在这个CPU上正常运行；
 - 实现了一个ANSI C89编译器，可生成在该CPU上运行代码；
 - 该编译器支持链接功能；
 - 用C89, Python, Java, Javascript这4种语言实现了该CPU的模拟器；
 - 支持交叉编译；
 - 所有这些只依赖标准C库。

针对op-cpu的特征描述，请同学们通过代码阅读和执行对自己有兴趣的部分进行分析，给出你的分析结果和评价。
