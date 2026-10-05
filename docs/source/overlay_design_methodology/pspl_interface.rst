.. _pspl_interfaces:


PS/PL Interfaces
================

The PS (processing system) and the PL (programmable logic) are connected by AXI
interfaces, GPIO and interrupt signals. The interfaces depend on the device
family.

Zynq-7000
---------

The Zynq-7000 has 9 AXI interfaces between the PS and the PL. On the PL side,
there are 4x AXI Master HP (High Performance) ports, 2x AXI GP (General Purpose)
ports, 2x AXI Slave GP ports and 1x AXI Master ACP port. There are also GPIO
controllers in the PS that are connected to the PL.

.. image:: ../images/zynq_interfaces.png
   :height: 500px
   :align: center

Zynq UltraScale+
----------------

Zynq UltraScale+ devices have master ports from the PS to the PL (HPM ports) and
slave ports into the PS (HP, HPC and other ports) which allow IP in the PL to
access the PS DRAM.

Versal
------

On Versal, the PL does not reach DRAM through dedicated PS ports. Memory access
goes through the Network on Chip (NoC), and the processor system is configured
in the CIPS IP. On the VCK190 golden design, the PL-facing boundary provides:

* four AXI memory interfaces into the NoC, ``noc_pl/S00_AXI`` to ``S03_AXI``;
* ``M_AXI_FPD`` and ``M_AXI_LPD``, which the PS uses to control IP in the PL;
* one PL clock and reset; and
* one PL-to-PS interrupt.

Python classes
--------------

There are four ``pynq`` classes that are used to manage data movement between
the PS (including the PS DRAM) and PL interfaces.

* :class:`pynq.gpio.GPIO` - General Purpose Input/Output
* :class:`pynq.mmio.MMIO` - Memory Mapped IO
* :func:`pynq.buffer.allocate` - Memory allocation
* :class:`pynq.lib.dma.DMA` - Direct Memory Access

The class used depends on the PS interface the IP is connected to, and the
interface of the IP.

Python code running on PYNQ can access an AXI Slave IP connected to a PS master
port (a GP port on Zynq-7000, an HPM port on Zynq UltraScale+, or ``M_AXI_FPD`` /
``M_AXI_LPD`` on Versal). *MMIO* can be used to do this.

IP connected to an AXI Master port is not under direct control of the PS. The
AXI Master port allows the IP to access DRAM directly (through an HP port on
Zynq, or through the NoC on Versal). Before doing this, memory should be
allocated for the IP to use. The *allocate* function can be used to do this.
For higher performance data transfer between PS DRAM and an IP, DMAs can be
used. PYNQ provides a DMA class.

When designing your own overlay, you need to consider the type of IP you need, 
and how it will connect to the PS. You should then be able to determine which 
classes you need to use the IP. 

PS GPIO
-------

The number of GPIO wires from the PS to the PL depends on the device. The
Zynq-7000 has 64. On Versal there are two GPIO controllers, ``versal_gpio`` and
``pmc_gpio``, so pass the controller name as ``target_label`` when using the
``GPIO`` class, for example ``GPIO.get_gpio_pin(0, "versal_gpio")``.

PS GPIO wires from the PS can be used as a very simple way to communicate between
PS and PL. For example, GPIO can be used as control signals for resets, or
interrupts.

IP does not have to be mapped into the system memory map to be connected to GPIO. 

More information about using PS GPIO can be found in the
:ref:`pynq-libraries-psgpio` section.

MMIO
----

Any IP connected to a PS master port (a GP port on Zynq-7000, an HPM port on
Zynq UltraScale+, or ``M_AXI_FPD`` / ``M_AXI_LPD`` on Versal) will be mapped
into the system memory map.
MMIO can be used to read/write a memory mapped location. A MMIO read or write
command is a single transaction to transfer 32 bits of data to or from a memory
location. As burst instructions are not supported, MMIO is most appropriate for
reading and writing small amounts of data to/from IP connected to these ports.

More information about using MMIO can be found in the
:ref:`pynq-libraries-mmio` section.

allocate
--------

Memory must be allocated before it can be accessed by the IP. ``allocate``
allows memory buffers to be allocated. The :func:`pynq.buffer.allocate`
function allocates a contiguous memory buffer which allows efficient transfers
of data between PS and PL. Python or other code running in Linux on the PS can
access the memory buffer directly.

As PYNQ is running Linux, the buffer will exist in the Linux virtual memory.
The PS memory interfaces (the AXI Slave ports on Zynq, or the NoC on Versal) 
allow an AXI-master IP in an overlay to access physical memory. The numpy array 
returned can also provide the physical memory pointer to the buffer which can 
be sent to an IP in the overlay. The physical address is stored in the 
``device_address`` property of the allocated memory buffer instance. An IP 
in an overlay can then access the same buffer using the physical address.

More information about using allocate can be found in the
:ref:`pynq-libraries-allocate` section.

DMA
---

AXI stream interfaces are commonly used for high performance streaming
applications. AXI streams can be used with the PS memory interfaces
(HP ports on Zynq, the NoC on Versal) via a DMA.

The :class:`pynq.lib.dma.DMA` class supports the `AXI Direct Memory Access IP
<https://www.amd.com/content/dam/xilinx/support/documents/ip_documentation/axi_dma/v7_1/pg021_axi_dma.pdf>`_.
This allows data to be read from DRAM, and sent to an AXI stream, or received
from a stream and written to DRAM.

More information about using DMA can be found in the
:ref:`pynq-libraries-dma` section.

Interrupt
---------

There are dedicated interrupts which are linked with asyncio events in
the python environment. To integrate into the PYNQ framework, dedicated
interrupts are normally attached to an **AXI Interrupt Controller**, which is in
turn attached to the first interrupt line to the processing system. If more than 32
interrupts are required then AXI interrupt controllers can be cascaded. This
arrangement leaves the other interrupts free for IP not controlled by PYNQ
directly, such as Vitis accelerators.

The AXI Interrupt Controller can be avoided for overlays with only one
interrupt. In such overlays the interrupt pin must be connected directly to the
first interrupt line of the processing system. On the VCK190, the golden design
provides only one PL-to-PS interrupt, so an overlay with more than one interrupt
needs an AXI Interrupt Controller.

Interrupts are managed by the Interrupt class, and the implementation is built
on top of *asyncio*, part of the Python standard library.


More information about using the Interrupt class can be found in the 
:ref:`pynq-libraries-interrupt` section.

For more details on *asyncio*, how it can be used with PYNQ see
the :ref:`pynq-and-asyncio` section.


