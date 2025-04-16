#  6uC/OS-Ⅲ 操作系统开发

## Basic

uCOS-III，轻量级，商业收费

应用，而不是移植

OSInit()

两个任务是一定创建的，OS_IdleTask(), OS_TickTask()

三个任务是可选的： OS_TmrTask, OS_IntQTask, OS_StatTask

![uCOS-III任务状态](D:\File\qq\492298100\FileRecv\uCOS-III任务状态.jpg)

![uCOS-III任务状态切换](D:\File\qq\492298100\FileRecv\uCOS-III任务状态切换.jpg)

创建任务，任务是一个死循环

```C
void OSTaskCreate (OS_TCB			*ptcb,       // 输出类型，任务的 OS_TCB 结构体的指针，用来存储任务的 TCB（任务控制块）
				   CPU_CHAR			*pname,		 // 任务的名字，字符串
				   OS_TASK_PTR		p_task,		 // 函数名，即指向任务的指针
				   void				*p_arg,		 // 函数传递的参数
				   OS_PRIO			prio,		 // 任务优先级
				   CPU_STK			*p_stk_base, // 任务栈的基地址指针
				   CPU_STK_SIZE		stk_limit, 	 // 用于监视并确保栈不会溢出，剩余栈空间低于该值则报警
				   CPU_STK_SIZE		stk_size,	 // 栈空间大小
				   OS_MSG_QTY		q_size,		 // 可选的内部消息队列
				   OS_TICK			time_quanta, // 时间片轮转一片的时间
				   void 			*p_ext,		 // 指向用户额外的内存地址，作为 TCB 的扩展
				   OS_OPT			opt,		 // 包含任务具体的选项，每个选项为一个位
				   OS_ERR			*p_err		 // 输出参数，一个指针，指向存放错误代码的变量
)
/*
OS_TCB *ptcb:  输出参数，结构体指针
CPU_CHAR *pname: 
OS_TASK_PTR p_task:
void *p_arg:
OS_PRIO prio: 优先级
2 - 61，数值越小，则优先级越高

CPU_STK *p_stk_base:  任务栈用来存储局部变量，函数参数，返回地址，以及中断过程中 CPU 寄存器信息
必须这样定义，CPU_STK MyTaskStk[128]

CPU_STK_SIZE stk_limit:  通常为栈空间的 10%
CPU_STK_SIZE stk_size:
OS_MSG_QTY q_size:
if OS_CFG_TASK_Q_EN 设为 DEF_ENABLED 在 os_dfg.h 中，则开启，该参数才有效
0 该任务无法收到消息

OS_TICK	time_quanta:
void *p_ext:  基本不用
OS_OPT opt:
OS_OPT_TASK_NONE     无
OS_OPT_TASK_STK_CHK  是否允许栈检查
OS_OPT_TASK_STK_CLR  是否清零栈
OS_OPT_TASK_SAVE_FP	 是否
OS_OPT_TASK_NO_TLS

OS_ERR *p_err:
OS_ERR_NONE  函数成功

*
```



使用

```C
OS_TCB MyTaskTCB;
CPU_STK MyTaskStk[128];
OS_ERR err;
void OSTaskCreate ((OS_TCB *)			&MyTaskTCB,
				   (CPU_CHAR *)			"LedTask",
				   (OS_TASK_PTR)		led_task,
				   (void *)				0,
				   (OS_PRIO)			2,
				   (CPU_STK *)			&MyTaskStk[0],
				   (CPU_STK_SIZE)		10,
				   (CPU_STK_SIZE)		128,
				   (OS_MSG_QTY)			0u,
				   (OS_TICK)			0u,
				   (void *) 			0,
				   (OS_OPT)				(OS_OPT_TASK_STK_CHK | OS_OPT_TASK_STK_CLR),
				   (OS_ERR *)			&err
);
```

简单任务

```C
void led_task(void *p_arg){
	(void)p_arg;  // 无作用，只是去除警告
	for(;;);
}
```

## SysTick



```C
void OSInitEnter(void);  // 记录中断嵌套层数
操作系统中断进入函数，在中断处理程序开头调用

void OSInitExit(void);
操作系统中断退出函数，
```





```c
void OSTimeDly( OS_TICK dly,  // 相对于现在进行延时时间
				OS_OPT opt,
				OS_ERR *perr
);
/*

OS_OPT opt:
OS_OPT_TIME_DLY 设置为相对延时
OS_OPT_TIME_TIMEOUT
OS_OPT_TIME_PERIODIC
OS_OPT_TIME_MATCH
*/

void OSTimeDlyHMSM(CPU_INT16U	hours,
                   CPU_INT16U	minutes,
                   CPU_INT16U	seconds,
                   CPU_INT16U	milli,
                   OS_OPT		opt,
                   OS_ERR		*p_err
);
/*

OS_OPT opt:
OS_OPT_TIME_HMSM_STRICT  格式严格，如 分钟 0-59
OS_OPT_TIME_HMSM_NON_STRICT  格式不严格，如 分钟 0-999


*/
```



```C
OS_ERR err;
OSTimeDly(1000. OS_OPT_TIME_DLY, &err);
// ==
OSTimeDlyHMSM(0, 0, 1, 0,OS_OPT_TIME_HMSM_STRICT, &err);
```





使用自己定义的 Delay 函数，它会一直占用 CPU 资源，不允许操作系统进行调度，从而优先级最高的那个任务一直运行，而其他任务不运行。

在优先级相同的情况下，哪个任务先被创建，那个任务就先执行。



## 任务删除，挂起和恢复

任务删除：

```C
void OSTaskDel(OS_TCB *p_tcb, OS_ERR *p_err)
/*  删除任务，指内核不在管理该任务
OS_TCB *p_tcb: 要删除的任务的 TCB
(OS_TCB *)0 把自己这个任务删除
*/
```

任务挂起：

```C
void OSTaskSuspend( OS_TCB *p_tcb, OS_ERR *p_err);
/*  无条件地挂起（或阻塞）一个任务
OS_TCB *p_tcb:  要暂停的任务的 TCB
(OS_TCB *)0 把自己这个任务挂起
*/
```

任务恢复：

```C
void OSTaskResume ( OS_TCB *p_tcb, OS_ERR *p_err );
/*  恢复一个由 OSTaskSuspend 挂起的任务
OS_TCB *p_tcb: 要继续的任务的 TCB
*/
```



## 时间片轮转

时间片 time quanta，uCOS Ⅲ才支持

==在任务优先级相同情况下，允许通过时间片来切换任务，前提是任务的优先级相同。==



```C
// 在 os_cfg.h 中使能
OS_CFG_SCHED_ROUND_ROBIN_EN    1u
```

配置

```C
void OSSchedRoundRobinCfg( CPU_BOOLEAN en, OS_TICK dflt_time_quanta, OS_ERR *p_err );
/*
CPU_BOOLEAN en: 
DEF_ENABLED  使能
DEF_DISABLED  使能

OS_TICK dflt_time_quanta: 用于给任务的默认的时间片（一个时间片为1ms）
该参数被用在，当任务被创建时，OS_TICK time_quanta 设置为0
如，2000，则 任务一运行 2s，然后任务二（优先级相同）运行2s，

OS_TICK time_quanta 设置为非 0，则为该任务的运行时间，时间到了再让出 CPU 使用权给其他相同优先级的任务。OS_TICK dflt_time_quanta 此时无效



OS_ERR *p_err:
*/
```



使用

```C
// 先在 os_cfg.h 中使能
OS_CFG_SCHED_ROUND_ROBIN_EN    1u
// 然后再配置时间片
OSSchedRoundRobinCfg( DEF_ENABLED, 2000, *err );
// 实现了优先级相同的任务一，任务二，依次执行2s。这样即使使用 Delay 函数，也可以实现任务的切换
```







## 软件定时器

在 os_cfg.h 中 OS_CFG_TMR_EN 使能定时器

OS_CFG_TMR_DEL_EN  使能定时器删除

定时器的频率，在 os_cfg_app.h 中 OS_CFG_TMR_TASK_RATE_HZ 默认为 10Hz（T = 100ms） 

```C
void OSTmrCreate(OS_TMR					*p_tmr,
				 CPU_CHAR				*p_name,
				 OS_TICK				dly,
				 OS_TICK				period,
				 OS_OPT					opt,
				 OS_TMR_CALLBACK_PTR	p_callback,
				 void					*p_callback_arg,
				 OS_ERR					*p_err
)
/*  创建一个软件定时器，计算减到0时，p_callback 回调函数将被执行。是在 stop 状态创建的，因此要调用 OSTmrStart() 函数开启
OS_TMR *p_tmr: 指针，指向所创建的定时器控制块

CPU_CHAR *p_name: 定时器名字

OS_TICK	dly: 定时器的初始延时
若为单次模式，该值为要定时的值。
若为周期模式，该值为首次定时的时间，之后每个周期定时的值为周期 period。

OS_TICK period:
若为单次模式，该值设为 0
若为周期模式，该值为周期值

OS_OPT opt:
OS_OPT_TMR_PERIODIC  周期模式
0S_OPT_TMR_ONE_SHOT  单次模式，如需要再次启动，需要再次调用 OSTmrStart() 函数

OS_TMR_CALLBACK_PTR p_callback: 回调函数，函数应该尽可能的短
回调函数形式 void MyCallback(OS_TMR *p_tmr, void *p_arg);

void *p_callback_arg: 传递给回调函数的参数，uCOS 下没有 NULL, 直接写 0

OS_ERR *p_er: 返回的错误代码
*/
```



```C
// 开启定时器
CPU_BOOLEAN OSTmrStart(OS_TMR *p_tmr, OS_ERR *p_err);
/*
OS_TMR *p_tmr: 要启动的定时器
*/


// 停止定时器
CPU_BOOLEAN OSTmrStop(OS_TMR *p_tmr, 
                      OS_OPT opt,
                      void *p_callback_arg,
                      OS_ERR *p_err
                     );
/*
OS_TMR *p_tmr: 要停止的定时器

OS_OPT opt:

void *p_callback_arg:  给回调函数传递一个新的参数

OS_ERR *p_er:
*/


//删除定时器
CPU_BOOLEAN OSTmrDel(OS_TMR *p_tmr, OS_ERR *p_err);
/*
OS_TMR *p_tmr: 要删除的定时器

*/
```

使用

```C
/* 首先，在 os_cfg.h 中 OS_CFG_TMR_EN      使能定时器
					   OS_CFG_TMR_DEL_EN  使能定时器删除;  */
OS_TME tmr1;
OS_ERR err;
OSTmrCreate((OS_TMR				*)&tmr1,
				 (CPU_CHAR				*)"Timer1",
				 (OS_TICK				)20,
				 (OS_TICK				)10,
				 (OS_OPT				)OS_OPT_TMR_PERIODIC,
				 (OS_TMR_CALLBACK_PTR	)Timer1_Callback,
				 (void					*)0,
				 (OS_ERR				*)&err
);

void *Timer1_Callback(OS_TMR *p_tmr, void *arg){
    
    
    
}					   

```





















## 信号量 semaphore

==一种实现任务间通信的机制，可以实现任务之间的同步或临界资源的互斥访问（临界资源指同一时刻只能有有限个访问）==

==使用信号量可以实现一个任务控制另一个任务的运行==

```C
void OSSemCreate(OS_SEM *p_sen, 
				 CPU_CHAR *name,
				 OS_SEM_CTR cnt,
				 OS_ERR *p_err
				 );
/*  初始化一个信号量，当想要排他性地去访问资源，使用互斥机制保护共享资源
OS_SEM *p_sen: 指针，指向信号量控制块
如： OS_SEM MySem;  // 设为全局变量

CPU_CHAR *name:  // 信号量名字
OS_SEM_CTR cnt:  // 信号量初始计数值
OS_ERR *p_err:
*/
```

等待信号量

```C
OS_SEM_CTR OSSemPend( OS_SEM *p_sem,
					  OS_TICK timeout,
					  OS_OPT opt,
					  CPU_TS *p_ts,  // 时间戳 0
					  OS_ERR *p_err
);
/*  被调用时，若信号量>0，则会 -1；若信号量为0，则该任务阻塞，直到有任务使用 OSSemPost 增加了信号量，或者等待超时
OS_SEM *p_sem：要等待的信号量

OS_TICK timeout：
0 则一直阻塞等待

OS_OPT opt：
OS_OPT_PEND_BLOCKING     阻塞等待
OS_OPT_PEND_NON_BLOCKING 非阻塞等待

*/
```

增加信号量

```C
OS_SEN_CTR OSSemPost( OS_SEM *p_sem, OS_OPT opt, OS_ERR *p_err);
/*
OS_SEM *p_sem：要操作的信号量

OS_OPT opt
OS_OPT_POST_1    只把信号量发给正在等待该信号量的优先级最高的任务
OS_OPT_POST_ALL  把信号量发给正在等待该信号量所有任务
OS_OPT_POST_NO_SCHED


OS_ERR *p_err
*/
```

使用

```c
/*
首先，在 os_cfg.h 中使能 OS_CFG_SEM_EN
						OS_CFG_SEM_DEL_EN
						OS_CFG_SEM_PEND_ABORT_EN
						OS_CFG_SEM_SET_EN
*/
// STEP 1 创建信号量
OS_SEM MySem;
OSSemCreate((OS_SEM *) &MySem, 
			(CPU_CHAR *)"mysem"
			(OS_SEM_CTR) 1,
			(OS_ERR *)&err
);
// STEP 2 等待信号量
OSSemPend( (OS_SEM *)&MySem,
		   (OS_TICK )0,
		   (OS_OPT )OS_OPT_PEND_BLOCKING,
		   (CPU_TS *)0,
		   (OS_ERR *)&err
);
// STEP 3 增加信号量
OSSemPost( &MySem, OS_OPT_POST_ALL, &err);
```



#### 优先级反转

高优先级的任务，等待信号量，处于阻塞状态，要等其他任务释放信号量，这会导致高优先级的任务一直等待（能够释放信号量的）低优先级的任务，即优先级反转。

```C
void OSSched(void)  // 允许系统进行调度
```



#### 互斥信号量  Mutual Excusion Semaphore，即互斥锁

==可以解决优先级反转的问题，其实反转问题也是由信号量所引起的。==

==原理：将具有信号量的任务的优先级暂时调到与 当前等待信号量的任务中最高优先级的任务 相同的优先级==

首先，在 os_cfg.h 中使能 OS_CFG_MUTEX_EN

创建  OSMutexCreate 

删除  OSMutexDel  

挂起  OSMutexPend  （与 OSSemPend 类似）

释放  OSMutexPost   （与 OSSemPost 类似）

```C
void OSMutexCreate( OS_MUTEX	*p_mutex,
					CPU_CHAR    *p_name,
					OS_ERR      *p_err
);
/*  创建信号量
OS_MUTEX	*p_mutex:  指针，指向要创建的互斥控制块
如：OS_MUTEX MyMutex;

CPU_CHAR    *p_name:  互斥信号量名字

OS_ERR      *p_err:
*/
```



## 消息队列  Message Queues

用于任务之间通信，任务之间值的传递

首先，在 os_cfg.h 中使能 OS_CFG_Q_EN

创建：	OSQCreate

接收：	OSQDel

挂起：	OSQPend

发送：	OSQPost

```C
#include "os.h"
#include "os_q.h"
void OSQCreate ( OS_Q			*p_q,
				 CPU_CHAR		*p_name,
				 OS_MSG_QTY		max_qty,
				 OS_ERR			*p_err

);  // 创建一个消息队列，允许任务发送消息给一个或多个任务
/*
OS_Q			*p_q:  // 指针，指向消息队列控制块
如：OS_Q MyMsgQ;

CPU_CHAR		*p_name:  // 消息队列名字
OS_MSG_QTY		max_qty:  // 消息队列的最大尺寸，必须非0，
设置足够大就可

OS_ERR			*p_err:
*/
```



通过消息队列先一个任务发送消息

```C
void OSQPost( OS_Q			*p_q,     // 指针，指向要使用的消息队列
			  void			*p_void,  // 要发送的消息 如：(void *)"hello uCOS."
			  OS_MSG_SIZE	msg_size, // 消息的大小，单位 bytes，如：sizeof("hello uCOS.")
			  OS_OPT		opt,	  // 
			  OS_ERR 		*p_err
);
// OS_OPT		opt: 发送的类型
OS_OPT_POST_FIFO     先进先出
OS_OPT_POST_LIFO	 后进先出
OS_OPT_POST_ALL 	 发送给所有正在等待这个消息队列的任务
OS_OPT_POST_NO_SCHED 发布后不触发调度器
```

接收消息

```C
void *OSQPend ( OS_Q			*p_q,  		  // 指针，指向要使用的消息队列
				OS_TICK			timeout,      // 0 则为阻塞等待
				OS_OPT			opt,
				OS_MSG_SIZE		*p_msg_size,  // 输出类型的参数，读取到的消息队列的长度，单位 bytes
				CPU_TS			*p_ts,    	  // 指针，指向接收消息的接收时间的时间戳
				OS_ERR			*p_err
);
/*
OS_OPT			opt：
OS_OPT_PEND_BLOCKING  阻塞等待
OS_OPT_PEND_NON_BLOCKING  非阻塞等待

OS_ERR			*p_err:
ERROR_NONE 成功接收到消息

返回值：指针，指向消息或 NULL(当没有消息时)
*/
```

使用

```C
// 创建
OS_Q MyMsgQ;
OSQCreate ( (OS_Q)			*) &MyMsgQ,
			(CPU_CHAR)		*) "MyMessageQ",
			(OS_MSG_QTY		 ) 10000,
			(OS_ERR			*) &err

); 
// 发送
void OSQPost( (OS_Q			*) &MyMsgQ,
			  (void			*) "hello uCOS."),
			  (OS_MSG_SIZE	) (sizeof("hello uCOS.")),
			  (OS_OPT		) (OS_OPT_POST_ALL | OS_OPT_POST_FIFO),
			  (OS_ERR 		*) &err
);
// 接收
char *p_Msg;
OS_MSG_SIZE Msg_Size;
p_Msg = OSQPend ((OS_Q			*)  &MyMsgQ,  		 
				 (OS_TICK		) 0, 
				 (OS_OPT		) OS_OPT_PEND_BLOCKING,
				 (OS_MSG_SIZE	*) &Msg_Size,
				 (CPU_TS		*) 0,
				 (OS_ERR		*) &err
);
if( OS_ERR_NONE == err)
    printf("Successfully receive the message: %s\n", p_Msg);
```



## 事件标志组  Event Flag

==若干个事件同时发生的标志==

==如：与同步，两个或多个事件，同时发生，则触发一个标志；==

==或同步，两个或多个事件中任意一个发生，则触发一个标志。==

创建事件标志组： OSFlagCreate

删除： OSFlagDel

等待事件标志组： OSFlagPend

事件位置1或置0： OSFlagPost

1. 首先在 os_cfg.h 中使能 OS_CFG_FLAG_EN

2. 创建事件标志组

```C
void OSFlagCreate ( OS_FLAG_GRP		*p_grp,
					CPU_CHAR 		*p_name,
					OS_FLAGS		flags,
					OS_ERR			*p_err
)
/*
OS_FLAG_GRP		*p_grp: 指针，指向事件标志组
如：OS_FLAG_GRP MyEventFlag; 定义一个全局变量

CPU_CHAR 		*p_name: 事件标志组名字

OS_FLAGS		flags:  事件标志组中标志的初始值。
OS_ERR			*p_err: 
					
*/
```

3. 等待事件标志组

```C
OS_FLAGS OSFlagPend ( OS_FLAG_GRP	*p_grp,   // 指针，指向要等待的事件标志组
					  OS_FLAGS		flags,    // 要检查哪一位，如：0x03 检查第 0 和第 1 位
					  OS_TICK		timeout,  // 超时时间，若设为 0 ，则阻塞等待
					  OS_OPT		opt, 	  // OS_OPT_PEND_FLAG_SET_ALL | OS_OPT_PEND_FLAG_CONSUME | OS_OPT_PEND_BLOCKING  // 检查 0和1位是否都置1，阻塞等待，等到该标志后，清除该标志（此时将第 0 和第 1 位置0）
					  CPU_TS		*p_ts,    // 时间戳
					  OS_ERR		*p_err

)
/*
OS_OPT		opt： // 配置是否检查所有位
OS_OPT_PEND_FLAG_CLR_ALL    检查标志组的所有位是否为 0
OS_OPT_PEND_FLAG_CLR_ANY    检查标志组的任一位是否为 0
OS_OPT_PEND_FLAG_SET_ALL    检查标志组的所有位是否为 1
OS_OPT_PEND_FLAG_SET_ANY    检查标志组的任一位是否为 1

OS_OPT_PEND_FLAG_CONSUME    等到该标志后，清除该标志
OS_OPT_PEND_BLOCKING   阻塞等待
OS_OPT_PEND_NUN_BLOCKING  非阻塞等待
*/  
    
```

4. 事件位置1或置0

```C
OS_FLAGS OSFlagPost ( OS_FLAG_GRP 	*p_grp,  // 指针，指向要操作的事件标志组
					  OS_FLAGS		flags,	 // 要设置哪一位
					  OS_OPT		opt,	 // 置 1 还是置 0
					  OS_ERR 		*p_err
)
/*
OS_FLAGS	flags：
 如 0x31 设置位0，4，5   
    
OS_OPT		opt:
OS_OPT_POST_FLAG_SET  置1
OS_OPT_POST_FLAG_CLR  置0
*/ 
```

5. 使用

```c
// 创建
OS_FLAG_GRP MyEventFlag;
OSFlagCreate ( (OS_FLAG_GRP	*) &MyEventFlag,
			   (CPU_CHAR 	*) "EventFlag",
			   (OS_FLAGS	 ) 0,     // 事件标志组中标志的初始值为 0 
			   (OS_ERR	    *) &err
);
// 在某任务中，满足了 xxx 条件则标志组置 1
OSFlagPost ( (OS_FLAG_GRP 	*) &MyEventFlag,  
			 (OS_FLAGS		 ) 0x1,	      // 操作第 0 位
			 (OS_OPT		 ) OS_OPT_POST_FLAG_SET,	// 置 1
			 (OS_ERR 		*) &err
);
// 在另一个任务中检查标志组是否置 1
OSFlagPend ( (OS_FLAG_GRP	*)  &MyEventFlag,   // 指针，指向要等待的事件标志组
			 (OS_FLAGS		 ) 0x03,    // 要检查哪一位
			 (OS_TICK		 ) 0,       // 超时时间，若设为 0 ，则阻塞等待
			 (OS_OPT		 ) OS_OPT_PEND_FLAG_SET_ALL | OS_OPT_PEND_FLAG_CONSUME | OS_OPT_PEND_BLOCKING , 	  //
			 (CPU_TS		*) 0,       // 不使用时间戳
			 (OS_ERR		*) &err

)
```



## 等待多个内核对象

一个任务既在等待信号量又在等待消息队列

如

```C
OSSemPend(){  // 等待信号量
		
}
OSQPend(){  // 等待消息队列


}
// 两者都是阻塞等待，此时就算消息队列来了，但是程序被先阻塞在了上面的 信号量等待。
// 如何跨过前面的信号量等待的阻塞状态，从而响应信息队列？？？
```

1. 首先，在 os_cfg.h 文件中使能 OS_CFG_PEND_MULTI_EN

当要等待多个内核对象时， 使用 OSPendMulti() ，当一个或多个对象就绪时，所有就绪的内核对象会返回给该函数；否则，一直阻塞，直到一个内核对象就绪，或超时时间到，或一个或多个内核对象被删除。



```C
#include "os.h"
#include "os_pend_multi.c"
OS_OBJ_QTY OSPendMulti ( OS_PEND_DATA	*p_pend_data_tbl,
						 OS_OBJ_QTY		tbl_size,  // 要等待多少个内核对象
						 OS_TICK		timeout,   // 超时时间 0 则阻塞等待
						 OS_OPT			opt,       // OS_OPT_PEND_BLOCKING 阻塞等待
						 OS_ERR			*p_err
);
/*
OS_PEND_DATA	*p_pend_data_tbl: 指针，指向一个 OS_PEND_DATA（结构体） 类型的表（即数组）。
用户必须初始化 OS_PEND_DATA.PendObjPtr，要等待的内核对象
如：等待两个内核对象
OS_PEND_DATA Mul_Pend_Arry[2];
Mul_Pend_Arry[0].PendObjPtr = (OS_PEND_OBJ *)&MySem;
Mul_Pend_Arry[1].PendObjPtr = (OS_PEND_OBJ *)&MyMsgQ;

OS_OBJ_QTY	tbl_size:
如：等待两个内核对象 则 设为 2

OS_OPT		opt:  是否阻塞等待
OS_OPT_PEND_BLOCKING
OS_OPT_PEND_NON_BLOCKING

返回值：返回 多个要等待的内核对象 中的就绪对象数量。如果一个对象被 pend aboted或者deleted，返回1，可以通过 p_err的值来得知具体是哪个。
当要等待的内核对象就绪，p_pend_data_tbl 存储了额外信息
p_pend_data_tbl.RdyObjPtr   指针，指向就绪的对象；否则 NULL 无对象就绪
p_pend_data_tbl.RdyMsgPtr   如果等待的是 消息队列，并且消息队列已发布。则此处存放接收到的消息队列
p_pend_data_tbl.RdyMsgSize  接收到的消息队列的大小，单位 bytes
p_pend_data_tbl.RdyTS   

*/
```

使用

```c
// 内核对象1：信号量
OS_SEM MySem;
OSSemCreate((OS_SEM *) &MySem, 
			(CPU_CHAR *)"mysem"
			(OS_SEM_CTR) 1,
			(OS_ERR *)&err
);

// 内核对象2：消息队列
OS_Q MyMsgQ;
OSQCreate ( (OS_Q)			*) &MyMsgQ,
			(CPU_CHAR)		*) "MyMessageQ",
			(OS_MSG_QTY		 ) 10000,
			(OS_ERR			*) &err
); 

// 
OS_PEND_DATA Mul_Pend_Arry[2];
Mul_Pend_Arry[0].PendObjPtr = (OS_PEND_OBJ *)&MySem;
Mul_Pend_Arry[1].PendObjPtr = (OS_PEND_OBJ *)&MyMsgQ;

// 任务一：满足 xxx条件，则发布信号量和消息队列。周期（软件定时器）发送或者满足条件发送
/***  省略  ***/  // 如在软件定时器中，定时扫描按键是否被按下，按下则发送 信号量
/***  省略  ***/  // 每隔一段时间发送 消息队列

// 任务二：同时等待 信号量和消息队列
OS_OBJ_QTY OSPendMulti ( (OS_PEND_DATA	*) Mul_Pend_Arry,
						 (OS_OBJ_QTY	)  2,
						 (OS_TICK		)  0,
						 (OS_OPT		)  OS_OPT_PEND_BLOCKING,
						 (OS_ERR		*) &err
);
if( Mul_Pend_Arry[0].RdyObjPtr == Mul_Pend_Arry[0].PendObjPtr ){  // 是要等待的 信号量
    puts("semaphore got.");
    
}
if( Mul_Pend_Arry[1].RdyObjPtr == Mul_Pend_Arry[1].PendObjPtr ){  // 是要等待的 消息队列
/*  按照原来方式来接收 消息队列，但是现在不需要了
    char *p_Msg;
	OS_MSG_SIZE Msg_Size;
	p_Msg = OSQPend ((OS_Q			*)  &MyMsgQ,  		 
					 (OS_TICK		) 0, 
					 (OS_OPT		) OS_OPT_PEND_BLOCKING,
					 (OS_MSG_SIZE	*) &Msg_Size,
					 (CPU_TS		*) 0,
					 (OS_ERR		*) &err
	);
	if( OS_ERR_NONE == err)
   		printf("message queues got,and = %s.\r\n",p_Msg);
*/
    printf("message queues got,and = %s, size = %d bytes.\r\n",
           (char *)Mul_Pend_Arry[1].RdyMsgPtr,Mul_Pend_Arry[1].RdyMsgSize);
}

```



## 任务信号量  Task Semaphores

之前学习的信号量，由某个任务发布信号量，其他一个或多个任务捕获该信号量（发布时带参数 OS_OPT_POST_ALL，则所有等待该信号量的任务都会响应一次）。

实际开发中，这种方式用的较少。

- 一个任务发布，多个任务接收 （一对多）
- 信号量独立于任务，使用 OSSemCreate 创建（已经定义了一个全局变量存放该信号量）

==用的较多的是：一个或者多个任务 发布信号量给 某一个特定任务。==

- 多发布，一接收（一对一，或多对一）

- 与任务相关联，是任务的特有属性，紧紧依赖于一个任务（与任务消息队列类似）

- ==只发布给某一个任务，只有该任务能够接收该信号量，故名 任务的信号量（操作简单，相比于多值信号量；所以能使用任务信号就使用任务信号量）==

- 不需要创建信号量，任务创建的时候，它的任务信号量也就创建了

- 常用 API：OSTaskSemPend   （任务信号量不需要在 os_cfg.h 中使能，默认就开启了）

  ​				 OSTaskSemPost

等待任务信号量：

```C
OS_SEM_CTR OSTaskSemPend ( OS_TICK 	timeout,  // 超时时间 0 阻塞等待
						   OS_OPT	opt,      // 是否阻塞等待
						   CPU_TS	*p_ts,	  // 时间戳，0 不使用
						   OS_ERR	*p_err
)
/* 
OS_OPT	opt: 如下
OS_OPT_PEND_BLOCKING
OS_OPT_PEND_NON_BLOCKING


*/   
    
```



发布任务信号量

```C
OS_SEM_CTR OSTaskSemPost ( OS_TCB	*p_tcb,  // 指针，指向希望发布给的那个任务
						   OS_OPT	opt,	 // OS_OPT_POST_NONE 默认，使用调度器
						   OP_ERR	*p_err	 //
)
/*  // 通过本地的信号量发布一个信号给某个任务
OS_OPT	opt: 如下：
OS_OPT_POST_NONE  默认，使用调度器
OS_OPT_POST_NO_SCHED	发布后不使用调度器，即发布后还要在本任务中执行一会才会被消费

*/
```

使用：

```C

// Task01_TCB 在任务一中，等待任务信号量
for(;;)}
	OSTaskSemPend ( (OS_TICK ) 0,
					(OS_OPT	 ) OS_OPT_PEND_BLOCKING,
					(CPU_TS	*) 0,
					(OS_ERR	*) &err
	);
	LED_TOGGLE;  // Led 翻转，或者 puts 信息
}
// 在任务二中，通过在软件定时器中检测按键是否被按下，按下则发布一个任务信号量
OSTaskSemPost ( (OS_TCB	*) &Task01_TCB,  // 指定该信号量发布给任务一
				(OS_OPT	 ) OS_OPT_POST_NONE,
				(OP_ERR	*) &err
);
```



### 任务消息队列

和任务信号量一样，都是属于某一个任务，不需要单独创建。只要任务存在，就会有该任务的任务信号量和任务消息队列，同时也只有该任务才能接收到该任务消息队列里的消息。

首先，在 os_cfg.h 中使能  OS_CFG_TASK_Q_EN

主要使用 API： OSTaskQPend

​						 OSTaskQPost

等待任务消息队列

```C
void *OSTaskQPend ( OS_TICK			timeout,   	 // 超时时间， 0 则阻塞等待
					OS_OPT			opt,		 // 是否阻塞等待
					OS_MSG_SIZE		*p_msg_size, // 输出类型参数，指针，指向一个变量，该变量存放消息队列的尺寸
					CPU_TS			*p_ts,		 // 时间戳，0 不使用
					OS_ERR			*p_err		 // 
)
/*  // 等待一个任务消息队列
OS_OPT			opt:
OS_OPT_PEND_BLOCKING      阻塞等待
OS_OPT_PEND_NON_BLOCKING  非阻塞等待

OS_MSG_SIZE		*p_msg_size:

*/    
```

发布任务消息队列

```C
void OSTaskQPost ( OS_TCB			*p_tcb,   // 指针，指向任务的 TCB（任务控制块），若为 NULL 则发给自己这个任务块
				   void				*p_void,  // 实际发送的消息
				   OS_MSG_SIZE		msg_size, // 消息的大小，单位 bytes
				   OS_OPT			opt,      // 
                   OS_ERR			*p_err 	  // 
)
/*

OS_OPT  opt: 
OS_OPT_POST_FIFO     先入先出，将消息放在队列最后
OS_OPT_POST_LIFO 	 后入先出，将消息放在队列最前方
OS_OPT_POST_NO_SCHED 不触发调度器，则意味着发布任务消息队列后不会立马响应

*/
```

使用

```C
// 任务一中，通过在软件定时器中检测按键是否被按下，按下则发布一个任务信号量
char TaskMsgQ_Send[] = "It is easy uCOS 3.";
OSTaskQPost ( (OS_TCB			*) &Task02_TCB,   // 指针，指向任务的 TCB（任务控制块），若为 NULL 则发给自己这个任务块
			  (void				*) TaskMsgQ_Send,  // 实际发送的消息
			  (OS_MSG_SIZE		 ) sizeof(TaskMsgQ_Send), // 消息的大小，单位 bytes
			  (OS_OPT）          ) OS_OPT_POST_FIFO,  // 消息先入先出
              (OS_ERR			*) &err 	  // 
)
// 任务二( Task02_TCB )中，等待任务消息队列
OS_MSG_SIZE p_msg_size;
char *pTaskMsg;
pTaskMsg = OSTaskQPend ( (OS_TICK		 ) 0,   	 // 超时时间， 0 则阻塞等待
			 		   (OS_OPT			 ) OS_OPT_PEND_BLOCKING,		 // 是否阻塞等待
					   (OS_MSG_SIZE		*) &p_msg_size, // 指针，指向一个变量，该变量存放消息队列的尺寸
					   (CPU_TS			*) 0,		 // 时间戳，0 不使用
			  		   (OS_ERR			*) &err		 // 
)
```



## 内存管理

==多次分配和释放会产生内存碎片，即断断续续的小的内存块。==

==使用内存管理可以避免产生内存碎片。==

首先，在 os_cfg.h 中使能 OS_CFG_MEM_EN

使用的 API :  OSMemCreate 

​					 OSMemGet

​					 OSMemPut

1. ==创建并初始化一个内存分区，一个内存分区包含配置的指定数量的固定大小的内存块==。一个任务可以使用其中一个内存块，任务结束后，要释放内存块，释放到原来的内存分区中。

```C
void OSMemCreate ( OS_MEM		*p_mem,   // 指针，指向内存分区控制块
				   CPU_CHAR		*p_name,  // 内存分区的名字
				   void			*p_addr,  // 要创建的内存分区的起始地址
				   OS_MEM_QTY	n_blks,   // 多少块
				   OS_MEM_SIZE	blk_size, // 每块多大空间，单位 bytes
				   OS_ERR		*p_err
)
/*  
OS_MEM		*p_mem:
如:  OS_MEM MyMemPartition

void		*p_addr:  输出参数
如:  void *MyMemArray[N][M]  // N 个块，M 是每块所占的内存空间
OS_MEM_QTY	n_blks:    N
OS_MEM_SIZE	blk_size:  M

*/  
```



2. 从一个内存分区中获得一个内存块

```C
void *OSMemGet ( OS_MEN		*p_mem,  // 指针，指向要使用的内存分区
				 OS_ERR		*p_err   // 
)
/*
返回值: 指针，指向所分配的内存块
*/
```

3. 归还一个内存块给内存分区

```C
void OSMemPut ( OS_MEM 		*p_mem,  // 指针，指向要归还的内存分区
			 	void		*p_blk,  // 指针，指向要归还内存块
			 	OS_ERR		*p_err
)
```



使用

```C
/*****     在 main 函数 OSInit(&err); 系统初始化之后创建并初始化一个内存分区      ******/
OS_MEM MyMemPartition;
unsiged char MyMemArray[3][50];  //开辟内存分区，包含 3 个块，每个块占用 50 个字节
OSMemCreate ( (OS_MEM		*) &MyMemPartition,
			  (CPU_CHAR		*) "mymempartition",
			  (void			*) MyMemArray,
			  (OS_MEM_QTY	 ) 3,
			  (OS_MEM_SIZE	 ) 50,
			  (OS_ERR		*) &err
)
    
/*****     在任务一中      ******/
unsigned char *p_MemBlk;
p_MemBlk = OSMemGet ( (OS_MEN		*) MyMemPartition,  // 指针，指向要使用的内存分区
				 	  (OS_ERR		*) &err   
);
/*  将计数值存在内存块中  */
sprintf(p_MemBlk, "%d", cnt++);     // 将计数值存在内存块中
/*  将存块中的字符串发送给任务二  */
if(KEY_ON == KEY_Scan(GPIOA,GPIO_Pin_0)){
    OSTaskQPost ( (OS_TCB			*) &Task02_TCB,    // 指针，指向任务的 TCB（任务控制块），若为 NULL 则发给自己这个任务块
			 	(void				*) p_MemBlk,       // 实际发送的消息
			 	(OS_MSG_SIZE		 ) sizeof(p_MemBlk),  // 消息的大小，单位 bytes
			 	(OS_OPT）         	) OS_OPT_POST_FIFO,  // 消息先入先出
             	(OS_ERR				*) &err 	  
	)   
}  
/*  归还内存控制块，使用完毕后    */
OSMemPut ( (OS_MEM 		*) &MyMemPartition,  // 指针，指向要归还的内存分区
		   (void		*) &p_MemBlk,       // 指针，指向要归还内存块
		   (OS_ERR		*) &err
);

/*****  任务二( Task02_TCB )中，等待任务消息队列   ******/
OS_MSG_SIZE p_msg_size;
char *pTaskMsg;
pTaskMsg = OSTaskQPend ( (OS_TICK		 ) 0,   	 // 超时时间， 0 则阻塞等待
			 		   (OS_OPT			 ) OS_OPT_PEND_BLOCKING,		 // 是否阻塞等待
					   (OS_MSG_SIZE		*) &p_msg_size, // 指针，指向一个变量，该变量存放消息队列的尺寸
					   (CPU_TS			*) 0,		 // 时间戳，0 不使用
			  		   (OS_ERR			*) &err		 // 
)
    
```



## 中断管理

==中断相当于优先级最高的任务，用于处理比较紧急的事件，中断程序一定要简短。一般，在其中做标记的工作（如发布信号量，发布事件标志组，发布消息队列等）。复杂的事情，退出中断在去做。==

- 第一种情况：直接在中断中发布信号量，发布事件标志组，发布消息队列等；
- 第二种情况：==中断延迟==，在中断中，只是置 flag，退出中断之后，发布信号量，发布事件标志组，发布消息队列等。

因为，发布事件标志组，发布消息队列等，会触发一次任务的调度，任务状态的切换，这很消耗时间和 CPU。

直接发布模式下，uCOS Ⅲ通过关闭中断来保护临界段代码；延迟发布模式下，UCOS Ⅲ 通过锁定任务调度来保护临界段代码。在访问中断队列时，仍然需要关闭中断，但这个时间非常短。

临界段代码：也叫临界区，是指那些必须完整连续运行，不可被打断的代码段。当访问这些临界代码的时候需要对这些临界代码进行保护。

当宏 OS_CFG_ISR_POST_DEFERRED_EN  为0时， UCOS3 通过关闭中断的方式来保护临界代码；当置为 1时，会采用给调度器上锁的方式来保护临界段代码。

首先，在 os_cfg.h 中使能 OS_CFG_ISR_POST_DEFERRED_EN 

```C
void OSIntEnter();  // 进入中断
void OSIntExit();   // 退出中断
```

使用案例，在串口一接收数据，通过任务消息队列发送消息，并在任务中显示消息内容。

```C
/*****     Task01_TCB 任务一 接收任务消息队列，显示消息队列的内容     *****/
OS_MSG_SIZE p_msg_size;
char *pTaskMsg;
pTaskMsg = OSTaskQPend ( (OS_TICK		 ) 0,   	    // 超时时间， 0 则阻塞等待
			 		   (OS_OPT			 ) OS_OPT_PEND_BLOCKING,		 // 是否阻塞等待
					   (OS_MSG_SIZE		*) &p_msg_size, // 指针，指向一个变量，该变量存放消息队列的尺寸
					   (CPU_TS			*) 0,		    // 时间戳，0 不使用
			  		   (OS_ERR			*) &err		    
);
if(OS_ERR_NONE == err)
    printf("Task: the task message queues received successfully, and = %c, size = %d\r\n",&pTaskMsg,p_msg_size);
/*****     串口中断     *****/
void USART1_IRQHandler(void){
    OS_ERR err;
    volatile char ch;
    volatile int cnt;
    OSInitEnter();
    cnt = 0;
    while(SET==USART_GetFlagState(USART1,USART_FLAG_RXEN) ){
        ch = USART_ReceiveData(USART1);
        cnt += 1;
    }
    if(cnt>0){
        USART_SendData(USART1,cnt+'0');
        OSTaskQPost ( (OS_TCB			*) &Task01_TCB,   // 指针，指向任务的 TCB（任务控制块），若为 NULL 则发给自己这个任务块
					(void				*) &ch,  // 实际发送的消息
			        (OS_MSG_SIZE		 ) sizeof(ch), // 消息的大小，单位 bytes
			 		(OS_OPT）        	) OS_OPT_POST_FIFO,  // 消息先入先出
             		(OS_ERR				*) &err);
    }
    OSIntExit();    
}
/* 现象
发送 ab 不发送新行
11Task: the task message queues received successfully, and = a, size = 1
说明进入了两次中断并执行了两次任务消息队列的发布，但是第二次执行发布会返回错误OS_ERR_Q_MAX（队列已满，未发布消息）。
因此只执行了一次消息队列的接收
*/
```

改正后 OK

```C
/*****     串口中断     *****/
char string[50];
int cnt = 0;
void USART1_IRQHandler(void){
    OS_ERR err;
    volatile char ch;
    volatile int cnt;
    OSInitEnter();

    while(SET==USART_GetFlagState(USART1,USART_FLAG_RXEN) ){
        string[cnt] = USART_ReceiveData(USART1);
        cnt += 1;
    }
    if((sting[cnt-1]=='\n') && (sting[cnt-2]=='\r')){  // 接送到 \r\n 则说明接收结束
        USART_SendData(USART1,cnt+'0');
        OSTaskQPost ( (OS_TCB			*) &Task01_TCB,   // 指针，指向任务的 TCB（任务控制块），若为 NULL 则发给自己这个任务块
					(void				*) string,  // 实际发送的消息
			        (OS_MSG_SIZE		 ) cnt, // 消息的大小，单位 bytes
			 		(OS_OPT）        	) OS_OPT_POST_FIFO,  // 消息先入先出
             		(OS_ERR				*) &err);
        cnt = 0;
    }
    OSIntExit();    
}
```

扩展，通过内存管理，创建内存分区，再申请内存块，将串口接收到的数据保存在内存块中。同时，在任务一中读取内存块内容。

```C
/*****     在 main 函数 OSInit(&err); 系统初始化之后创建并初始化一个内存分区      ******/
OS_MEM MyMemPartition;
unsiged char MyMemArray[3][50];  //开辟内存分区，包含 3 个块，每个块占用 50 个字节
OSMemCreate ( (OS_MEM		*) &MyMemPartition,
			  (CPU_CHAR		*) "mymempartition",
			  (void			*) MyMemArray,
			  (OS_MEM_QTY	 ) 3,
			  (OS_MEM_SIZE	 ) 50,
			  (OS_ERR		*) &err
);
    
/*****     Task01_TCB 任务一 接收任务消息队列，显示消息队列的内容     *****/
OS_MSG_SIZE p_msg_size;
char *p_MemBlk;
char *pTaskMsg;
// 申请内存块
p_MemBlk = OSMemGet ( (OS_MEN		*) MyMemPartition,  // 指针，指向要使用的内存分区
				 	  (OS_ERR		*) &err   
);
pTaskMsg = OSTaskQPend ( (OS_TICK		 ) 0,   	    // 超时时间， 0 则阻塞等待
			 		   (OS_OPT			 ) OS_OPT_PEND_BLOCKING,		 // 是否阻塞等待
					   (OS_MSG_SIZE		*) &p_msg_size, // 指针，指向一个变量，该变量存放消息队列的尺寸
					   (CPU_TS			*) 0,		    // 时间戳，0 不使用
			  		   (OS_ERR			*) &err		    
);
if(OS_ERR_NONE == err)
    printf("Task: the task message queues received successfully, and = %c, size = %d\r\n",&pTaskMsg,p_msg_size);
//  归还内存控制块，使用完毕后   
OSMemPut ( (OS_MEM 		*) &MyMemPartition,  // 指针，指向要归还的内存分区
		   (void		*) &p_MemBlk,       // 指针，指向要归还内存块
		   (OS_ERR		*) &err
);

/*****     串口中断     *****/
extern char *p_MemBlk;
char string[50];  // 串口缓冲
int cnt = 0;      // 缓冲长度 bytes
void USART1_IRQHandler(void){
    OS_ERR err;
    volatile char ch;
    volatile int cnt;
    OSInitEnter();
    while(SET==USART_GetFlagState(USART1,USART_FLAG_RXEN) ){
        string[cnt] = USART_ReceiveData(USART1);
        cnt += 1;
    }
    if((sting[cnt-1]=='\n') && (sting[cnt-2]=='\r')){  // 接送到 \r\n 则说明接收结束
        p_MemBlk = string;
        USART_SendData(USART1,cnt+'0');
        OSTaskQPost ( (OS_TCB			*) &Task01_TCB,  // 指针，指向任务的 TCB（任务控制块），若为 NULL 则发给自己这个任务块
					(void				*) p_MemBlk,     // 实际发送的消息
			        (OS_MSG_SIZE		 ) cnt,           // 消息的大小，单位 bytes
			 		(OS_OPT）        	) OS_OPT_POST_FIFO,  // 消息先入先出
             		(OS_ERR				*) &err);
        cnt = 0;
    }
    OSIntExit();    
}
```



# uCOS Ⅲ 的移植

## 

RTOS: real-time operating system 实时操作系统

专注于处理实时性任务和资源管理，通常非常轻量级，具有快速启动时间和低延迟，使其非常适合嵌入式系统和实时嵌入式应用程序。

通常支持多任务处理，具有任务调度器，可以根据任务的优先级和调度算法，动态地分配处理器资源。还提供了实时时钟和定时器功能，以便准确地计时任务的执行和触发事件。

不同的 RTOS 具有不同的特性和功能，以适应不同的实时应用场景。常见的包括 FreeRTOS, VxWorks, QNX, Micrium 等。

1. 分而治之，功能划分成多个任务栏
2. 延时函数，期间进行任务调度
3. 抢占式，高优先级任务抢占低优先级任务
4. 任务堆栈，每个任务都有自己的栈空间
5. 中断（属于内核）比所有任务（软件）优先级要高



UCOSⅢ 即 Micro COS Three，uC/OS-Ⅲ 基于 C 语言的第三代小型操作系统

任务数无限制，单个优先级任务数无限制，支持时间片调度，支持事件标志，支持队列，可裁剪，代码量6-24K

稳定，可靠，资料丰富，支持系列广，移植简单，可移植性好，商用收费

调度器就是使用相关的调度算法来决定当前需要执行哪个任务

- 抢占式调度：针对优先级不同的任务，数值越小优先级越高
- 时间片调度：针对优先级相同的任务，根据设置的时间片轮流执行这些任务

时间片是以系统时钟节拍（1ms）为单位的，默认设置是100个时间片

任务状态：

1. 运行态：仅在就绪态和中断态的任务可转变为运行态
2. 就绪态：被创建的任务，初始状态均为就绪态
3. 挂起态
4. 休眠态：已经在内存中（任务被删除了），不能被调度器管理。（被删除的任务进入休眠态）
5. 中断态：运行态的任务被中断打断，CPU 跳转去执行中断服务函数，则它会切换到中断态，直到中断结束，再切换回运行态

三大类列表用来跟踪任务状态

1. 就绪列表：就绪态的任务放在就绪列表：OSRdyList[x], x 代表任务优先级。（0-31，一共32位，哪一位有任务则置1，）
2. Tick 列表：正在等待延时超时或挂起的对象超时的任务，将放在 OSTickList
3. 挂起列表：当任务等待信号量，事件时，任务被放置在挂起列表 PendList

有一个空闲任务，优先级最低，一直处于就绪态，不可被阻塞

### 移植到到 STM32F103C8T6

1. 获取源码

目标：获取 uC-OS3 ( c3.08.01 ), uC-CPU ( v1.32.01 ), uC-LIB ( v1.39.01 ) 三个源代码

在 uC-OS3 文件夹中

| 名程     | 描述                                     |
| -------- | ---------------------------------------- |
| Cfg      | 包含 uC/OSⅢ 配置文件的模板文件           |
| Ports    | 存放接口文件，是 CPU 与 uCOS3 的连接桥梁 |
| Source   | 是 uCOS3 的源代码，也是核心文件          |
| Template | 与动态Tick管理相关的文件                 |

uC-CPU 文件夹中，主要包含了与 ARM Cortex-M 内核的 CPU 相关的移植文件，如前导置零指令，大小端模式设置等。（只需要看 ARM-Cortex-M 文件夹）

uC-LIB 文件夹中，包含官方提供的 ASCII 字符操作，数字，内存管理，字符串操作的库。 只是方便开发，不是必需的。

2. 移植

   - 添加 UCOS Ⅲ 源码，将 UCOS3 源码添加至基础工程，头文件路径等

   - 修改 SYSTEM 文件，即修改其中的 sys.c，delay.c， usart.c

   - 修改中断相关文件，即修改 Systick 中断，PendSV 中断

   - 添加四个头文件，即  cpu_cfg.h,  lib_cfg.h,  os_cfg.h,  cfg_app.h

     

3. 添加应用程序，以验证移植是否成功











# FIRE



| 模型       | 事件响应 | 事件处理 | 特性                       |
| ---------- | -------- | -------- | -------------------------- |
| 轮询系统   | 主程序   | 主程序   | 轮询响应事件，轮询处理事件 |
| 前后台系统 | 中断     | 主程序   | 实时响应事件，轮询处理事件 |
| 多任务系统 | 中断     | 任务     | 实时响应事件，实时处理事件 |

操作系统，只占用很小的 Flash 和 RAM

裸机中，主栈 SP，在启动文件中配置，局部变量存储在该栈中

任务栈，每个任务都有自己独立的栈空间（本质是内存RAM），且大小可以自己定义

```c
static CPU_STK TASK01Stk[TASK01_STK_SIZE];
```

任务是一个独立的函数，无限循环并且不能返回。

CPU 寄存器：

PC 寄存器，指向正在指向的指令

LR 寄存器，指向函数返回

R0 寄存器，形参







------

任务时间片运行

SysTick 是 ARM 内核定时器，内嵌在 NVIC 中，是一个 24 位的递减计数器，每次计一个数的时间为 1/SYSCLK，当重装载数值寄存器的值递减到 0 时，系统定时器就产生一次中断。



TROS 需要一个时基来驱动，系统调度的频率等于该时基的频率。

------

阻塞延时和空闲任务

RTOS 中的延时叫阻塞延时，即任务需要延时的时候，任务会放弃 CPU 使用权，CPU 可以去执行其他任务，当延时时间到，重新获得 CPU 使用权，任务继续运行，这样子充分利用了 CPU 资源。

空闲任务在 os_cfg_app.c 创建， os_core.c中定义 OS_IdleTaskInit()，初始化在 OSInit() 中完成，即系统在启动之前空闲任务就已经创建号。



------

时间戳，实际上是一个时间点。使用的是 ARM Cortex-M 系统内核中的 DWT 外设（M3,M4,M7才有）

该外设有一个 32 位寄存器叫 CYCCNT，向上计数器，记录的是内核时钟 HCLK （主频，T = 1/72M = 14ns）运行的个数，当溢出之后，会清零并重新开始向上计数。精度很高，2^32 * 14ns = 60s，即最多计时是 60s

在 cpu_core.c 中的 CPU_Init() 函数中：1 初始化时间戳；2 初始化中断失能时间测量；3初始化 CPU 名字



------

临界段代码，也称作临界域，是一段不可分割的代码，即一段在执行的时候不可被中断的代码。UCOS中包含了很多临界段代码。

- 如果临界段代码可能被中断打断，那么就需要关中断以保护临界段。通过操作 PRIMASK 寄存器（CPSID I 指令关中断 CPSIE I 指令开中断）

- 如果临界段代码可能被任务级代码打断，那么需要锁定调度器以保护临界段代码。

- 对临界段代码的保护本质上还是对总断的开和关的控制

OS_CRITICAL_ENTER()    进入临界段，关闭中断（本质还是调用 CPU_SR_Sava ），在 cpu.h 中宏定义

OS_CRITICAL_EXIT()		退出临界段，开启中断（本质还是调用 CPU_SR_Restore ）

OS_CRITICAL_EXIT_NO_SCHED() 	退出临界段，不调度

OS_CRITICAL_ENTER_CPU_EXIT()



开关中断是在 cpu_a_asm 汇编代码中实现，底层操作关中断的函数是 CPU_SR_Sava , 底层的开中断的函数是 CPU_SR_Restore 



------

就绪列表

优先级表：OSPrioTB1[] 一个数组，每个优先级对应的是一个位，当有64个优先级时，数组中有两个元素。当某个优先级的任务就绪，则该位会置1

如，假设有32个优先级，则 优先级为0的任务就绪时，OSPrioTB1[0] 的第 31 位会被置1；同理，优先级2的任务就绪时， 第 29位会被置1

```C
// 置位优先级表中相应位
void OS_PriodInsert(OS_PRIO prio){  // 置1
	OS_PRIO ix = prio/32;  // 计算数值索引
	CPU_DATA bit_nbr= prio & (32-1u);  // 取余，对2的幂次方求余，等价于 x &(2^n -1)
	CPU_DATA bit = 1u;
	bit <<= (32-1u) - bit_nbr;  // 获取优先级在优先级表中对应位的位置
	OSPrioTB1[ix] |= bit;  // 将优先级在优先级表中的位置置 1
}
// 清除优先级表中相应位
void OS_PrioRemove (OS_PRIO prio){  // 置0
	OS_PRIO ix = prio/32;  // 计算数值索引
	CPU_DATA bit_nbr= prio & (32-1u);  // 取余，对2的幂次方求余，等价于 x &(2^n -1)
	CPU_DATA bit = 1u;
	bit <<= (32-1u) - bit_nbr;  // 获取优先级在优先级表中对应位的位置
	OSPrioTB1[ix] &= ~bit;  // 将优先级在优先级表中的位置置 0
}
// 获取最高的优先级
OS_PRIO OS_PrioGetHighest (void){
    OS_PRIO prio = (OS_PRIO)0;
    CPU_DATA *p_tb1 = &OSPrioTB1[0];   // 获取优先级表首元素地址
    while (*p_tb1 == (CPU_DATA)0 ){    // 从 OSPrioTB1[0] 开始找数组中第一个不为0的元素
        prio += 32;
        p_tb1 ++;
    }
    prio += (OS_PRIO)CPU_CntLeadZeros(*p_tb1);  // CPU_CntLeadZeros() 函数由汇编实现，从高位开始找1，叫计算前导1
    return (prio);
}
```

就绪列表：OSRdyList[] ，数组大小由宏 OS_CFG_PRIO_MAX 决定，支持多少个优先级，OSRdyList 就有多少元素。任务的优先级与 OSRdyList 的索引一一对应，比如，优先级为 3 的任务的 TCB 会被放在 OSRdyList[3] 中

```C
struct os_rdy_list {
	OS_TCB 			*HeadPtr;
	OS_TCB			*TailPtr;
	OS_OBJ_QTY		NbrEntries;
}
```









# 100ASK

主流架构： ARM 架构

​				  RISC-V架构，开源免费，全新的架构

## 堆和栈

堆就是一段空闲的内存，

```C
char heap_buf[1024];
int pos = 0;
void *my_malloc(int size){
	int pre_pos = pos;
	pos += size;
	return &heap_buf[pre_pos];
}
```

栈，从高地址向低地址生长

SP (Stock Register): 栈寄存器

LR ( Link Register ): 保存返回地址。函数调用时，返回地址存入 LR，进入被调用函数，它会划分自己的栈，把 LR 的内容压入栈中，同时函数的局部变量也保存在栈中 。



C 的 main 函数之前：

1. 划分栈 （LR，... 局部）
2. LR 存入栈
3. 执行 main()

```c
Reset_Handler	PROC
				IMPORT   main
				LDR		 SP, = 0X20000000+0X100
				BL		 main
				ENDP
				END
```





## 任务

xTaskCreate

```C
BaseType_t xTaskCreate(    TaskFunction_t pvTaskCode,
                            const char * const pcName,
                            configSTACK_DEPTH_TYPE usStackDepth,
                            void *pvParameters,
                            UBaseType_t uxPriority,
                            TaskHandle_t *pxCreatedTask
                          );
```



FreeRTOS 编程规范

定义了两个数据类型

TickType_t

BaseType_t

![img](file:///D:\File\qq\492298100\Image\C2C\9UR1H]Q2E7D~6}7WX5{]UXC.png)

![QQ图片20230804153028](D:\Administrator\Pictures\forTypora\QQ图片20230804153028.jpg)

变量名：

![QQ图片20230804153054](D:\Administrator\Pictures\forTypora\QQ图片20230804153054.jpg)

函数：

![QQ图片20230804153540](D:\Administrator\Pictures\forTypora\QQ图片20230804153540.jpg)

prv 即 static 函数

宏

![QQ图片20230804153536](D:\Administrator\Pictures\forTypora\QQ图片20230804153536.png)

## 静态创建任务

```C
TaskHandle_t xTaskCreateStatic( TaskFunction_t pxTaskCode,
                                 const char * const pcName,
                                 const uint32_t ulStackDepth,
                                 void * const pvParameters,
                                 UBaseType_t uxPriority,
                                 StackType_t * const puxStackBuffer,
                                 StaticTask_t * const pxTaskBuffer );
```

uxPriority 值越大，优先级越高。相同优先级的任务，以时间片方式运行

删除任务：

```C
void vTaskDelete( TaskHandle_t xTask );

// vTaskDelete(NULL);  删除自身任务
```

## 任务管理

![QQ图片20230804174410](D:\Administrator\Pictures\forTypora\QQ图片20230804174410.jpg)

任务切换的基础：SysTick 中断，基时间默认是 1ms，而 UCOS 中是 10us

running

ready

blocked

suspended

```C
TickType_t xTaskGetTickCount(void);  // 返回时间 ms
```

两个延时函数的区别

```c
void vTaskDelay(const TickType_t xTicksToDelay);  // 延时 n 个 SysTick

BaseType_t xTaskDelayUntil (TickType_t *const pxPerviousWakeTime, const TickType_t xTimeIncrement);
/*  让程序周期性地运行
直到 *pxPerviousWakeTime + xTimeIncrement
*pxPerviousWakeTime = *pxPerviousWakeTime + xTimeIncrement

如：
TickType_t tStart = xTaskGetTickCount();
while(1){
	// de something
	vTaskDelayUntil(tStart,20);  // 实现 something 周期运行，20ms 
}

*/
```



## 空闲任务及其钩子函数

空闲任务只能处于 running 和 ready

空闲任务钩子函数

- 执行一些低优先级的，后台的，需要连续执行的函数
- 测量系统的空闲时间：空闲任务能被执行就意味着所有的高优先级任务都停止了，所以测量空闲任务占据时间，就可以算出处理器占有率
- 绝对不能使钩子函数进入 blocked，suspended 状态
- 如果你会使用 vTaskDelete() 来删除任务，那么钩子函数要非常高效地执行。如果空闲任务移植卡在钩子函数里的话，它无法释放内存。











## 任务调度算法

就绪态的任务，可以被调度器挑选出来切换为运行状态，调度器永远是挑选最高优先级的就绪态任务并让它进入运行状态。

阻塞状态 blocked 的任务，在等待“事件”，当事件发生时任务就会进入就绪状态

事件又分为两类

- 时间相关的事件

  设置超时时间，在指定的时间内阻塞，时间到了就进入就绪态

  使用时间相关的事件，可以实现周期性工作的功能，可以实现超时功能

- 同步事件

  即某个任务在等待某些信息，别的任务或者中断服务程序会给它发送信息

  发送信息的方法

  1. 任务通知  task notification
  2. 队列     queue
  3. 事件组  event group
  4. 信号量  semaphoe
  5. 互斥量  mutex

  这些方法用来发送同步信息，比如表示某个外设得到了数据。

在 FreeRTOSConfig.h 中的宏 configUSE_PREEMPTION 使能则允许任务抢占，否则不允许抢占

宏 configUSE_TIME_SLICING 使能则相同优先级任务以时间片轮转，否则不轮转









## 多任务通信





***

### 互斥

消息队列 queue

```C
/*****     通过关中断来实现互斥     ******/
//  原理：调度器是通过 SysTick 中断进行任务调度的，关闭了中断，则调度器不起作用。关了中断后相当于裸机了
QueueSend(){
    /*  关闭中断  */
    // 写数据
    /*  开启中断  */
}  
```

核心：关中断，链表操作（哪个任务在等待消息队列），环形缓冲区存放数据（ w = (w+1)%LEN ）

```C
QueueHandle_t xQueueCreate( UBaseType_t	uxQueueLength,
                           	UBaseType_t	uxItemSize
);

/*
读消息队列，1）关闭中断；
		  2）有数据，则下一步；无数据，a)返回 ERROR（不设置阻塞等待的话）；b）把自己放入队列中 xTasksWaitingToReceive[]（设置阻塞等待时），把任务从 			  RedyList[] 中移到 DelayList[]; 
		  3）读取数据，唤醒任务（从队列 xTasksWaitingToSend[] 中） 
		  			 a）唤醒任务具体操作，把任务从 xTasksWaitingToSend[] 中第一个任务移除
		  			 b）把它从 DelayList[] 放入 ReadyList[]
*/
// 备注，真正的休眠是 把任务从 RedyList[] 中移到 DelayList[]
BaseType_t xQueueReceive ( QueueHandle_t xQueue,     // 要接收的消息队列的句柄
                           void *pvBuffer,           // 指针，指向缓冲区，接收到的数据将放在该缓冲区
                           TickType_t	xTicksToWait // 如果 INCLUDE_vTaskSuspend 置1，则将阻塞时间指定为 portMAX_DALAY会导致无限期地等待
);


/*
写消息队列，1）关闭中断；
		  2）有空间，则下一步；无空间，a)返回 ERROR（不设置阻塞等待的话）；b）把自己放入队列中 xTasksWaitingToSend[]（设置阻塞等待时），把任务从 				 RedyList[] 中移到 DelayList[]； 
		  3）写数据，唤醒任务（从队列 xTasksWaitingToReceive[] 中） 
		  			 a）唤醒任务具体操作，把任务从 xTasksWaitingToReceive[] 中第一个任务移除
		  			 b）把它从 DelayList[] 放入 ReadyList[]
*/
// 备注，真正的休眠是 把任务从 RedyList[] 中移到 DelayedList[]
BaseType_t xQueueSend ( QueueHandle_t xQueue,     // 队列的句柄，数据将发布到此队列
                        const void *pvBuffer,     // 指针，指向要写入队列的数据
                        TickType_t	xTicksToWait  // 如果 INCLUDE_vTaskSuspend 置1，则将阻塞时间指定为 portMAX_DALAY会导致无限期地等待
);
```

 xTasksWaitingToSend[] 与 xTasksWaitingToSend[] 是两个链表3

比如，ReadyList，它有 ReadyList[0]  存放优先级为 0 的任务

​									ReadyList[1]   存放优先级为 1 的任务

​									ReadyList[2]   存放优先级为 2 的任务		 

 		DelayedList，任务x1（timeout = 100）, 任务x2（timeout = 300）, 任务x2（timeout = 300）

SysTick 每次 1ms 中断中，计数值加 1，判断 DelayedList 中是否有超时的任务，有的话就移动到 ReadyList 中。





***

### 信号量 semaphore

从代码上来看，是一种特殊的队列，因为核心代码还是消息队列的

核心：int count 信号量，列表

```C
SemaphoreHandle_t xSemaphoreCreateBinary (void);  // 创建一个二进制信号量

xSemaphoreGive( SemaphoreHandle_t xSemapore);  // 用于释放信号量的宏
xSemaphoreTake( SemaphoreHandle_t xSemapore    // 用于获取信号量的宏
				TickType_t xTicksToWait
);
// 宏分别如下
#define xSemaphoreCreateBinary()    xQueueGenericCreate( ( UBaseType_t ) 1, semSEMAPHORE_QUEUE_ITEM_LENGTH, queueQUEUE_TYPE_BINARY_SEMAPHORE )

#define xSemaphoreGive( xSemaphore )    xQueueGenericSend( ( QueueHandle_t ) ( xSemaphore ), NULL, semGIVE_BLOCK_TIME, queueSEND_TO_BACK )

#define xSemaphoreTake( xSemaphore, xBlockTime )    xQueueSemaphoreTake( ( xSemaphore ), ( xBlockTime ) )
```



***

#### 互斥信号量 mutex

信号量分为计数型，二进制型（只有0，1）

==互斥信号量相当于二进制信号量，加上优先级继承机制==

优先级反转：低优先级任务使用了信号量，一直在运行，而此时高优先级等待该信号量，一直等，直到那个任务退出释放信号量。

解决方式：使用 mutex semaphore。

​				==低优先级任务在使用mutex信号量，==

​				==CASE 1: 此时高优先级等待该mutex信号量，则把正在使用mutex的低优先级任务的优先级提高，它退出时释放信号量，把优先级恢复低，同时唤									 醒等 mutex信号量的高优先级。  -----  优先级继承  -----==

​				==CASE 2: 若此时更低优先级等待该mutex信号量，则不会发生任务优先级的变化。==

***

### 事件组 event group

协作，用位来标志事件是否发生

```
EventGroupHandle_t xEventGroupCreate(void)  // 创建事件组
```

EventGroupDef_t 结构体有一个 list，

```c
List_t xTasksWaitingForBits;
```

```C
// 等待位
EventBits_t xEventGroupWaitBits(
                       const EventGroupHandle_t xEventGroup,  // 要等待位的事件组
                       const EventBits_t uxBitsToWaitFor,     // 要等待的位，如：0x05，则等待位0和位2；0x07则等待位0，1，2
                       const BaseType_t xClearOnExit,    // 若 xClearOnExit 设为 pdTURE 则使用后会把要等待的位清除掉；设为 pdFALSE则不清除
                       const BaseType_t xWaitForAllBits, //若xWaitForAllBits设为pdTURE则是等待uxBitsToWaitFor中的所有位；否则等待任意一位
                       TickType_t xTicksToWait );        // 等待时间
/*
const BaseType_t xClearOnExit,   // 若 xClearOnExit 设为 pdTURE 则使用后会把要等待的位清除掉；xClearOnExit设为 pdFALSE则不清除
const BaseType_t xWaitForAllBits, // 若 xWaitForAllBits 设为 pdTURE 则是等待uxBitsToWaitFor中的所有位；xWaitForAllBits pdFALSE则是等待uxBitsToWaitFor中的任意一位
*/

// 设置位
 EventBits_t xEventGroupSetBits( EventGroupHandle_t xEventGroup,  // 要设置位的事件组
                                 const EventBits_t uxBitsToSet ); // 设置一位或多个位
// 如： uxBitsToSet 设为 0x08，则设置位3；0x09，则设置位3和位0
```

不会在中断中使用事件组，所以等待位时只需要关闭调度器（ vTaskSuspendAll() ），而不用关闭中断（ vTaskEnterCtitical() ）,信号量的写需要都关闭。

xEventGroupSetBitsFromISP()  // 在中断服务程序（ISP）调用的 xEventGroupSetBits() 版本

但是在中断服务程序中并不会 SetBits，而是触发/唤醒“守护任务”，由它去 SetBits（相当于任务级）。

要是在断服务程序中 SetBits，会唤醒其他所有等待该事件组的任务，所以中断会变长，这不合法。





### 任务通知 TaskNotify

每个任务 TCB 都有一组任务通知，每个通知都包含状态（ucNotifyState）和一个32位值（ulNotifiedValue），任务通知时直接发送至该任务的事件，

```C
// 发送任务通知，将事件直接发送到 RTOS 任务并可取消该任务的阻塞状态
BaseType_t xTaskNotify( TaskHandle_t xTaskToNotify,  // 要通知的任务，即目标任务
                         uint32_t ulValue,           // 用于更新目标任务的通知值
                         eNotifyAction eAction );    // 

 BaseType_t xTaskNotifyIndexed( TaskHandle_t xTaskToNotify, 
                                UBaseType_t uxIndexToNotify, 
                                uint32_t ulValue, 
                                eNotifyAction eAction );
// 任务等待
uint32_t ulTaskNotifyTake( BaseType_t xClearCountOnExit,
                            TickType_t xTicksToWait );
 
 uint32_t ulTaskNotifyTakeIndexed( UBaseType_t uxIndexToWaitOn, 
                                   BaseType_t xClearCountOnExit, 
                                   TickType_t xTicksToWait );
/*
ulTaskNotifyTake() 是一个宏，在任务通知 被用作更快、更轻的二进制 或计数信号量替代时使用。 使用 xSemaphoreTake() API 函数获取 FreeRTOS 信号量，ulTaskNotifyTake() 是使用通知值代替信号量的等效宏。

*/
```

发送任务通知流程

1. 关闭中断
2. 写 ulNotifiedValue，唤醒等待任务通知的任务
3. 开中断

等待任务通知：

1. 关闭中断
2. 判断状态，三种状态（）

```C
#define taskNOT_WAITING_NOTIFICATION       ( ( uint8_t ) 0 ) /* Must be zero as it is the initialised value. */
#define taskWAITING_NOTIFICATION           ( ( uint8_t ) 1 )
#define taskNOTIFICATION_RECEIVED          ( ( uint8_t ) 2 )
```

3. 开中断，或休眠





















## CPU 寄存器相关

CM3 采用了哈佛结构，拥有独立的指令总线和数据总线，可以让取指与数据访问

并行不悖。这样一来数据访问不再占用指令总线，从而提升了性能。为实现这个特性， CM3 内部

含有好几条总线接口，每条都为自己的应用场合优化过，并且它们可以并行工作。但是另一方面，

指令总线和数据总线共享同一个存储器空间（一个统一的存储器系统）。

![image-20230805090757943](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230805090757943.png)

- ==R0-R12 通用寄存器==

  都是32位寄存器，用于数据操作，复位后的初始值不可预料。。其中R0-R7低组寄存器，所有指令都能访问它们。

  R8-R12高组寄存器，少数16位的 Thumb 指令可以访问，32位指令则不受限制。

- ==Banked R13 寄存器两个堆栈指针==

  分别是 MSP 和 PSP，它们是 banked，因此任意时刻只能使用其中一个

  MSP 主栈指针：复位后缺省使用的堆栈指针，用于操作系统内核以及异常处理例程（包括中断服务例程），由OS内核，异常服务例程所有需要特权访问的应用程序代码来使用

  PSP 进程堆栈指针：由用户的常规应用程序代码使用（不处于异常服务例程中时）

  堆栈指针的最低两位永远是0，这意味着堆栈总是4字节对齐的

  简单的应用程序只使用 MSP就够了，推着指针用于访问堆栈，并且 PUSH 和 POP 指令默认使用 SP.

- ==R14 LR(link register)==

  由R14存储返回地址

- ==R15 程序计数寄存器==

  指向当前的程序地址，如果修改它的值，就能改变程序的执行流。

- ==特殊功能寄存器==

  它们只能被专用的 MSR 和 MRS 指令访问，并且它们没有存储地址。

  1. ==PSRs：程序状态字寄存器组==

     内部又分为三个子状态寄存器

     中断程序 APSR

     中断号 IPSR

     执行 EPSR

     它们既可以单独访问，也可以组合访问。三合一访问时，使用名字 PSR 或 xPSR

     ![image-20230805093931076](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230805093931076.png)

  2. ==PRIMASK,FAULTMASK,BASEPRI 中断屏蔽寄存器组==

     用于控制异常的使能和失能。

     PRIMASK 一位的寄存器，置1，关掉所有可屏蔽的异常，只剩下 NMI和硬fault可以响应。默认0不关中断。

     FAULTMASK 一位的寄存器，置1，只有 NMI才能响应。默认0不关异常。

     BASEPRI 最多有9位（由表达优先级的数来决定），定义了被屏蔽优先级的阈值。设为某个值后，所有优先级号大于等于该值的中断都被关闭（优先级号越大，优先级越低）。默认为0，不关闭任何总断。

  3. ==CONTROL 控制寄存器==

     用于定义特权级别，还用于选择当前使用 MSP还是 PSP

     CONTROL[1]: 置0，则选择 MSP（复位后缺省值），置1选择PSP。（在 handler 模式中，总是0，在线程模式中可以为0或1）

     CONTROL[0]: 置0，特权级的线程模式；置1，用户级的线程模式。仅在特权级下操作时才允许写该位。一旦进入了用户级，唯一写该位的方式就是触发一个软中断，在服务例程里写该位。

  ![image-20230805091603096](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230805091603096.png)

  ![image-20230805091638473](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230805091638473.png)

用到的汇编指令

1. LDR  R0, [addr]    ; 读数据，并把数据存在 R0

   ​	   LDRH  读 2 bytes

   ​		LDRB  读 1 bytes

2. STR R0, [addr]      ; 写数据，将R0的数据存到 addr 地址中

3. ADD R0,R1,R2      ; R0 = R1 + R2

4. PUSH {R3, LR}      ; 把 R3, LR 的值写入内存（位置由SP指向的栈位置决定） // { } 内数据无顺序，低标号的寄存器保存在低地址（即先压入LR（靠近栈顶一点，是高地址）） 

   ​								PUSH LR;

   ​								PUSH R3;

5. POP {R3, PC}        ; 本质读指令，从内存中(SP位置)读出数据，赋给 R3, PC

   ​								R3 = [SP]  ; 取出SP 指向的数据，赋给 R3

   ​								SP -=4    ; SP 指针指向栈上面一个地址 

   ​								PC = [SP]  ; 取出SP 指向的数据，赋给 PC

   ​								栈指针 SP 是向下生长型，一开始指向栈顶，是高地址

6. SUB sp,sp,#0x194    ; sp -= 0x194

7. BX lr                      ; 跳到 lr







入栈的三个场景

- 任务切换：所有16个寄存器的值都要保存

- 函数调用：函数使用 R0,R1,R2来传递参数，因此这三个寄存器不用保存（被调用函数使用 PUSH 保存）

- 硬件中断：对于 M3M4硬件上保存R0,R1,R2 在栈中，软件上保存一些用到的寄存器

































