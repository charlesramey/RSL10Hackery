## Overview
![The RSL10-SIP-001GEVB evaluation board I am working with](https://charlesramey.github.io/RSL10Hackery/images/RSL10-SIP-001GEVB.jpg)
The On Semiconductor RSL10 System on a Chip (SoC) is a really neat, _very_ low power microprocessor / RF front end combination chip. This objective of this rough page is to outline the process of getting started developing applications with the On Semiconducter RSL10 SDK and dive deeper into some of the functionalities of this chip.

## Getting to Blinky
Fortunately, the getting started PDF straight from On Semi was actually extremely comprehensive and provided pretty much step by step instructions from unboxing the SoC development board, to intalling the Eclipse based SDK, to loading the blinky example code, to finally programming the SoC board and running the blinky code on the platform. All in all, the process of unboxing the SoC to running the blinky example took less than an hour and a half. [You can find this excellent peice of documentation here.](https://www.onsemi.com/pub/Collateral/RSL10%20GETTING%20STARTED%20GUIDE.PDF)

## Figuring out how to view Blinky's printf()'s
### Print Configuration out of the Box
The print.h file which the Blinky example includes looks like this out of the box from the SDK

```C
#ifndef PRINTF_H
#define PRINTF_H

#define OUTPUT_DISABLED                -1
#define OUTPUT_UART                    0 /* Note: if application uses UART or DIO5, PRINTF over UART will be a conflict.  */
#define OUTPUT_RTT                     1 /* Note: for RTT PRINTF, add the SEGGER RTT component on your .rteconfig file */

 #ifndef OUTPUT_INTERFACE
     #define OUTPUT_INTERFACE           OUTPUT_DISABLED
 #endif

#if OUTPUT_INTERFACE == OUTPUT_UART
    #define THREE_BLOCK_APPN(x, y, z)       x##y##z
    #define DMA_IRQn(x)                     THREE_BLOCK_APPN(DMA, x, _IRQn)
    #define DMA_IRQ_FUNC(x)                 THREE_BLOCK_APPN(DMA, x, _IRQHandler)
    #define VA_NARGS_IMPL(_1, _2, _3, _4, _5, _6, _7, _8, _9, _10, _11, _12, _13, _14, _15, _16, _17, _18, _19, _20, N, ...) N
    #define VA_NARGS(...) VA_NARGS_IMPL(__VA_ARGS__, 20, 19, 18, 17, 16, 15, 14, 13, 12, 11, 10, 9, 8, 7, 6, 5, 4, 3, 2, 1)
    int UART_printf(const int narg, const char *sFormat, ...);
    #define PRINTF(...) UART_printf(VA_NARGS(__VA_ARGS__), __VA_ARGS__)
    void printf_init(void);
    void DMA_IRQ_FUNC(DMA_UART_TX)(void);

#elif OUTPUT_INTERFACE == OUTPUT_RTT
#include <SEGGER_RTT.h>
    void printf_init(void);
    #define PRINTF(...) SEGGER_RTT_printf(0, __VA_ARGS__)
#else
    #define PRINTF(...)
    #define printf_init()
#endif

#endif /* PRINTF_H */
```
### Custom Print Configuration
You'll notice in the code snippet above that the printf statements are disabled by default but we are given two options for outputting print statements: RTT and UART. I found the documentation (linked above) to be perhaps out of date with the SDK code expamples for this particular development board. Part of the confustion with getting print statments to work revolved around just how the development board was being programmed over USB and wether we need to utilize RTT or UART. Long story short, I found that by switching:
```
 #ifndef OUTPUT_INTERFACE
     #define OUTPUT_INTERFACE           OUTPUT_DISABLED
 #endif
 ```
 to
 ```
  #ifndef OUTPUT_INTERFACE
     #define OUTPUT_INTERFACE           OUTPUT_UART
 #endif
  ```
  Got print statementss working. 
  *One final gotya though*: In order to actually see the print statements in realtime, you need to use the J-Link Viewer application (which the SDK installs during the J-Link driver install process).

 
  
 
