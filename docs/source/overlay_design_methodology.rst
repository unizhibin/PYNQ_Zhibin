.. _overlay-design-methodology:

**************************
Overlay Design Methodology
**************************

PYNQ *Overlays* are analogous to software
libraries. A programmer can download overlays into the AMD-Xilinx Programmable 
Logic at runtime to
provide functionality required by the software application.

An *overlay* is a class of Programmable Logic design. Programmable Logic designs
are usually highly optimized for a specific task. Overlays however, are designed
to be configurable, and reusable for broad set of applications. A PYNQ overlay
will have a Python interface, allowing a software programmer to use it like any
other Python package.

There are a number of components required in the process of creating an overlay:

  * Overlay design
  * Board or platform settings
  * Partial reconfiguration  
  * Interfaces between host processor and programmable logic
  * Python AsyncIO  
  * MicroBlaze Soft Processors
  * Python utilities
  * Python/C Integration
  * Python Overlay API 
  * Python Packaging
  
While they are conceptually similar, there are differences in the process for 
building Overlays for different platforms. e.g. Zynq vs. Zynq UltraScale+ vs. 
Kria SoM, and Versal. Most of the differences relate to the configuration of the
processing system (PS), and the interfaces between the host processor and the
programmable logic (PL). For example, Zynq and Zynq UltraScale+ devices connect
the PS and PL through dedicated AXI interfaces whereas Versal devices use the Control, 
Interfaces and Processing System (CIPS) IP and the Network on Chip (NoC) to connect 
the PS, PL, and other blocks such as the AI Engines. Most of the differences will 
be related to the hardware PL design.

This section will give an overview of the process of creating an overlay and
integrating it into PYNQ, but will not cover the hardware design process in
detail. Hardware design will be familiar to developers with experience in AMD-Xilinx
adaptive SoCs (Zynq, Zynq UltraScale+, Kria and Versal) or FPGAs.

.. toctree::
   :maxdepth: 2
   :hidden:
   
   overlay_design_methodology/board_settings
   overlay_design_methodology/overlay_design
   overlay_design_methodology/partial_reconfiguration
   overlay_design_methodology/pspl_interface
   overlay_design_methodology/pynq_and_asyncio
   overlay_design_methodology/pynq_microblaze_subsystem
   overlay_design_methodology/pynq_utils
   overlay_design_methodology/python-c_integration
   overlay_design_methodology/python_overlay_api
   overlay_design_methodology/python_packaging

   
