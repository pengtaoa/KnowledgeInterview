## 基本特征

### 1.并发
* 并发是指宏观上在`一段时间`内能同时运行多个程序
* 并行则指`同一时刻`能运行多个指令
* 并行需要硬件支持，如多流水线、多核处理器或者分布式计算系统。
* 操作系统通过引入进程和线程，使得程序能够并发运行。

### 2.共享
* 共享是指系统中的资源可以被多个并发进程共同使用。

## 进程与线程

### 1.进程

`进程`是`资源分配`的基本单位，用来管理资源(例：内存、文件、网络等资源)

`进程控制块` (Process Control Block, PCB) 描述进程的基本信息和运行状态，所谓的创建进程和撤销进程，都是对PCB的操作。(PCB`进程控制块`是描述进程的数据结构)

### 2.线程

`线程`是`独立调度`的基本单位。

一个进程里面可以有多个线程，他们共享进程资源。

QQ 和浏览器是两个进程，浏览器进程里面有很多线程，例如 HTTP 请求线程、事件响应线程、渲染线程等等，线程的并发执行使得在浏览器中点击一个新链接从而发起 HTTP 请求时，浏览器还可以响应用户的其它事件。


### 3.区别

（一）拥有资源
`进程`是`资源分配`的基本单位，但是线程不拥有资源，线程可以访问隶属进程的资源。

（二）调度
`线程`是`独立调度`的基本单位，在同一进程中，线程的切换不会影响进程的切换，从一个进程内的线程切换到另一个进程中的线程时，会引起进程切换。

（三）系统开销
由于创建或撤销`进程`时，系统都要为之分配或回收资源，如内存空间、I/O设备等，所付出的开销远大于创建或撤销线程的开销。

在进行`进程切换`时，设计当前执行进程CPU环境的保存及新调度进程CPU环境的设置，

`线程切换`时，只需保存和设置少量寄存器内容，开销很小。

（四）通信方面 
进程间通信(IPC)需要`进程同步`和`互斥手段`的辅助，以保证数据的一致性。而`线程间`可以通过直接`读/写`同一进程中的数据段(如`全局变量`)来进行通信。


### 4. JS 单线程带来的好处

在 JS 运⾏的时候可能会阻⽌ UI 渲染，这说明了两个线程是互斥的。这其中的原因是因为 JS 可以修改 DOM，如果在 JS 执⾏的时候 UI 线程还在⼯作，就可能导致不能安全的渲染 UI。这其实也是⼀个单线程的好处，得益于 JS 是单线程运⾏的，可以达到节省内存，节约上下⽂切换时间，没有锁的问题的好处。当然前⾯两点在服务端中更容易体现，对于锁的问题，形象的来说就是当我读取⼀个数字 15 的时候，同时有两个操作对数字进⾏了加减，这时候结果就出现了错误。解决这个问题也不难，只需要在读取的时候加锁，直到读取完毕之前都不能进⾏写⼊操作。


## 死锁
### 死锁概念、产生原因
死锁是指多个进程循环等待彼此占有的资源而无限期的僵持等待下去的局面。原因是：

 - 系统提供的资源太少了，远不能满足并发进程对资源的需求
 - 进程推进顺序不合适，互相占有彼此需要的资源，同时请求对方占有的资源，往往是程序设计不合理

### 死锁产生的必要条件
需要同时具有以下四个条件：
 - 互斥条件：即某个资源在一段时间内只能由一个进程占有，不能同时被两个或两个以上的进程占有
 - 不可抢占条件：进程所获得的资源在未使用完毕之前，资源申请者不能强行的从资源占有者手中夺取资源，而只能由该资源的占有者进程自行释放
 - 占有且等待条件：进程至少已经占有了一个资源，但又申请了一个新的被其他进程所占有的资源，此时处于等待状态
 - 循环等待条件：若干个进程形成环形链，每个都占用对方申请的下一个资源

## 死锁的处理策略
为使系统不发生死锁，必须设法破坏产生死锁的四个必要条件之一，或者允许死锁产生，但当死锁发生时能检测出思索，并有能力实现恢复。
一般有死锁的预防、死锁避免、死锁的检测与恢复三种方法。

1.  死锁预防：破坏导致死锁必要条件中的任意一个就可以预防死锁。例如，要求用户申请资源时一次性申请所需要的全部资源，这就破坏了保持和等待条件；将资源分层，得到上一层资源后，才能够申请下一层资源，它破坏了环路等待条件。预防通常会降低系统的效率。
2.  死锁避免：避免是指进程在每次申请资源时判断这些操作是否安全，例如，使用银行家算法。死锁避免算法的执行会增加系统的开销。
3.  死锁检测：死锁预防和避免都是事前措施，而死锁的检测则是判断系统是否处于死锁状态，如果是，则执行死锁解除策略。
4.  死锁解除：这是与死锁检测结合使用的，它使用的方式就是剥夺。即将某进程所拥有的资源强行收回，分配给其他的进程。

### 死锁预防：
 - 打破互斥条件：允许进程同时访问资源（有些资源就是不可以同时访问的，无实用价值）
 - 打破不可抢占条件：比如给进程设置优先级，高优先级的可以抢占资源(实现困难，降低系统性能)
 - 打破占有且等待条件：实行资源预分配策略，即进程在运行前一次性的向系统申请它所需要的全部资源(不可预测资源的使用，利用率低，降低并发性)
 - 破坏循环等待条件：采用这种策略，即把资源事先分类编号，按号分配，使进程在申请，占用资源时不会形成环路。所有进程对资源的请求必须严格按资源序号递增的顺序提出（限制和编号实现困难，增加系统开销，有些资源暂时不用也需要先申请，增加了进程对资源的占用时间）

### 死锁避免
允许进程动态的申请资源，但系统在进行资源分配前，应先计算此次资源分配的安全性。若此次分配不会导致系统进入不安全状态，则将资源你分配给进程，否则，让进程等待。

所谓安全状态，是指系统能按某种进程推进顺序，为每个进程分配其所需的资源，直至满足每个进程对资源的最大需求，是每个进程都可以顺序的完成。此时成P1P2P3...为安全序列，如果系统无法找到一个安全序列，则称系统处于不安全状态。

并非所有的不安全状态都是死锁状态，但当系统进入不安全状态后，便可能进入死锁状态；反之，只要系统处于安全状态，系统便可以避免进入死锁状态。
**银行家算法**是最著名的死锁避免算法。

### 死锁的检测
资源分配图&&死锁定理

### 死锁解除
1. 资源剥夺法。挂起某些思索进程，并抢占它的资源，将这些资源分配给其他的死锁进程。但应防止被挂起的进程长时间得不到资源时，而处于资源匮乏的状态。
2. 进程撤销法。强制撤销一个或一部分进程并剥夺这些进程的资源。撤销的原则可以按进程的优先级和撤销进程代价的高低进行。
3. 进程回退法。让一个或多个进程回退到足以回避死锁的地步，进程回退时资源释放资源而不是被剥夺。要求系统保持进程的历史信息，设置还原点。

## 银行家算法
当一个进程申请使用资源的时候，银行家算法通过先**试探分配**给该进程资源，然后通过安全性算法**判断分配后的系统是否处于安全状态**，若不安全则试探分配作废，让该进程继续等待。

 - 银行家算法中的进程：
   - 包含进程Pi的需求资源数量（也是**最大需求资源数量**，MAX）
   - **已分配**给该进程的资源A（Allocation）
   - **还需要**的资源数量N（Need=M-A）

 - Available为**空闲资源数量**，即资源池（注意：资源池的剩余资源数量+已分配给所有进程的资源数量=系统中的资源总量）

假设资源P1申请资源，银行家算法先试探的分配给它（当然先要看看当前资源池中的资源数量够不够）。
若申请的资源数量小于等于Available，然后接着判断分配给P1后剩余的资源，能不能使进程队列的某个进程执行完毕，若没有进程可执行完毕，则系统处于不安全状态（即此时没有一个进程能够完成并释放资源，随时间推移，系统终将处于死锁状态）。

若有进程可执行完毕，则假设回收已分配给它的资源（剩余资源数量增加），把这个进程标记为可完成，并继续判断队列中的其它进程，若所有进程都可执行完毕，则系统处于安全状态，并根据可完成进程的分配顺序生成安全序列。

```
如{P0，P3，P2，P1}表示将申请后的剩余资源Work先分配给P0
–>
回收（Work+已分配给P0的A0=Work）
–>
分配给P3–>回收（Work+A3=Work）
–>
分配给P2
–>
······
```
满足所有进程

如此就可避免系统存在潜在死锁的风险。

