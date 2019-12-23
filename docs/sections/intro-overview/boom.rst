The Berkeley Out-of-Order Machine (BOOM)
========================================

.. figure:: /figures/boom-pipeline-detailed.png
    :scale: 25 %
    :align: center
    :alt: Detailed BOOM Pipeline

    Detailed BOOM Pipeline. \*'s denote where the core can be configured.

Berkeley Out-of-Order Machine (BOOM) 深受MIPS R10000 [1]_ 和Alpha 21264 [2]_ 乱序处理器的启发。
与MIPS R10000和Alpha 21264一样，BOOM是 a unified physical register file design (also known as “explicit register renaming").

BOOM实现开源的 `RISC-V ISA <riscv.org>`__ 用 `Chisel <chisel-lang>`__ 语言生成core。
生成器可以看作是通用的RTL设计。
单独的RTL设计可以看作是生成器设计的一个实例。
因此，BOOM是一系列乱序设计，而不是一个单独的core。
此外，为了构建一个具有BOOM内核的SoC，BOOM使用 `Rocket Chip  <https://github.com/chipsalliance/rocket-chip>`__ SOC生成器作为库来重用不同的微体系结构（TLBs、PTWs等）。


.. [1] Yeager, Kenneth C. "The MIPS R10000 superscalar microprocessor." IEEE micro 16.2 (1996): 28-41.

.. [2] Kessler, Richard E. "The alpha 21264 microprocessor." IEEE micro 19.2 (1999): 24-36.
