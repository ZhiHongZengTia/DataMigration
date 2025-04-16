STM32 复习

## 初级篇

什么是 STM32 ?

- 基于 ARM 内核的 32 位 MCU系列

  内核为 Cortex-M 内核

  标准的 ARM 架构

- 结构

  高性能，低电压，低功耗，创新的内核和丰富的外设

- 简单易用/自由/低风险

Cortex-M3 系列属于 ARMv7 架构，ARMv7架构定义以下三个系列：

- A 系列：面向尖端的基于虚拟内存的操作系统和用户应用
- R 系列：针对实时系统
- M 系列：针对微控制器

STM 32F1 属于 Cortex-M 系列中的 Cortex-M3 内核，采用 ARMv7-M 架构

1. 小容量产品：闪存存储器容量在 16K~32K 字节之间的 STM32F101xx STM32F102xx STM32F103xx
2. 中容量产品：闪存存储器容量在 64K~128K 字节之间的 STM32F101xx STM32F102xx STM32F103xx
3. 大容量产品：闪存存储器容量在 256K~512K 字节之间的 STM32F101xx STM32F103xx
4. 互联型产品：STM32F105xx STM32F107xx 微控制器

为什么选择？

1. 极高的性能，主流的 Cortex 内核
2. 丰富的外设，较低的功耗，合适的价格
3. 强大的软件支持，丰富的软件包
4. 全面丰富的技术文档
5. 芯片型号种类多，覆盖面广
6. 强大的用户基础

### 串口下载

| BOOT0 | BOOT1 | 启动模式        | note |
| ----- | ----- | --------------- | ---- |
| 0     | 任意  | 主 FLASH 存储器 |      |
| 1     | 0     | 系统存储器      |      |
| 1     | 1     | 内嵌 SRAM       |      |

使用串口一，PA9 为 TX，PA10 为 RX；

Boot0 接 1，Boot1 接 0；

串口波特率 115200

勾选“编程后执行”，下载完后发送命令从 FLASH 启动

DTR 的低电平复位，RTS 的高电平进 BootLoader

下载完成后，希望下一次上电运行程序，需要 BOOT0 接 0，按一次复位

### GPIO 基本原理与寄存器配置



#### GPIO 基本结构和工作方式

datsheet 中带 FT 可容忍 5V 输入

4 种输入模式：

- ==输入浮空 GPIO_IN_FLOATING==

  一般用于外部按键输入

- ==输入上拉 GPIO_IPU==

- ==输入下拉 GPIO_IPD==

- ==模拟输入 GPIO_AIN==

  ADC 模拟输入

4 种输出模式：

- ==开漏输出 GPIO_OUT_OD==

  输出端相当于三极管的集电极，可以输出高低电平，高电平得靠外部电阻拉高，输出的低电平为 0V , 同时也可以读取输入的值。例如，I2C 通信使用 GPIO_OUT_OD, 外接上拉电阻，能够正确输出 1 和 0；读取输入时，先 GPIO_SetBits（GPIOB, GPIO_Pin_0）拉高电平，然后使用 GPIO_ReadInputDataBit（GPIOB, GPIO_Pin_0）读取输入

- ==开漏复用功能 GPIO_AF_OD==

  输出信号来自于 片上外设模块 的 复用功能输出

- ==推挽式输出  GPIO_OUT_PP==

  输出的高低电平是确定的

- ==推挽式复用功能  GPIO_AF_PP==



#### GPIO 寄存器说明

每组 GPIO 端口得寄存器包括

- ==两个 32 位配置寄存器 (GPIOx_CRL, GPIOx_CRH)==

  用来控制 GPIO 口得工作模式和工作速度

  其中 低寄存器 GPIOx_CRL 配置 Px0 - Px7

- ==两个 32 位数据寄存器 (GPIOx_IDR, GPIOx_ODR)==

  输入数据寄存器：GPIOx_IDR，输出数据寄存器：GPIOx_ODR（高 16 位都保留，只用了低 16 位）

- ==一个 32 位设置/清除寄存器 (GPIOx_BSRR)==

- ==一个 16 位复位寄存器 (GPIOx_BRR)==

- ==一个 32 位锁存寄存器（GPIOx_LCKR）==

  

每个 I/O 端口位可以自由编程，然而 I/O 端口寄存器必须按 32 位字被访问（不允许半字或字节访问）

#### STM32F10x GPIO 引脚说明

- ==端口复用功能==

  STM32 大部分端口都具有复用功能，就是一些端口不仅可以作为通用 IO 口，还可以复用为一些外设引脚，比如 PA9, PA10 可以复用为 串口一

  ==作用：最大限度地利用端口资源==

- ==端口重映射==

  把某些功能引脚映射到其他引脚，比如串口一默认为 PA9, PA10，可以通过配置重映射映射到 PB6, PB7

  作用：==方便布线==

所有 IO 端口都可以作为中断输入

















## 中级篇





## 高级篇



## 嵌入式技术公开课



### 新建工程步骤

1. 创建总文件夹 template，创建子文件夹 Lib、Project 和 User，在Keil中新建工程保存到 Project 

   ​										User 中创建 main.c

2. 将 CMSIS 和 STM32F10x_StdPeriph_driver 复制到 Lib 文件夹下

3. Keil 中Groups，添加 Start、Lib、User

   ​						 向 Start 中添加file  startup_stm32f10x_md.s

   ​						 向 Lib 中添加 Lib/CMSIS/CM3/DeviceSupport/ST/STM3F10x/system_stm32f10x.c 与 stm32f10x_it.c

   ​												Lib/CMSIS/CM3/CoreSupport/core_cm3.c

   ​												Lib/STM32F10x_StdPeriph_driver/src/ 所有.c 文件	

4. 魔术棒，C/C++ 中 Define: STM32F10X_MD, USE_STDPERIPH_DRIVER

   ​			 添加路径: Lib/CMSIS/CM3/DeviceSupport/ST/STM32F10x

   ​							 Lib/CMSIS/CM3/CoreSupport

   ​							 Lib/STM32F10x_StdPeriph_driver/inc

创建 uCOS-III 模板工程，需要修改 startup_stm32f10x_md.s

```C
// row 76
DCD     OS_CPU_PendSVHandler          

// row 192
OS_CPU_PendSVHandler PROC
				EXPORT OS_CPU_PendSVHandler
```



### GPIO 点灯

输出模式：1）通用推挽输出，2）通用开漏输出，3）复用推挽输出，4）复用开漏输出

输入模式：1）模拟输入，2）浮空输入，3）上拉/下拉输入 

寄存器地址 = 基地址 + 偏移地址（基地址可在 STM32F103x8B_DS_CH_V10 数据手册中找 memory mapping ）

I2C 使用开漏输出

- 方法一：使用寄存器点灯

```C
// 开时钟，挂载在哪条时钟总线，在 STM32F103x8B_DS_CH_V10 数据手册中11页
// APB2 总线地址 = RCC 基地址 0x40021000 + 偏移地址 0x18
*(unsigned int *)(0x40021000 + 0x18) |=(1<<4)  // 使能 GPIOC 在 APB2 使能
    
// GPIOC Pin13 的地址 = 基地址0x40011000 + 偏移地址0x04
*(unsigned int *)(0x40011000 + 0x04) &= 0xff0fffff;  // 清除 GPIOC_CRL 地址的内容的 [20:23]
*(unsigned int *)(0x40011000 + 0x04) |= 0x00300000;  // [20:23] 设为 0011，通用推挽输出

// GPIOC ODR地址 = 基地址0x40011000 + 偏移地址0x0C
*(unsigned int *)(0x40011000 + 0x0C) &= ~(1<<13);  // GPIOC ODR 第 13 位清零
*(unsigned int *)(0x40011000 + 0x0C) |= (1<<13);   // GPIOC ODR 第 13 位置1
```

struct 与 typedef struct

```C
struct str01 {
   		     // ...
};
struct str01 mystr01;
// compare
typedef struct {
   		     // ...
}str02 ;
str02 mystr02;
```

- 方法二：使用结构体点灯 （ 库函数就是使用的结构体，因为结构体中的成员地址连续 ）

```C
// .h 文件中定义 结构体 和 宏
typedef struct{
	unsigned int CR; unsigned int CFPR;
	unsigned int CIR; unsigned int APB2RSTR;
	unsigned int APB1RSTR; unsigned int AHBENR;
	unsigned int APB2ENR; unsigned int APB1ENR;
	unsigned int BDCR; unsigned int CSR;
	unsigned int AHBRSTR; unsigned int CFGR2;
}RCC_Typedef;

typedef struct{
	unsigned int CRL; unsigned int CRH;
	unsigned int IDR; unsigned int ODR;
	unsigned int BSRR; unsigned int BRR;
	unsigned int LCKR; 
}GPIOC_Typedef;
#define RCC  ((RCC_Typedef *)0x40021000)      // == 库函数中 #define RCC                 ((RCC_TypeDef *) RCC_BASE)
#define GPIOC  ((GPIOC_Typedef *)0x40011000)  // == 库函数中 #define GPIOC               ((GPIO_TypeDef *) GPIOC_BASE)
// .c 文件
RCC->APB2ENR |= (1<<4);
GPIOC->CRH &= 0xFF0FFFFF;
GPIOC->CRH |= 0x00300000;
GPIOC->ODR &= ~(1<<13);
```

- 方法三：库函数

```C
// 初始化 RCC 和 GPIO
GPIO_InitTypeDef GPIO_InitStructure;

RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE);
	
GPIO_InitStructure.GPIO_Pin = GPIO_Pin_13;
GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
GPIO_Init(GPIOC,&GPIO_InitStructure);
// 使用 GPIO
GPIO_WriteBit(GPIOC,GPIO_Pin_13,Bit_RESET); // == GPIO_ResetBits(GPIOC,GPIO_Pin_13);
```





### 系统定时器 SysTick 

SysTick提供1个24位、降序、零约束、写清除的计数器，具有灵活的控制机制。

![image-20230713220705763](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230713220705763.png)

在固件库 V3.5 中，core_cm3.h 中，对 SysTick 寄存器的设置只有一个函数 SysTick_Config(unit32_t ticks);  参数不能超过 24 位，否则配置错误，返回 1，该函数设置了自动重载值寄存器（LOAD）的值，SysTick IRQ 中断优先级，复位了当前寄存器的值（VAL），开始计数并使能 SysTick 中断（CTRL）。

```C
// 初始化：
void SysTick_Configuration(void){
	while(SysTick_Config(72));  // 1us SysTick IRQ and Timer，count 72 to interrupt, T = 1ms
	Systick -> CTRL &= ~(1<<0); // disable SysTick
}
```

```C
// 产生精确延时 us
void Delay_us(unsigned long Count){
	TimingDelay = Count;
	SysTick -> CTRL |= (1<<0);  // ensable SysTick
	while(TimingDelay);
	Systick -> CTRL &= ~(1<<0);
}
void SysTick_Handle(void){
	if(TimingDelay)
		TimingDelay--;
}
```



## 看门狗

STM32F10xxx 内置两个看门狗（独立看门狗和窗口看门狗），用来检测和解决由软件错误引起的故障，当计数器达到给定的超数值时，触发一个中断（仅适用于窗口型看门狗）或产生系统复位。

### 独立看门狗 IWDG （independent watch dog）

独立看门狗 IWDG 由专用的低速时钟 （LSI）驱动，即使主时钟发生故障他也仍然有效。IWDG 适合应用于那些需要看门狗作为一个在主程序之外，能够完全独立工作，并且对时间精度要求较低的场合。

- 自由运行的递减计时器
- 时钟由独立的 RC 振荡器提供（可在停止和待机模式下工作）
- 看门狗被激活后，则在计时器计数至 0x000 时产生复位





### 窗口看门狗 WWDG （windows watch dog）

窗口看门狗 WWDG 由从 APB1 时钟分频后得到的时钟驱动，通过可配置的时间窗口来检测应用程序非正常的过延或过早的操作。WWDG 适合那些要求看门狗在精确计时窗口起作用的应用程序

窗口看门狗通常被用来监测，由外部干扰或不可预见的逻辑条件造成的应用程序背离正常的运行序列而产生的软件故障。除非递减计数器的值在T6位变成0前被刷新，看门狗电路在达到预置的时间周期时，会产生一个MCU复位。在递减计数器达到窗口寄存器数值之前，如果7位的递减计数器数值(在控制寄存器中)被刷新， 那么也将产生一个MCU复位。这表明递减计数器需要在一个有限的时间窗口中被刷新。

特性：

- 可编程的自由运行递减计数器

- 条件复位

  当递减计数器的值小于0x40，(若看门狗被启动)则产生复位。

  当递减计数器在窗口外被重新装载，(若看门狗被启动)则产生复位

- 如果启动了看门狗并且允许中断，当递减计数器等于0x40时产生早期唤醒中断(EWI)，它可以被用于重装载计数器以避免WWDG复位。

工作原理

![image-20230714150316670](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230714150316670.png)

1. 当窗口看门狗的递减计数器 T[6:0] 大于上限窗口 W[6:0] 时，如果此时尝试去喂狗的话，将立即产CPU 复位
2. 当递减计数器 T[7:0] 到达下限窗口 0x3F 的值时，会立即触发 CPU 复位

### 串口 USART

在通信领域内，有两种数据通信方式：并行和串行通信。串口的数据传输是以串行方式进行的，串口在数据通信时，一次只传输一个 bit 的数据，串行数据的传输速度用 bps 或波特率来描述每秒传输的二进制位数，单位为 bps（bit per second），也称波特率

串行通信又可分为：单工，半双工，全双工（USART）

UART 通信应用场合

1. 芯片间通信

   芯片1 TXD -> RXD 芯片2；芯片1 RXD -> TXD 芯片2；GND -> GND

2. 芯片与 PC 机之间的通信

   - 芯片 TXD -> TTL 转换 USB 器 ->PC 机 RXD
   - 芯片 RXD -> TTL 转换 USB 器 ->PC 机 TXD                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     
   - 芯片 GND ->  ->PC 机 GND

3. 芯片与外围模块（传感器）之间的通信


要使用 同步串口的话，还需要 UASRT_CK  ：时钟

​	流控引脚： USART_CTS：硬件流控（MCU 的输入），clear to send 允许发送，用于向MUC 发送，模块是否准备好，低电平有效

​					  USART_RTS:  硬件流控（MCU 的输出），require to send 请求发送，由于 MCU 通知模块，模块是否可向 MCU 发送信息，低电平有效

重定向 fputc 函数

```C
int fputc(int c, FILE *fp){
	while((RESET == USART_GetFlagStatus(USART1,USART_FLAG_TXE)));
	USART_SendData(USART1,C);
	while((RESET == USART_GetFlagStatus(USART1,USART_FLAG_TC)));
}
// 使用时需要在 Target 里面勾选 Use Micro LIB
```

#### 自定义数据格式



### SPI 接口

serial peripheral interface，串行外围设备接口。主要应用于 EEPROM FLAGH，实时时钟，AD 转换器，还有数字信号处理器和数字信号解调器之间。

SPI 一种高速的，全双工，同步的通信总线，并且在芯片的管脚上只占用四根线，节约了芯片管脚，同时为 PCB 的布局节省空间。

越来越多的芯片集成了这种通信协议，STM32的 SPI 时钟最高可达 18MHz 支持 DMA，可以配置为 SPI 协议或者 I2S（音频通信）协议。

- MISO: 主收从发，master input slave output

- MOSI: 主发从收

- CS: 片选信号线，当有多个 SPI 设备与 MCU 相连时，每个设备的这个片选信号线是与MCU单独的引脚相连的，而其他的三根线则为多个设备并联到相同的线上。当CS信号线为低电平时，片选有效，开始SPI通讯。

- SCLK: 时钟线，由主设备产生，不同设备支持的时钟频率不一样，如 STM32 的SPI时钟频率最大为 fpclk/2

CS线由任意 IO 口就行



***

主从机都有一个串行移位寄存器，主机通过向他的SPI串行寄存器写入一个字节来发起一次传输。寄存器通过MOSI 将字节传给从机，同时从机通过MISO将自己的移位寄存器里面的内容传给主机。这样，主从机两个移位寄存器里面的内容完成了交换。

写操作和读操作时 同步完成的。如果只想进行写，则忽略接收到的字节，反之，若主机想要读取从机的一个字节，就必须发送一个空字节来引发从机的传输。

***

SPI模式，根据时钟极性（CPOL）和时钟相位（CPHA）配置的不同，分为四种模式

时钟极性：SPI 通讯设备处于空闲状态时（也可认为是SPI通讯开始时，即CS线为低电平），SCK信号线的电平信号。CPOL为0时，SCK在空闲状态为低电平，否则为高电平。

时钟相位：数据的采样的时刻。当CPHA为0时，MOSI或MISO数据线上的信号将会在SCK时钟线的第一个跳变沿（奇数沿，可为上升或下降）采样。当 CPHA 为1时，数据线在 SCK 的第二个跳变沿（偶数沿，可为上升或下降）采样

时钟极性（0或1）和时钟相位（0或1）决定在什么时候进行采集，共四种模式。为了配合不同的外设

总结：

- CPOL：0，空闲时候为低电平；1，空闲时候为高电平

- CPHA：0，在奇数个跳变沿采样数据；1，在偶数个跳变沿采样

  如：CPOL=0；CPHA=0；则在上升沿采样。CPOL=0；CPHA=1；则在下降沿采样（偶数）

  ​	  CPOL=1；CPHA=0；则在下降沿采样。CPOL=1；CPHA=1；则在上升沿采样（偶数）

-  





### I2C 通讯

I2C EEPROM AT24C02

I2C 使用多路硬件 I2C 会出现 bug，所以使用软件 I2C，不用硬件 I2C。

内存：1K = 128 * 8 bit      AT24C01A

​		  2K = 256 * 8 bit      AT24C02

​		  4K = 512 * 8 bit      AT24C04

​		  16K = 2048 * 8 bit

串行同步通讯，

SCL:

SDA:

靠地址来区分设备

1010 $A_2$  $A_1$  $A_0$  R/W    

写操作：R=0，地址为0XA0

读操作：R=1，地址为0XA1

时序

启动：SCL 为高，SDA 由高向低的跳变

停止：SCL 为高，SDA 由低向高的跳变

应答：告诉发送方已经成功收到8个字节， 接收方发送 0，在第九个时钟周期



### 































### 电源控制 PWR

![image-20230714213516131](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230714213516131.png)

STM32F10xxx 具有三种低功耗模式：

1. 睡眠模式：Cortex -M3 内核停止，所有外设包括 Cortex - M3 内核的外设，如 NVIC、系统时钟（SysTick）等仍然运行  （最不省电）
2. 停止模式：所有的时钟都停止  （省电次之）
3. 待机模式：1.8V 电源关闭     （最省电）

![image-20230714213443961](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230714213443961.png)

1. 睡眠模式：通过执行 WFI 或 WFE 指令进入睡眠模式

```
__WFI();  // ARM 内核命令进入睡眠模式 __命令
```

2. 停止模式：

```
RCC_APB1PeriphClockCmd(RCC_APB1Periph_PWR,ENABLE);  // 打开 PWR 所挂载的时钟
PWR_EnterSTOPMode(PWR_Regulator_LowPower,PWR_STOPEntry_WFI);  // 进入停止模式
```

唤醒之后，高速内部(72MHz)、高速外部时钟是关闭的，只有低速内部（40KHz）和低速外部时钟在工作，钟频率变了，LED 闪烁频率变慢，而且 printf 函数不能正常使用

所以，需要重新初始化时钟

```
SetSysClock_to_72MHZ();  // 从 SystemInit 中复制时钟复位函数 SetSysClock(); 并重新定义
```

3. 待机模式

   ```
   RCC_APB1PeriphClockCmd(RCC_APB1Periph_PWR,ENABLE);  // 打开 PWR 所挂载的时钟
   PWR_EnterSTANDBYMode();  // 进入待机模式
   ```

   

待机唤醒之后约等于重启后状态





### DMA 控制器 direct memory access 直接存储器存取

==直接存储器存取(DMA)用来提供在外设和存储器之间或者存储器和存储器之间的高速数据传输==。==无须 CPU 干预，数据可以通过 DMA 快速地移动==，这就节省了CPU的资源来做其他操作。 两个 DMA 控制器有 12 个通道(DMA1 有 7 个通道，DMA2 有 5 个通道)，每个通道专门用来管理来自于一个或多个外设对存储器访问的请求。还有一个仲裁器来协调各个DMA请求的优先权。

STM32F103C8T6 只有 DMA1 有 7 个通道，每个通道专门用来管理来自于一个或者多个外设对存储器访问的请求。还有一个仲裁器来协调各个 DMA 请求的优先权。



### ADC

analogue digital convet 

==C8T6具有 ADC12, 即 ADC1 和 ADC2==

12 位 ADC 是一种逐次逼近型模拟数字转换器。具有多达 18 个通道，可测量 16个外部和2个内部信号源（通道16 接内部温度传感器，测量V_sense，17接内部电源模块，测量V_refint）。各个通道的 A/D 转换可以单次，连续，扫描或者间断模式执行。ADC 的结果可以左对齐或右对齐方式存储在 16 位数据寄存器中。

### TIM2 定时器

通用定时器（TIM2-5），基本定时器（TIM6 和 TIM 7）和高级控制定时器（TIM1 和 TIM 8）

==C8T6只有 TIM1, 2, 3, 4 , 一个高级，三个通用定时器==

按照硬件复杂程度，从低到高

- 基本定时器（最简单）：只有定时，计数功能。TIM6 和 TIM7 各包含一个 16 位自动装载计数器，由各自的可编程预分频器驱动。可以为通用定时器提供时间基准，特别的可以为 DAC（数模转换器）提供时钟，实际上，它们在芯片内部直接连接到 DAC 并通过
- 通用定时器：包含基本定时器的功能，
- 高级定时器：由一个 16 位的自动装载计数器组成，它由一个可编程的预分频器驱动。适合多种用途，包含测量输入信号的脉冲宽度（输入捕获），或者产生输出波形（输出比较，PWM, 嵌入式死区时间的互补PWM等）。使用定时器预分频器和 RCC 时钟控制预分频器，可以实现脉冲宽度和波形周期从几个 us 到几个 ms 的调节。高级控制定时器和通用定时器是完全独立的，它们不共享任何资源，所以它们可以同步操作。
- 

重点内容

1. ==基本定时和计数功能（三种定时器都有的功能）==
2. 输出比较 ( PWM 控制舵机和步进电机）   // 第二阶段视频
3. 输入捕获（ 测量波形周期，高电平时间）  //



#### 定时器 PWM

PWM 参数：周期，占空比（高电平时间/一个周期）

TIM_Period 自动重装载值，写入 TIMx->ARR 寄存器，

TIM_Pulse  比较值，写 TIMx->CCRx 寄存器，与 TIM_SetComparex(TIMx, compareValue) 功能一致

TIM_OCMode ：模式 TIM_OCMode_PWM1 或者 TIM_OCMode_PWM2

TIM_OCPolarity : TIM_OCPolarity_High 或者 TIM_OCPolarity_Low

如：

TIM_OCMode_PWM1 模式，TIM_OCPolarity_High ，

ARR 决定一个周期计多少数，每个周期内，当此时计数值小于 CCRx 则，输出高电平；否则输出低电平。



#### 定时器输入捕获





### RTC 







## STM32 进阶及外设模块应用开发

### 1. 矩阵按键









## Linux 学习



### 安装

虚拟机的创建：

- CPU 分配：分配核数量，一般五五分（i7八核16线程）
- 内存分配：16G 以上
- 硬盘：需要创建一个独立的分区，专门给虚拟机用，最好 300G 以上

### 命令

| ls   目录信息查看           | cd   目录切换           |
| --------------------------- | ----------------------- |
| pwd  当前路径显示           | uname   系统信息查看    |
| clear   清屏                | cat   显示文件内容      |
| sudo   管理员               | cp   文件拷贝           |
| su  切换用户                | mv   移动文件夹或重命名 |
| mkdir   创建文件夹          | touch   创建文件        |
| rm  删除                    | rmdir  删除目录         |
| ifconfig   显示网络配置信息 | reboot  重启            |
| poweroff   关机             |                         |
| man   系统帮助              | sync   数据同步写入磁盘 |
| find   查找文件             | grep  查找内容          |
| du  文件夹大小查看          | df  磁盘空间检查        |
| gedit  打开某个文件         | ps  当前系统的进程查看  |
| top  查看进程实时运行状态   | file   查看文件类型     |
|                             |                         |

命令 --help	打开 help

ls -a	显示所有文件和文件夹

ls -l	文件一列排布  = ll



sudo su 切换为管理员

sudo su zhihongzeng



mv test bb  将文件夹 test 重命名 bb 

mv a.c /home/zhihongzeng/test/	把 a.c 文件移动到 test 文件夹下



rm a.c	删除文件	rm  -rf a.c 

rm  test -rf	删除文件夹	rm  -rf test   或者 rmdir test



使用 ifconfig 之前需要安装 sudo spt instll net-tools

sudo ifconfig ens33 192.168.1.110	修改 ip 地址

sudo ifconfig ens33 reload	重启网络







### Ubuntu 文件系统结构

- /bin：存放二进制可执行文件，这些命令在单用户模式下也能够使用，可以被 root 和一般账户使用
- /boot：Ubuntu 内核和启动文件，比如 vmlinuxz-xxx， gurb 引导转载程序
- /dev：设备驱动文件
- /etc：存放一些系统配置文件，比如用户账号密码文件，各种服务器的起始地址
- /home：系统默认的用户文件夹，一般创建用户账号的时候，默认的用户文件夹都会放到此目录下
- /lib：存放库文件
- /media：放置可插拔设备，比如 SD 卡，U盘
- /mnt：用户可使用的挂载点，比如要挂载一些额外的设备，那么就可以挂载到此处
- /opt：可选的文件夹和程序存放目录，给第三方软件放置的目录
- /root：root 用户目录，机系统管理员目录
- /sbin：和bin 类似，也是存放一些二进制可执行文件， sbin 下面的一般是系统开机过程的所需文件
- /srv：服务相关目录，比如网络服务
- /sys：记录内核信息，虚拟文件系统
- /tmp：临时目录
- /var：存放一些变化的文件，比如日志文件
- /usr：usr 不是 user 的缩写，而实 UNIX software resource 的缩写，存放系统用户相关的文件，会占用大量的空间
- /proc：虚拟文件目录，数据放置在内存中，存放系统运行信息

绝对路径和相对路径

- 绝对路径：从根目录 / 算起的路径

- 相对路径：相对于当前路径的文件路径写法，如 /home/zhihongzeng，不是以 / 开头

  ​	.  代表当前路径，或者 ./

  ​	..  代表上一级目录， 或者  ../

### Ubuntu 磁盘管理

/dev/sdx 文件，此类文件是磁盘设备文件，并不能直接访问磁盘，必须要将磁盘挂载到某一目录下才可以访问

如，接上 U 盘，多出 sdb 和 sdb1，其中 sdb 代表 U 盘，sdb1 代表 U 盘第一个分区

df ：列出文件系统的整体磁盘使用，主要查看文件系统的使用量

du：评估文件系统的磁盘使用量，主要查看单个文件的大小

   du -h --max-depth=1

磁盘的挂载和卸载

mount 和 umount 命令

   sudo mount /dev/sab1   /media/zhihonzeng/where 这样做会出现中文乱码，使用   sudo -o iocharset=utf8 mount /dev/sab1   /media/zhihonzeng/where 解决



​	sudo umount /media/zhihongzeng/what -f

磁盘分区

fdish -l

磁盘格式化（就是给分区装一个文件系统，如FAT ，EXT）

磁盘分区创建好后就可以格式化磁盘，使用 mkfs

mkfs -t vfat /dev/sdx

Windows 下使用软件 SDFormat 轻松格式化

### Ubuntu 下压缩与解压缩

Linux 下常用的压缩扩展名：.tar	==.tar.bz2	.tar.gz==

英文 Linux 下很多文件是 .bz2     .gz  的压缩文件，因此需要在 Windows 下安装 7ZIP 软件

------

gzip 压缩工具

==gzip  xxx  //压缩==

==gzip -d xxx.gz  //解压缩==

gzip 对文件夹进行压缩

==gzip -r xxx  // 对文件夹进行压缩，本质是把文件夹里面文件单独各个压缩成压缩文件，并没有打包成 xxx.gz 文件==

==gzip -rd xxx.gz  //对文件夹进行解压缩==

gzip 虽然可以对文件夹进行压缩，但是不能提供打包的服务，只是对文件夹中的所有文件进行了单独的压缩 

------

bzip2 压缩工具

和 gzip 类似，bzip2 工具负责压缩和解压缩 .bz2 格式的压缩包

bzip2 -z xxx  //压缩

bzip2 -d xxx.gz  //解压缩

------

tar 打包工具

tar 工具提供打包服务，就是将多个文件打包

tar -f   //使用压缩文件

tar -c   // 创建压缩文件

tar -x  //解压缩

tar -j   // 使用 bzip2 压缩格式

tar -z  //使用 gzip 压缩格式

tar -v   //打印命令执行过程    如 tar -vcf

tar -vcf test test.tar  //将 test 文件夹打包成 test.tar

tar -vxf test.tar  //解包

上面的 tar 命令只提供了打包和解包的功能，tar 在提供打包和解包的同时使用 gzip/bzip2 进行压缩，实现了类似 Windows 系统下 winRAR 软件的功能

- ==对 tar.bz2 文件进行压缩和解压缩==

  ==tar -vxjf xxx.tar.bz2  //解压缩==

  ==tar -vcjf xxx.tar.bz2  xxx //压缩xxx 成 xxx.tar.bz2==

- ==对 tar.gz 进行压缩和解压缩==

  ==tar -vxzf xxx.tar.gz   //解压缩==

  ==tar -vczf xxx.tar.gz xxx   //压缩xxx 成 xxx.tar.bz2==

- 其他格式压缩文件

  - .rar 格式

    需要先安装 rar ：sudo apt-get install rar

    rar x xxx.rar   //解压缩

    rar a xxx.rar xxx  // 压缩 xxx 成 xxx.rar

  - .zip 格式

    zip -rv xxx.zip xxx  //将 xxx 压缩成 xxx.zip

    unzip -v xxx.zip   // 解压缩

------

### Ubuntu 用户和用户组

Linux 是一个多用户操作系统，不同的用户拥有不同的权限，可以查看和操作不同的文件，三种用户

- 初次创建的用户
- root 用户
- 普通用户

初次创建的用户权限比普通用户多，但是没有 root 权限多，初次创建的用户可以创建普通用户

Linux 用户记录在 /etc/passwd 文件夹

Linux 用户密码记录在 /etc/shadow 文件夹

每个用户都有一个ID，叫做 UID

Linux 用户组

为了方便管理，将用户进行分组，这样就可以设置非本组成员不能访问某些文件，每个用户可以属于不同的组

用户：家里有你，弟弟，妹妹，每个人都有自己的房间，你们三个是用户，都不能随便翻别人了的房间

用户组：你们三个人是一个家庭的，也就是属于一个用户组，你们三个可以共用厨房，书房等空间

==因此：用户和用户组的存在是为了控制文件的访问权限==

==每个用户都有一个 ID ，叫 GID==

==用户组信息存储在 /etc/group 文件夹==

------

创建用户和用户组

图形化界面安装，需要先安装 sudo apt-get install gnome-system-tools

使用软件 用户和用户组

或使用命令创建用户和用户组

### Ubuntu 文件权限

- 文件权限概念

  文件权限指不同的用户或用户组对某个文件拥有的权限，文件的权限分为三种

  1. 读

  2. 写

  3. 可执行

     -rw-rw-r 就是文件权限，第一位表示文件类型，剩下的每三位表示一组权限，分别对应拥有者权限，拥有者所在组权限，其他用户权限

     可以使用二进制表示文件权限

     r=100（4）

     w=010（2）

     x=001（1）

     使用 ls a.c -l  出现 -rwrw-rw-r， 意思是 a.c 用户拥有读写权限，无可执行权限；组内其他用户也是一样；其他用户仅有可读权限

     111 | 111 | 111 或者 777 说明文件权限完全开放

- 文件权限修改

==chmod   修改文件权限==

gcc a.c -o a   //将 a.c 文件编程成为 a

./a    //执行 a

ls ./a -l  //查看文件 a 的权限    -rwx-rwx-r-x

chmod 664 ./a  //修改文件 a 的权限  变为  -rw-rw-r--

chmod 111 ./a   //修改权限为  ---x--x--x

==chown    修改文件所属用户==

sudo chown root ./a  //将 a 文件的所属用户改成 root

sudo chown .root ./a  //将 a 文件的用户组改成 root

sudo chown zhihongzeng.zhihongzeng ./a   //将 w 文件的所属用户和用户组都改成 zhihongzeng

 sudo chown -hR root.root ./test  //将 test 文件夹内所有文件的所属用户和用户组都改成 root   （ -hR 要大写）

### Linux 连接文件

Linux 有两种连接文件，符号连接（软连接）和硬连接，==符号连接类似于 Windows 下的快捷方式==，==硬连接通过文件系统的 inode 连接来产生新文件名，而不是产生新文件==。

inode 记录文件属性，一个文件一个 inode ，inode 相对于文件 ID ， 查找文件的时候要先找到 inode ，然后才能读出文件内容分

- 符号连接

  ln 用于创建链接文件

  ln 【选项】 源文件 目标文件

  选项：-s 创建符号连接

  ​		  -f 强制创建连接文件，如果目标存在，那么先删除目标文件，然后再建立连接文件

  ==符号连接类似于 Windows 下的快捷方式，用的多，符号连接相对于创建了一个独立的文件，这个文件会让数据读取指向他连接的那个文件的文件名，特点==

  1. 可以连接到目录

  2. 可以跨文件系统

  3. 删除源文件后，符号连接文件也就打不开了

  4. 符号链接文件通过 -> 来指示具体的连接文件

  5. 符号连接要使用绝对路径，否则会出问题，拷贝时候文件失效

     ln -s /home/zhihongzeng/test/a asoftlink

  6. 拷贝的时候需要 cp -d 

- 硬连接

  ln 源文件 硬连接文件

  硬连接是多个文件都指向同一个 inode，并不常用，硬连接知识点

  1. 具有相同 inode 的多个文件互为硬连接文件，创建硬连接相对于文件实体多了入口
  2. 对于硬连接文件，只有删除了源文件以及对应的所有硬连接文件，文件实体才会被删除
  3. 根据硬连接文件的特点，我们可以通过给文件创建硬连接的方式来防止文件误删除
  4. 不论修改源文件还是硬连接文件，另一个文件的数据都会被改变
  5. 硬连接不能跨文件系统
  6. 硬连接不能连接到目录

### vim 编辑器

Linux 系统自带 vi 编辑器，但是 vi 编辑器太难用了，通常使用 vim 编辑器，安装命令 sudo apt-get install vim

：qa！退出编辑器

- vim 编辑器三种工作模式

  vi xxx  使用 vi 编辑器打开文件

  ==一般模式==（指令模式）默认模式，用 vi 打开一个软件之后自动进入该模式，

  ==编辑模式==：一般模式中无法编辑文件，要编辑文件要进入编辑模式，按下 “i、l、a、A、o、O、s、r”等就会进入编辑模式，一般按下 a 进入编辑模式， 按下 esc 键可以退出编辑模式

  ==命令行模式==（底行模式）：先进入一般模式，然后输入 、 /、 ？这三个任意一个就可以进入到命令行模式

- 保存退出

  当文件编辑好之后，输入 wq 来保存退出，：q 退出（未修改）， ：q！退出不保存， w 保存

- 其他操作方法

  按下键盘上的上下左右键来控制光标

  一般模式下：

  1. dd   删除光标所在行   ndd 删除光标行以及以下 n 行
  2. u    撤销，恢复上一步
  3. .     重复前一个操作
  4. yy    复制光标所在行
  5. nyy   复制光标所在向下  n 行，比如，10yy 就是复制光标下10行
  6. p 和 P   p 为粘贴到光标下一行，P 粘贴到光标上一行

### Linux C 编程

- 编写 C 程序

  使用 vim 编辑器编写程序，也可以使用 vscode

- 编译 C 程序

  使用 gcc 编译器编译 C 程序

- Make 工具 Makefile 文件

  当源码文件比较多的时候就不适合通过直接输入 gcc 命令来编译，这时候就需要一个自动化的编译工具

  make：一般来说 GNU Make，是一个软件，用于将源代码文件编译为可执行的二进制文件，make 工具主要用于完成自动化编译，make 工具编译的时候需要 Makefile 文件提供编译文件

  Makefile：make 工程所使用的文件，Makefile 指明了编译规则

  利用 make 工具可以自动完成编译工作，包括

  - 如果修改了某几个源文件，这只重新编译这几个源文件

  - 如果某个头文件被修改了，这重新编译所有包含该头文件的源文件

    利用这种自动化编译可以大大简化开发工作，避免不必要的重新编译，make 工具通过一个称为 Makefile 的文件来完成并自动维护编译工作，Makefile 文件描述了整个工程的编译，连接规则

    Makefile 描述了整个工程的编译连接规则，Makefile 的基本规则为

    ```
    TARGET...: DEPENDENCIES ...
    
    ​	COMMAND
    
    ...
    ```

    其中，TARGET 目标工程产生的文件，如可执行文件和目标文件，目标可以是要执行的动作，如 clean，也称为伪目标

    DEPENDENCIES 依赖是用来产生目标的输入文件列表，一个目标通常依赖于多个文件

    COMMAND 命令是 make 执行的动作（命令是 shell 命令或是可在 shell 下执行的程序），注意每个命令行的起始字符必须是 TAB 字符

    如果 DEPENDENCIES 中有一个或多个文件更新的话，COMMAND 就要执行，这就是 Makefile 最核心的内容

    比如：

    ```
    main：main.o input.o calcu.o
    	gcc -o main main.o input.o calcu.o
    main.o:main.c
    	gcc -c main.c
    input.o:input.c
    	gcc -c iput.c
    calcu.o:calcu.c
    	gcc -c calcu.c
    clean:
    	rm *.o
    	rm main
    ```

    命令列表中的每条命令必须以 TAB 键开始

    | make           | 做菜 | 描述         |
    | -------------- | ---- | ------------ |
    | make 工具      | 厨师 | 依照规则做菜 |
    | gcc 编译环境   | 厨具 | 加工工具     |
    | Makefile       | 食材 | 所需材料     |
    | 最终可执行文件 | 最终 | 最终         |

    

- 

​                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           

### Makefile 基本语法

- 变量，都是字符串类型

```
# Makefile 变量的使用
objects = main.o input.o calcu.o
main: $(objects)
	gcc -o main $(objects)
```

- = 取变量最终的值赋值，  ：= 取变量当前值赋值   ？= 若之前未赋值，则执行     += 添加内容

- 模式规则，将所有的 .c 编译成 .o

  ```
  %.o: %.c
  	命令
  %.o : %.c
  	gcc -o $<   # $< 自动化变量
  ```

  

- 伪目标  （避免 Makefile 中的命令与文件重名导致 命令不能使用）

  ```
  .PHONY:clean   //在命令之前加入
  ```

  

### shell 脚本

shell 脚本，将很多命令放在一个文件里面，然后直接运行这个文件即可，类似于 Windows 的批处理命令，==shell 脚本就是将连续执行的命令写成一个文件==

shell 脚本提供数组，循环，条件判断等功能，shell 脚本一般是 Linux 运维或者系统管理员要掌握的，对于嵌入式开发，只需要掌握 shell 脚本最基础的部分

- 写法

  shell 脚本是一个纯文本文件，命令从上而下，一行一行地开始执行，shell 脚本扩展名为 sh，第一行为

  ```
  #!/bin/bash
  ```

  表示使用 bash

- 语法

  ```
  #!/bin/bash
  echo "Hello Linux!" 
  ```

  输入输出：

  ```
  #!/bin/bash
  echo "please enter your name:"
  read name
  echo "your name:" $name
  ```

  数值计算

  ```
  #!/bin/bash
  echo "please input two int number"
  read -p "the first number:" first
  read -p "the other number" second
  total=$(($first+$second))   // 等号两端不能有空格
  echo "$first + $second = $total"
  ```

  test 命令

  默认变量

  条件判断，

  

C++ 的编译过程

main.c -- 预处理(pre-processing) -- main.i --编译(compiling) --main.s -- 汇编(assembling) -- main.o -- 链接(link) -- main.exe

```
g++ main.cpp -o main
```

## Linux 下 ARM 开发

### 环境搭建

- TPD 文件传输

- NFS 和 SSH 服务器

- 交叉编译器安装

  交叉是指在 X86 架构的 PC 上编译 ARM 架构代码的 GCC 编译器，即在一个架构上编译另一个架构的代码，相当于两种架构交叉了

  sudo apt-get install gcc-arm-linux-gnueabihf

- VSCode

  插件：C/C++   				

  ​			C/C++ Snippets			C/C++ 重用代码块

  ​			C/C++ Advanced	C/C++ 静态检测

  ​			Code Runner		代码运行

  ​			Include AutoComplete	自动头文件包含

  ​			Rainbow Brackets	彩虹花括号

  ​			One Dark Pro		主题

  ​			GBKtoUTF8	将 GBK 转换为 UTF8

  ​			ARM	支持ARM汇编语法高亮显示

  ​			vscode-icons	图标插件，资源管理器下各个文件夹的图标

  ​			compareit	比较插件，可用于比较两个文件的差异

  ​			DeviceTree	设备树语法插件

  ​	

- CH340 串口驱动安装（在 Windows 系统下安装就可）

- SecureCRT 软件安装和使用



## C Primer Plus 5

### Chapter 3 数据和 C

#### 整形 int

进制输出

```C
printf("Dec %d = %d.\n",100,100);    //十进制
printf("Octal %d = %#o.\n",100,100); //八进制 以 0 开头
printf("Hex %d = %#x.\n",100,100);   //十六进制 以 0x 或 0X 开头
```

其他整数类型

- short int：简写 short，有符号，可能占用比 int 类型更少的存储空间，用于仅需小数值的场合以节省空间
- long int：简写 long，有符号，可能占用比 int 类型更大的存储空间，用于使用大数值的场合
- long long int：简写 long long，有符号，可能占用比 int 类型更少的存储空间，用于使用大数值的场合空间
- unsigned int：写 unsigned，非负，16 位的 int 取值范围 -32768~32768，而 unsigned int 表示 0~65535

对于 32 位单位，long 的最小取值 -2147483647~2147483647，unsigned long 取值范围为 0~4294967295

020L  ： 八进制 long 类型

0x10L ：十六进制 long 类型

5ull 表示 5 是 unsigned long long 类型

10LLU ：unsigned long long 类型

9Ull  ：unsigned long long 类型

```C
unsigned int un = 300000000;
printf("un = %u\n",un);
printf("un = %d\n",un); // 结果为负，因为超出 int 范围
// un 存储的值是固定的，但是不同的输出格式会读出不同的值
// 若 un =300 不超出 int 范围，则两种方式都打印正确

short sh = 300000000;
printf("sh = %hd\n",sh);
printf("sh = %d\n",sh); // 结果一样
// 无论使用 %d 还是 %hd，short 类型变量在传递参数时 C 自动将 short 转换为 int 类型

long lo= 65537;
printf("lo = %d\n",lo);  // 输出 65537
printf("lo = %hd\n",lo); // 输出 1
// %hd 说明符将他只显示后 16 位
```

==printf（） 说明符决定数据的显示方式，而不是决定数据的存储方式==

==使用 printf 时，切记每个要显示的值都必须对应自己的格式说明符，并且显示值的类型要同说明符先匹配==

```C
printf("the char %c is saved as an int number %d.\n",'a','a')
```

short 使用 %hd 和 %d一样

long             %ld

long long      %lld

unsigned int   %u

#### 字符类型 char

用于储存字母和标点符号之类的字符，但是 char 实际存储的是整数而不是字符。为了处理字符，使用一种数字编码，用特定的整数表示特定的字符，美国最常用的编码时 ASCII 码，定义了128个字符的编码（0-127）

```C
   0 NUL          32  SPACE        64  @            96  `
   1 SOH          33  !            65  A            97  a
   2 STX          34  "            66  B            98  b
   3 ETX          35  #            67  C            99  c
   4 EOT          36  $            68  D           100  d
   5 ENQ          37  %            69  E           101  e
   6 ACK          38  &            70  F           102  f
   7 BEL          39  '            71  G           103  g
   8 BS           40  (            72  H           104  h
   9 HT           41  )            73  I           105  i
  10 LF           42  *            74  J           106  j
  11 VT           43  +            75  K           107  k
  12 FF           44  ,            76  L           108  l
  13 CR           45  -            77  M           109  m
  14 SO           46  .            78  N           110  n
  15 SI           47  /            79  O           111  o
  16 DLE          48  0            80  P           112  p
  17 DC1          49  1            81  Q           113  q
  18 DC2          50  2            82  R           114  r
  19 DC3          51  3            83  S           115  s
  20 DC4          52  4            84  T           116  t
  21 NAK          53  5            85  U           117  u
  22 SYN          54  6            86  V           118  v
  23 ETB          55  7            87  W           119  w
  24 CAN          56  8            88  X           120  x
  25 EM           57  9            89  Y           121  y
  26 SUB          58  :            90  Z           122  z
  27 ESC          59  ;            91  [           123  {  
  28 FS           60  <            92  \           124  |  
  29 GS           61  =            93  ]           125  }
  30 RS           62  >            94  ^           126  ~
  31 US           63  ?            95  _           127  DEL
```

转义序列

```C
\a  警报（ANSI C）    \b  退格           \f   走纸
\n  换行              \r  回车           \v   垂直制表符  
\\  输出 \            \‘  输出 ’         \“  输出 ” 
\?  输出 ?            \0xx  八进制值（xx表示一个八进制数字，可省略0）     \xhh  十六进制值（hh 表示一个十六进制数）    
```



#### _Bool 类型

布尔值，即逻辑值，1 true  0 false

#### 浮点类型 float double  与 long double

float 类型必须至少能表示 6 位有效数字，取值范围至少为  $10^{-37} -  10^{37}$  ，6位有效数字指浮点数至少应能精确表示像 33.333333 这样的数字的前 6 位

double 双精度浮点型，必须至少能表示 10 位有效数字，和 float 具有相同的最小取值范围要求，一般 double 使用 64 位而不是 32 位长度。

浮点常量：

字母 e 或 E 代表 10 的指数

2.87e-5         3.e10        .45e-12       .2        4e6       100.

在浮点常量中不使用空格

默认情况下，编译器将浮点常量当作 double 类型，保证计算精度

```C
printf("a float = %f, which also = %e.\n",3.14,3.14);  // 3.140000    3.140000e0
printf("overfloat = %f\n",4e30*100.f);   // inf   发生上溢
```

#### sizeof 运算符

返回给出类型的大小，单位为 字节

```C
printf("sizeof char = %d\n",sizeof(char));         // char  1 字节 8 位
printf("sizeof int = %d\n",sizeof(int));           // int  4
printf("sizeof short = %d\n",sizeof(short));       // short 2
printf("sizeof long int  = %d\n",sizeof(long int));// long 8
printf("sizeof _Bool = %d\n",sizeof(_Bool));       // _Bool 1
printf("sizeof float = %d\n",sizeof(float));       // float 4
printf("sizeof double = %d\n",sizeof(double));	   // double 8
printf("sizeof long double = %d\n",sizeof(long double)); // long double 16
```

printf() 语句将输出传递给 缓冲区 buffer 的中介存储区域，缓冲区域中的内容在不断地传递给屏幕，==当缓冲区满的时候，程序运行结束的时候，遇到换行符（\n）的时候以及需要输入的时候，缓冲区内容传递给屏幕或文件，这称为刷新缓冲区（flushing the buffer）。==

```
printf("cost is %d\n",3.14);   // 3
printf("pi is %f\n",3.1415926536);  // 3.141493
```



### Chapter 4 字符串和格式化输入/输出

字符串 character string，就是一个或多个字符的序列，"xxx"，双引号标识字符串，实际是存储在 char 数组中，字符串中的字符存放在相邻的存储单元中，每个字符占用一个单元，最后一个单元是 '\0' 空字符，用来表识字符串的结束。空字符不是数字0，是非打印字符，其 ASCII 码值为0，这意味着数组的单元数必须至少比要存储的字符数多1.

数组是同一类型的数据元素的有序序列

scanf() 开始读取输入以后，会在遇到的第一个空白字符空格、制表符tab、或者换行符 \n 或者回车 \r 处停止读取。C 语言使用其他读取函数如 gets（） 来处理一般字符串。

'x'   实际存储为 x

"x" 实际存储为  x   \0    占用两个单元

#### strlen() 函数

返回字符串的长度，以字符为单位。sizeof() 返回数据的大小，以字节为单位

```C
#include <strlen>    // 使用 strlen() 需要
#define NAME "Bob"
char ch[100] = "hello world!";
printf("sieze of the string = %d\r\n",sizeof(ch));   // = 100
printf("strlen of the string = %d\r\n",strlen(st));  // = 12   
printf("%s comoses of %d characters and occupies %d memory.\r\n",NAME,strlen(NAME),sizeof(NAME));   //  3 characters 4 memory
```

#### printf() 函数

转换说明符

![image-20230716103113482](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230716103113482.png)

修饰符

```C
const double RENT = 3852.99;
printf("*%f*\r\n",RENT);     // 3852.990000
printf("*%5f*\r\n",RENT);    // 3852.990000  宽度不够，自动补全
printf("*%3.1f*\r\n",RENT);  // 3853.0
printf("*%10.3f*\r\n",RENT); //  *  3852.990*   右对齐，用空格补齐 10 位宽度
printf("*%.3e*\r\n",RENT);     // 3.853e+03
printf("*%10.3e*\r\n",RENT);   //  * 3.853e+03*  右对齐，补齐 10 位宽度
printf("*%+.3f*\r\n",RENT);  // +3852.990
printf("*%010.2f*\r\n",RENT);   // *0003582.99*  用 0 补齐 10 位宽度

printf("*%x,%#x*\r\n",31,31); // *1f*  *0x1f*
printf("*%X,%#X*\r\n",31,31); // *1F*  *0X1F*

printf("*%d*,*% d*\r\n",42,42);    // *42*  * 42*
printf("*%d*,*% d*\r\n",-42,-42);  // *-42*  *-42*
printf("*%5d* \r\n",-42);          // *  -42*  
printf("*%5.3d* \r\n",-42);        // * -042* 
printf("*%05.3d* \r\n",-42);        // * -042*  // 在 %d 整型数据格式中使用精度说明符 %.3d 指保留三位有效数字，但是 %05.3d 如果 0 标志和精度说明符同时出现，那么 0 表指会被忽略 
printf("*%05d* \r\n",-42);         // *-0042* 

#define BLURB "Hello World"
printf("*%s*\r\n",BLURB);       // *Hello World*
printf("*%4s*\r\n",BLURB);      // *Hello World*
printf("*%14s*\r\n",BLURB);     // *    Hello World*  // 右对齐，用空格补齐 14 位宽度
printf("*%-14.5s*\r\n",BLURB);  // *Hello          *  // 左对齐，显示五位有效字符，用空格补齐 14 位宽度

```

转换（conversion）把存储在计算机中的二进制格式的数值转换成一系列字符（一个字符串）以便于显示，如数值 76 ，内部存储为 01001100 %d 转换说明符将之转换成字符 7 和 6，并显示成 76，转换说明符实际就是翻译说明。

==printf() 函数的返回值是所打印字符的数目。==

占位符 *

```C
printf("%05d,123");  // 00123
printf("%0*d,5,123");  // 00123
// 常用于打印地址
printf("%0*x",2*sizeof(void *),&ptr);  // 20010028
printf("%08x",&ptr);  // 同上
printf("%p",&ptr);    // 同上
```

#### scanf() 函数

把输入的字符串转换成各种形式：整数，浮点数，字符和字符串，

是 printf() 的逆操作，把整数，浮点数，字符和字符串转换成要在屏幕显示的格式

两者都使用控制字符串和参数列表，控制字符串指出输入将被转换成的格式，主要区别在于参数列表中，printf 函数使用变量名，常量和表达式，而 scanf 使用指向变量的指针

- 使用 scanf 读取某种基本变量类型（int char float）的值，在变量前加 &
- 使用 scanf 把一个字符串读进一个字符数组中，不加



scanf 使用 空格，换行，制表符Tab，来决定怎样把输入分成几个字段

```C
int in_int;
char in_char[10];
float in_float;
scanf("%d %f %s",&in_int,&in_float,in_char);  // 依次输入 int float 和 string 以 空格，换行，制表符Tab 为分隔
printf("the input int is %d, input float is %.2f, and the input char is %s\r\n",in_int,in_float,in_char);
scanf("%d,%f,%s",&in_int,&in_float,in_char);  // 依次输入 int float 和 string 以 , 为分隔
```

scanf 和 printf 所用的转换说明符几乎完全相同，区别在于 printf 把 %f %e %E %g %G 同时用于 float 和 double 类型，而 scanf 只用于 float 类型，而用于 double 类型时要用 1 修饰

==scanf（%c, &char）不会跳过 空格，换行等，因为他们也是 字符==

==除了 %c 以外的说明符会自动跳过输入项之前的空格==

```c
char in_char;
int in_int;
scanf("% c",&char);  // 读取遇到的第一个非空白字符
// 连续输入时，在 %c 前加 空格
scanf("%d %c",&in_int,&in_int);
```

scanf() 的返回值是 成功读入的项目的个数

- 返回 0 ：当它期望一个数字而您却键入了一个非数字字符串时

- 返回 EOF（-1）：当它检测到 "文件结尾" 时

  ```
  while(scanf("%d",&input_int))  // 输入 整数或浮点数才进入循环，否则不进入
  	//
  printf("Done\r\n");
  ```

  

#### * 修饰符在 printf 和 scanf 中

1. 在 printf() 中

%*d 参数列表中应该包含一个 * 的值和一个 d 的值，其中 * 的值表示要显示的数据宽度

```C
unsigned int width;
int in_int;
float in_float;
printf("Please enter an int number and the width of it");
scanf("%d%d",&in_int,&width); //  45  4
printf("the input int = *%*d*, and the width = %d",width,in_int,width);  // *  45*   4
scanf("%f%d",&in_float,&width); //  3.14  4
printf("the input float = *%7.*f*, and the width = %d",width,in_float,width);  // *  3.140*   4   // 输出 7 位宽度，4 位精度
```

2. 在 scanf() 中

==与 printf 中截然不同，* 放在 % 和说明符字母之间时，使得函数跳过相应的输入项目==

```c
int in_int;
printf("Please enter an int number:");
scanf("%*d %*d %d",&in_int);             // 12 13 14 enter
printf("the input int = %d\r\n",in_int); // 14 
```

printf 打印整齐数据格式

```C
int a[9] = {1,2,3, 44,55,66, 333,4455,66666};
for(char i = 0; i<9; )
    printf("%-8d %-8d %-8d",a[i++],a[i++],a[i++]);  // 指定足够大的宽度使输出更加整齐清晰
```

```C
int in_int;
char name[10];
printf("Please enter your name:");
scanf("%s",name);                               // zhihong zeng
printf("Please enter your weight:");
scanf("%d",&in_int);
printf("%s, your weight = %d\r\n",name,in_int); // zhihong 随机值
// 程序把 zenghong 读取给 name 而 zeng 还留在缓冲区内，把 zeng 读取给 in_int 出错
```



%lu  unsigned long

%4o  八进制输出，四个宽度右对齐，如 *  77*， * 212*

%#x  十六进制 0x 输出   			%#X   十六进制 0X 输出				%#4X   十六进制 0X 输出，四个宽度右对齐

%8.8s    只输出字符串中前八个字符

double 可以使用 %ld

空白字符有 空格 回车 制表符 Tab

### Chapter 5 运算符，表达式和语句

#### 基本运算符

赋值运算符 = 

加/减法运算符 +/-   二元运算符（需要两个操作数）

符号运算符 +/-       一元运算符

乘/除法运算符  *  /      整数除法运算产生一个整数结果，只取整数部分，小数直接舍弃

### ![image-20230717092754543](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230717092754543.png)

#### 其他运算符

sizeof 运算符和 size_t 类型

sizeof () 以字节为单位返回其操作数的大小，即返回 size_t 类型的值，unsigned long int 无符号整数类型（实际上是使用 typedef 如 typedef double real ）

取模运算 %   只用于整数

![image-20230717101617860](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230717101617860.png)

增量和减量运算符 ++ 和 --

优先级很高，只有 () 比他们优先级高，前缀比后缀优先级高

尽量不在函数的参数里面使用增量运算符，因为编译器会选择先计算参数里面的哪个参数的值，不一定从左到右，如 printf

y = n++ + n++ // y 的值也是不确定的，应避免这样子用，因为 C 语言没有指明每个子表达式在计算后增加还是在整个表达式被计算后增加

使用时注意

- 如果一个变量出现在同一个函数的多个参数中时，不要将增量运算符用于它上面
- 当一个变量多次出现在一个表达式里时，不要将增量运算符运用于它上面

#### 表达式和语句

表达式（expression）是运算符和操作数的组合，最简单的表达式只有一个常量或一个变量而没有运算符

语句（statement）是构建程序的基本成分，程序（program）是一系列带有某种必需的标点的语句的集合，一条语句是一条完整的计算机指令。分为简单语句和复合语句

复合语句（compound statement）：是使用花括号组织起来的两个或更多的语句，也被称为一个代码块（block）

#### 类型转换

1. 在表达式中，signed 和 unsigned 的char 和 short 类型都将自动被转换为 int，在需要的情况下，被转换为更大的 unsigned int，因为转换成较大的类型，提升 promotion

2. 在包含两种数据类型的任何运算里，两个值都被转换成两种类型里较高的级别

3. 类型级别从高到低的顺序：long double，double ，float，unsigned long long， long long，unsigned long，long ，unsigned int ，int。一个例外是，当long和int 具有相同大小时，unsigned int 比 long 级别更高，short 和 char 类型自动提升到 int 或 unsigned int

4. 在赋值语句中，计算最后结果被转换成将要被赋值的那个变量的类型，可以提升，也可能降级。提升通常是一个平滑的无损害的过程，而降级可能导致问题，因为一个较低级别的类型可能不够大，不能存放一个完整的数，如把浮点类型降级为整数类型是，被趋零结尾或舍入，如 23.12 或 23.99 都变为 23

5. 但作为函数的参数被传递时（或者表达式中），char 和 short 也会被转换为 int，float 会被转换为 double（表达式中不会）

#### 指派运算符

即强制类型转换，指派 cast，在变量的前面放置用 （）括起来的希望被转换成的类型名，（int）一起构成了指派运算符 cast operator

表达式是运算符和操作算的组合

当 char 和 short 出现在表达式和作为函数参数时，他们都被自动提升为 int 类型

当 float 类型作为函数参数时，被提升为 double 类型。在 K&R C（而不是 ANSI C ）下，当float 出现在表达式中时，也提升为 double



### Chapter 6 控制语句：循环

```C
// 循环输入
status = scanf("%ld",&input_long);
while(1 == status)
	// enter the loop
// or
while (scanf("%ld",&input_long))
	// enter the loop
```

只有位于判断条件之后的单个语句（简单语句或复合语句）才是循环的部分，缩进只是便于阅读

```
while(scanf("%d",&input_int))
	; // 
// 跳过 空格或整数输入
```

#### 比较大小：使用关系运算符和表达式

![image-20230717165430774](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230717165430774.png)

fabs() 函数返回一个浮点值的绝对值

关系运算符的优先级低于包括 + - 在内的算术运算符，但是高于赋值运算符

```C
x = y > 2; // 若 y>2, 则x=1，否则 x=0
```

![image-20230717172425094](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230717172425094.png)

![image-20230717172447079](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230717172447079.png)

#### for 循环

#### 更多赋值表达式 +=    -+    *=     /+     %=

#### , 运算符

， 运算符保证被它分开的表达式从左到右的次序进行，

可以使用 ， 在 for 循环中使用多个初始化或更新表达式，

- ==且，左边表达式在右边表达式执行之前已生效==
- ==,  运算符最终结果是，右边的值==

```C
for(ounces++,cost = ounces);;);   // cost 获得的是 ounces 的新值，
x = (y = 3, (z = ++y + 2) + 5 );  // y=3 then x=(3,11),y=4  last x=11

x = (249,500);  // x = 500
x = (houseprice = 249, 500);  // house = 249, x=500
```



#### do while 退出条件循环

退出条件循环，判断条件在执行循环之后进行检查，这样可以保证循环体中的语句至少被执行一次，这类循环称为 do while； 循环，如用于输入密码

```
do{
	//
}while(xxx);
```

for( ; i; ) = while(i)

#### 嵌套循环

需要打印行列时

```C
#define ROW 10
#define Column 10
// ==
const char ROW = 10;
const char COLUMN = 10;
```

#### 数组

一个数组就是线性存储的一系列相同类型的值，例如10个字符或15个整数

![image-20230718104548584](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230718104548584.png)

字符数组就是元素被赋予字符值得数组，如果字符数组包含了空字符 \0 那么字符数组的内容就构成一个字符串，其中空字符标志着字符串的结尾









### Chapter 7 C控制语句：分支和跳转



#### if 语句

#### getchar() 和 putchar()

专门为面向字符 I/O 而设计的一对函数，本质上是预处理宏

getchar() 无参数，返回来自输入设备的下一个字符

```C
ch = getchar();    putchar(ch);
//   ==                ==
scanf("%c",&ch);    printf("%c",ch);
```

==ctype.h== 中有一系列标准的函数来分析字符，函数接收一个字符作为参数，如果该字符属于某特定的种类则返回真，否则返回0。例如，isalpha函数的参数是一个字母，则它返回一个非零值

![image-20230718201225448](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230718201225448.png)

isspace() 判断空白字符，空白字符包括

- ==空格 ‘ ’==
- ==回车符  '\r'==
- ==换行符  '\n'==
- 换页符  '\f'
- 制表符  '\t'
- 垂直制表符  '\v'

针对 字符大小写 问题

![image-20230718201424366](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230718201424366.png)

它们不改变 原始的参数，只返回改变后的值

#### 逻辑运算符

![image-20230718210821562](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230718210821562.png)

！优先级很高，和 增量运算符的优先级相同，仅次于 ()

&& 的优先级高于 ||，二者的优先级都低于关系运算符（<>），高于赋值运算符

```C
 a > b && b > c || b > d
 // == ((a>b)&&(b>c)) || (b>d)
```

&& 判断顺序是从左到右

```C
while( x ++ < 10 && x + y < 20 )   // 右边表达式中 x 使用自增后的值
```

bool 在 stdbool.h 中

#### 条件运算符 ?:

expression1 ? expression2: expression3

若 expression1为真，整个表达式的值为expression2，否则为expression3 的值

```C
max = (a>b)?a:b;
```

#### 循环辅助手段：continue 和 break

#### switch

switch 判断表达式应该具有整数值（包括 char ），case 标签必须是整型（包括 char ）常量或者整数常量表达式（仅仅包含整数常量的表达式），不能用变量作为 case 标签。

选择是基于一个浮点型变量或者表达式的值，就不能用 switch

#### goto 语句

导致程序跳转到由指定标签定位的语句，冒号用来将被标记的语句同他的标签相分隔，标签名遵循变量的命名规则，被标记的语句可以出现在 goto 之前或之后

```C
goto part2
part2: printf("ok\r\n");
```

==使用 goto 从一组嵌套的循环中跳出（单条break仅跳出最里层的循环）==

 EOF 即 end of file 是一个宏，当读取到文件末尾时，getchar 等函数会返回 'EOF'，键盘ctl + d 同样可以实现

```C
if( (input_char <= 'z')&& (input_char>= 'a') )      // == islower(input_char)   #include <ctype.h>
if( (input_char <= 'Z')&& (input_char>= 'A') ) 		// == isupper(input_char)
```













### Chapter 8 字符输入/输出和输入确认



#### 单字符I/O：getchar()  和 putchar()

```C
while((ch = getchar()) != '#')
    printf("%c",ch);
```

#### 缓冲区

输入字符立即回显是非缓存（unbuffered）或直接输入的一个实例，它表示所输入的字符对正在等待的程序立即变为可用。

相反，延迟回显是缓冲（buffered）输入的实例，这种情况下所键入的字符被收集并存储在一个被称为缓冲区（buffer）的临时存储区域中。按下回车键可使键入的字符对程序变为可用。

![image-20230719200944790](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230719200944790.png)

使用缓冲输入的优点：

- 将若干字符作为一个块传输比逐个发送这些字符耗费的时间要少
- 如果输入有误，可以使用键盘修正错误

然而，一些交互性的程序需要非缓冲输入，例如，在游戏中，一按下键就执行

缓冲分为两种：==完全缓冲I/O（fully buffered）== 和==行缓冲I/O（line-buffered）==。对于完全缓冲输入，缓冲区满时（512字节和4096字节是常见的值）被清空（内容被发送至其目的地）。通常出现在文件输入中。

==对行缓冲 I/O 来说，遇到一个换行字符时将被清空缓冲区。键盘输入是标准的行缓冲，因此按下回车键将情空缓冲区。==

#### 终止键盘输入

C 语言对待输入和输出设备与其对待存储设备上的普通文件相同。键盘和显示设备作为每个C程序自动打开的文件来对待。键盘输入由一个被称为 stdin 的流表示，而到屏幕（打字机或其他输出设备）上的输出由一个被称为 stdout 的流表示。

文件结尾：C 的处理方法是让 getchar() 函数在到达文件结尾时返回一个特殊值，而不去管操作系统是如何检测文件结尾的。该值为 EOF（end of file），scanf() 函数在检测到文件结尾时也返回 EOF。

```c
#define EOF (-1)  // 在 stdio.h 中定义
while( (input_int = getchar()) != EOF)
```

大多数系统具有一种从键盘模拟文件结尾条件的方法，如 ctrl + d

#### 重定向和文件

```C
./a.out < words    //  < 输入重定向 == words>.\a.out
./a.out > mywords  //  > 输出重定向
./a.out  < words > mywords  // 组合重定向  == ./a.out > mywords < words 
```

![image-20230719205929960](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230719205929960.png)



```
while(getchar() != 'y'){
	printf("is it = %d\r\n",++i);
	while(getchar() != '\n')  // 剔除首字符之外的所有字符，包括换行符
		continue; 
}
```

```C
if(2 == scanf("%d %d",&input_int1,&input_int2) ){
	printf("wrong input format\r\n");
    // 打印 参考模板
	continue;
}

```

#### 输入确认

```C
1 == scanf("%d",&input_int)
```

```C
int get_int(void){  // 返回一个 int 值
	int input_int;
	char input_char;
	while( 1 != scanf("%d",&input_int) ){  // 若输入错误
		while( (input_char = getchar() )!= '\n')  // 剔除该次错误的输入
			putchar(input_char);  // or continue;
		printf(" is not an integer.\r\n");
		printf("Please enter an integer value, such as 45,-12 or 4:");
	}
	return input_int; // 直到输入正确
}
```

#### 菜单游览

```C
printf("Hello\b world!");  // 删除 o
printf("Hello world!\b");  // 无效 \b
```





### Chapter 9 函数

函数function 是用于完成特定任务的程序代码的自包含单元

函数原型 function prototype 即声明

调用 function call

定义 function difinition

void 类型函数，函数定义最后可写 return；或不写

```c
int printf(char *, ...); // ... 表示参数类型和数量不固定
```

#### 递归

C 允许一个函数调用其本身，这种调用称为递归 recursion，若没有设置终止递归的条件检测，会无限制地进行递归调用。

递归一般可以代替循环语句使用，有些情况下使用循环语句好，有时使用递归更有效。递归方法虽然使程序结构优美，但是执行效率没有循环语句高。

```C
void up_and_down(int n ){
	printf("%d,location = %p\r\n",n,&n);
	if(n<3)
		up_and_down(n++);
	printf("%d,location = %p\r\n",n,&n);
}
/*
结果：
1，location = 0x7ff
2
3
3
2
1
*/
```

#### 尾递归

最简单地递归形式是把递归调用语句放在函数结尾，return 语句之前，这种形式被称为为递归 tail recursion 或结尾递归 end recursion 

每次递归调用都拥有自己的变量集合，所以需要占用较多的内存，每次递归调用需要把新的变量集合存储在堆栈中。其次，由于进行函数调用需要花费一定的时间，所以递归的执行速度较慢。

优点：为某些编程问题提供了最简单的解决方法，缺点是一些递归算法会很快耗尽计算机的内存资源，同时难于阅读和维护。

#### 多源代码文件程序的编译



#### 地址运算符 & 与指针 *

指针 pointer 就是用来存储地址的变量

```C
int *ptr;     // 指向一个整数变量的指针
ptr = &bah;   // 指针 ptr 指向 bah 的地址
value = *ptr; // * 取出该地址指向的值
// == 
val = bah;
```



- & 后跟一个变量名时，取出该变量的地址
- *后跟一个指针名或地址时，取出存储在被指向地址中的数值

#### 使用指针在函数间通信

```C
int function(int * ptr);  // 声明
function(&iput_int);      // 调用
```

需要改变调用函数中的多个变量的值时，就需要使用这种调用形式，因为带返回的函数只能返回一个值！实际上，scanf("%d",&input_int) 也是这样







### Chapter 10 数组和指针

数组数据类型相同

```C
int powers[8];  // 所有元素为随机的
int powers[8] = {1,2,4,6,8,16,32,64};  // 只能在初始化时 {} 赋值
int powers[8] = {1,2};  // 未赋值的部分会自动赋值0
int powers[] = {1,2};  // == int powers[2] = {1,2}; 
```

#### 指针和数组

数组名 = 第一个元素地址

```C
array_int = &array_int[0];   // 是常量

int arrayInt[10];
int *ptrInt;
ptrInt = arrayInt;  // 指针指向数组首地址
ptrInt += 1;		// 地址 +1 ，地址值 +4，由于 int 类型的 size 为4   // == arrayInt +1; 但是 ++arrayInt 是错误的，但是在被调用函数中可以这样使用

int arrayDouble[10];
int *ptrDouble;
ptrDouble = arrayDouble;  // 指针指向数组首地址
ptrDouble += 1;		// 地址 +1，指向数组下一个元素，地址值 +8，由于 Double 类型的 size 为8

ptrDouble +2 == &arrayDouble[2];   // 具有相同地址
*(ptrDouble +2 ) = arrayDouble[2]; // 具有相同的值
```

#### 函数与指针

```C
// 函数参数为指针
int sum(int *ptr,int num);    // 调用时候 sum(arrayInt,SIZE);  或者  sum(&arrary[0],SIZE)
//  ==  只在声明和定义中，int *ptr == int ptr[] 都表示一个 int 类型的指针
int sum(int ptr[],int num);   // 调用时候 sum(arrayInt,SIZE);  或者  sum(&arrary[0],SIZE)

```

```
int func(int *start,int *end){
	while(start < end){
		total += *start;
		start ++;   // or total += *(start++)  // == or total += *start++ 因为 *与++优先级一样，结合时从右向左进行
	}
}
func(arrayInt,arrayInt+SIZE);
```

#### 指针操作

指针的指针

```C
&ptrInt  // 指针变量在内存中存储的位置
```

不能对未初始化的指针取值

```C
int *ptrInt;
*ptrInt = 5; // 错误
```

指针与指针求差，计算之间的元素距离

```C
ptrStart - ptrEnd == SIZE;
```

对于处理数组的函数，传递指针，可以使效率更高；因为传值的话，被调函数中必须分配足够存放一份原数组的拷贝的存储空间，然后把原数组的所有数据复制到这个新数组中。

然而，指针操作，很容易修改原数组的数据，有时候需要保护数组内容

- 对形式参数使用 const

  ```C
  int sum(const int araryInt[],int n);  // 函数使用时不改变原数组数据
  ```

  

- 

------



#### 常量指针

==指向常量的指针，不能用于修改指针所指向的对象（变量）==

```c
double rates[5] = { 1,2,3,4,5};
const double *pd = rates;  // 指向数组开头，指向 常量，变量，数值任意元素 都可以

*pd = 28;      // error
pd[2] = 45;    // error
rates[0] = 66; // OK 因为 rates 数组不是常量

```

==常量指针 可以指向 常量，变量，数值任意元素==

==但是，只有非常量数据的地址才可以赋给普通的指针==

```C
double rates[5] = { 1,2,3,4,5};
const double locked[5] = { 1,2,3,4,5};

const double *ptr;
ptr = rates;     // OK
ptr = &rates[4]; // OK
ptr = locked;    // OK

double *pd
pd = rates;      // OK
pd =  &rates[4]; // OK
pd = locked;     // ERROR
```



#### 指针常量

==指向的地址不能修改==

```C
double rates[5] = { 1,2,3,4,5};
double *const ptr = rates;  //  指针常量，指向的地址不能修改
ptr = &rates[4];  // ERROR
*ptr = 98;        // OK
```

#### 指向常量的指针常量

既不可以更改指向的地址，也不能通过该指针来修改所指向的对象（常量）

```C
const double rates[5] = { 1,2,3,4,5};
const double *const ptr = rates;  //  指向常量的指针常量，指向的地址不能修改,地址里的内容也不能修改
ptr = &rates[4];  // ERROR
*ptr = 98;        // ERROR
```

------

#### 指针与多维数组

```C
int zippo[4][2];
zippo == &zippo[0]  == &&zippo[0][0]; // zippo[0] 是第一个元素的地址
(*zippo)+1 == &zippo[0][1];
zippo + 1 == &zippo[1] == &&zippo[1][0];
zippo[0]+1 ==(&zippo[0])+1 == &zippo[0][1];
*zippo[0] ==  zippo[0][0];
 
**zippo == *&zippo[0] == **&&zippo[0][0] == zippo[0][0]; // 因为 zippo 是地址的地址，需要两次取值才可以得到通常的数值
   
```

指向多维数组的指针

```C
int (*ptr)[2]; // 定义一个指针 ptr 指向一个包含两个 int 值的数组  // ptr = zippo;
int *ptr[2];   // 定义两个指针组成的数组，指向 int 数据
```

可以用数组符号访问元素，也可以使用指针，且 数组名 == 指针名

```C
zippo[m][n] == *( *(zippo+m)+n );
ptr[m][n] == *( *(ptr+m) + n );
```

#### 指针兼容性

指针之间的赋值规则更为严格

```C
int n = 9;
double m =8;
int *ptrInt = &n;
double *ptrDouble = &m;
m = n;  // OK
ptrDouble = ptrInt;  // ERROR
```

![image-20230722151257219](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230722151257219.png)

![image-20230722151650633](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230722151650633.png)



```C
int sum(int *ptrInt); // == int sum(int pt[])   // 传入一维数组
int sum(int *(ptrInt)[4] ,int rows); // == int sum( int pt[][4] ,int rows)   // 传入二维数组，定义参数时候需要指明 行列的值！！！
```

#### 变长数组 VLA

==变长数组中的变并不是表示在创建数组后，可以修改其大小。变长数组的大小在创建后就是保存不变的，变的意思是其唯独大小可以用变量来指定。==

```c
int sum2d(int rows , int cols, int ar[rows][cols]);  // OK  ar 是一个变长数组
int sum2d(int ar[rows][cols] ,int rows , int cols,); // ERROR
```

```C
// 二维数组作为参数的函数定义
void func01(double (*ptr)[5],int row);    //    double func01(int rows, int cols, double arr[][]);
// 调用
double arrayDouble[3][5] = {0};
func01(arrayDouble,3);                     //    func01(3,5,arrarDouble);
func01(&arrayDouble[0],3); 

//
arrayDouble[0] == &arrayDouble[0][0]
arrayDouble == &arrayDouble[0]
```



### Chapter 11 字符串和字符串函数

字符串包含 " , 使用 \"

字符串被当作指针来对待

```c
printf("%s, %p, %c\r\n","we","are",*"space farers");
//          we        地址       s
```

指定字符数组大小时，一定要确保数组元素数量比字符串数组长度至少多1（多出来一个元素位置容纳空字符’'\0'），初始化时，未初始化的字符默认为 '\0'



```C
// 字符串 == 字符数组+'\0'
char str01[] = "love";  // 系统在末尾自动添加 '\0'
// ==
char str02 = {'l','o','v','e','\0'}

// 字符数组名字 == 指向首元素的地址
char *m3 = "world";   // 所以 字符串 被看成是首元素的地址，所以有 *("world"+2) == 'r' == *(m3+2)  printf("%s",m3) == "world"
// 指针方式产生字符串，不仅生成了一个字符串，而且定义了一个指针m3，也为m3分配内存空间，初始化时复制字符串的地址，而且 m3 指向的地址值可以改变
// ==
char m3[] = "world";  
// 数组方式初始化字符串（从静态区域把一个字符串复制给数组），数组名是字符串首元素地址，是常量，不能改变，不能 m3++
```



```C
const char (*mytal)[3] = { "one",
                          "two","three"    
};
// 定义了 mytal 一维数组，每个元素时一个char 类型值的指针，第一个指针 mytal[0] 指向第一个字符串的第一个字符 mytal[0]==&(mytal[0][0])
// 第二指针 mytal[1] 指向第二个字符串的第一个字符，*(mytal[1]) == 't'

const char mytal02[3][5] = { "one",
                          "two","three"    
};
// mytal02 是数组的数组，存放3个完整的字符数组
```

指针定义字符数组建立的是不规则的数组，不浪费任何存储空间

![image-20230723094900176](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230723094900176.png)

复制字符串，使用指针指向该字符串首元素地址就行了，简单方便。确实需要复制字符串，可以使用 strcpy() 或者 strncpy() 函数

#### 字符串输入

读取字符串，必须首先预留存储字符串的空间，然后使用输入函数来获取字符串

```C
// 错误写法
char *name;
scanf("%s",name);
// 因为指针 name 未初始化，指向地址随机，在随机地址存储内存可以会覆盖原始存储的数据

// 正确做法
char name[81];
```

#### gets() 函数

代表 get string 对于交互式程序非常方便，它读取换行符之前（不包括换行符）的所有字符，在这些字符后添加空字符 '\0'，然后把这个字符串存在形参里面，注意函数返回字符串的地址。

不检查目标数组是否能够容纳输入，所以不安全，会 warning

```C
char name[81];
char *ptrChar;
ptrChar = gets[name];  // name 作为一个地址，因为要改变该参数，形参改变实参
// name 赋值为输入的字符串，ptrChar 指向输入的字符串的首地址

```

```C
// gets() 函数定义
char *gets(char * s){
	return s;
}
```

```C
while( gets(name) != NULL );     // 遇到文件结尾，返回空（0）地址，也称空指针
while( (ch = getchar()) != EOF); // 遇到文件结尾，返回一个值（-1）
// 空指针是一个地址，而空字符是一个char 类型的数据对象，其值为 0.
```



#### fgets() 函数

get() 函数不检查预留储存区是否能够容纳实际输入的数据，多出来的字符简单地溢出到相邻地内存区。

==fgets() 函数改进了这个问题，可以指定最大读入字符数==。fgets 函数是为文件 I/O 设计的，处理键盘输入不如 get 函数方便

区别如下：

- 需要第二个参数来说明最大读入字符数，n，会读取最多n-1 个字符或者读完一个换行符为止，有这两者中最先满足地那个来结束输入
- 如果 ==fgets 函数读到换行符，就会把它存到字符串里==，而不是丢弃它
- 还需要第三个参数来说明读哪个文件，从键盘读取时，使用 stdin 作为参数（在stdio.h中）

```
char *fgets(char *str, int n, FILE *stream)
/*
char *str: 存储要读取的字符串
int n: 读取最大字符数，包括最后的 '\0'
FILE *stream: 
*/
```



#### scanf("%s",) 函数

更基于获取单词而不是获取字符串

%10s 读入 10 个字符，或者遇到第一个空白字符时，任意一个满足则结束。空字符如下：

- ==空格 ‘ ’==
- ==回车符  '\r'==
- ==换行符  '\n'==
- 换页符  '\f'
- 制表符  '\t'
- 垂直制表符  '\v'

在 Unix/Linux  系统中，回车符用 '\n'

在 Windows 系统中，回车符由 '\r\n' 组成

#### 字符串输出

1. puts() 函数

   ==给出字符串参数的地址，打印 字符，遇到 '\0' 结束。并自动在其后添加换行符==

   ```C
   #define DEF "I LOVE TEH WORLD!"
   
   char string01[10] = "love ";
   char string02[10] = "the ";
   puts("do you love the world?");  // OK 因为双引号中的字符串常量被看作是 指针
   puts(DEF);  // OK 
   puts(string01);  // OK 
   puts(&string01[1]);   // he  == puts(string01+1); 
   
   
   ///////////////
   char string01[10] = "love ";  // 末尾自动添加 '\0'
   char string02[10] = { 'W','0', 'W','!'};
   puts(string02);  // 结果 WOW!love   //因为字符数组后面没有结束符 '\0'
   ```

   

2. fputs() 函数

   是 puts 的面向文件版本。主要区别如下：

   - fputs 需要第二个参数来说明要写的文件，可以使用 stdout （在stdio.h 中定义）作为参数来进行输出显示

   - 与 puts 函数不同，fputs 并不会为输出自动添加换行符

     ==记法：gets 丢掉输入里的换行符，puts为输出自动添加换行符，fgets存储输入中的换行符，而fputs也不为输出添加换行符==

     ```C
     char inputString[81];
     while(gets(inputString))  // 不存储 换行符
     	puts(inputString);    // 自动添加 换行符
     // 相互组合使用
     while (fgets(inputString,81,stdin))
         fputs(inputString,stdout);
     ```

     

3. printf() 函数

   可以格式化多种数据类型，结尾不会自动添加换行符

   一行内显示一个字符串，使用 puts 效率高；一行内显示多个字符串，使用 printf 

#### 自定义字符串输入/输出函数

不一定要使用标准 C 库的函数进行输出/输入，

```C
while(string[i] != '\0');
//  ==
while (*string); // 当 string 指向空字符时，*string 的值为 0
```

#### 字符串函数

在 string.h 中

1. strlen() 函数

   得到字符串的长度

2. strcat() 函数

   string concatenation 接受两个字符串参数，将第二个字符串的一份拷贝添加到第一个字符串的结尾，从而使第一个字符串成为一个新的组合字符串，第二个字符串不改变。

   函数原型是 char * 类型，返回第一个参数的值，即第一个字符串组合后的字符串的 第一个字符的地址。

   然而，strcat 并不检查第一个数组是否能够容纳新的组合字符串

3. strncat() 函数

   stncat（str1，str2，13）；把str2 字符串中的内容添加到 str1 上，直到加到 13 个字符或者遇到空字符为止。最后在str1 结尾添加结束的空字符。

4. strcmp() 函数

    string comparison 字符串比较，传入两个字符串，比较时只看第一个空字符之前的部分，相等的话返回 0。它可以用来比较放在不同大小数组里的字符串。

   原理：两个字符串从头一个一个开始比较，直到发现不同的字符，若第一个字符串中该字符 ASCII 码值大于后面的，则返回1，否则返回 -1。

5. strncmp() 函数

   第三个参数指定比较前 n 个字符是否相同

6. strcpy() 和 strncpy() 函数

   ```C
   char *strcpy( char *s1, char *s2 );  // 把 s2 指向的字符串（包括空字符）复制到 s1 指向的位置，返回值为 *s1
   char *strncpy( char *s1, char *s2 size_t n);  // 把 s2 指向的字符串（包括空字符）复制到 s1 指向的位置，复制的字符数不超过 n 个，返回值为 *s1
   ```

   

7. sprintf() 函数

   与 printf() 作用一样，但是它写到一个字符串里面而不是写到输出显示

   ```C
   const float award = 1000;
   char firstName[20] = "Zeng";
   char lastName[20] = "Zhihong";
   char scholar[50];
   sprintf(scholar,%s %s: %.2lf,lastName,firstName,award);
   puts(scholar);
   ```

   

#### 字符串排序

选择排序：

第一轮：[0] 与[1: -1] 的值进行比较，小的数放在 [0] 处。 //  结果，确定了最小的数，并放在 [0] 位置

第二轮：[1] 与[2: -1] 的值进行比较，小的数放在 [2] 处。 //  结果，确定了第二小的数，并放在 [2] 位置

//  一直循环 //

第 n-1 轮：[n-1] 与[n] 的值进行比较，小的数放在 [n-1] 处。// 结果，完成排序

#### ctype.h 字符函数和字符串

ctype.h 系列字符相关的函数不能被应用于整个字符串，但是可以被应用于单个字符

```C
void ToUpper(char *ptr){
	while(*ptr){  // 判断是否为结尾 '\0'
		*ptr = toupper(*ptr);
		++ptr;
	}
}
```



NULL  空指针

'\0' 空字符

### 命令行参数

```C
// 参数是在命令行中输入的参数条数，以及具体命令，以空格来区分
int main(int argc, char *argv[]){  // 第二个参数是字符串指针构成的数组 
	printf("the command has %d\r\n",argc);
    printf("the first command are as follows\r\n");
    for (int i=0; i<argc;i++) 
    	puts(argv[i]);
}
```

将字符串转换成整数

atoi() 函数 alphanumeric to integer , 返回相应的整数值。在 stdlib.h 中

```C
#include <stdlib.h>
int main(int argc, char *argv[]){  // 第二个参数是字符串指针构成的数组 
	int inputInt;
    inputInt = atoi(argv[1]);
    ptintf("%d",inputInt);
}
// ./a.out 100      则输出100
// ./a.out 10abc1   则输出10
// ./a.out abc1   则输出 0
```

atoi("500") == 500 把一个字符串转换为整型数据

atof()  把一个字符串转换为 double 类型

atol()   把一个字符串转换为 long 类型

![image-20230725171259105](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230725171259105.png)

```C
char number[30] = "01atbc";
char *end;  // long == long int 通常为 8 个字节
long value = strtol(number,&(end),16);  // 将字符串转换成 long型，结束地址保存到 end指针中，按照 16 进制转换
ptintf("number = %ld, and the end = %c\r\n",value,*(end));
// number = 26, and the end = t
```

字符数组 != 字符串，字符数组最后添加 '\0' 才是字符串。

```C
char charArray[5] = {'a','3','b'};  // strlen() == 3  有'\0'结尾  sizeof() == 5
char charArray01[] = {'a','3','b'}; // strlen() == 6  无'\0'结尾  sizeof() == 3
char charArray02[] = "a3b";         // strlen() == 3  有'\0'结尾  sizeof() == 4
```

字符变量在内存中占用一个字节，字符常量 '' 占一个int类型大小的字节，字符串"$"占两个字节









### Chapter 12 存储类、链接和内存管理

![image-20230724145106319](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230724145106319.png)

#### 存储类

五种不同的存储类型，或称存储类，以及基于指针的第六种存储模型。按照变量的存储时期（storage duration），作用域（scope）和他的链接（linkage）来描述。不同的存储类型提供了变量的作用域，链接和存储时期的不同组合

- ==存储时期：变量在内存中保留的时间==
- ==作用域和链接一起表明程序的哪些部分可以通过变量名来使用该变量==

比如，作用域：共多个不同的源代码文件共享的变量；某个特定文件中的所有函数都可以使用的变量；只有在某个特定函数中才可以使用的变量；只有某个函数的一个小部分内可以使用的变量。

存储时期：整个程序运行期间都存在的变量；只有在包含该变量的函数执行时才存在的变量

![image-20230724215505440](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230724215505440.png)

#### 作用域

代码块作用域（包括for内）：在代码块内定义或者作为函数参量定义的变量。并且只在该代码块及其子代码块中可见。

文件作用域：在函数之外定义的变量，并对该变量声明之后定义的全部函数可见。

#### 链接

外部链接 external linkage ，外部链接 internal linkage， 空链接 no linkage

- 具有代码块或者函数原型作用域的变量具有空链接，means 它们是由其定义所在的代码块或函数原型所私有的。

- 具有文件作用域的变量可能有外部或者内部链接，辨别：外部定义是否使用了 static 说明符，==有 static 说明是内部链接==

- ==一个具有外部链接的变量可以在一个多文件程序的任何地方使用==，==一个具有内部链接的变量可以在一个文件的任何地方使用==

#### 存储时期

- ==静态存储时期：程序运行期间将一直存在，具有文件作用域的变量具有静态存储时期（无论具有内部还是外部链接）==

- ==自动存储时期：具有代码块作用域的变量具有。程序进入进入这些代码块时，将为这些变量分配内存，当推出这个代码块时，分配的内存将被释放。==
- ==分配的，内存通过调用 malloc() 或其他相关函数分配，通过调用函数 free() 释放==

根据以上三个特性来定义了 5 种存储内：

1. 自动
2. 寄存器
3. 具有代码块作用域的静态
4. 具有外部链接的静态
5. 具有内部链接的静态

![image-20230724151953307](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230724151953307.png)

#### 自动变量

具有自动存储时期，代码块作用域和空连接，在代码块或函数的头部定义的任意变量都属于自动存储类。

内层代码块定义覆盖，即内层代码块定义的名字是内存代码块所使用的变量。当退出该代码块后，外部重名的变量恢复作用。

```C
void func(void){
	int a;       // 自动变量 == auto int a; 初始化为随机值
	auto int b;  // 效果一样

}
```



```C
for(;;) expression;   // expression;  也代表一个代码块，只是省略了 { }
```

#### 寄存器变量

幸运的话，寄存器变量可以被存放在 CPU 寄存器中，或更一般的，存储在速度最快的可用内存中，从而可以比普通变量更快的被访问和操作。因为寄存器变量多是存储在一个寄存器而非内存中，所以无法获得寄存器变量的地址。除此之外，寄存器变量和自动变量没有区别。

```C
register int quick;
```

#### 具有代码块作用域的静态变量

static variable 静态是指变量的位置固定不变，

静态变量和外部变量在程序调入内存时已经就位了。静态内存的数据在编译时就决定了，静态数据在程序载入内存时就被载入了内存。程序在运行时为自动变量分配和释放内存，因此在程序运行时，自动变量使用的内存数量会不断变化。

```C
void func1(void){  // ！函数形参 不能加 static
	int a = 1;
	static int b =2;  // 静态变量，只初始化一次，一直都存在，只有 func1 函数可以看到该变量
	static int c;     // 静态变量，不初始化则默认初始化为 0
}
```

#### 具有外部链接的静态变量

全局变量的初始化默认为 0

#### 具有内部链接的静态变量

```C
int traveler = 1;    // 外部链接 ， 可在其他文件中使用
static int svil = 1; // 内部链接 ， 只可在当前文件中使用
void func(void);
```

#### 存储类的说明符

五个：==auto register static extern 以及 typedef==

![image-20230724161904818](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230724161904818.png)





### 存储类和函数

函数也具有存储类，可以是外部的，静态的。外部函数可以被其他文件中的函数调用，而静态函数只可以在定义它的文件中使用

### 随机函数和静态变量

rand() 产生伪随机数，介于0和 'RAND_MAX'(0x7FFF) 之间。是伪随机数

srand() 接受一个整数作为种子，并根据该种子生成随机数序列。

要生成真正的随机数，使用

```C
#include <stdlib.h>
#include <time.h>   // time(NULL) 函数获取当前时间作为种子
srand( time(0) );   // 使用当前时间来产生随机种子
randomNum = rand(); // 利用产生的随机种子生成真正随机数
```

### 分配内存： malloc() 和 free()

malloc 形参为所需内存字节数，返回那块内存第一个字节的地址。通常，把该地址赋值给一个指针变量，并使用该指针来访问那块内存。因为 char 代表一个字节，传统上将malloc 定义为指向char 的指针类型。

ANSI C 使用了一个新类型：指向 void 的指针，即”通用指针“

```C
double *ptrDouble;
ptrDouble = ( double *)malloc(30*sizeof(double) );  // 请求30个double类型值的空间，并把 ptrDouble 指向该空间首地址
if( ptrDouble == NULL)  // 空指针，说明分配失败
```

一般，对应每个 malloc 调用，应该调用一次 free 函数。free 的参数是先前 malloc 返回的地址，它释放先前分配的内存。

```C
for (int i=0; i<100; i++)
	double *temp = (double *)malloc(200*sizeof(double)) ;
// 没有使用 free 每一个循环结束，指针 temp 作为一个自动变量消失，但它所指向的 16000 个字节的内存仍然存在，我们无法访问这些内存，因为地址消失了，由于没有调用free ，不可以再使用这些内存。
// 实际上，循环不到100次，程序很可能已经内存溢出了。这类问题称为内存泄漏 memory leak，可以再函数末尾处调用 free，防止该类问题出现。
```

calloc() 函数

与malloc 函数类似，calloc 在 ANSI 以前的版本中返回一个 char 指针，在 ANSI 中返回一个 void 指针。

该函数接受两个参数，第一个是所需内存单元的数量， 第二个是每个单元以字节计的大小。区别：申请成功内存之后，会把该内存清零

```C
long *ptrLong
ptrLong = (long *)calloc(100,sizeof(long));  // 申请成功内存之后，会把该内存清零
// long 使用4个字节，建立了 100 个 4 字节单元
if( ptrLong == NULL)  // 空指针，说明分配失败
   ;
else free(ptrLong);  // 释放内存
```

![image-20230724200952293](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230724200952293.png)

使用 malloc 和 calloc 函数容易产生内存碎片

### ANSI C 的类型限定词

#### const

const int a;   // 修饰变量，不能通过赋值，增量或减量运算来修改该变量的值

const float *ptrFloat;   // 常量指针，ptrFloat 指向一个常量浮点数值 ，不能通过该指针指向的值，但是 ptrFloat 本身的值可以改变，可以指向另一个 const 值

//  == float const *ptrFloat;

double *const ptrDouble； //指针常量 ptrDouble 的值不能改变，即存放的地址不能改变，但是可以该变指向的值

const int *const ptrInt;  // 指向常量的指针常量  ptrInt 的值和所指向位置里存储的值都不能改变。

// !! 总结，一个位于 * 左边的 const 使得数据成为常量，一个位于 * 右边的 const 使得指针变成常量

```C
void display(const char array[],int n);  // 在这个被调用函数中，array 指向的数据是不可变的
char *strcat(char *, const char *);  // 将第二个字符串复制一份添加到第一个字符串的后面 ，同时第二个字符串不改变
```



要让常量全局变量在多个文件中使用

```C
//constant.h
static const float PI = 3.1415926;
// 在 #include ”constant.h“ 的文件中都可以使用 PI，如果不加 static 则会报重定义的错误
#include "constant.h"  // constant.c
printf("%f\r\n",PI);   // OK
#include "constant.h"  // main.c
printf("%f\r\n",PI);   // OK
// 在 constant.c 与 main.c 中 PI 只是名字相同，存放地址不同，它们互相看不到

```

#### volatile

告诉编译器该变量除了可被程序改变还可以被其他代码方式改变。要求编译器严格从地址中取值，而不做优化从缓存中取值。典型的，被用于硬件地址和其他并行运行的程序共享的数据。

![image-20230724205117776](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230724205117776.png)



#### restrict

==通过允许编译器优化某几种代码增强了计算支持。它只可用于指针，并表明指针是访问一个数据对象的唯一且初始的方式。==

![image-20230724205648457](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230724205648457.png)

```C
par[0]+=5;
restar[0] += 5;
ar[n] *= 2;
par[0] += 3;
restar[0] += 3;
// 编译器优化时，把 par[0]+=5; restar[0] += 3;合并为 restar[0] += 8; 而par合并 par[0] += 8; 会出现错误
```

![image-20230724211849263](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230724211849263.png)

静态空链接：在函数内部定义，static 修饰

静态内部链接：函数外部定义，static 修饰，具有文件作用域

静态外部链接：函数外部定义，无static 修饰，具有文件作用域

```C
int func(const int arr[],int n);  // arr是常量指针，指向常量的指针，不允许使用arr 指针来修改调用函数的实参
```















### Chapter 13 文件输入/输出







### Chapter 14 结构和其他数据形式

![image-20230725200912345](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230725200912345.png)



```C
//  结构体声明
struct book {
    char tile[10];  //成员列表
    char author[20];
    float value;
} ;
//  定义结构体变量
struct book library;  // 初始化不会设成默认值，都是随机值
// 编译器使用 book 模板为该变量分配空间：一个具有10个元素的char数组，一个具有20个元素的char数组和一个float型变量。这些存储空间是以一个名字library被结合在一起的。

// 将声明和定义合并在一起
struct {   // 此时可省略book
    char tile[10];  //成员列表
    char author[20];
    float value;
} library;

//  定义指向该类型结构体指针
struct book *ptrStruct;

//  初始化结构体变量
library = {
    "The Story of Stone",
    "Tom",
    20;
};
```

结构成员运算符 .

```C
library.title = "The Courage to Be Disliked";
library.author = "Ichiro Kishimi"
library.value = 33;
```

#### 结构数组

```C
struct book library[100];
while( (fgets(library[0].title) != NULL) && ( library[0].title[0]!='\0' ) )

```

### 嵌套结构体

一个结构体含有另一个结构体。

```C
struct names {
	char firstname[20];
	char lastname[30];
	struct book favoritebook;
} ;
// 定义变量
struct names Lihua = {
    "Li", 
    "hua",
    {.value=100,.author = "Rowling",.title="Harry Potter";};
};
// 访问
printf("%s",Lihua.favoritebook.title);  // 打印出 Harry Potter
```

#### 指向结构体的指针

指向结构体的指针通常比结构体更容易操作；指向结构体的指针可以作为参数被传递到函数

==和数组不同，结构体的名字  不代表  结构体的首地址，必须要使用 &运算符==

```C
struct name *ptrStruct;   // 定义结构体指针
printf("address = %p\r\n",&Lihua);
ptrStruct = &Lihua;       // 指向一个结构体变量
// 使用指针来访问结构体成员
printf("%s %s loves the book: %s\r\n", ptrStruct->lastname, ptrStruct->firstname, ptrStruct->favoritebook.title);
// 第二种方式  
printf("%s %s loves the book: %s\r\n", (*ptrStruct).lastname, (*ptrStruct).firstname, (*ptrStruct).favoritebook.title);
```

### 向函数传递结构体信息

1. 传递结构体成员

   ```C
   struct names Lihua = {
       "Li", 
       "hua",
       {.value=100,.author = "Rowling",.title="Harry Potter";};
   };
   float a = func(Lihua.favoritebook.value);
   ```

   

2. 传递结构体地址

   ```C
   double func(const struct names *ptrStruct);
   func(&Lihua);
   ```

   

3. 传递结构体

```C
double func(struct names name1);
func(Lihua);
```

其他结构特性

C 允许把一个结构体赋值给另一个结构体，而数组不能这么操作。

```C
struct name name02 = Lihua;  // 新定义一个结构体 namw02 ，并把结构体 Lihua 赋给它
```

因此，结构体还可以作为函数的返回值

```C
struct name getinfo(struct name info){
	return info;
}
```

![image-20230726104454031](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230726104454031.png)

==通常，为了追求效率，使用结构体指针作为函数参量，但需要保护数据，防止意外改变数据时对指针使用const限定词==。而传递结构体是处理小型结构体常用的方式。

```C
struct testStruct {
	char lastname[20];   // 强烈推荐写法，而不用字符指针
	char firstname[20];
} ;
//  == 
struct test02Struct {
	char *lastname;    // OK  但是，要注意，结构体变量初始化时候，必须给该地址赋值，否则容易出问题（因为地址是随机的）
	char *firstname;   // 要正确地在结构体中使用字符指针，需要配合 malloc 函数
} ;
```

#### 结构体，指针和 malloc()

在结构体中使用指针处理字符串需要和 malloc 配合，使用 malloc 分配内存，并用指针来存放地址。这个方法地优点是可以请求 malloc 分配刚好满足字符串需求数量的空间。

```C
struct test02Struct {
	char *lastname;    // calloc 在分配内存的同时会将内存初始化为 0，然而 malloc 不会初始化内存，它的内容是不确定的
	char *firstname;   // 要正确地在结构体中使用字符指针，需要配合 malloc 或 calloc 函数
} ;
struct test02Struct temp;
temp.lastname = (char *)malloc(20*sizeof(char));
gets(inputChar);
strncpy(temp.lastname,inputChar,20);
free(temp.lastname);  // 释放内存
```

#### 使用结构体数组的函数

```C
struct fruit {
    char name[20],
	float price;
} ;
struct fruit myFruit[2] = {
    {"tomato", 3.1},
    {"potato", 1.2}
};
double func(struct fruit * ptrStruct){  // ==void func(struct fruit temp[]);  // 都代表结构体指针
	return ptrStruct[0].price + ptrStruct[1].price;
}
```

### 将结构体内容保存到文件中

由于结构体可以保存多种多样的信息，所以它是建立数据库的重要工具。

```C
FILE *ptrbooks;
if( (ptrbooks = fopen("book.text","a+b"))  == NULL ){
    puts("Open failed!\r\n");
    exit(EXIT_FAILURE);  // 程序异常终止
}
rewind(ptrbooks);
while( fread(&liarary[i],sizeof(struct books),1,ptrbooks) == 1 )
```

### union 联合

是一个能在同一个存储空间中（但不同时）存储不同类型数据的数据类型。

使用联合类型的数组，可以创建相同大小单元的数组，每个单元都能存储多种类型的数据。

典型应用：一种表，设计它是用来以某种既没有规律，事先也未知的顺序保存混合型数据。使用联合类型的数组，可以创建相同大小单元的数组，每个单元都能存储多种类型的数据。

```C
// 联合与结构体的定义方式相同，需要有一个联合模板和一个联合变量
union hold {   // 该联合可以含有一个 int 型数值或一个 double 型数值，或一个 char 型数值
	int digit;    // 32 bit
	double bigfl; // 64 bit
	char letter;  // 8 bit
} ;
union hold fit;       // 定义一个联合变量 // 分配最大可能性需要的空间，32 bit
union hold sava[10];  // 10个联合变量的数组  // 10 个元素，每个元素大小为 8 个字节
union hold *ptrUnion; // 指向 hold 类型变量的指针  // 存放一个 hold 联合体的地址

// 与结构体的差异
struct hold { };  // 该结构体同时含有一个 int 型数值和一个 double 型数值，以及一个 char 型数值
    
// 联合体的访问
union hold valA;
valA.letter = 'A';
union hold valB = valA;
union hold valC = {88};  // digit 赋值
union hold valD = {.bigfl = 88};

union hold valC = 2.0;  // 清除原来数据88，存放 2.0 使用8字节
union hold valC = 'C';  // 清除原来数据2.0，存放 'C' 使用1字节
  
ptrUnion = &valA;
x = ptrUnion->letter;  // 等同于 x = valA.letter
    
// 易错
valA.letter = 'A';  // valA.digit，valA.bigfl 和valA.letter 存放的都是 'A'，因为它们共享内存
flnum = 3.1*valA.bigfl;  // 错误，因为存储了一个 char 类型，而这里却假定 valA 的内容是 double 类型的
```

间接成员运算符：->

直接成员运算符： .

它们都可以用于联合和结构体，方法一样

```C
union {  
	int code;
	float cost;
} item, *ptrUnion;
// item.code == ptrUnion->code  ==  (*ptrUnion).code 三种写法等价
```

### 枚举类型

enumerated type 枚举类型声明代表整数常量的符号名称，使用关键字 enum，可以创建一个新类型，并指定它可以具有的值（实际上，enum 常量是 int 类型的，因此在使用 int 类型的任何地方都可以使用它）。枚举类型的目的是提高程序的可读性。语法与结构体的语法相同。

```C
enum spectrum { red, orange, yellow, green, blue, violet };  // 设置 spectrum 为标记名，花括号内的标识符枚举了 spectrum 变量可能有的值
enum spectrum color;  // 定义一个 spectrum 枚举类型的变量
color = blue;  // 赋值
if(blue == color);  // 比较
for (color = red; color <= violet; color++)
    printf("%d",color);  // 使用 %d 因为实际是 enum 常量是 int 类型
// 默认为 red 0， orange 1， ……  violet 5

enum deline{ cat, lynx = 10, puma, tiger};
// cat 1, lynx 10, puma 11, tiger 12
```

#### 共享的名字空间

![image-20230726143315375](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230726143315375.png)

```C
struct test {};
union test {};  
enum test {};  // ERROR 因为三者共享同一个名字空间，
int test;      // OK
```

### typedef 

一种高级的数据特性，能够为某一类型创建您自己的名字，和 #define 相似，区别如下

1.  与#define 不同，typedef 给出的符号名称仅限于对类型，而不是对数值
2.  typedef 的解释由编译器，而不是预处理器执行
3.  虽然他的范围有限，但在其受限范围内，typedef 比 #define 更灵活

```C
typedef unsigned char BYTE;  // 相当于给 unsigned char 起了一个别名 BYTE
// == #define BYTE unsigned char 
BYTE a = 100;  // == unsigned char a = 100;

typedef char * PTRCHAR;  // PTRCHAR 相当于char 指针的标识符，而 #define 做不到
PTRCHAR b,c;  // == char *b,*c;
// 与 #define 区别
#define PTRCHAR char *
PTRCHAR b,c;  // == char *b, c;

// 与结构体
typedef struct complex {
    float real; float imag;
} COMPLEX;
// 可以使用类型 COMPLEX 来代替 struct complex

```

使用 typedef 可以为经常出现的类型创建一个方便的，可识别的名称，如 typedef char * STRING

```C
typedef char (*FRPTC())[5];  // 把FRPTC 声明为一个函数类型，该类型函数返回一个指向含有 5 个元素的char 数组的指针
// 理解如下
char * pt[5];     // pt 是一个包含5个元素的指针数组，每个元素都是指向 char 类型数据的指针
char (*pt)[5];    // pt 是一个指针，指向由 5 个char 元素构成的数组。
char (*pt())[5];  // pt 是一个函数，这个函数的返回值是一个指向由 5 个char 类型数组的指针。
// 备注
char (*pt[3])[4];  // 一个具有 3 个元素的数组，每个元素都是指向具有 4 个 char 类型元素的数组的指针
```



### 函数和指针

```C
char *func();  // func 是一个函数，返回一个指向 char 的指针
char (*func01)();  // func01 是一个函数指针，指向的是返回值类型为 char 的函数
char (*func02[3])();  // func02 是一个包含3个元素的函数指针数组，其中每个元素都是一个函数指针，指针指向的是返回值为 char 类型的函数
```

函数指针，保存着函数代码起始处的地址

声明一个函数指针时，必须声明它指向的函数类型，要指定函数类型，就要指出函数的返回值类型以及函数的常量类型

```C
void ToUpper(char *);
void (*ptrFunc)(char *);  // pf 是一个函数的指针  // 技巧 直接把函数名称换成 (*ptrFunc), 则 ptrFunc 是可指向那种函数的指针
ptrFunc = ToUpper;   // OK
ptrFunc = ToUpper(); // ERROR
```

可以使用 函数指针来访问函数

```C
ptrFunc = ToUpper;
ptrFunc("hello");    //  == ToUpper("hello");
(*ptrFunc)("hello"); //  == ToUpper("hello");  因为函数名就是指针
```

函数指针最普遍的用法之一也是作为函数参数

```C
void show( void (*ptrFunc02)(char *), char *str );
show(ToUpper, "world");  //  == 如下
show(ptrFunc, "world");

// 如何使用传递过来的函数指针
void show( void (*ptrFunc02)(char *), char *str ){
    (*ptrFunc02)(str);  // 使用 ptrFunc02() 或 (*ptrFunc02)() 来调用函数
}

// 注意区分
function01(ptrFunc);       // 传递的是 ptrFunc 指向的函数的地址
function02(ptrFunc("ok")); // 传递的是 ptrFunc("ok")函数的返回值
```

















### Chapter 15 位操作

![image-20230725100353993](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230725100353993.png)



### 位逻辑运算符

按位取反 ~

位与 AND &

位或 OR |

位异或  ^ : 位相同为0，不同为1

```C
val ^= 0337;  // 以0开头，说明是八进制数
```

用法：掩码

```C
// 只保留某些位不变，其余置0
#define MASK 0B10;
val &= MASK;  //  将 val 的第二位保持不变，其余位置0
```

==用法：打开位==

```C
// 使某些位置位，其余保持不变
val |= MASK;  // 将第二位置位，其余不变
```

==用法：关闭位，即位清零==

```C
val &= ~MASK;  // 将第二位复位，其余不变
```

==用法：转置位，即部分位取反，即异或==

一个位如果打开，则关闭；如果该位关闭，则打开？？？？？？？？？？？

```C
val ^= MASK; // 将val 对应 MASK 为1的位将被转置，对应MASK 为0的位不改变
```

==用法：查看一位的值==

```C
if( (val&MASK) == MASK )   //  若val的第二位为1，则返回1，否则返回0    // MASK 0B10;
```

#### 移位操作符

左移 <<   // 每移一次相对于 *2

右移 >>   // 每移一次相对于 /2

### 位字段

![image-20230725151027742](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230725151027742.png)

```C
struct {
	unsigned int code1: 2;
	unsigned int code2: 2;
    unsigned int 	  : 3;  // 未命名的字段，也称为洞，占用3个位
    unsigned int codex: 1;
    unsigned int  	  : 0;  // 使用宽度0的未命名字段，强制下一个字段放在下一个 unsigned int 内存中
	unsigned int code3: 8;
} prcode;  // 不能超过总位数 unsigned int 32位
// 创建两个2位字段和一个8位字段
prcode.code1 = 0;  // 0-3
prcode.code2 = 3;  // 0-3
prcode.code3 = 100; // 0 - 1111 1111 即 255
```

#### 位字段与位运算符























### Chapter 16 C 预处理器和 C 库

![image-20230727084315551](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230727084315551.png)



### #define 

预处理器发现程序中的宏后， 会用它的等价文本代替宏。如果该字符串中还包括宏，则继续替代这些宏。例外就是 "" 双引号里面的宏不做替代

```C
#define PI 3.14
#define PI2 Pi*PI
printf("PI2 = %.2f\r\n",PI2);   // 打印 PI2 = 9.87  // 因为 双引号之间的宏不做替换
```

const 修饰常量，但是本质上还是变量，所以不能够在使用常量的地方使用，如

```c
#define LIMIT 10;
const int LIM = 10;
static int data[LIMIT];  // OK
static int data2[LIM];   // ERROR   static 修饰后会立马分配空间，并初始化为 0
int data2[LIM];          // OK   因为未分配存储空间，索引写什么都可以

const int lim2 = 2*LIMIT;  // OK
const int lim02 = 2*LIM;   // 看编译器，有的 OK 有的 ERROR
```

#### 在 #define 中使用参数，即函数宏

![image-20230727091224725](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230727091224725.png)

```C
#define SQUARE1(X) X*X  // 容易出问题
#define SQUARE2(X) (X)*(X)  // 容易出问题
#define SQUARE3(X) ((X)*(X))  // OK
// 宏中必带 ()，不然会 ERROR
SQUARE1(2+3) == 2+3*2+3       // ERROR
SQUARE3(2+3) == ((2+3)*(2+3))   
    
100/SQUARE2(2+3) == 100/5*5    // ERROR
100/SQUARE3(2+3) == 100/(5*5)

//宏中不要使用 增量或减量 运算符  !!!!
int x = 6;
SQUARE3(++x) == ((++x)*(++x))  // 不同编译器产生不同结果
```

#运算符，使得在 字符串 中实现宏替换

```C
#define PSQR(x) printf("the square of "#x" is %d \r\n",((x)*(x) );
```

"#PI" 则会在字符串中发生宏替换

##运算符

用于类函数宏的替换部分，还可用于类对象宏的替换部分

```C
#define TEST(n) xn
int TEST(1) = 19;  // == int xn = 19;
int TEST(2) = 1;   // ERROR 重定义 xn

#define TEST(n) x##n
int TEST(1) = 19;  // == int x1 = 19;
int TEST(2) = 12;  // == int x2 = 12;
```

可变宏：... 和 __VA_ARGS_  

```
__VA_ARGS__
```

![image-20230727095010474](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230727095010474.png)

### 宏与函数

宏：高效，不用入栈出栈，但是内存占用多，在调用比较少时使用

函数：占用 cpu 资源，内存占用少，在调用次数多的时候使用

![image-20230727100004459](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230727100004459.png)

### 文件包含 #include

预处理器发现 #include 指令后，就会找到该文件名，并把这个文件的内容包含到当前文件中，别包含文件中的文本将替代源代码中的 #include 指令，==就相当于 把包含文件中的全部内容键入到当前文件中的这个特定位置一样==。

![image-20230727100428675](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230727100428675.png)

头文件 .h 包含如下

```C
#define PI 3.14  // 常量
				 // 宏函数 

struct name{};   // 结构体定义

typedef struct name yourname;  // 类型定义
extern int status;  // 具有文件作用域，外部链接的变量

void func01(void);  // 函数声明
void func02(void);
```

### 其他指令

- #undef 指令

  取消定义一个给定的 #define

  ```c
  #define LIMIT 100
  #undef LIMIT
  ```

  #define 的作用域直到 遇到 #undef，否则就是文件结尾

- 条件编译

  #ifdef，#ifndef，#else，#elif， #endif 指令

  使用了 #ifdef 或者 #if 结尾必须加上 #endif 

  ```C
  #ifdef
  	#;
  	#;
  #else
  	#;
  	#C;
  #endif  // #endif 切记
  
  // 如
  #define JUST_CHECKING
  
  #ifdef JUST_CHECKING
  	printf("test");
  #endif
  ```

  #ifndef 防止同一个文件被多次包含，若#include “xxx.h”多次，在 xxx.h 中有 #ifndef __xxx_h___，则只包含一次

  因为头文件中有些语句在一个文件中只能出现一次（如结构体类型的声明）

  ```C
  #ifndef __STDIO_H__  // 前后加__是防止与标准头文件发生冲突
  #define __STDIO_H__
  
  #endif
  ```

  

- 预定义宏

  ![image-20230727105524068](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230727105524068.png)

- #program 将编译器指令置于源代码中

  ```
  #program c9x on
  ```



### 内联函数

![image-20230727105739306](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230727105739306.png)

```C
inline void func01(void);  // 内联函数的声明
func01();  // 调用
// 编译器看到内联函数，会用函数本体代替函数调用（类似于函数宏），其效果等同于您在此处键入了函数体的代码。
```

内联函数应该比较短小。对于很长的函数，调用函数的时间少于执行函数本体的时间，此时，使用内联函数不会节省多少时间。

![image-20230727110310325](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230727110310325.png)

==头文件中的特例，内联函数在头文件中定义，需要使用该函数的文件，包含该头文件即可。==

### C 库

想了解一个库函数的用法，Ubuntu 命令行中：man + 指令

```C
man printf
```

### 数学库 math.h

嵌入式开发中，很少直接用，因为太消耗 CPU

在 Ubuntu 中，使用 math.h 的程序，编译时需要加上 -lm

在 math.h 中 pai 的定义为 M_PI

以下三角函数，输入 x 为弧度，![image-20230727111135667](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230727111135667.png)

![image-20230727111048935](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230727111048935.png)

### 通用工具库

![image-20230727111309906](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230727111309906.png)

- exit() 和 atexit() 函数

从 main() 返回时自动隐式地调用 exit() 函数。使用 atexit() 函数可以指定执行 exit() 时调用特定的函数

```C
atexit(func01);
```

- qsort() 函数

![image-20230727111805215](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230727111805215.png)

void 类型的指针，允许将任意类型的指针转换为 void 类型指针，但是会失去每个数组元素的大小信息。

```C
int *function(void);   // 函数定义 function 返回一个指向 int 类型的指针
int (*function)(void); // 函数指针的定义，返回一个 int 类型，function指向一个没有参数并返回 int 类型值的函数
```

```C

int main(void){
	int arr[10] = {5,2, 11, 8, 32, 1, 12, 29, 7, 13};
    qsort(arr, 10, sizeof(int),mycomp);  // 排序结果也存储在 arr 中
	return 0;
}
int mycomp(const void *ptr1, const void *ptr2){
	const int *a1 = ptr1;
	const int *a2 = ptr2;
	if(*a1 == *a2)
		return 0;
	else if (*a1 < *a2)
		return -1;  // 从小到大升序排列， 改成1则为从大到小降序排列
	else return 1;
}
```

字符串的排序

![image-20230727123958311](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230727123958311.png)

### 诊断库

![image-20230727124023531](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230727124023531.png)

```C
while(1){
	scanf("%d",&inputInt);
	assert(inputInt>0);  // 若不满足，程序自动终止，并报告错误信息
	printf("successfully enter an integer %d\r\n",inputInt);
}
```

如果加上 #define NDEBUG，则 assert 作用关闭

### string.h 中的 memcpy() 和 memmove() 函数

==数组不能直接拷贝，必须使用指针。==

==常用的是 memcpy 函数，但是必须确保两个数组的内存区域没有重叠区域。==

![image-20230727124726686](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230727124726686.png)

```C
int array[10] = {1, 3, 2, 4, 6, 5, 8, 10, 7, 9};
int num[10];
memcpy(num, array, 10*sizeof(int));  // 将 array 拷贝到 num 中
```

```C
double data1[100];
double data2[300];
memcpy(data1, data2, 100*sizeof(double));
memcpy(data1, &(data2[200], sizeof(double));
```



### 可变参数：stdarg.h

可变宏，可以接受可变个数的参数。头文件 stdarg.h 也为函数提供了类似的能力，使用时必须：

1. 在函数原型中使用省略号
2. 在函数定义中创建一个 va_list 类型的变量
3. 用宏将该变量初始化为一个参数列表
4. 用宏访问该参数列表
5. 用宏完成清理工作

```C
void func01(int n,...);  // OK
int func02(int n, const char *s, ...);  // OK
char func03(char c1,...,char c2);  // error 省略号必须是最后一个参数
double func04();  // error
// 一个函数实现 n(可变) 个 double 值求和
double(int lim, ...){  // step 1
    double sum;
    va_list ap;        // step 2
    va_start(ap,lim);  // step 3
    for(int i = 0; i < lim; i++){
        sum += va_arg(ap,double);  // step 4  // 依次访问每一个输入参数，没访问一个就剔除一个
    }
    va_end(ap);
    return sum;
}
```









### Chapter 17 高级数据表示







## C 编程进阶

1. 难点整理归纳
2. 指针用法
3. 陷阱和缺陷
4. 高效编程

不同文件中的全局变量不能重名

==变量的定义意味着为变量分配内存空间，而声明不会==

```c
extern int a;   // 变量声明 OK
extern int a = 30; // ERROR
```



sizeof （返回单位为字节）不是函数，是关键字。

因为 sizeof(int) ，sizeof(i) 和 sizeof i ，其中 i 为一个变量，都可以使用，而函数后面必须加 ()

sizeof 用于计算数据类型占用内存空间大小时，必须加上 ()

------

==float 类型变量不能用于比较，因为它本身就是不精确的==

只能用于 >= 和 <=

一定要比较相等的话

```C
float a = 0.00;
if( a == 0)  // ERROR
if( (a>=-0.00001) && (a<=0.00001) )  // OK

```

==float 类型的有效位数是 7 位，整数位+小数位一共有效位是 7 位==

运算时候必须避免，一个很大的浮点值和一个很小的浮点值做运算

```C
float a = 9.87654321e15;
a = a + 0.1;  // 结果 a 不会变，因为浮点数本身不精确，是一种近似表示。
```

------

==变量的定义，初始化，和赋值==

```C
int a;      // 变量定义
a = 10;     // 变量的赋值，运行期间，动态赋值，消耗 CPU 资源
int a = 0;  // 变量初始化，编译时已经完成，节省 CPU 运行时间
```

------

==整型数据在内存中的存储形式==

二进制的形式存放，数值是以补码的形式表示的。

- 正数的源码，反码，和补码完全相同
- 负数的原码，补码加1；而补码等于 原码除了符号位不变，其余位取反



```C
for( int i = 0; i<32; i++){
	printf("%d", (n&(1<<(31-i))?1:0 );  // 取出 32 位二进制数
}
```

------

==const 常量==

- 只有在定义的时候可以进行初始化，否则就没有机会了，默认值为 0；

- 本质上，const常量 并不是真正意义的常量，不可以显式的修改，但是可以使用指针间接的修改；

- const 常量不能用于确定值的场合，如 数组的大小；

- const 和指针的用法：

  const int *ptr;   // 常量指针，不能通过指针来修改指向的对象。const 在 * 左边    == int const *ptr

  int * const ptr;  // 指针常量，指针的值不能改变，即不能指向其他位置。const 在 * 右边

  const 修饰形参，在该函数中不能改变该变量的值，如 void func( const int *ptr )

  

------

==使用逻辑表达式需要注意==

&&   ||   ！

```C
( expression1 && expression2 )    ( expression1 || expression2 )
// 表达式1 和 表达式2 不一定会都判断到
```

位运算符

&   |   ~  ^

------

==数组和指针只是有些情况下表现相同==

指针可以增量和减量运算

而数组名是常量，不能

------

==#define 和 typedef 的区别==

typedef 只是给变量类型起了一个别称

```C
#define myname int 
typedef int myname2;

myname a;   // OK
myname2 b;  // OK

unsigned myname a;   // OK
unsigned myname2 b;  // ERROR

// 不同点2
#define ptrInt int *
typedef int * ptrInt2;
ptrInt a,b;   // == int *a,b;
ptrInt2 a,b;  //  == int *a,*b;
```

------

==函数宏不是函数==

函数在调用时消耗大量资源，涉及到为形参分配内存，入栈和出栈等操作

使用宏可以提高系统运行速度，减少额外开销，但是要加 ()，并仔细

```C
#define abs(x) ( ((x)>=0)?(x):(-(x)) )  
// 即使全部加上 () 也可能会出现问题，如，x 包含增量或减量运算
// 所以 在函数中尽量避免 ++ 或 ——出现在函数参数中传递
```

在需要大量调用同一个功能时，使用宏函数

------

==理解指针的指针==



------

==复制数组与复制字符串==

复制数组：memcpy()

```C
double data1[100];
double data2[300];
memcpy(data1, data2, 100*sizeof(double));
memcpy(data1, &(data2[200], sizeof(double));
```

复制字符串: strcpy(),  strncpy()

```C
char *strcpy( char *s1, char *s2 );  // 把 s2 指向的字符串（包括空字符）复制到 s1 指向的位置，返回值为 *s1
char *strncpy( char *s1, char *s2 size_t n);  // 把 s2 指向的字符串（包括空字符）复制到 s1 指向的位置，复制的字符数不超过 n 个，返回值为 *s1
```

![image-20230727124726686](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230727124726686.png)

==整形数据 →字符串== 与 ==字符串→整形数据==

在 stdlib.h 中

atoi()    将字符串转换为 int 类型， 如 atoi("500") == 500

atof()	将字符串转换为 double 类型

atol()	将字符串转换为 long 类型

strtol()  

strtoul()

strtod()

```C
long int strtol( const char *str, char **endptr, int base)
/*
把 str 指向的字符串根据给定的基数 base 转化为一个 长整形数据
char **endptr: 输出参数，其值由函数设置为 str 中数值后的下一个字符
如：
char str[]="900";
char *endptr;
long int num = strtol (str, &endptr, 10 );
*/
```

发送格式化输出到字符串

sprintf()

```C
int sprintf ( char *str, const char *format, ...)
/*
char *str: 指针，指向要输出到的字符串
const char *format: 
如：
char str[50];
sprintf(str, "C is so easy %d.",20230801);  
*/

```











































 