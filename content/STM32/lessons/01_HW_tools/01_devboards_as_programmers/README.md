This article considers conversion of existing minimal development boards into debuggers / programmers

## 1. Blue Pill or Maple Mini
These boards are based on STM32F103C_T6 microcontrollers, which are the same as used in original STLink v2 or its variation as STLink v2.1 on Nucleo and Discovery boards. This, the firmware should be compatible.


### 1.0. Preparation work / info
We'll need either USB-> UART adapter or another ST-Link.

### 1.1. As ST-Link





### 1.2. As Black Magic Probe

Connect BluePill or BluePill+ to USB-UART, boot into programming mode and erase the content of MCU. From official page with [automated builds](http://builds.blacksphere.co.nz/blackmagic/) download the firmware of BMP, which consists of two files:
- [`blackmagic_dfu-swlink.bin`](http://builds.blacksphere.co.nz/blackmagic/blackmagic_dfu-swlink.bin) : DFU bootstrap, load it at `0x08000000`
- [`blackmagic-swlink.bin`](http://builds.blacksphere.co.nz/blackmagic/blackmagic-swlink.bin) : BMP code itself, load it at `0x08002000`

*NB!* : if flashing with STM32 Flash Demonstrator, MANDATORY select the 128k in the program.

After installation on Windows there will be 2 new devices in Device Manager needed manual driver installation from [Zadig](https://zadig.akeo.ie/) utility:
- Black Magic Firmware Upgrade (SWLink) : install as `libusbK` driver
- Black Magic Trace Capture : install as `WinUSB` driver 

### 1.3. As J-Link
The developers of SEGGER J-Link created the instructionf to convert the ST-Link into J-Link ([official instructions](https://www.segger.com/products/debug-probes/j-link/models/other-j-links/st-link-on-board/)). However, it can be done if your ST-Link has firmware variation for STM32 only.

Thus, use ST-Link firmware upgrade ([STSW-LINK007](https://www.st.com/en/development-tools/stsw-link007.html)) to change the type of STLink, and then, convert it to J-Link as SEGGER suggests.

### 1.4. As CMSIS-DAP probe


## 2. STM32F411 Black Pill
