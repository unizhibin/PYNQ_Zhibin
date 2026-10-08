# `xrfclk` Package

This is a package implementing the drivers to configure RF reference clocks
for the Xilinx Zynq RFSoC boards (e.g., ZCU111).

## Boards and Chips

For simple (safe) use, refer to `set_ref_clks()`.

For RFSoC experts, you can specify custom clock frequencies, assuming you
know what you're doing. In that case, pass them to `set_ref_clks()` through
the `lmk_freq` and `lmx_freq` arguments. A frequency is only accepted if a
matching register file exists (see "Register Values" below).

For example, checking ZCU111 schematic, you should be able to find that the
ZCU111 board has LMK04208 and LMX2594 chips. 

For other boards, the LMK and LMX chips are found through the SPI nodes in
the device tree, so make sure your device tree describes them.

## Register Values

The register values in this package (stored in `*.txt`) are generated using
the [TICS Pro software](https://www.ti.com/tool/TICSPRO-SW).

Users can specify their own register values. To do this, simply put the
exported `*.txt` output from [TICS Pro software](https://www.ti.com/tool/TICSPRO-SW)
into the folder `xrfclk`. You may have noticed that there are already
a few `*.txt` files put in this folder. Just make sure to rename your own file
with the convention `<CHIPNAME>_<freq>.txt`.

For example, suppose you have enabled 100MHz clock on LMK04208. You can rename
the TICS Pro software output as `LMK04208_100.0.txt` and put it under folder
`xrfclk`. Then in your Python terminal or Jupyter cell, simply call

```python
from xrfclk import set_ref_clks
set_ref_clks(lmk_freq=100)
```

You can also see [this forum post](https://adaptivesupport.amd.com/s/question/0D52E00006hpjOCSAY/how-to-setup-zcu111-rfsoc-dac-clock?language=en_US)
for additional information on how to generate custom register values.

Copyright (C) 2021 Xilinx, Inc

Copyright (C) 2022-2026 Advanced Micro Devices, Inc. All rights reserved.

SPDX-License-Identifier: BSD-3-Clause