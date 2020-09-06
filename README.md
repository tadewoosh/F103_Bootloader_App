# F103 Bootloader App
This repo contains a Atollic True Studio project containing an app (program called by the bootloader) boilerplate for STM32F103 microcontroller. For the bootloader see [this](https://github.com/tadewoosh/F103_Bootloader) repositorium.

## How is this app diferent?
In order for the app to run not from the default start flash location (0x08000000) but from the location chosen during [partitioning](https://github.com/tadewoosh/F103_Bootloader#partitioning) some changes mus be made to the code:
* The linker script must be adjusted
* The low-level startup must be disabled / modified

### Change the linker script

```C
/* Specify the memory areas */
MEMORY
{
  FLASH (rx)      : ORIGIN = 0x08004000, LENGTH = 112K
  RAM (xrw)       : ORIGIN = 0x20000000, LENGTH = 16K
  MEMORY_B1 (rx)  : ORIGIN = 0x60000000, LENGTH = 0K
}
```

### Disable low-level startup
