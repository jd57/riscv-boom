The Berkeley Out-of-Order Machine (BOOM)
========================================

.. figure:: /figures/boom-pipeline-detailed.png
    :scale: 25 %
    :align: center
    :alt: Detailed BOOM Pipeline

    Detailed BOOM Pipeline. \*'s denote where the core can be configured.

伯克利乱序机器（BOOM）受MIPS R10000 [1]_ 和Alpha 21264 [2]_ 乱序处理器的启发。与MIPS R10000和Alpha 21264一样，BOOM是统一的物理寄存器文件设计（也称为“显式寄存器重命名”）。

BOOM实现了开源 `RISC-V ISA <riscv.org>`__ ，并利用 `Chisel <chisel-lang>`__ 硬件构造语言为内核构造了生成器。
可以将生成器考虑为通用的RTL设计。
可以将单独RTL设计视为生成器设计的单个实例。
因此，BOOM是一个乱序设计系列，而不是一个核心实例。
此外，为了构建具有BOOM内核的SoC，BOOM利用 `Rocket Chip <https://github.com/chipsalliance/rocket-chip>`__ SoC生成器作为库来重用不同的微体系结构（TLBs，PTWs等）。


.. [1] Yeager, Kenneth C. "The MIPS R10000 superscalar microprocessor." IEEE micro 16.2 (1996): 28-41.

.. [2] Kessler, Richard E. "The alpha 21264 microprocessor." IEEE micro 19.2 (1999): 24-36.
