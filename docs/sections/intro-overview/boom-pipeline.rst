The BOOM Pipeline
=================

.. _boom-pipeline:
.. figure:: /figures/boom-pipeline.svg
    :alt: Simplified BOOM Pipeline with Stages

    Simplified BOOM Pipeline with Stages

Overview
--------

概念上来说, BOOM划分为10个Stages: **Fetch**, **Decode**,
**Register Rename**, **Dispatch**, **Issue**, **Register Read**, **Execute**, **Memory**,
**Writeback** 和 **Commit**. 实际上很多stages在实现中进行合并，
因此 **7** 个stages: **Fetch**, **Decode/Rename**, 
**Rename/Dispatch**, **Issue/RegisterRead**, **Execute**,
**Memory** 和 **Writeback** (**Commit** 异步发生，因此不将其计算为“pipeline”的一部分)。
:numref:`boom-pipeline` 显示了一个简化的BOOM pipeline，其中列出了所有pipeline阶段。

Stages
------

Fetch
^^^^^

指令从指令内存中 *fetched* 并推入FIFO队列，称为:term:`Fetch Buffer` 。
分支预测也发生在这个阶段，根据需要重定向fectch的指令。[1]_

Decode
^^^^^^

**Decode** 从：term:`Fetch Buffer` 中提取指令，并生成相应的 
:term:`Micro-Op(s) (UOPs)<Micro-Op (UOP)>` 放入pipeline中. [2]_

Rename
^^^^^^

The ISA, or "logical", register specifiers (e.g. x0-x31) are
then *renamed* into "physical" register specifiers.

Dispatch
^^^^^^^^

The :term:`UOP<Micro-Op (UOP)>` is then *dispatched*, or written, into
a set of :term:`Issue Queue` s.

Issue
^^^^^

:term:`UOPs<Micro-Op (UOP)>` sitting in a :term:`Issue Queue` wait until all of
their operands are ready and are then *issued*. [3]_ This is
the beginning of the out–of–order piece of the pipeline.

Register Read
^^^^^^^^^^^^^

Issued :term:`UOPs<Micro-Op (UOP)>` s first *read* their register operands from the unified
**Physical Register File** (or from the **Bypass Network**)...

Execute
^^^^^^^

... and then enter the **Execute** stage where the functional
units reside. Issued memory operations perform their address
calculations in the **Execute** stage, and then store the
calculated addresses in the **Load/Store Unit** which resides in the
**Memory** stage.

Memory
^^^^^^

The **Load/Store Unit** consists of three queues: a **Load Address Queue
(LAQ)**, a **Store Address Queue (SAQ)**, and a **Store Data Queue (SDQ)**.
Loads are fired to memory when their address is present in the
**LAQ**. Stores are fired to memory at **Commit** time (and
naturally, stores cannot be *committed* until both their
address and data have been placed in the **SAQ** and **SDQ**).

Writeback
^^^^^^^^^

ALU operations and load operations are *written* back to the
**Physical Register File**.

Commit
^^^^^^

The **Reorder Buffer (ROB)**, tracks the status of each instruction
in the pipeline. When the head of the **ROB** is not-busy, the **ROB**
*commits* the instruction. For stores, the **ROB** signals to the
store at the head of the **Store Queue (SAQ/SDQ)** that it can now write its
data to memory.

Branch Support
--------------

BOOM supports full branch speculation and branch prediction. Each
instruction, no matter where it is in the pipeline, is accompanied by a
**Branch Tag** that marks which branches the instruction is "speculated
under". A mispredicted branch requires killing all instructions that
depended on that branch. When a branch instructions passes through
**Rename**, copies of the **Register Rename Table** and the **Free
List** are made. On a mispredict, the saved processor state is
restored.

Detailed BOOM Pipeline
----------------------

Although :numref:`boom-pipeline` shows a simplified BOOM pipeline, BOOM supports RV64GC and the privileged ISA
which includes single-precision and double-precision floating point, atomics support, and page-based virtual memory.
A more detailed diagram is shown below in :numref:`boom-pipeline-detailed`.

.. _boom-pipeline-detailed:
.. figure:: /figures/boom-pipeline-detailed.png
    :alt: Detailed BOOM Pipeline

    Detailed BOOM Pipeline. \*'s denote where the core can be configured.

.. [1] While the :term:`Fetch Buffer` is ``N``-entries deep, it can instantly read
    out the first instruction on the front of the FIFO. Put another way,
    instructions don’t need to spend ``N`` cycles moving their way through
    the :term:`Fetch Buffer` if there are no instructions in front of
    them.

.. [2] Because RISC-V is a RISC ISA, currently all instructions generate
    only a single :term:`Micro-Op (UOP)` . More details on how store :term:`UOPs<Micro-Op (UOP)>` are
    handled can be found in :ref:`The Memory System and the Data-cache Shim`.

.. [3] More precisely, :term:`Micro-Ops (UOPs)<Micro-Op (UOP)>` that are ready assert their request, and the
    issue scheduler within the Issue Queue chooses which :term:`UOPs<Micro-Op (UOP)>` to issue that cycle.
