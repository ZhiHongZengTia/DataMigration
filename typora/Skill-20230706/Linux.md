

# Linux 编程

## 程序，进程，进程 ID 概念

- 程序

  静态概念，源代码，指令，包括安装包

- 进程

  动态概念，运行着的的程序，对内核来说，进程由内核开辟的实体，系统为其分配空间，资源 。每次创建和销毁都消耗大量的资源。

  进程之间相互独立，如迅雷和百度网盘，占用的系统资源是分开的

- 线程

  从属于进程，一个进程可以有多个线程，线程之间共享进程的资源

  线程在创建和销毁所消耗的资源很少，所以多使用线程而不是进程。创建线程要快于创建进程

- 任务

  具体要做的事情

```C
getpid();  // 返回当前线程 ID  类型 pid_t  在 sys/types.h  unistd.h 中
getppid(); // 返回当前线程的父线程 ID
pstree     // Ubuntu 终端查看进程树
sysemd(1)  // 所有进程的父进程
```

### 进程创建函数 fork

```C
pid_t pid = fork();  // 函数返回两个值，当父线程运行时，返回它所创建的子线程的 ID 号；而当子线程运行时，它返回 0（如果进程创建成功的话）
if( pid > 0 )
    printf("now the parent process ID = %d\r\n",getpid());   // 父进程
if( pid == 0)
    printf("now the child process ID = %d\r\n",getpid());    // 父进程;   // 子进程
// 不能通过打印的先后来确定哪个进程先运行，哪个进程后运行

// 父进程与子进程使用独立的内存，所以它们之间的变量互不干扰
```

### 监控子进程 wait

等待任意子进程终止，wait() 函数返回那个结束的子进程的 ID（pid_t类型）。

没有子进程在运行，则返回 -1。

```C
#include <sys/wait.h>
pid_t endPid;
endPid = wait(NULL);
if ( -1 == endPid)
	puts("there is no more child process\n");
```

#### 创建线程函数 pthread_create

线程之间共享进程的资源

```
gcc xxx.c -pthread
```

```C
#include <pthread.h>
int pthread_create(pthread_t *thread, const pthread_attr_t *attr,
				void *(*start_routine)(void *), void *arg );
/*
pthread_t *thread:
指向 pthread_t 类型的指针，输出类型的参数
### 存放的是新线程的 ID 号 ###

const pthread_attr *attr:
结构体指针，指向结构体 pthread_attr_t
### 用来决定新线程的属性attributes，设为 NULL 默认 ###

void *(*start_routine)(void *):
函数指针，指向的函数如 void *function（void *）
### 该函数是新创建出来线程所指向的内容，直接放函数名，因为函数名就是地址 ###

void *arg:
空指针，即可以是任意类型数据的指针
### 给该线程函数传递的参数，设为 NULL 默认###

成功创建线程，则返回 0，否则返回错误信息，使用 perror("pthread_create()") 打印错误信息
*/
```



==线程从属于进程，使用的是进程的资源。如果进程运行结束，则操作系统会回收该进程所有的资源，所以线程也被回收了。==

==与父进程，子进程不同，它们使用不同的资源，相互独立，互不干扰==

#### 监控线程  pthread_join()

```C
int pthread_join(pthread_t thread, void **retval)
/* wait for the thread specified by thread to terminate
pthread_t thread:
等待结束的线程 ID

void *retva:
若不是 NULL, 则会保存线程的退出状态

函数返回 0 ，若调用成功，否则返回错误编号
*/
```

如何给空指针参数传递参数并读取

```C
void func(void *arg){
	int getVal = *( (* int)arg );  // 先将该空指针强制类型转换为 int *，然后再取出值
}
int a;
func(&a);
```

==多个线程共享进程资源，进程中的全局变量在每个线程函数中都可见，且是同一个。==

==这与进程不同，子进程复制父进程的东西再保存在自己独立的内存空间中，两者中的变量相互独立，不可见，即使变量名字相同。==进程间通信要更难。

### 无名管道 unnamed pipe 进程间通信

==任务间通信与同步的方式：管道，信号，信号量，互斥锁，消息队列，共享内存，source 套接字， 7种方式==

- 有名管道 named pipe
- 无名管道 unnamed pipe

```C
#include <unistd.h>
int pipe( int pipefd[2] );
/*  create a pipe
int pipefd[2]:
pipefd[0]: 管道的 读取端     文件描述符  close(pipefd[0]) 关闭读取端
pipefd[1]: 管道的 写入端     文件描述符，写入的数据被缓存在内核中，直到从读取端读取出来

函数返回值：调用成功返回0；否则返回 -1
*/
```

#### 无名管道的容量

写入 65536 字节后则写不下了

无名管道，两条管道实现双向传输，父子进程来回双向通信



#### 有名管道  mkfifo

无名管道只适用于具有父子关系的进程之间通信，

mkfifo() 函数创建一个有名管道，任何进程都可以打开该文件， 进行读，写操作，和普通文件一样的方式进行操作。

```C
#include <sys/types.h>
#include <sys/stat.h>
int mkfifo(const char *pathname, mode_t mode);
/*  函数创建一个 FIFO 特定文件（先入先出特殊文件，也即有名管道），名字为 pathname
const char *pathname
mkfifo("my_fifo",666);
mode_t mode
配置文件的权限
权限就三种：读权限，写权限，可执行

函数返回：成功返回 0； 否则返回 -1
*/
```

写入文件之前先清空  memset(string, '\0', sizeof(string) );

### 共享内存  shmget

先开辟一个空间，然后将该空间映射到不同的进程中（各映射一份），每个进程操作映射的空间，就等同于对原始空间进行操作

1. 开辟共享空间
2. 创建映射
3. 解除映射
4. 删除共享空间

------

四个使用函数如下：

```C
// 开辟共享内存
#include <sys/ipc.h>
#include <sys/shm.h>
int shmget(key_t key, size_t size, int shmflg);
/*  返回一个 System5 共享内存单元的标识符，与 key 关联在一起，
key_t key:
IPC_PRIVATE  则创建一个新的共享内存，大小为 size，向上取整到 PAGE_SIZE（页大小）的倍数
否则，该参数无效，

size_t size:
PAGE_SIZE

int shmflg:
IPC_CREAT：创建一个与 key 相关的共享单元，如果已经有的话，则检查是否有权限
IPC_EXCL: 与 IPC_CREAT 一起使用，以确保创建一个共享内存，当共享内存已经存在的话，则创建失败

函数返回值：若成功，则返回一个有效的共享内存的描述符，否则失败返回 -1
*/

```

```c
// 创建映射  attach
void *shmat(int shmid, const void *shmaddr, int shmflg);
/*  将 由shmid 所标识的 System5版本 的共享内存描述符 关联到（映射） 调用进程的地址空间里
int shmid: 共享内存的标识符

const void *shmaddr：映射到的地址
若为 NULL，则系统选择一个合适的（未使用的）地址，用来关联该地址

int shmflg:
SHM_EXEC 允许可执行权限 （前提是该进程具有对该内存的执行权限）
SHM_RDONLY 只读权限    （前提是该进程具有对该内存的读权限）
0 空着  具有读写权限    （前提是该进程具有对该内存的读写权限）

返回值：成功返回映射的共享内存的地址，否则返回 （void *）-1
*/
```

```C
// 解除映射  detach
void shmdt(const void *shmaddr);
/*  解除绑定
const void *shmaddr：映射到进程中的地址

返回值：成功返回0 ，否则返回 -1
*/

```

```C
// 删除共享内存   待研究
#include <sys/ipc.h>
#include <sys/shm.h>
int shmctl(int shmid, int cmd, struct shmid_ds *buf);
/*  对共享内存的控制
int shmid:

int cmd: 具有以下命令
IPC_STA   
IPC_Set
IPC_RMID 在最后一个进程 detach解除之后，销毁该共享内存
IPC_INFO

struct shmid_ds *buf:
指向 shmid_ds 结构体类型的指针，暂时不用

返回值：成功返回0，否则返回 -1
*/

```



------

使用：

```C
int shimid = shmget(IPC_PRIVATE, 1024, IPC_CREAT);  // 创建一个共享内存，大小 1024 字节
char *addr = shmat(shimid, NULL, 0);
shmdt(addr);
shmctl(shmid,IPC_RMID,NULL);
```

==需要 sudo ./a.out  才能成功==



#### 非亲缘关系进程通过共享内存通信

```C
// 需要使用相同的 key 值
#define MYKEY 9527
int shimid = shmget(MYKEY, 1024, IPC_CREAT);  // 创建一个共享内存，大小 1024 字节
// 区别： 在父子进程中
//int shimid = shmget(IPC_PRIVATE, 1024, IPC_CREAT);  
```

在父子进程中可以使用 IPC_PRIVATE 这个 key 值，因为子进程会复制父进程中的内容

而在非亲缘关系的进程中，要使用相同的 key 值 才能保证



### 消息队列 msgget

1. 创建消息队列

```C
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/msg.h>

int msgget (key_t key, int msgflg);
/*  函数返回 Syetem 5版本消息队列的描述符，该描述符与 key 值相关联
key_t key: 键值（在内核层标识的，在应用层叫 identifier 描述符）
IPC_PRIVATE

int msgflg: 和创建有名管道 shmget 开辟共享内存函数一样
IPC_CREAT：创建一个与 key 相关的共享单元，如果已经有的话，则检查是否有权限
IPC_EXCL: 与 IPC_CREAT 一起使用，以确保创建一个共享内存，当共享内存已经存在的话，则创建失败

函数返回值：若成功则返回消息队列的描述符，否则返回 -1
*/
```

2. 消息队列的发送和接收

发送到消息队列

```C
int msgsnd(int msqid, const void *msgp, size_t msgsz, itn msgflg);
/*
int msqid: 消息队列的描述符，即 msgget() 函数的返回值

const void *msgp: 指针，指向用户自定义的结构体的指针，具有如下形式：
struct msgbuf {
	long mtype;     // 消息类型，必须 > 0 
	char mtext[100];  // 消息数据，这里存放要发送的内容，后续可以添加各种数据，如 int mnumber; 
};

size_t msgsz: 消息大小，结构体中除了 long mtype 外所有数据的大小，如 
struct msgbuf buff;
msgzs = sizeof(buff) - sizeof(buff.mtype)

itn msgflg:

函数返回值：
*/
```

从消息队列接收

```C
ssize_t msgrcv(int msqid, void *msgp, size_t msgsz, long msgtyp, int msgflg );
/*
int msqid: 同上，消息队列的描述符

void *msgp: 同上，接收消息存储的位置

size_t msgsz: 同上，消息的大小

 long msgtyp: 消息的类型
 0 : 读取第一个消息
 >0: 
 <0: 
 
 int msgflg:
 0 默认
 
 函数返回值：
*/


```

3. 删除消息队列



```C
int msgctl(int maqid, int cmd ,struct msqid_ds *buf);
/*
int maqid:

 int cmd:

struct msqid_ds *buf:
IPC_RMID 在最后一个进程 detach解除之后，销毁该消息队列

函数返回值：
*/
```



使用：

```C
int msqid = msgget(IPC_PRIVATE, IPC_CREAT);
buff.mtype = 9527;
msgsnd(msqid, &buff, sizeof(buff)-sizeof(buff.mtype), 0 );   // 发送消息队列
msgrcv(msqid,&buff, sizeof(buff)-sizeof(buff.mtype), buff.mtype, 0);  // 读取
msgctl(msqid, IPC_RMID, NULL);  // 删除消息队列

```

读的时候：

```C
msgrcv(msqid, &bufff, sizeof(buff)-sizeof(buff.mtype), buff.mtype, 0 );
```

==需要 sudo ./a.out==



#### 非亲缘关系进程通过消息队列通信

同非亲缘关系进程通过有名管道通信。

==将 msqid = msgget(IPC_PRIVATE, IPC_CREAT); 中的 IPC_PRIVATE 变成一个相同的键值，在两个不同的进程中==

```C
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/msg.h>

// 发送方
#define MY_KEY 8198
int main(void){
	struct msgbuf{
	long mytype;
	char mtext[100];
	int mnumber;
	} buff;
	int msqid = msgget(MY_KEY,IPC_CREAT);
	while( 1 == scanf("%d",&inputInt) ){
        while(getchar != '\0');
        puts("Please enter a string to the message");
        
        fgets(buff.mtext,100,stdin);
        buff.mnuber = inputInt;
        buff.mtype = 9527;
		msgsnd(msqid,&buff, sizeof(buff)-sizeof(buff.mtype), 0);

		printf("the process %d is sending.\n",getpid());
	}
}

// 接收方
#define MY_KEY 8198
int main(void){
	struct msgbuf{
	long mytype;
	char mtext[100];
	int mnumber;
	} buff;
    buff.mtype = 9527;
	int msqid = msgget(MY_KEY,IPC_CREAT);
	for(;;){
		msgrcv(msqid,&buff, sizeof(buff)-sizeof(buff.mtype), buff.mtype, 0);
		printf("the data from the message = %s %d\n",buff.mtext,buff.mnuber);
		printf("the process %d is waiting.\n",getpid());
	}
}
```



### 无名信号 sem_init

任务之间的同步，即共享资源访问的优先级

信号量用于同步，本质上是一个整数 >=0。

```text
比如，一块共享内存，信号量初始值大于0。
进程 A 希望访问它，首先检查信号量是否大于0，发现确实大于0，则进行访问，同时 信号量-1。
此时，进程 B 也希望访问它，检查信号量是否大于0，发现不是，所以进程 B 进入阻塞状态，一直等待。
当进程 A 结束访问时，信号量 +1，则进程 B 可以进行访问了。
```

两种类型的 POSIX 信号量：

- 命名信号量：这种信号量拥有一个名字。通过使用相同的名字调用 sem_open() ，不相关的进程能够访问同一个信号量
- 未命名信号量：这种信号量没有名字，相反，它位于内存中一个预先商定的位置处。未命名信号量可以在进程之间或一组线程之间共享。但在进程之间共享时，信号量必须位于一个共享内存区域中（System 5，POSIX或mmap() ）。而当在线程之间共享时，信号量可以位于被这些线程共享的一块内存区域中（如在堆上或在一个全局变量中）

```C
#include <semaphore.h>
int sem_init(sem_t *sem, int pshared, unsigned int value);
/*  创建一个无名信号量，并指定该信号量是用于进程间还是线程之间的同步
sem_t *sem:

int pshared:
0 说明信号量用于同一进程的不同线程之间，此时该信号量应该存储在所有进程进程可见的区域，如全局变量
非0 说明信号量用于不同进程之间，此时信号量应该存储在共享的内存区域中，如 mmap(), shmget(), shm_open()

unsigned int value:
信号量初始值

返回值：成功返回0，否则返回 -1
*/
```

检查信号量

```C
int sem_wait(sem_t *sem);
/* 若sem指向的信号量大于0，则该信号量-1；否则，处于阻塞状态
成功返回0，否则返回 -1
*/
```

增加信号量

```
int sem_post(sem_t *sem);
/* 增加 sem 指向的信号量

返回值：成功返回0，否则返回 -1
*/
```

创建共享的内存

```C
#include <sys/mman.h>
void *mmap(void *addr, size_t length, int prot, int flags, int fd, off_t lffset);  // 创建共享映射
/*  在调用进程的虚拟地址空间中创建一个新映射
void *addr: 映射被放置的虚拟地址
NULL 则内核选择一个合适的地址

size_t length: 虚拟内存空间大小，单位字节

int prot: 保护信息
PROT_NONE   区域无法访问
PROT_READ   区域内容可读取
PROT_WRITE	区域内容可修改
PROT_EXEC	区域内容可执行

int flags: 位掩码，指定了施加于映射之上的保护信息
MAP_PRIVATE	创建一个私有映射。区域中内容上所发生的变更对使用同一个映射的其他进程是不可见的。对于文件映射而言，说发生的变更将不会反应在底层文件上。
MAP_SHARED	创建一个共享映射，区域内容上所发生的变更对使用 MAP_SHARED 特性映射同一区域的进程时可见的。对于文件映射而言，说发生的变更会直接反应在底层文件上。

int fd:

off_t lffset:

返回值：成功时返回映射区域的起始地址
*/
```



使用无名信号量，用于父子进程间同步

```C
#include <semaphore.h>
#include <sys/mman.h>
sem_t semID ;
mmap(NULL, sizeof(semID), PROT_READ|PROT_WRITE, MAP_SHARED|MAP_ANONYMOUS, -1, 0);  // 创建共享映射
// 使用匿名映射，后面两个参数没用
int sem_init(sem, 1, 2);  // 创建一个信号量，用于进程间同步，初始值为 2
```

编译时候 gcc xxx.c -pthread







#### 命名信号量  sem_open

```
#include <fcntl.h>
#include <sys/stat.h>
#include <semaphore.h>
sem_t *sem_open(const char *name, int oflag, mode_t mode, unsigned int value);
/*  初始化并打开一个命名信号量
const char *name: 名字

int oflag: 控制调用操作
O_CREAT  信号量不存在则创建, 若设置为 O_CREAT，则后面两个参数必须有 

mode_t mode: 配置许可权限 0666 所有人具有读写权限

unsigned int value: 信号量初始值

返回值：若成功则返回创建的信号量的地址，否则返回 SEM_FAILED
*/
```

关闭信号量

```C
int sem_close(sem_t *sem);  // 关闭所指向的信号量
int sem_unlink(const char *name)  // 删除一个命名信号量
```

使用命名信号量实现 非亲缘关系的进程之间通信



#### 线程间信号量同步 

```C
#include <semaphore.h>
#include <sys/mman.h>
#include 
sem_t *semID ;
int sem_init(semID, 0, 2);  // 创建一个信号量，用于同一进程的不同线程之间，初始值为 2
```

使用无名信号量使用同一进程的不同线程之间通信

```C
#include <pthread.h>
#include <sys/types.h>
#include <sys/ipc.h>
#include <semaphore.h>
sem_t semID;
int main(void){
    int cntThread02 = 5;
    pthread_t threadID01;
	pthread_t threadID02;

	pthread_create(&threadID01,NULL,threadFunc01,NULL);
	pthread_create(&threadID02,NULL,threadFunc02,&cntThread02);
    
    sem_init(&semID,0,0);
    for(;;);
    return 0;
}
void *threadFunc01(void *arg){
    for(;;){
        sem_wait(&semID);
        puts("the thread runs well.");
    }
}
void *threadFunc02(void *arg){
    for(int i =0; i<*( (int *)arg ); i++){
        sem_post(&semID);
        puts("the thread increases the semaphore.");
        sleep(2);
    }
}

```



### 互斥锁 mutex

==常常用于线程之间，用于任务之间同步，类似于只有加锁和解锁两种操作的信号量==

互斥锁用于保护共享数据，（一个线程在访问的时候，不允许其他线程访问，即互斥锁不能同时被两个线程占用）

==有两种状态，加锁和解锁状态（加锁理解为正在访问，解锁理解为停止访问），加锁时候他就属于某一个线程。一个线程尝试去给已经是加锁状态的互斥锁上锁时，该线程会被挂起，直到互斥锁被拥有它的线程解锁之后。==

互斥锁初始化：

```C
#include <pthread.h>
int pthread_mutex_init(pthread_mutex_t *mutex, const pthread_mutex_attr_t *mutexattr);
/* 初始化 mutex 指向的互斥锁
pthread_mutex_t *mutex:

const pthread_mutex_attr_t *mutexattr:
NULL 则使用默认配置

返回值: 只会返回 0
*/
```

其他函数

```
int pthread_mutex_lock(pthread_mutex_t *mutex);		// 加锁
int pthread_mutex_trylock(pthread_mutex_t *mutex);	// 尝试加锁
int pthread_mutex_unlock(pthread_mutex_t *mutex);   // 解锁
int pthread_mutex_destroy(pthread_mutex_t *mutex);  // 销毁互斥锁
返回值：成功返回 0，否则非 0 
```



使用

```C
#include <pthread.h>
pthread_mutex_t mutex;
pthread_mutex_init( &mutex, NULL);  // 创建一个互斥锁
pthread_mutex_lock(&mutex);    // 加锁
pthread_mutex_unlock(&mutex);  // 解锁，访问完之后

```



### 信号 kill  signal

!= 信号量

信号是指  内核和应用进程之间  以及  应用进程与应用进程之间  传递的控制命令。

如 Ubuntu 终端使用 ctrl+C 就是信号 SIGINT

kill -l 列出 Ubuntu 系统下所有信号

```C
#include <sys/types.h>
#include <signal.h>
int kill(pid_t pid, int sig);
/*  向一个进程发送信号
pid_t pid: 进程ID
>0 发送到该进程
0 则发送信号到所有进程（属于同一进程组）
-1 则发送信号到所有进程（只要许可的）

int sig:
0 不发任何信号
SIGKILL 终止进程

返回值: 成功返回0，否则返回 -1
*/

```

使用

```C
#include <sys/types.h>
#include <signal.h>
#include <unistd.h>  // sleep
#include <stdlib.h>  // NULL exit
#include <stdio.h>
int mian(void){
	pid_t pid = fork;
	if(pid > 0){
		puts(the parent process is running);
		sleep(3);
		kill(pid,SIGKILL);
	}
	else if(0 == pid){
		for (int i = 0; ;i++)
			printf("cnt %d the child process is running\n",i)
	}
	
}
```

------

==进程接收到信号，也可以选择执行某个特定函数，类似于中断处理函数==

然而 SIGKILL，SIGQUIT 信号的处理函数不可修改，修改了也没用

```C
#include <signal.h>
typedef void (*sighandler_t)(int);  // sighandler 函数指针，指向的函数，返回值 void，具有一个 int 类型参数 
sighandler_t signal(int signum, sighandler_t handler);
/*
int signum: 信号

sighandler_t handler: 信号处理函数

返回值：

*/
```

使用

```c
signal(SIGKILL,func);
signal(SIGINT,func);
for(int i =0; ;i++){
	printf("count %d the process id = %d",i,getpid());
}
void func(int signum){
	if(SIGKILL == signum){
		puts("SIGKILL encounters")
		exit(1);
	}
	else if(SIGINT == signum){  // SIGKILL 不可修改
		puts("SIGINT encounters")
		exit(1);
	}

}
```





```C
#include <stdlib.h>
int main(int argc,char *argv[])
atoi(argv[1])  // 将传进来的字符串转换为 整形数据
```



### Linux 网络编程简介

即 Socket 套接字编程，即该标准协议在 Linux 下实现的 api

TCP/IP 协议，相当于标准，规则

| OSI 层     | 功能                                 |
| ---------- | ------------------------------------ |
| 应用层     | 文件传输，电子邮件，虚拟终端等       |
| 表示层     | 数据格式化，代码转换，数据加密       |
| 会话层     | 解除或建立与别的接点的联系           |
| 传输层     | 提供端对端的通信接口  (TCP)          |
| 网络层     | 为数据包选择路由  (IP)               |
| 数据链路层 | 传输有效地址的帧以及错误描绘功能     |
| 物理层     | 以二进制数据形式在物理媒体上传输数据 |

![image-20230729164929880](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230729164929880.png)



![image-20230729185344956](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230729185344956.png)

![image-20230729185425205](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230729185425205.png)

TCP 面向连接的，需要三次握手，安全可靠

UDP 不是面向连接的，不安全可靠，传输效率高，适合吞吐量大的场景，如视频等



专注于应用层开发，使用应用层套接字函数

![image-20230729165009228](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230729165009228.png)

#### TCP 服务器端编程

1. socket()

```C
#include <sys/types.h>
#include <sys/socket.h>
int socket(int domin, int type, int protocol);
/*  创建一个用于通信的套接字，并且返回该套接字的描述符
int domin： 配置通信域，选择哪种协议
AF_UNIX,AF_LOCAL
AF_INET		实际为 ipv4
AF_INET6	实际为 ipv6
AF_IPX
AF_NETLINK
AF_X25
AF_AX25
AF_ATMPVC
AF_APPLETALK
AF_PACKET
AF_ALG

int type：使用的套接字类型
SOCK_STREAM		常用 流式套接字   TCP
SOCK_DGRAM		常用 数据包套接字  UTP
SOCK_SEQPACKET
SOCK_RAM
SOCK_ROM

int protocol：协议
0 默认

返回值：若成功，返回一个新的套接字的描述符，否则返回 -1
*/
```

2. bind 绑定一个地址到套接字

socket 创建一个套接字后，它存在于一个名字空间，但是没有地址。bind 指定了一个地址 addr 给一个套接字，该套接字的文件描述符为 dockfd

```C
int bind(int dockfd, const struct sockaddr *addr, socklen addrlen);
/*
int dockfd: 

const struct sockaddr *addr:

socklen addrlen: addr 的大小

返回值:
*/
const struct sockaddr{
	sa_family_t sa_family;
	char sa_data[14];
}

```



3. listen 监听

```C
int listen(int sockfd, int backlog);
/*
int sockfd:

int backlog: 用于挂起连接套接字的队列的最大长度

返回值: 成功返回 0 ，否则 -1
*/
```

4. accept

```
int accept(int sockfd, struct aockaddr *addr, socklen_t *addrlen);
/*
int sockfd: 

struct aockaddr *addr:

socklen_t *addrlen:

*/

```

使用

```C
#define PORT_ID 8800
struct sockaddr_in myaddr;

int sockfd = socket(AF_INET, SOCK_STREAM,0);

myaddr.sin_family = AF_INET;
myaddr.sin_port = htons(PORT_ID);
myaddr.sin_addr.s_addr = INADDR_ANY;
bind(dockfd,(struct sockaddr *)&my_addr);

listen(sockfd,10);

```



#### TCP 客户端编程





#### UDP 服务器端编程





#### UDP 客户端编程





#### 并发服务器模型





















































## 











































































































































