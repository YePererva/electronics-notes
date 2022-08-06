# Develop, Flash, and Debug for counterfeit STM32 or clones


## 1. Developing and Debug

On the latest STM32CubeMX 1.10.1 when the replica is connected for debugging via STLink, you'll receive the following error:

![](images/stlink-gbd-error.png)

and the terminal window will show something alike:
```
STMicroelectronics ST-LINK GDB server. Version 5.8.0
Copyright (c) 2020, STMicroelectronics. All rights reserved.

Starting server with the following options:
        Persistent Mode            : Disabled
        Logging Level              : 1
        Listen Port Number         : 61234
        Status Refresh Delay       : 15s
        Verbose Mode               : Disabled
        SWD Debug                  : Enabled
        InitWhile                  : Enabled


Error in initializing ST-LINK device.
Reason: ST-LINK: Could not verify ST device! Abort connection.
```

Reason is added requirement for chip authenticity check in more recent versions. However, there are work arounds:

### 1.1. Official tools from STMicroelectronics
The last version of STM32CubeIDE which still allows to program and debug non-original STM32-chips is 1.7.0 (also tested: 1.5.0 ; 1.5.1; 1.6.0; 1.6.1).

Tested on STLink V2 with firmware V2.J37.S7 (in fact, a clone based on CKS32F103C8T6) and STlink V3 Mini with firmware V3.J10.M3 .

Worth mentioning, that even this workaround raises a lot of warnings:
```
Open On-Chip Debugger 0.11.0-rc2+dev-00044-g8340bb0 (2021-06-02-17:29)
Licensed under GNU GPL v2
For bug reports, read
	http://openocd.org/doc/doxygen/bugs.html
Info : Listening on port 6666 for tcl connections
Info : Listening on port 4444 for telnet connections
Info : STLINK V2J37S7 (API v2) VID:PID 0483:3748
Info : Target voltage: 3.175049
Info : clock speed 4000 kHz
Info : stlink_dap_op_connect(connect)
Info : SWD DPIDR 0x2ba01477
Info : STM32F103C8Tx.cpu: hardware has 6 breakpoints, 4 watchpoints
Info : starting gdb server for STM32F103C8Tx.cpu on 3333
Info : Listening on port 3333 for gdb connections
Info : accepting 'gdb' connection on tcp/3333
Info : device id = 0x20036410
Info : flash size = 128kbytes
Warn : GDB connection 1 on target STM32F103C8Tx.cpu not halted
undefined debug reason 8 - target needs reset
Info : accepting 'gdb' connection on tcp/3333
Warn : GDB connection 2 on target STM32F103C8Tx.cpu not halted
undefined debug reason 8 - target needs reset
target halted due to debug-request, current mode: Thread
xPSR: 0x01000000 pc: 0x080003a8 msp: 0x20005000
target halted due to debug-request, current mode: Thread
xPSR: 0x01000000 pc: 0x080003a8 msp: 0x20005000
shutdown command invoked
Info : dropped 'gdb' connection
shutdown command invoked
```

![](images/st-link-v3-error-fake-opencdc.png)


Interestingly enough, this work around didn't work when used STLink v3 from Nucleo-G474 board. In such case it raises error:

```
Open On-Chip Debugger 0.11.0-rc2+dev-00044-g8340bb0 (2021-06-02-17:29)
Licensed under GNU GPL v2
For bug reports, read
	http://openocd.org/doc/doxygen/bugs.html
Info : Listening on port 6666 for tcl connections
Info : Listening on port 4444 for telnet connections
Info : STLINK V3J10M3 (API v3) VID:PID 0483:374E
Info : Target voltage: 3.268526
Info : clock speed 8000 kHz
Info : stlink_dap_op_connect(connect)
Info : SWD DPIDR 0x2ba01477
Warn : target STM32F103C8Tx.cpu examination failed
Info : starting gdb server for STM32F103C8Tx.cpu on 3333
Info : Listening on port 3333 for gdb connections
Info : accepting 'gdb' connection on tcp/3333
Error: Target not examined yet
Error: auto_probe failed
Error: Connect failed. Consider setting up a gdb-attach event for the target to prepare target for GDB connect, or use 'gdb_memory_map disable'.
Error: attempted 'gdb' connection rejected
```

Version 1.8.0 already shows following error regardless of used parameters an programmer:
![](images/1.8.0-error.png)

```
Open On-Chip Debugger 0.11.0+dev-00438-ga75fc63 (2021-11-03-15:27)
Licensed under GNU GPL v2
For bug reports, read
	http://openocd.org/doc/doxygen/bugs.html
Info : Listening on port 6666 for tcl connections
Info : Listening on port 4444 for telnet connections
Info : STLINK V2J37S7 (API v2) VID:PID 0483:3748
Info : Target voltage: 3.177113
Info : clock speed 4000 kHz
Info : stlink_dap_op_connect(connect)
Info : SWD DPIDR 0x2ba01477
Info : STM32F103C8Tx.cpu: Cortex-M3 r2p1 processor detected
Info : STM32F103C8Tx.cpu: target has 6 breakpoints, 4 watchpoints
Info : starting gdb server for STM32F103C8Tx.cpu on 3333
Info : Listening on port 3333 for gdb connections
Info : accepting 'gdb' connection on tcp/3333
Info : device id = 0x20036410
Info : flash size = 128kbytes
Warn : GDB connection 1 on target STM32F103C8Tx.cpu not halted
undefined debug reason 8 - target needs reset
O.K.

O.K.:0xE00FFFD0

undefined debug reason 8 - target needs reset
shutdown command invoked
Info : dropped 'gdb' connection
```

## 2. Flash

In case if you need to upload existing (compiled) firmware (*.hex, *.bin, etc.) to the microcontroller which is not original
