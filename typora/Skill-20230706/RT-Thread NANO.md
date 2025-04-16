# RT-Thread NANO

## 新建工程

1. 下载tr-thread nano 源码，拷贝源码到 STM32 工程，只需要 bsp，components，include，libcpu，src 文件夹

2. Manage Project Items下新建文件夹 RT-Thread/Kernel: 添加文件 rt-thread/src/ 下所有 .c 

   ​														 RT-Thread/Ports:   添加文件 rt-thread/libcpu/arm/cortex-ms/context_rvds.S 与 cpuport.c

   ​														 RT-Thread/Finsh:   添加文件 rt-thread/components/finsh/cmd.c 与 msh.c 与 shell.c

   ​														RT-Thread/Bsp:      添加文件 rt-thread/bsp/bord.c 与 rtconfig.h

3. stm32f10x_it.c 中注释 void HardFault_Handler(void) 与 void PendSV_Handler(void)

4. rtconfig.h 与  board.c 中

```C
// rtconfig.h line 7
//#include "RTE_Components.h"

// bord.c line 13
#if 0

#endif // line 46

// board.c
#if 0   // line 66
    /* System Clock Update */
    SystemCoreClockUpdate();
    
    /* System Tick Configuration */
    _SysTick_Config(SystemCoreClock / RT_TICK_PER_SECOND);
#endif
		SysTick_Configuration();  // 使用自定义的 SysTick 初始化

```



```C
#ifdef RT_USING_HEAP  // 使用 堆
	re_thread_create();  // 动态创建线程，在堆里面创建，只需要设置栈大小
#else
	re_thread_init();    // 静态创建线程，需要自己定义堆栈地址
#endif
```

静态创建是在栈上创建对象，在对象的作用域结束后自动销毁；

动态创建时在堆上创建对象，然后栈上的指针指向创建的对象

系统初始化会创建两个线程，一个application，一个 idle 最后都会调用 rt_thread_startup() 函数，该函数开启线程，并把线程放入就绪列表

***

要使用 rt_kprintf() 函数，需要

定义 rt_hw_console_output()

***

要使用 Finsh 即 msh

需要在 rtconfig.h 中 #define RT_USING_FINSH

另外，定义函数 void rt_hw_console_getchar(void) 

## 创建线程



```C
// 需要在rtconfig.h中 #define RT_USING_HEAP
// 在 board.c中有 #define RT_HEAP_SIZE 1024, 故最大的 stack_size 为 4096
Led_Task_Thread = rt_thread_create( (const char *)  "LedThread",  // 动态创建线程，需要在rtconfig.h中 #define RT_USING_HEAP
									Led_Thread_Entry,
									(void       *)  NULL,
									(rt_uint32_t )  256,  // 不能超过 4096
									(rt_uint8_t  )  5,
									(rt_uint32_t )  10);  // 10ms
if ( Led_Task_Thread != RT_NULL)
{
		rt_thread_startup(Led_Task_Thread);
}
```

系统线程：主线程（main），空闲线程

动态线程与静态线程的区别是：动态线程是系统自动从动态内存堆上分配栈空间与线程句柄（初始化 heap 之后才能使用 create 创建动态线程）

静态线程是由用户分配栈空间与线程句柄。

要使用钩子函数，在rtconfig.h 中 #define RT_USING_HOOK



要使用空闲任务钩子函数，在rtconfig.h 中 #define RT_USING_IDLE_HOOK

***

==自动初始化过程：==

INIT_BOARD_EXPORT() 内的函数会在系统

其余 五种 修饰的函数在 main() 之前执行

如: INIT_APP_EXPORT(finsh_system_init);  // Finsh 函数的初始化

***

## 信号量

线程间同步：信号量，互斥量，事件集

中断与线程同步

首先，在rtconfig.h 中#define RT_USING_SEMAPHORE

```C
rt_sem_init  // 
rt_sem_create()
rt_sem_take()
rt_sem_release()
```



## 线程之间通信：邮箱，消息队列，信号

消息队列：

```C
rt_mq_creat/init()   // 动态创建/初始化
rt_mq_send/urgent()  // 发送/发送紧急消息
rt_mq_recv()		 // 接收
rt_mq_delete/detach()  // 删除/脱离
```

## 软件定时器

RTT 中有 timer.c， timer.h 文件

- HARD_TIMER（默认）：在中断中执行定时器超时函数，（在中断的上下文环境运行）

- SOFT_TIMER：会被操作系统调度（在线程的上下文环境运行），使用的就是 SysTick 基时间

首先，在 rtconfig.h 中使能 #define RT_USING_TIMER_SOFT 1

```C
rt_timer_create()    //  rt_timer_init()    静态创建
rt_timer_delete()    //  rt_timer_detach()  静态删除
```

模式：

```C
RT_TIMER_FLAG_ONE_SOFT     // 单次定时
RT_TIMER_FLAG_PERIOD 	   // 周期定时
RT_TIMER_FLAG_HARD_TIMER   // 硬件定时器  // 定时器回调函数在中断中执行
RT_TIMER_FLAG_SOFT_TIMER   // 软件定时器   // 定时器回调函数受调度器调度
// 可以 “或”
RT_TIMER_FLAG_PERIOD|RT_TIMER_FLAG_HARD_TIMER
//
默认模式：使能 #define RT_USING_TIMER_SOFT 1 时 ， RT_TIMER_FLAG_SOFT_TIMER
        未使能时 ， RT_TIMER_FLAG_HARD_TIMER

```



## 高精度延时

RTT 定时器的最小精度是由 SysTick 时基决定的（1 os tick = 1/RT_TICK_PER_SECOND 秒，默认为1ms），定时器设定的时间必须是 os tick的整数倍。

SysTick 已经被 RTT 用于 os tick 时基使用，中断处理函数用的是默认的 SysTick_Handler。  



## ESP 8266 模块

AT指令

```
AT
AT+RST
AT+CWMODE=1   //STA 模式

AT+CWQAP   //

AT+SWJAP="TP-LINK_4A12","lzggy666"
AT+CIPMODE=1   // 透传模式
AT+CIPSEND     //开始透传
+++            // 停止透传，不要 \r\n
AT+CIPCLOSE    // 断开服务器


AT+USART=115200,8,1,0,0
AT+CIFSR   // 模式 IP 信息


```

使用的 OneNet 网站：https://open.iot.10086.cn/console/device/manage/2116642397/attribute

账号：18402809049  密码：@Neversay1v1r

1. 创建产品（产品ID：RntTdSdMmN），MQTT 协议

2. 自定义功能

3. 新增设备（设备名test02，设备密钥：b3VXNE5sdUZFN2d0dHllWWNUMGNSWHNNMXdOWGxJcm4=）

4. 发布

![image-20230813214539437](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230813214539437.png)

```C
设备密钥：b3VXNE5sdUZFN2d0dHllWWNUMGNSWHNNMXdOWGxJcm4=
产品ID: RntTdSdMmN
设备名：test02

// token 计算值
version=2018-10-31&res=products%2FRntTdSdMmN%2Fdevices%2Ftest02&et=1872735919&method=md5&sign=x65RfIszoamPcy%2FMUMKXdg%3D%3D

// 配置MQTT 用户属性
AT+MQTTUSERCFG=0,1,"test02","RntTdSdMmN","version=2018-10-31&res=products%2FRntTdSdMmN%2Fdevices%2Ftest02&et=1872735919&method=md5&sign=x65RfIszoamPcy%2FMUMKXdg%3D%3D",0,0,""

// 连接/关闭 MQTT Broker 
AT+MQTTCONN=0,"mqtts.heclouds.com",1883,1
AT+MQTTCLEAN=0
    
// 订阅/取消订阅 主题
AT+MQTTSUB=0,"$sys/RntTdSdMmN/test02/thing/property/post/reply",1
AT+MQTTPUB=0,"$sys/RntTdSdMmN/test02/thing/property/post","{\"id\":\"123\"\,\"params\":{\"power\":{\"value\":66\}}}",0,0

// 发布字符串消息 
AT+MQTTPUB=0,"$sys/RntTdSdMmN/test02/thing/property/post","{\"id\":\"123\"\,\"params\":{\"power\":{\"value\":66\}\,\"temperature\":{\"value\":88\}}}",0,0

    
// 发布字符串消息   
AT+MQTTPUB=0,"$sys/RntTdSdMmN/test02/thing/property/post","
{\"id\":\"123\"\,\"params\":
	{\"power\":
		{\"value\":66\}\,
      \"volta\":
        {\"value\":88\}
    }
 }",0,0
        
// 其他 AT+MQTT 指令
 
AT+MQTTPUBRAW    // 发送二进制消息
AT+MQTTCONNCFG   // 配置 MQTT 连接属性
AT+MQTTCLIENTID  // 配置 MQTT 客户端ID
AT+MQTTUSERNAME	 // 配置 MQTT 登录用户名
AT+MQTTPASSWORD  // 配置 MQTT 登录密码
```



token 计算工具使用：

![20230813](D:\Administrator\Pictures\forTypora\20230813.jpg)

### 获取网络时间

NTP （network time protocol）网络时间

基于 UDP 报文进行传输，端口号为123



### CJSON-心知天气获取





获取api网站：https://www.seniverse.com/signup/verify?email=244304117%40qq.con&state=SEND_SUCCESS

账号：2443041171@qq.com  password: Neversay1v1r









## 设备驱动框架











## 内部 flash 编程

Keil 编译下：

- Code：代码区占用大小，即程序大小
- RO：只读变量占用大小，如const修饰的常量
- RW：读写变量占用大小，如已初始化的静态和全局变量
- ZI：非0变量占用大小，如未初始化的static修饰的静态变量，全局变量以及堆栈所占的空间 ，这些空间编译器将默认清零

实际使用的单片机ROM和RAM 大小是：

ROM=CODE+RO+RW

RAM=RW+Z

![image-20230815142340009](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230815142340009.png)

小容量：32K          （1K为一页）0x400 即1024 为 1K

中容量：128K         （1K为一页）

大容量：256K-512K （2K为一页）

故 STM32F103ZET6, 地址由

0X0800 0000 到 0X0807 FFFF   则为512K字节，一页为 2K, 一共256页

![image-20230815143002643](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230815143002643.png)

主存储块：存放代码

信息块：系统存储器：ARM 存储的一小段代码（串口烧录程序时boot0为1，boot1为0，该段代码起作用）

​			  选择字节：

| BOOT0 | BOOT1 | 启动模式        | note |
| ----- | ----- | --------------- | ---- |
| 0     | 任意  | 主 FLASH 存储器 |      |
| 1     | 0     | 系统存储器      |      |
| 1     | 1     | 内嵌 SRAM       |      |

flash 编程一次可以写入16位（半字），flash擦除操作可以按页擦除或完全擦除。全擦除不影响信息块

为了确保不发生过度编程，flash编程和擦除控制器是由一个固定的时钟控制的。

写操作（编程或擦除）结束时可以触发中断，仅当flash控制器接口时钟开启时，此中断可以用来从 WFI 模式退出。

```C
void FLASH_Unlock(void)
void FLASH_Lock(void)
FLASH_Status FLASH_ErasePage(uint32_t Page_Address)
FLASH_Status FLASH_ProgramWord(uint32_t Address, uint32_t Data);
FLASH_Status FLASH_ProgramHalfWord(uint32_t Address, uint32_t Data);
```



## 数字温湿度传感器 DHT11

- 相对湿度和相对温度测量（0~50℃，20-90%RH）
- 全部校准，数字输出
- 卓越长期稳定性，无需额外部件，超长的信号传输距离，超低能耗，

一根DATA 线串行通信，单总线

数据包格式：

| byte4 | byte3 | byte2 | byte1 | byte0  |
| ----- | ----- | ----- | ----- | ------ |
| 八位  | 八位  | 八位  | 八位  | 八位   |
| 整数  | 小数  | 整数  | 小数  | 校验和 |
|       | 湿度  |       | 温度  | 校验和 |

工作流程：

MCU 发送一次开始信号后，DHT11 从低功耗模式转换到高速模式，等待主机开始信号结束后。DHT11 发送40 bit 数据，并触发一次信号采集，完成后，转换到低速模式。

总线空闲为高电平，主机把总线拉低至少 18ms，随后拉高20-40us 即为开始信号；

DHT11 拉低电平80us，随后拉高8 0us 作为响应，后开始传输40bit 数据。

DHT11 的输出数字‘0’：低电平12-14us，随后高电平26-28us

DHT11 的输出数字‘1’：低电平12-14us，随后高电平116-118us

==不同信号，总线通信协议不一样，需要仔细测试==









































