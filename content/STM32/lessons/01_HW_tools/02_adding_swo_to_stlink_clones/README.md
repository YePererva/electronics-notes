# SWO + HardWare Reset for STLink clones
This article describes process of adding SWO and Reset contacts to STLink v2 clones

## Reasons to add
- `SWO` is needed to debug. To print text information for debug purposes, and don't load excessively the MCU itself.
- `RESET` is needed to forcefully restart the MCU for programming if SWD is programmably disabled. \
  While clones already have pin marked as RESET, that pin is actually for restarting STM8 MCU, not STM32.


### How To

To add working SWO, you should consider 2 extra pins:
- `PA10` : SWO
- `PB0` :  SWD_RESET

Additionally, if you want to have a Virtual COM Port, consider having:
- `PA3` : as RX
- `PA2` : as TX


Each of them should be connected to target via 22-100 Ohm resistors. It can work even without resistors, however it it preferrable to have those.
