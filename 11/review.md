# review

## 总结：

如今的计算机系统设计只提供粗粒度的进程隔离，已经不适合新时代的计算机安全设计，为各种各样基于buffer overflow的攻击提供了可能，严重威胁系统安全。其中一种解决方案是利用胖指针，即对普通指针附加metadata（基址和界限信息），在运行时检查访问的内存区域，避免产生内存溢出。基于此，这篇文章采用了一种胖指针简洁描述形式来提供内存空间访问的安全性验证，同时为指针添加硬件管理标签从而保证了指针的不可伪造。性能方面，有效地降低了对系统性能的影响，在最差情况下只产生3%的内存碎片，依赖于专用检查硬件与处理器的数据通路的并行化处理，没有产生额外的运行开销。

背景介绍：胖指针是由3部分所组成，即指向地址、基址和界限范围。目前无论是基于软件或者硬件的实现，都产生了较大的内存开销，同时软件表达方式的胖指针不支持capabilities。基于分段隔离的系统可以用于保护敏感数据和共享数据，但是只能提供粗粒度的隔离；而基于capability的系统则可以通过定义一系列访问权限，结合基址和界限来提供更加细粒度的隔离方式。

简化的安全处理器SAFElite：作者提出的胖指针机制是基于一个简化版本的安全处理器（SAFElite），是64位单地址空间的RISC 处理器。为支持capability，以tag的形式定义了一些硬件类型，比较有意思的类型是Out-of-Bounds-Pointer、Out-of-Bounds-Memory-Location和Error。Out-of-Bounds-Pointer表示当前地址在基址之前或者超过了界限，Out-of-Bounds-Memory-Location表示该内存不在任何指针的界限范围中，而Error类型表示当前操作是违反预设语义的。在SAFElite中定义了4层管道结构，引入Hardware Type Unit (HTU) 来对指令和运算的类型进行检查，Pointer Unit (PU)管理与指针类型有关的算术运算，Memory Manager(MM)是唯一能创建胖指针的管理单元。最后，将SAFElite部署在一块FPGA开发板上，并设置处理器的时钟周期为4ns（可满足所有操作）。

胖指针编码：对于一个地址对齐的胖指针，只需要额外存储B bit的开销就可以计算出胖指针的基址和界限，但是这种对齐编码方式会带来较大的空间浪费，产生许多内存碎片。因此提出另一种表达方式——BIMA法，可以有效降低内存空间浪费问题，定义最小域I来表示基址，最大域M来表示界限，便可以计算出胖指针的基址和界限，同时这种表达方式可表示的地址空间足够大。对于一个Out-of-Bounds-Pointer，只有将其作为load或store的地址时才会触发错误。通过将未引用的内存标记为Out-of-Bounds-Memory-Location，当对其进行内存操作时，将触发错误。BIMA法还提供了一种分段方式，可以降低内存的浪费，通过最大化M−I而最小化B，可以将内存的碎片率降低到3%。在对胖指针的界限进行解码时，可以将解码后的界限保存在寄存器中，从而降低内存开销。

实现与评估：基于逻辑门实现了对胖指针的更新和验证，显示出硬件要求低，并且常见操作的路径比处理器ALU操作小。此外，胖指针的检查和更新操作可以在Virtex 6（40nm）上以4ns的时钟周期运行，同时仅使用1100个6-LUT或双精度浮点加法器。

## 评价：

优点：

1. 提出了一种简洁的胖指针编码方式，即BIMA编码；
2. 设计硬件来实现胖指针的界限检查和更新；
3. 采用了流水线的模式。

缺点：

1. 胖指针只提供了空间上的内存保护，而不能抵御时间上的内存攻击；
2. 增加了开发人员编程的难度；
3. 造成了一定程度的性能下降，如3%的内存碎片；
4. 只适用于RISC指令集和单地址空间。
