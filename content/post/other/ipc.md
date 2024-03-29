---
title: IPC, 进程间通信IPC (InterProcess Communication)
author: "-"
date: 2012-02-28T13:12:53+00:00
url: ipc
categories:
  - Linux
tags:
  - reprint
---
## IPC, 进程间通信IPC (InterProcess Communication)

每个进程各自有不同的用户地址空间，任何一个进程的全局变量在另一个进程中都看不到，所以进程之间要交换数据必须通过内核，在内核中开辟一块缓冲区，进程1把数据从用户空间拷到内核缓冲区，进程2再从内核缓冲区把数据读走，内核提供的这种机制称为进程间通信 (IPC，InterProcess Communication)
进程间通信模型

### 进程间通信的7种方式

- 匿名管道
- 命名管道
- 消息(Message)队列
- 共享内存 (share memory)
- 信号量(semaphore)
- socket (socket)

### 管道/匿名管道(pipe)

管道是半双工的，数据只能向一个方向流动；需要双方通信时，需要建立起两个管道。
只能用于父子进程或者兄弟进程之间(具有亲缘关系的进程);
单独构成一种独立的文件系统: 管道对于管道两端的进程而言，就是一个文件，但它不是普通的文件，它不属于某种文件系统，而是自立门户，单独构成一种文件系统，并且只存在与内存中。
数据的读出和写入: 一个进程向管道中写的内容被管道另一端的进程读出。写入的内容每次都添加在管道缓冲区的末尾，并且每次都是从缓冲区的头部读出数据。

进程间管道通信模型
管道的实质:
管道的实质是一个内核缓冲区，进程以先进先出的方式从缓冲区存取数据，管道一端的进程顺序的将数据写入缓冲区，另一端的进程则顺序的读出数据。
该缓冲区可以看做是一个循环队列，读和写的位置都是自动增长的，不能随意改变，一个数据只能被读一次，读出来以后在缓冲区就不复存在了。
当缓冲区读空或者写满时，有一定的规则控制相应的读进程或者写进程进入等待队列，当空的缓冲区有新数据写入或者满的缓冲区有数据读出来时，就唤醒等待队列中的进程继续读写。

管道的局限:
管道的主要局限性正体现在它的特点上:

只支持单向数据流；
只能用于具有亲缘关系的进程之间；
没有名字；
管道的缓冲区是有限的 (管道制存在于内存中，在管道创建时，为缓冲区分配一个页面大小) ；
管道所传送的是无格式字节流，这就要求管道的读出方和写入方必须事先约定好数据的格式，比如多少字节算作一个消息 (或命令、或记录) 等等；

### 命名管道(named pipe)

匿名管道，由于没有名字，只能用于亲缘关系的进程间通信。为了克服这个缺点，提出了有名管道(FIFO)。
有名管道不同于匿名管道之处在于它提供了一个路径名与之关联，以有名管道的文件形式存在于文件系统中，这样，即使与有名管道的创建进程不存在亲缘关系的进程，只要可以访问该路径，就能够彼此通过有名管道相互通信，因此，通过有名管道不相关的进程也能交换数据。值的注意的是，有名管道严格遵循先进先出(first in first out),对匿名管道及有名管道的读总是从开始处返回数据，对它们的写则把数据添加到末尾。它们不支持诸如lseek()等文件定位操作。有名管道的名字存在于文件系统中，内容存放在内存中。

匿名管道和有名管道总结:
 (1) 管道是特殊类型的文件，在满足先入先出的原则条件下可以进行读写，但不能进行定位读写。
 (2) 匿名管道是单向的，只能在有亲缘关系的进程间通信；有名管道以磁盘文件的方式存在，可以实现本机任意两个进程通信。
 (3) 无名管道阻塞问题: 无名管道无需显示打开，创建时直接返回文件描述符，在读写时需要确定对方的存在，否则将退出。如果当前进程向无名管道的一端写数据，必须确定另一端有某一进程。如果写入无名管道的数据超过其最大值，写操作将阻塞，如果管道中没有数据，读操作将阻塞，如果管道发现另一端断开，将自动退出。
 (4) 有名管道阻塞问题: 有名管道在打开时需要确实对方的存在，否则将阻塞。即以读方式打开某管道，在此之前必须一个进程以写方式打开管道，否则阻塞。此外，可以以读写 (O_RDWR) 模式打开有名管道，即当前进程读，当前进程写，不会阻塞。

延伸阅读: 该博客有匿名管道和有名管道的C语言实践

### 信号 (Signal)

信号是Linux系统中用于进程间互相通信或者操作的一种机制，信号可以在任何时候发给某一进程，而无需知道该进程的状态。
如果该进程当前并未处于执行状态，则该信号就由内核保存起来，知道该进程回复执行并传递给它为止。
如果一个信号被进程设置为阻塞，则该信号的传递被延迟，直到其阻塞被取消是才被传递给进程。

### 信号 (Signal)

信号是比较复杂的通信方式,用于通知接受进程有某种事件发生, 除了用于进程间通信外,进程还可以发送信号给进程本身；Linux除了支持Unix早期信号语义函数sigal外,还支持语义符合Posix.1标准的信号函数sigaction (实际上,该函数是基于BSD的,BSD为了实现可靠信号机制,又能够统一对外接口,用sigaction函数重新实现了signal函数)

Linux系统中常用信号:  

1. SIGHUP: 用户从终端注销，所有已启动进程都将收到该进程。系统缺省状态下对该信号的处理是终止进程。
2. SIGINT: 程序终止信号。程序运行过程中，按Ctrl+C键将产生该信号。
3. SIGQUIT: 程序退出信号。程序运行过程中，按Ctrl+\\键将产生该信号。
4. SIGBUS和SIGSEGV: 进程访问非法地址。
5. SIGFPE: 运算中出现致命错误，如除零操作、数据溢出等。
6. SIGKILL: 用户终止进程执行信号。shell下执行kill -9发送该信号。
 (7) SIGTERM: 结束进程信号。shell下执行kill 进程pid发送该信号。
 (8) SIGALRM: 定时器信号。
 (9) SIGCLD: 子进程退出信号。如果其父进程没有忽略该信号也没有处理该信号，则子进程退出后将形成僵尸进程。

#### 信号来源

信号是软件层次上对中断机制的一种模拟，是一种异步通信方式，信号可以在用户空间进程和内核之间直接交互，内核可以利用信号来通知用户空间的进程发生了哪些系统事件，信号事件主要有两个来源:

硬件来源: 用户按键输入 Ctrl+C 退出、硬件异常如无效的存储访问等。
软件终止: 终止进程信号、其他进程调用kill函数、软件异常产生信号。
信号生命周期和处理流程
 (1) 信号被某个进程产生，并设置此信号传递的对象 (一般为对应进程的pid) ，然后传递给操作系统；
 (2) 操作系统根据接收进程的设置 (是否阻塞) 而选择性的发送给接收者，如果接收者阻塞该信号 (且该信号是可以阻塞的) ，操作系统将暂时保留该信号，而不传递，直到该进程解除了对此信号的阻塞 (如果对应进程已经退出，则丢弃此信号) ，如果对应进程没有阻塞，操作系统将传递此信号。
 (3) 目的进程接收到此信号后，将根据当前进程对此信号设置的预处理方式，暂时终止当前代码的执行，保护上下文 (主要包括临时寄存器数据，当前程序位置以及当前CPU的状态) 、转而执行中断服务程序，执行完成后在回复到中断的位置。当然，对于抢占式内核，在中断返回时还将引发新的调度。
信号的生命周期

### 消息(Message)队列

消息队列是存放在内核中的消息链表，每个消息队列由消息队列标识符表示。
与管道 (无名管道: 只存在于内存中的文件；命名管道: 存在于实际的磁盘介质或者文件系统) 不同的是消息队列存放在内核中，只有在内核重启(即，操作系统重启)或者显示地删除一个消息队列时，该消息队列才会被真正的删除。
另外与管道不同的是，消息队列在某个进程往一个队列写入消息之前，并不需要另外某个进程在该队列上等待消息的到达。延伸阅读: 消息队列C语言的实践
消息队列特点总结:
 (1) 消息队列是消息的链表,具有特定的格式,存放在内存中并由消息队列标识符标识.
 (2) 消息队列允许一个或多个进程向它写入与读取消息.
 (3) 管道和消息队列的通信数据都是先进先出的原则。
 (4) 消息队列可以实现消息的随机查询,消息不一定要以先进先出的次序读取,也可以按消息的类型读取.比FIFO更有优势。
 (5) 消息队列克服了信号承载信息量少，管道只能承载无格式字 节流以及缓冲区大小受限等缺。
 (6) 目前主要有两种类型的消息队列: POSIX消息队列以及System V消息队列，系统V消息队列目前被大量使用。系统V消息队列是随内核持续的，只有在内核重起或者人工删除时，该消息队列才会被删除。

### 共享内存 (share memory)

使得多个进程可以可以直接读写同一块内存空间，是最快的可用IPC形式。是针对其他通信机制运行效率较低而设计的。
为了在多个进程间交换信息，内核专门留出了一块内存区，可以由需要访问的进程将其映射到自己的私有地址空间。进程就可以直接读写这一块内存而不需要进行数据的拷贝，从而大大提高效率。
由于多个进程共享一段内存，因此需要依靠某种同步机制 (如信号量) 来达到进程间的同步及互斥。
延伸阅读: Linux支持的主要三种共享内存方式: `mmap() 系统调用` 、Posix 共享内存， 以及System V 共享内存实践, mmap可以认为是属于POSIX的

#### IPC> 共享内存 > mmap

```c
// 成功返回共享内存的地址，失败返回(void *)-1
void *mmap(void *addr, size_t length, int prot, int flags, int fd, off_t offset);
// 成功返回0，失败返回-1
int munmap(void *addr, size_t length);
```

#### POSIX共享内存 API

1. 函数shm_open和shm_unlink非常类似于为普通文件所提供的open和unlink系统调用.
2. 如果要编写一个可移植的程序,那么shm_open和shm_unlink是最好的选择.
3. shm_open:创建一个新的共享区域或者附加在已有的共享区域上.区域被其名字标识,函数返回各文件的描述符.
4. shm_unlink:类似于unlink系统调用对文件进行操作,直到所有的进程不再引用该内存区后才对其进行释放.
5. mmap:用于将一个文件映射到某一内存区中,其中也使用了shm_open函数返回的文件描述符.
6. munmap:用于释放mmap所映射的内存区域.
7. msync:同步存取一个映射区域并将高速缓存的数据回写到物理内存中,以便其他进程可以监听这些改变.

#### System V共享内存 API

1. System V API广泛应用于X windows系统及其扩展版本中,许多X应用程序也使用它.
2. shmget:创建一个新的共享区域或者附加在已有的共享区域上(同shm_open).
3. shmat:用于将一个文件映射到内存区域中(同mmap).
4. shmdt:用于释放所映射的内存区域(同munmap)
5. shmctl:对于多个用户,断开其对共享区域的连接(同shm_unlink)

```c
#include <sys/ipc.h>
#include <sys/shm.h>
//创建或访问共享内存
int shmget(key_t key,size_t size,int shmflg);

//附加共享内存到进程的地址空间
void *shmat(int shmid,const void *shmaddr,int shmflg); 
//shmaddr通常为NULL，由系统选择共享内存附加的地址;shmflg可以为SHM_RDONLY
//从进程的地址空间分离共享内存
int shmdt(const void *shmaddr); //shmaddr是shmat()函数的返回值
//控制共享内存
int shmctl(int shmid,int cmd,struct shmid_ds *buf);
struct shmid_ds{
 struct ipc_perm shm_perm;
 // …
};
```

cmd的常用取值有:(a)IPC_STAT获取当前共享内存的shmid_ds结构并保存在buf中(2)IPC_SET使用buf中的值设置当前共享内存的shmid_ds结构(3)IPC_RMID删除当前共享内存
  
### 信号量(semaphore)

信号量是一个计数器，用于多进程对共享数据的访问，信号量的意图在于进程间同步。
为了获得共享资源，进程需要执行下列操作:
 (1) 创建一个信号量: 这要求调用者指定初始值，对于二值信号量来说，它通常是1，也可是0。
 (2) 等待一个信号量: 该操作会测试这个信号量的值，如果小于0，就阻塞。也称为P操作。
 (3) 挂出一个信号量: 该操作将信号量的值加1，也称为V操作。

为了正确地实现信号量，信号量值的测试及减1操作应当是原子操作。为此，信号量通常是在内核中实现的。Linux环境中，有三种类型: Posix (可移植性操作系统接口) 有名信号量 (使用Posix IPC名字标识) 、Posix基于内存的信号量 (存放在共享内存区中) 、System V信号量 (在内核中维护) 。这三种信号量都可用于进程间或线程间的同步。
两个进程使用一个二值信号量
两个进程所以用一个Posix有名二值信号量
一个进程两个线程共享基于内存的信号量
信号量与普通整型变量的区别:
 (1) 信号量是非负整型变量，除了初始化之外，它只能通过两个标准原子操作: wait(semap) , signal(semap) ; 来进行访问；
 (2) 操作也被成为PV原语 (P来源于荷兰语proberen"测试"，V来源于荷兰语verhogen"增加"，P表示通过的意思，V表示释放的意思) ，而普通整型变量则可以在任何语句块中被访问；

信号量与互斥量之间的区别:
 (1) 互斥量用于线程的互斥，信号量用于线程的同步。这是互斥量和信号量的根本区别，也就是互斥和同步之间的区别。
互斥: 是指某一资源同时只允许一个访问者对其进行访问，具有唯一性和排它性。但互斥无法限制访问者对资源的访问顺序，即访问是无序的。
同步: 是指在互斥的基础上 (大多数情况) ，通过其它机制实现访问者对资源的有序访问。
在大多数情况下，同步已经实现了互斥，特别是所有写入资源的情况必定是互斥的。少数情况是指可以允许多个访问者同时访问资源
 (2) 互斥量值只能为0/1，信号量值可以为非负整数。
也就是说，一个互斥量只能用于一个资源的互斥访问，它不能实现多个资源的多线程互斥问题。信号量可以实现多个同类资源的多线程互斥和同步。当信号量为单值信号量是，也可以完成一个资源的互斥访问。
 (3) 互斥量的加锁和解锁必须由同一线程分别对应使用，信号量可以由一个线程释放，另一个线程得到。

### socket (socket)

 socket 是一种通信机制，凭借这种机制，客户/服务器 (即要进行通信的进程) 系统的开发工作既可以在本地单机上进行，也可以跨网络进行。也就是说它可以让不在同一台计算机但通过网络连接计算机上的进程进行通信。

Socket是应用层和传输层之间的桥梁

 socket 是支持TCP/IP的网络通信的基本操作单元，可以看做是不同主机之间的进程进行双向通信的端点，简单的说就是通信的两方的一种约定，用 socket 中的相关函数来完成通信过程。

 socket 特性
 socket 的特性由3个属性确定，它们分别是: 域、端口号、协议类型。
 (1)  socket 的域
它指定 socket 通信中使用的网络介质，最常见的 socket 域有两种:
一是AF_INET，它指的是Internet网络。当客户使用 socket 进行跨网络的连接时，它就需要用到服务器计算机的IP地址和端口来指定一台联网机器上的某个特定服务，所以在使用socket作为通信的终点，服务器应用程序必须在开始通信之前绑定一个端口，服务器在指定的端口等待客户的连接。
另一个域AF_UNIX，表示UNIX文件系统，它就是文件输入/输出，而它的地址就是文件名。
 (2)  socket 的端口号
每一个基于TCP/IP网络通讯的程序(进程)都被赋予了唯一的端口和端口号，端口是一个信息缓冲区，用于保留Socket中的输入/输出信息，端口号是一个16位无符号整数，范围是0-65535，以区别主机上的每一个程序 (端口号就像房屋中的房间号) ，低于256的端口号保留给标准应用程序，比如pop3的端口号就是110，每一个 socket 都组合进了IP地址、端口，这样形成的整体就可以区别每一个 socket 。
 (3)  socket 协议类型
因特网提供三种通信机制，
一是流 socket ，流 socket 在域中通过TCP/IP连接实现，同时也是AF_UNIX中常用的 socket 类型。流 socket 提供的是一个有序、可靠、双向字节流的连接，因此发送的数据可以确保不会丢失、重复或乱序到达，而且它还有一定的出错后重新发送的机制。
二个是数据报 socket ，它不需要建立连接和维持一个连接，它们在域中通常是通过UDP/IP协议实现的。它对可以发送的数据的长度有限制，数据报作为一个单独的网络消息被传输,它可能会丢失、复制或错乱到达，UDP不是一个可靠的协议，但是它的速度比较高，因为它并一需要总是要建立和维持一个连接。
三是原始 socket ，原始 socket 允许对较低层次的协议直接访问，比如IP、 ICMP协议，它常用于检验新的协议实现，或者访问现有服务中配置的新设备，因为RAW SOCKET可以自如地控制Windows下的多种协议，能够对网络底层的传输机制进行控制，所以可以应用原始 socket 来操纵网络层和传输层应用。比如，我们可以通过RAW SOCKET来接收发向本机的ICMP、IGMP协议包，或者接收TCP/IP栈不能够处理的IP包，也可以用来发送一些自定包头或自定协议的IP包。网络监听技术很大程度上依赖于SOCKET_RAW。

原始 socket 与标准 socket 的区别在于:
原始 socket 可以读写内核没有处理的IP数据包，而流 socket 只能读取TCP协议的数据，数据报 socket 只能读取UDP协议的数据。因此，如果要访问其他协议发送数据必须使用原始 socket 。

 socket 通信的建立

Socket通信基本流程

**服务器端**
 (1) 首先服务器应用程序用系统调用socket来创建一个 socket ，它是系统分配给该服务器进程的类似文件描述符的资源，它不能与其他的进程共享。
 (2) 然后，服务器进程会给 socket 起个名字，我们使用系统调用bind来给 socket 命名。然后服务器进程就开始等待客户连接到这个 socket 。
 (3) 接下来，系统调用listen来创建一个队列并将其用于存放来自客户的进入连接。
 (4) 最后，服务器通过系统调用accept来接受客户的连接。它会创建一个与原有的命名套接不同的新 socket ，这个 socket 只用于与这个特定客户端进行通信，而命名 socket  (即原先的 socket ) 则被保留下来继续处理来自其他客户的连接 (建立客户端和服务端的用于通信的流，进行通信) 。

客户端
 (1) 客户应用程序首先调用socket来创建一个未命名的 socket ，然后将服务器的命名 socket 作为一个地址来调用connect与服务器建立连接。
 (2) 一旦连接建立，我们就可以像使用底层的文件描述符那样用 socket 来实现双向数据的通信 (通过流进行数据传输) 。
延伸阅读 : Java socket编程

三、参考引用

1. 进程间通信--管道
2. Linux进程间通信——使用共享内存
3. 进程间通信---共享内存
4. 信号量与互斥锁
5. 信号量

**[http://www.cnblogs.com/wangkangluo1/archive/2012/05/14/2498786.html](http://www.cnblogs.com/wangkangluo1/archive/2012/05/14/2498786.html)**

**Socket**
  
 socket (Socket)是由Berkeley在BSD系统中引入的一种基于连接的IPC，是对网络接口(硬件)和网络协议(软件)的抽象。它既解决了无名管道只能在相关进程间单向通信的问题，又解决了网络上不同主机之间无法通信的问题。

 socket 有三个属性:域(domain)、类型(type)和协议(protocol)，对应于不同的域， socket 还有一个地址(address)来作为它的名字。

域(domain)指定了 socket 通信所用到的协议族，最常用的域是AF_INET，代表网络 socket ，底层协议是IP协议。对于网络 socket ，由于服务器端有可能会提供多种服务，客户端需要使用IP端口号来指定特定的服务。AF_UNIX代表本地 socket ，使用Unix/Linux文件系统实现。

IP协议提供了两种通信手段:流(streams)和数据报(datagrams)，对应的 socket 类型(type)分别为流式 socket 和数据报 socket 。流式 socket (SOCK_STREAM)用于提供面向连接、可靠的数据传输服务。该服务保证数据能够实现无差错、无重复发送，并按顺序接收。流式 socket 使用TCP协议。数据报 socket (SOCK_DGRAM)提供了一种无连接的服务。该服务并不能保证数据传输的可靠性，数据有可能在传输过程中丢失或出现数据重复，且无法保证顺序地接收到数据。数据报 socket 使用UDP协议。

一种类型的 socket 可能可以使用多于一种的协议来实现， socket 的协议(protocol)属性用于指定一种特定的协议。

IPC进程间通信(Inter-Process Communication)就是指多个进程之间相互通信，交换信息的方法。Linux IPC基本上都是从Unix平台上继承而来的。主要包括最初的Unix IPC，System V IPC以及基于Socket的IPC。另外，Linux也支持POSIX IPC。

**System V,BSD,POSIX**

System V是Unix操作系统最早的商业发行版之一。它最初由AT&T(American Telephone & Telegraph)开发，最早在1983年发布。System V主要发行了4个版本，其中SVR4(System V Release 4)是最成功的版本。BSD(Berkeley Software Distribution，有时也被称为Berkeley Unix)是加州大学于1977至1995年间开发的。在19世纪八十年代至九十年代之间，System V和BSD代表了Unix的两种主要的操作风格。它们的主要区别如下:

系统                      System V           BSD
  
root脚本位置            /etc/init.d/       /etc/rc.d/
  
默认shell                 Bshell             Cshell
  
文件系统数据            /etc/mnttab     /etc/mtab
  
内核位置                  /UNIX             /vmUnix
  
打印机设备                lp                  rlp
  
字符串函数                memcopy       bcopy
  
终端初始化设置文件    /etc/initab       /etc/ttys
  
终端控制                  termio            termios

Linux系统的操作风格往往介于这两种风格之间。

POSIX(Portable Operating System Interface [for Unix])是由IEEE(Institute of Electrical and Electronics Engineers，电子电气工程协会)开发的。现有的大部分Unix都遵循POSIX标准，而Linux从一开始就遵循POSIX标准。

**最初的Unix IPC**

**_1、信号_**

信号是Unix/Linux系统在一定条件下生成的事件。信号是一种异步通信机制，进程不需要执行任何操作来等待信号的到达。信号异步通知接收信号的进程发生了某个事件，然后操作系统将会中断接收到信号的进程的执行，转而去执行相应的信号处理程序。

(1)注册信号处理函数
  
# include <signal.h>
  
/*typedef void (\*sighandler_t)(int);  sighandler_t signal(int signum,sighandler_t handler);*/
  
\* void (\*signal(int signum, void (*handler)(int)))(int);  //SIG_IGN && SIG_DFL
  
\* int sigaction(int signum, const struct sigaction \*act,struct sigaction *oldact);

(2)发送信号
  
# include <signal.h>
  
- int kill(pid_t pid,int sig); //#include <sys/types.h>
  
- int raise(int sig);            //kill(getpid(),sig);
  
- unsigned int alarm(unsigned int seconds); //(#include <unistd.h>) seconds秒后，向进程本身发送SIGALRM信号。

(3)信号集
  
信号集被定义为:typedef struct {unsigned long sig[_NSIG_WORDS];} sigset_t;
  
\* int sigaddset(sigset_t \*set,int sig);
  
\* int sigemptyset(sigset_t \*set);

_**2、管道(Pipe)**_

管道用来连接不同进程之间的数据流。

(1)在两个程序之间传递数据的最简单的方法是使用popen()和pclose()函数:
  
# include <stdio.h>
  
FILE \*popen(const char \*command, const char *open_mode);
  
int pclose(FILE *stream);
  
popen()函数首先调用一个shell，然后把_command_作为参数传递给shell。这样每次调用popen()函数都需要启动两个进程；但是由于在Linux中，所有的参数扩展(parameter expansion)都是由shell执行的，这样_command_中包含的所有参数扩展都可以在_command_程序启动之前完成。

(2)pipe()函数:
  
# include <unistd.h>
  
int pipe(int pipefd[2]);
  
popen()函数只能返回一个管道描述符，并且返回的是文件流(file stream)，可以使用函数fread()和fwrite()来访问。pipe()函数可以返回两个管道描述符:_pipefd[0]_和_pipefd[1]_，任何写入_pipefd[1]_的数据都可以从_pipefd[0]_读回；pipe()函数返回的是文件描述符(file descriptor)，因此只能使用底层的read()和write()系统调用来访问。pipe()函数通常用来实现父子进程之间的通信。

(3)命名管道:FIFO
  
# include <sys/types.h>
  
# include <sys/stat.h>
  
int mkfifo(const char *fifo_name, mode_t mode);
  
前面两种管道只能用在相关的程序之间，使用命名管道可以解决这个问题。在使用open()打开FIFO时，mode中不能包含O_RDWR。mode最常用的是O_RDONLY，O_WRONLY与O_NONBLOCK的组合。O_NONBLOCK影响了read()和write()在FIFO上的执行方式。

PS:要想查看库函数用法，最可靠的资料来自Linux manual page:

$sudo apt-get install manpages-dev

$man 3 _function_name_

    System V IPC
  
  
    System V IPC指的是AT&T在System V.2发行版中引入的三种进程间通信工具:(1)信号量，用来管理对共享资源的访问 (2)共享内存，用来高效地实现进程间的数据共享 (3)消息队列，用来实现进程间数据的传递。我们把这三种工具统称为System V IPC的对象，每个对象都具有一个唯一的IPC标识符(identifier)。要保证不同的进程能够获取同一个IPC对象，必须提供一个IPC关键字(IPC key)，内核负责把IPC关键字转换成IPC标识符。
  
  
    System V IPC具有相似的语法，一般操作如下:
  
  
    (1)选择IPC关键字，可以使用如下三种方式:
  
  
    a)IPC_PRIVATE。由内核负责选择一个关键字然后生成一个IPC对象并把IPC标识符直接传递给另一个进程。
 b)直接选择一个关键字。
 c)使用ftok()函数生成一个关键字。
  
    (2)使用semget()/shmget()/msgget()函数根据IPC关键字key和一个标志flag创建或访问IPC对象。如果key是IPC_PRIVATE;或者key尚未与已经存在的IPC对象相关联且flag中包含IPC_CREAT标志，那么就会创建一个全新的IPC对象。
  
  
    (3)使用semctl()/shmctl()/msgctl()函数修改IPC对象的属性。
  
  
    (4)使用semctl()/shmctl()/msgctl()函数和IPC_RMID标志销毁IPC实例。
  
  
    System V IPC为每个IPC对象设置了一个ipc_perm结构体并在创建IPC对象的时候进行初始化。这个结构体中定义了IPC对象的访问权限和所有者:
  
  
    struct ipc_perm{
 uid_t uid;   //所有者的用户id
 gid_t gid;   //所有者的组id
 uid_t cuid;  //创建者的用户id
 gid_t cgid;  //创建者的组id
 mode_t mode; //访问模式
 …
 };
  
    shell中管理IPC对象的命令是ipcs、ipcmk和ipcrm。
  
  
    1、信号量(Semaphores)
  
  
    System V的信号量集表示的是一个或多个信号量的集合。内核为每个信号量集维护一个semid_ds数据结构，而信号量集中的每个信号量使用一个无名结构体表示，这个结构体至少包含以下成员:
 struct{
 unsigned short semval;//信号量值，总是>=0
 pid_t sempid;  //上一次操作的pid
 …
 };
  
    #include <sys/types.h>
 #include <sys/ipc.h>
 #include <sys/sem.h>
 (1)创建或访问信号量

- int semget(key_t key,int nsems,int flag);
 nsems指定信号量集中信号量的个数，如果只是获取信号量集的标识符(而非新建)，那么nsems可以为0。flag的低9位作为信号量的访问权限位，类似于文件的访问权限;如果flag中同时指定了IPC_CREAT和IPC_EXCL，那么如果key已与现存IPC对象想关联的话，函数将会返回EEXIST错误。例如，flag可以为IPC_CREAT|0666。
  
    (2)控制信号量集
- int semctl(int semid,int semnum,int cmd,union semun arg);
 对semid信号量集合执行cmd操作;cmd常用的两个值是:SETVAL初始化第semnum个信号量的值为arg.val;IPC_RMID删除信号量。
  
    (3)对一个或多个信号量进行操作
- int semop(int semid,struct sembuf *sops,unsigned nsops);
- struct sembuf{
 unsigned short sem_num;  //信号量索引
 short   sem_op;     //对信号量进行的操作，常用的两个值为-1和+1，分别代表P、V操作
 short   sem_flag;   //比较重要的值是SEM_UNDO:当进程结束时，相应的操作将被取消；同时，如果进程结束时没有释放资源的话，系统会自动释放
 };
  
### 消息队列
  
消息队列保存在内核中，是一个由消息组成的链表。
  
    #include <sys/types.h>
 #include <sys/ipc.h>
 #include <sys/msg.h>
 (1)创建或访问消息队列

- int msgget(key_t key,int msgflg);
  
    (2)操作消息队列
- int msgsnd(int msqid,const void *msg,size_t nbytes,int msgflg);
 msg指向的结构体必须以一个long int成员开头，作为msgrcv()的消息类型，必须大于0。nbytes指的是msg指向结构体的大小，但不包括long int部分的大小
- ssize_t msgrcv(int msqid,void *msg,size_t nbytes,long msgtype,int msgflg);
 如果msgtype是0，就返回消息队列中的第一个消息;如果是正整数，就返回队列中的第一个该类型的消息;如果是负数，就返回队列中具有最小值的第一个消息，并且该最小值要小于等于msgtype的绝对值。
  
    (3)控制消息队列
- int msgctl(int msqid,int cmd,struct msqid_ds *buf);
- struct msqid_ds{
 struct ipc_perm msg_perm;
 …
 };
  
    Socket
  socket (Socket)是由Berkeley在BSD系统中引入的一种基于连接的IPC，是对网络接口(硬件)和网络协议(软件)的抽象。它既解决了无名管道只能在相关进程间单向通信的问题，又解决了网络上不同主机之间无法通信的问题。
  
     socket 有三个属性:域(domain)、类型(type)和协议(protocol)，对应于不同的域， socket 还有一个地址(address)来作为它的名字。
  
    域(domain)指定了 socket 通信所用到的协议族，最常用的域是AF_INET，代表网络 socket ，底层协议是IP协议。对于网络 socket ，由于服务器端有可能会提供多种服务，客户端需要使用IP端口号来指定特定的服务。AF_UNIX代表本地 socket ，使用Unix/Linux文件系统实现。
  
    IP协议提供了两种通信手段:流(streams)和数据报(datagrams)，对应的 socket 类型(type)分别为流式 socket 和数据报 socket 。流式 socket (SOCK_STREAM)用于提供面向连接、可靠的数据传输服务。该服务保证数据能够实现无差错、无重复发送，并按顺序接收。流式 socket 使用TCP协议。数据报 socket (SOCK_DGRAM)提供了一种无连接的服务。该服务并不能保证数据传输的可靠性，数据有可能在传输过程中丢失或出现数据重复，且无法保证顺序地接收到数据。数据报 socket 使用UDP协议。
  
    一种类型的 socket 可能可以使用多于一种的协议来实现， socket 的协议(protocol)属性用于指定一种特定的协议。

[http://www.ericbess.com/ericblog/2008/03/03/wp-codebox/#examples](http://www.ericbess.com/ericblog/2008/03/03/wp-codebox/#examples)

总结:

System V IPC API

1,消息队列

int ftok(const char *pathname, int prj_id);

int msgget(key_t key,int msgflag);

int msgsnd(int msqid,const void *msgp,size_t msgsz,int msgflg);

int msgrcv(int msqid,void *msgp,size_t msgsz,long msgtyp,int msgflg);

2,信号量

int semget(key_t key,int nsems,int semflag);

int semctl(int semid,int semnum,int cmd,…);

int semop(int semid,struct sembuf \*sops,unsigned nsops,struct timespec \*timeout);

3,共享内存

int shmget(key_t key,size_t size,int shmflag);

int shmctl(int shmid,int cmd,struct shmid_ds *buf);

POSIX IPC API

 [1]: http://blog.csdn.net/xuexingyang/article/details/6653189

IPC(Inter-Process Communication)是共享"命名管道"的资源，它是为了让进程间通信而开放的命名管道，通过提供可信任的用户名和口令，连接双方可以建立安全的通道并以此通道进行加密数据的交换，从而实现对远程计算机的访问。
  
System V， 曾经也被称为 AT&T System V，是Unix操作系统众多版本中的一支。它最初由 AT&T 开发，在1983年第一次发布。一共发行了4个 System V 的主要版本: 版本1、2、3 和 4。System V Release 4，或者称为SVR4，是最成功的版本，成为一些UNIX共同特性的源头，例如 "SysV 初始化脚本"  (/etc/init.d) ，用来控制系统启动和关闭，System V Interface Definition (SVID) 是一个System V 如何工作的标准定义。 AT&T 出售运行System V的专有硬件，但许多 (或许是大多数) 客户在其上运行一个转售的版本，这个版本基于 AT&T 的实现说明。流行的SysV 衍生版本包括 Dell SVR4 和 Bull SVR4。当今广泛使用的 System V 版本是 SCO OpenServer，基于 System V Release 3，以及SUN Solaris 和 SCO UnixWare，都基于 System V Release 4。 System V 是 AT&T 的第一个商业UNIX版本 (UNIX System III) 的加强。传统上，System V 被看作是两种UNIX"风味"之一 (另一个是 BSD) 。然而，随着一些并不基于这两者代码的UNIX实现的出现，例如 Linux 和 QNX， 这一归纳不再准确，但不论如何，像POSIX这样的标准化努力一直在试图减少各种实现之间的不同。

### System V 的 IPC 机制

为了提供与其它系统的兼容性， Linux 也支持三种 system V 的进程间通信机制: 消息、信号量(semaphores)和共享内存，Linux对这些机制的实施大同小异。我们把信号量、消息和共享内存统称 System V IPC的对象，每一个对象都具有同样类型的接口，即系统调用。就像每个文件都有一个打开文件号一样，每个对象也都有唯一的识别号，进程可以通过系统调用传递的识别号来存取这些对象，与文件的存取一样，对这些对象的存取也要验证存取权限，System V IPC可以通过系统调用对对象的创建者设置这些对象的存取权限。

在Linux内核中，System V IPC的所有对象有一个公共的数据结构pc_perm结构，它是IPC对象的权限描述，在linux/ipc.h中定义如下:

struct ipc_perm
  
{
  
key_t key; /*键*/
  
ushort uid; /*对象拥有者对应进程的有效用户识别号和有效组识别号*/
  
ushort gid;
  
ushort cuid; /*对象创建者对应进程的有效用户识别号和有效组识别号*/
  
ushort cgid;
  
ushort mode; /*存取模式*/
  
ushort seq; /*序列号*/
  
};

在这个结构中，要进一步说明的是键(key)。键和识别号指的是不同的东西。系统支持两种键: 公有和私有。如果键是公有的，则系统中所有的进程通过权限检 查后，均可以找到System V IPC 对象的识别号。如果键是公有的，则键值为0，说明每个进程都可以用键值0建立一个专供其私用的对象。注意，对System V IPC对象的引用是通过识别号而不是通过键，从后面的系统调用中可了解这一点。

### 信号量 semaphore

信号量及信号量上的操作是E.W.Dijkstra 在1965年提出的一种解决同步、互斥问题的较通用的方法，并在很多操作系统中得以实现， Linux改进并实现了这种机制。

信号量 (semaphore) 实际是一个整数，它的值由多个进程进行测试 (test) 和设置 (set) 。就每个进程所关心的测试和设置操作而言，这两个操作是不可中断的，或称"原子"操作，即一旦开始直到两个操作全部完成。测试和设置操作的结果是: 信号量的当前值和设置值相加，其和或者是正或者为负。根据测试和设置操作的结果，一个进程可能必须睡眠，直到有另一个进程改变信号量的值。

信号量可用来实现所谓的"临界区"的互斥使用，临界区指同一时刻只能有一个进程执行其中代码的代码段。为了进一步理解信号量的使用，下面我们举例说明。

假设你有很多相互协作的进程，它们正在读或写一个数据文件中的记录。你可能希望严格协调对这个文件的存取，于是你使用初始值为1的信号量，在这个信号量上 实施两个操作，首先测试并且给信号量的值减1，然后测试并给信号量的值加1。当第一个进程存取文件时，它把信号量的值减1，并获得成功，信号量的值现在变 为0，这个进程可以继续执行并存取数据文件。但是，如果另外一个进程也希望存取这个文件，那么它也把信号量的值减1，结果是不能存取这个文件，因为信号量 的值变为-1。这个进程将被挂起，直到第一个进程完成对数据文件的存取。当第一个进程完成对数据文件的存取，它将增加信号量的值，使它重新变为1，现在， 等待的进程被唤醒，它对信号量的减1操作将获得成功。

上述的进程互斥问题，是针对进程之间要共享一个临界资源而言的，信号量的初值为1。实际上，信号量作为资源计数器，它的初值可以是任何正整数，其初值不一 定为0或1。另外，如果一个进程要先获得两个或多个的共享资源后才能执行的话，那么，相应地也需要多个信号量，而多个进程要分别获得多个临界资源后方能运 行，这就是信号量集合机制，Linux 讨论的就是信号量集合问题。

#### 信号量的数据结构

Linux中信号量是通过内核提供的一系列数据结构实现的，这些数据结构存在于内核空间，对它们的分析是充分理解信号量及利用信号量实现进程间通信的基础，下面先给出信号量的数据结构(存在于include/linux/sem.h中)，其它一些数据结构将在相关的系统调用中介绍。

(1)系统中每个信号量的数据结构(sem)

1)系统中每个信号量的数据结构(sem)
  
struct sem {
  
int semval; /*信号量的当前值*/
  
int sempid; /*在信号量上最后一次操作的进程识别号*/
  
};

2)系统中表示信号量集合(set)的数据结构(semid_ds)
  
struct semid_ds {
  
struct ipc_perm sem_perm; /*IPC权限*/
  
long sem_otime; /*最后一次对信号量操作(semop)的时间*/
  
long sem_ctime; /*对这个结构最后一次修改的时间*/
  
struct sem \*sem_base; /*在信号量数组中指向第一个信号量的指针*/
  
struct sem_queue \*sem_pending; /*待处理的挂起操作*/
  
struct sem_queue *\*sem_pending_last; /* 最后一个挂起操作 */
  
struct sem_undo \*undo; /*在这个数组上的undo 请求*/
  
ushort sem_nsems; /*在信号量数组上的信号量号*/
  
};

3) 系统中每一信号量集合的队列结构(sem_queue)
  
struct sem_queue {
  
struct sem_queue \* next; /*队列中下一个节点*/
  
struct sem_queue *\* prev; /* 队列中前一个节点, \*(q->prev) == q */
  
struct wait_queue \* sleeper; /*正在睡眠的进程*/
  
struct sem_undo \* undo; /*undo 结构*/
  
int pid; /*请求进程的进程识别号*/
  
int status; /*操作的完成状态*/
  
struct semid_ds \* sma; /*有操作的信号量集合数组*/
  
struct sembuf \* sops; /*挂起操作的数组*/
  
int nsops; /*操作的个数*/
  
};

(4)几个主要数据结构之间的关系

从7.3图可以看出，semid_ds结构的sem_base指向一个信号量数组，允许操作这些信号量集合的进程可以利用系统调用执行操作。注意，信号量 信号量集合的区别，从上面可以看出，信号量用"sem" 结构描述，而信量集合用"semid_ds"结构描述，实际上，在后面的讨论中，我们以信号量集合为讨论的主要对象。下面我们给出这几个结构之间的关系， 如图7.3所示。

Linux对信号量的这种实现机制，是为了与消息和共享内存的实现机制保持一致，但信号量是这三者中最难理解的，因此我们将结合系统调用做进一步的介绍，通过对系统调用的深入分析，我们可以较清楚地了解内核对信号量的实现机制。

2. 系统调用: semget()
为了创建一个新的信号量集合，或者存取一个已存在的集合，要使用segget()系统调用，其描述如下:

原型: int semget ( key_t key, int nsems, int semflg );
  
返回值: 如果成功，则返回信号量集合的IPC识别号
  
如果为-1，则出现错误:
  
semget()中的第一个参数是键值， 这个键值要与已有的键值进行比较，已有的键值指在内核中已存在的其它信号量集合的键值。对信号量集合的打开或存取操作依赖于semflg参数的取值: IPC_CREAT : 如果内核中没有新创建的信号量集合，则创建它。
  
IPC_EXCL : 当与IPC_CREAT一起使用时，但信号量集合已经存在，则创建失败。如果IPC_CREAT单独使用，semget()为一个新建的集合返回标识 号，或者返回具有相同键值的已存在集合的标识号。如果IPC_EXCL与IPC_CREAT一起使用，要么创建一个新的集合，要么对已存在的集合返回 -1。IPC_EXCL单独是没有用的，当与IPC_CREAT结合起来使用时，可以保证新创建集合的打开和存取。作为System V IPC的其它形式，一种可选项是把一个八进制与掩码或，形成信号量集合的存取权限。第三个参数nsems指的是在新创建的集合中信号量的个数。其最大值在 "linux/sem.h"中定义:

```c
#define SEMMSL 250 /* SHMMAX的指针数组 */
struct vm_area_struct \*attaches; /* 对共享段的描述 */
};
```

2. 共享内存的处理过程
某个进程第一次访问共享虚拟内存时将产生缺页异常。这时， Linux 找出描述该内存的 vm_area_struct 结构，该结构中包含用来处理这种共享虚拟内存段的处理函数地址。共享内存缺页异常处理代码对shmid_ds 的页表项表进行搜索，以便查看是否存在该共享虚拟内存的页表项。如果没有，系统将分配一个物理页并建立页表项，该页表项加入 shmid_ds 结构的同时也添加到进程的页表中。这就意味着当下一个进程试图访问这页内存时出现缺页异常，共享内存的缺页异常处理代码则把新创建的物理页给这个进程。因 此说，第一个进程对共享内存的存取引起创建新的物理页面，而其它进程对共享内存的存取引起把那个页加添加到它们的地址空间。

当某个进程不再共享其虚拟内存时，利用系统调用将共享段从自己的虚拟地址区域中移去，并更新进程页表。当最后一个进程释放了共享段之后，系统将释放给共享段所分配的物理页。

当共享的虚拟内存没有被锁定到物理内存时，共享内存也可能会被交换到交换区中。

3. 系统调用: shmget()

原型: int shmget ( key_t key, int size, int shmflg );

返回: 成功，则返回共享内存段的识别号, 失败返回-1

shmget()系统调用类似于信号量和消息队列的系统调用，在此不进一步赘述。

4. 系统调用: shmat()

原型:  int shmat ( int shmid, char *shmaddr, int shmflg);

返回: 成功，则返回附加到进程的那个段的地址，失败返回-1。

其中shmid是由shmget()调用返回的共享内存段识别号，shmaddr是你希望共享段附加的地址，shmflag允许你规定希望所附加的段为只 读(利用SHM_RDONLY)以代替读写。通常，并不需要规定你自己的shmaddr，可以用传递参数值零使得系统为你取得一个地址。

这个调用可能是最简单的，下面看一个例子，把一个有效的识别号传递给一个段，然后返回这个段被附加到内存的内存地址。

char *attach_segment( int shmid )
  
{
  
return(shmat(shmid, 0, 0));
  
}
  
一旦一个段适当地被附加，并且一个进程有指向那个段起始地址的一个指针，那么，对那个段的读写就变得相当容易。

5. 系统调用:  shmctl()

原型:  int shmctl ( int shmqid, int cmd, struct shmid_ds *buf );

返回: 成功为 0 ， 失败 为-1

这个特殊的调用和semctl()调用几乎相同，因此，这里不进行详细的讨论。有效命令的值是:

IPC_STAT : 检索一个共享段的shmid_ds结构，把它存到buf参数的地址中。

IPC_SET : 对一个共享段来说，从buf 参数中取值设置shmid_ds结构的ipc_perm域的值。

IPC_RMID : 把一个段标记为删除

IPC_RMID 命令实际上不从内核删除一个段，而是仅仅把这个段标记为删除，实际的删除发生在最后一个进程离开这个共享段时。
  
当一个进程不再需要共享内存段时，它将调用shmdt()系统调用取消这个段，但是，这并不是从内核真正地删除这个段，而是把相关shmid_ds结构的 shm_nattch域的值减1，当这个值为0时，内核才从物理上删除这个共享段。

## Linux 进程间通信, 管道(pipe)、命名管道(FIFO), 信号(Signal)

Unix IPC(进程间通信 (IPC,Inter-Process Communication) )包括: 管道(pipe)、命名管道(FIFO)与信号(Signal)  

### 管道(pipe), 匿名管道

多个进程在协作完成同一任务时,通常彼此要传输数据,共享资源。
在 shell 中常常会用到管道符,如查看占用 80 端口的进程: netstat -an | grep :80,在 bash 中每个命令在执行时都是独立的进程,netstat 父进程通过管道将数据传输给 fork 出的 grep 子进程处理。这就是最简单的 IPC 管道通信。

管道可用于具有亲缘关系进程间的通信,命名管道克服了管道没有名字的限制,因此,除具有管道所具有的功能外,它还允许无亲缘关系进程间的通信；  

匿名管道: shell 中的 pipe 就是匿名管道,只能在父子进程 / 有亲缘关系的进程之间使用。
原因: 管道在 Linux 中是文件,想要通过匿名管道来读写数据,必须拥有相同的文件描述符,而拥有相同 fd 的两个进程需有亲缘关系。

#### 实现机制

管道是由内核管理的一个缓冲区,相当于我们放入内存中的一个纸条。管道的一端连接一个进程的输出。这个进程会向管道中放入信息。管道的另一端连接一个进程的输入,这个进程取出被放入管道的信息。一个缓冲区不需要很大,它被设计成为环形的数据结构,以便管道可以被循环利用。当管道中没有信息的话,从管道中读取的进程会等待,直到另一端的进程放入信息。当管道被放满信息的时候,尝试放入信息的进程会等待,直到另一端的进程取出信息。当两个进程都终结的时候,管道也自动消失。

从原理上,管道利用fork机制建立,从而让两个进程可以连接到同一个PIPE上。最开始的时候,上面的两个箭头都连接在同一个进程上。当fork复制进程的时候,会将这两个连接也复制到新的进程。随后,每个进程关闭自己不需要的一个连接 ,这样,剩下的红色连接就构成了PIPE。

#### 命名管道, named pipe(FIFO)

In computing, a named pipe (also known as a FIFO for its behavior)

A FIFO special file (a named pipe) is similar to a pipe
>[https://man7.org/linux/man-pages/man7/fifo.7.html](https://man7.org/linux/man-pages/man7/fifo.7.html)

命名管道: 允许无亲缘关系的进程间传输数据。  
由于基于fork机制,所以管道只能用于父进程和子进程之间,或者拥有相同祖先的两个子进程之间 (有亲缘关系的进程之间)。为了解决这一问题,Linux提供了FIFO方式连接进程。FIFO又叫做命名管道(named PIPE)。

FIFO (First in, First out)为一种特殊的文件类型,它在文件系统中有对应的路径。当一个进程以读(r)的方式打开该文件,而另一个进程以写(w)的方式打开该文件,那么内核就会在这两个进程之间建立管道,所以FIFO实际上也由内核管理,不与硬盘打交道。之所以叫FIFO,是因为管道本质上是一个先进先出的队列数据结构,最早放入的数据被最先读出来,从而保证信息交流的顺序。FIFO只是借用了文件系统(file system,命名管道是一种特殊类型的文件,因为Linux中所有事物都是文件,它在文件系统中以文件名的形式存在。)来为管道命名。写模式的进程向FIFO文件中写入,而读模式的进程从FIFO文件中读出。当删除FIFO文件时,管道连接也随之消失。FIFO的好处在于我们可以通过文件的路径来识别管道,从而让没有亲缘关系的进程之间建立连接

注意: ls命令的输出结果中的第一个字符为p,表示这是一个管道。最后的|符号是由ls命令的-F选项添加的,它也表示是这是一个管道。

### 管道特点

半双工: 数据只能是单向流动的 (优点: 简单,缺点: 单向)
面向字节流: 管道中的数据是原生的字节流 (优点: 职责单一,缺点: 相比消息队列实现的 IPC,无法选择接收或丢弃发来的数据)

#### FIFO读写规则

1. 从FIFO中读取数据:  约定: 如果一个进程为了从FIFO中读取数据而阻塞打开了FIFO,那么称该进程内的读操作为设置了阻塞标志的读操作
2. 从FIFO中写入数据:  约定: 如果一个进程为了向FIFO中写入数据而阻塞打开FIFO,那么称该进程内的写操作为设置了阻塞标志的写操作。

详见: [http://blog.csdn.net/MONKEY_D_MENG/article/details/5570468](http://blog.csdn.net/MONKEY_D_MENG/article/details/5570468)

#### 信号种类

每种信号类型都有对应的信号处理程序(也叫信号的操作),就好像每个中断都有一个中断服务例程一样。大多数信号的默认操作是结束接收信号的进程；然而,一个进程通常可以请求系统采取某些代替的操作,各种代替操作是:

忽略信号。随着这一选项的设置,进程将忽略信号的出现。有两个信号 不可以被忽略:
SIGKILL,它将结束进程；  
SIGSTOP,它是作业控制机制的一部分,将挂起作业的执行。
  
恢复信号的默认操作。
  
执行一个预先安排的信号处理函数。进程可以登记特殊的信号处理函数。当进程收到信号时,信号处理函数将像中断服务例程一样被调用,当从该信号处理函数返回时,控制被返回给主程序,并且继续正常执行。
  
但是,信号和中断有所不同。中断的响应和处理都发生在内核空间,而信号的响应发生在内核空间,信号处理程序的执行却发生在用户空间。

那么,什么时候检测和响应信号呢？通常发生在两种情况下:

当前进程由于系统调用、中断或异常而进入内核空间以后,从内核空间返回到用户空间前夕；
  
当前进程在内核中进入睡眠以后刚被唤醒的时候,由于检测到信号的存在而提前返回到用户空间。
  
函数原型等详见: [http://www.cnblogs.com/biyeymyhjob/archive/2012/08/04/2622265.html](http://www.cnblogs.com/biyeymyhjob/archive/2012/08/04/2622265.html)

#### 信号本质

信号是在软件层次上对中断机制的一种模拟,在原理上,一个进程收到一个信号与处理器收到一个中断请求可以说是一样的。信号是异步的,一个进程不必通过任何操作来等待信号的到达,事实上,进程也不知道信号到底什么时候到达。

信号是进程间通信机制中唯一的异步通信机制,可以看作是异步通知,通知接收信号的进程有哪些事情发生了。信号机制经过POSIX实时扩展后,功能更加强大,除了基本通知功能外,还可以传递附加信息。

#### 信号来源

信号事件的发生有两个来源: 硬件来源(比如我们按下了键盘或者其它硬件故障)；软件来源,最常用发送信号的系统函数是kill, raise, alarm和setitimer以及sigqueue函数,软件来源还包括一些非法运算等操作。

#### 关于信号处理机制的原理(内核角度)

内核给一个进程发送软中断信号的方法,是在进程所在的进程表项的信号域设置对应于该信号的位。这里要补充的是,如果信号发送给一个正在睡眠的进程,那么要看该进程进入睡眠的优先级,如果进程睡眠在可被中断的优先级上,则唤醒进程；否则仅设置进程表中信号域相应的位,而不唤醒进程。这一点比较重要,因为进程检查是否收到信号的时机是: 一个进程在即将从内核态返回到用户态时；或者,在一个进程要进入或离开一个适当的低调度优先级睡眠状态时。

内核处理一个进程收到的信号的时机是在一个进程从内核态返回用户态时。所以,当一个进程在内核态下运行时,软中断信号并不立即起作用,要等到将返回用户态时才处理。进程只有处理完信号才会返回用户态 (上面的例子程序中,在步骤5中,解除阻塞后,先打印caught SIGQUIT,再打印SIGQUIT unblocked,即在sigprocmask返回前,信号处理程序先执行) ,进程在用户态下不会有未处理完的信号。

内核处理一个进程收到的软中断信号是在该进程的上下文中,因此,进程必须处于运行状态。如果进程收到一个要捕捉的信号,那么进程从内核态返回用户态时执行用户定义的函数。而且执行用户定义的函数的方法很巧妙,内核是在用户栈上创建一个新的层,该层中将返回地址的值设置成用户定义的处理函数的地址,这样进程从内核返回弹出栈顶时就返回到用户定义的函数处,从函数返回再弹出栈顶时,才返回原先进入内核的地方,接着原来的地方继续运行。这样做的原因是用户定义的处理函数不能且不允许在内核态下执行 (如果用户定义的函数在内核态下运行的话,用户就可以获得任何权限) 。

在信号的处理方法中有几点特别要引起注意。

第一,在一些系统中,当一个进程处理完中断信号返回用户态之前,内核清除用户区中设定的对该信号的处理例程的地址,即下一次进程对该信号的处理方法又改为默认值,除非在下一次信号到来之前再次使用signal系统调用。这可能会使得进程在调用signal之前又得 到该信号而导致退出。在BSD中,内核不再清除该地址。但不清除该地址可能使得进程因为过多过快的得到某个信号而导致堆栈溢出。为了避免出现上述情况。在 BSD系统中,内核模拟了对硬件中断的处理方法,即在处理某个中断时,阻止接收新的该类中断。

第二个要引起注意的是,如果要捕捉的信号发生于进程正在一个系统调用中时,并且该进程睡眠在可中断的优先级上 (若系统调用未睡眠而是在运行,根据上面的分 析,等该系统调用运行完毕后再处理信号) ,这时该信号引起进程作一次longjmp,跳出睡眠状态,返回用户态并执行信号处理例程。当从信号处理例程返回 时,进程就象从系统调用返回一样,但返回了一个错误如－1,并将errno设置为EINTR,指出该次系统调用曾经被中断。这要注意的是,BSD系统中内 核可以自动地重新开始系统调用,或者手如上面所述手动设置重启。

第三个要注意的地方: 若进程睡眠在可中断的优先级上,则当它收到一个要忽略的信号时,该进程被唤醒,但不做longjmp,一般是继续睡眠。但用户感觉不 到进程曾经被唤醒,而是象没有发生过该信号一样。所以能够使pause、sleep等函数从挂起态返回的信号必须要有信号处理函数,如果没有什么动作,可 以将处理函数设为空。

第四个要注意的地方: 内核对子进程终止 (SIGCLD) 信号的处理方法与其他信号有所区别。当进程正常或异常终止时,内核都向其父进程发一个SIGCLD 信号,缺省情况下,父进程忽略该信号,就象没有收到该信号似的,如果父进程希望获得子进程终止的状态,则应该事先用signal函数为SIGCLD信号设 置信号处理程序,在信号处理程序中调用wait。

SIGCLD信号的作用是唤醒一个睡眠在可被中断优先级上的进程。如果该进程捕捉了这个信号,就象普通信号处理一样转到处理例程。如果进程忽略该信号,则 什么也不做。其实wait不一定放在信号处理函数中,但这样的话因为不知道子进程何时终止,在子进程终止前,wait将使父进程挂起休眠。

信号生命周期

### 参考资料

作者：Vamei 出处：[http://www.cnblogs.com/vamei](http://www.cnblogs.com/vamei) 欢迎转载，也请保留这段声明。谢谢！
[http://www.cnblogs.com/vamei/archive/2012/10/10/2715398.html](http://www.cnblogs.com/vamei/archive/2012/10/10/2715398.html)  

[http://bbs.chinaunix.net/thread-1947211-1-1.html](http://bbs.chinaunix.net/thread-1947211-1-1.html)  
[https://blog.dusing.top/posts/3097150806.html](https://blog.dusing.top/posts/3097150806.html)  
[https://blog.dusing.top/posts/3097150806.html](https://blog.dusing.top/posts/3097150806.html)  
>[https://www.cnblogs.com/biyeymyhjob/archive/2012/11/03/2751593.html](https://www.cnblogs.com/biyeymyhjob/archive/2012/11/03/2751593.html)

---

[https://www.jianshu.com/p/c1015f5ffa74](https://www.jianshu.com/p/c1015f5ffa74)
