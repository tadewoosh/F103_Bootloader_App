# F103 Bootloader App
This repo contains a Atollic True Studio project containing an app (program called by the bootloader) boilerplate for STM32F103 microcontroller. For the bootloader see [this](https://github.com/tadewoosh/F103_Bootloader) repositorium.

## How is this app diferent?
In order for the app to run correctly not from the default start flash location (0x08000000) but from the location chosen during [partitioning](https://github.com/tadewoosh/F103_Bootloader#partitioning) some changes mus be made to the code:
* The linker script must be adjusted
* The low-level startup must be disabled / modified

### Change the linker script
Analogically to the linker script in the bootloader project the FLASH start address and length must be adjusted. Since the bootloader size is 16K, therefore for the app the FLASH should start at base + 16K. Adjusting the length should prevent generating a binary that would overflow the available flash area. For a 128k total memory the script should look like this:
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
The Atollic True studio environment for this project uses `startup_stm32f10x_md.s` assembler file as the low-level startup file. As the preamble of the file states:
```C
  /*            This module performs:
  *                - Set the initial SP
  *                - Set the initial PC == Reset_Handler,
  *                - Set the vector table entries with the exceptions ISR address
  *                - Configure the clock system
  *                - Branches to main in the C library (which eventually calls main()).
  */                  
```
Among this a function `SystemInit` is called, located in `system_stm32f10x.c`. This function is responsible for clock configuration. Since a proper clock startup and configuration has been acchieved by the same function while bootloader started it is not necessary to call it. Moreover calling it while clock is already configured breaks the connfiguration in this case. In order to avoid those problems the fucntion call ine the assembler file should be connemented out.

### Result
After those changes the project compiles and runs smoothly after the bootloader gives control to it. The result, as seen in the console, of the two programs runnig should look like this:
```
Bootloader: Start
Bootloader: Handing over to main app... 

Main App: Start
```
