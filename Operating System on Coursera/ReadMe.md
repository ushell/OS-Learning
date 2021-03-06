##第一周

#### 第1讲
1. 操作系统接到了用户的请求之后 就会根据用户提供的文件名 到磁盘上找到这个程序的相关信息 找到信息之后，会去检查这个程序 是不是一个可执行文件，因为有的时候我们 的一个文件它不是可执行文件，那么操作系统就不能执行这个文件 就会去报错，那么如果是一个可执行文件，操作系统才能正确地执行 在检查完类型之后，操作系统会根据程序首部信息 来确定代码和数据在这个可执行文件当中的位置 并计算出它相应的磁盘块地址


2. 操作系统首先要创建一个新的进程 并将helloworld程序的可执行文件格式 映射到该进程结构 表示由该进程来执行这个helloworld程序 做完了这件事情，操作系统就把控制权 交给了调度程序，我们假设调度程序 正好选中了helloworld程序，那么 由操作系统为这个helloworld程序设置CPU 上下文环境，并跳到程序的开始之处 准备执行这条这个程序，那么下一个指令周期就是执行helloworld程序了

#### 第2讲
1. 方便使用: 用户界面和编程接口
2. 管理资源要用到数据结构和算法:  
确定资源分配策略:
(1) 静态分配资源: 会导致资源的浪费
(2) 动态分配资源
3. 资源管理角度的五大基本功能:
(1) 进程线程管理 (CPU管理)
(2) 存储管理
(3) 文件管理
(4) 设备管理
(5) 用户接口
4. 硬件操作复杂繁琐. 操作系统对硬件抽象, 提高可编程性

#### 第3讲
1. 操作系统的特征:
(1) 并发: 处理多个同时性的活动.
(2) 共享: 操作系统与多个用户的程序共同使用计算机系统的资源. 互斥共享 和 同时共享
(3) 虚拟: 一个物理实体映射为多个对应的逻辑实体 -- 分时或者分空间
(4) 随机: 必须随时对以不可预测的次序发生的事件进行响应并处理. 
进程的运行速度不可预知. 难以重现系统在某个时刻的状态.

#### 第4讲
windows架构几乎所有的硬件相关操作都被封装在: HAL(硬件抽象层), kernal(内核层)两层

#### 第5讲
操作系统分类:   
- 批处理操作系统: Spooling系统(simultaneous peripheral operation on-line 同时的外部设备联机操作计数, 1961),是用磁盘做缓冲 将输入、 计算、 输出分别组织成独立的任务流 在一台计算机上执行，使得输入、 输出和计算真正并行.   
输入进程 作业调度 作业处理 输出进程
- 分布式操作系统: 或以计算机网络为基础, 或以多处理机为基础, 基本特征是处理分布在不同的计算机上

## 第二周
#### 第6讲
1. CPU中的寄存器可以分为`用户可见寄存器`和`控制和状态寄存器`. 后者用于控制处理器的操作, 由操作系统代码使用.在某种特权级别下可以访问 修改.  
常见的控制和状态寄存器:   
* PC(program count)程序计数器, 记录将要取出的指令的地址  
* IR(instruction register)指令寄存器, 记录最近取出的指令  
* PSW(program state word) 程序状态字, 记录处理器的运行状态如条件码 模式 控制位等信息.   

2. 操作系统的需求: 保护  
实现保护与控制. 需要硬件提供基本运行机制: 
- 处理器具有特权级别, 能在不同特权级运行不同的指令
- 硬件机制可分离OS和用户程序

操作系统两种CPU状态: 内核态 用户态  
指令分为特权指令(privilege command)和非特权指令

e.g. X86架构支持4个处理器特权级别: R0(内核态), R1,R2, R3(用户态)

用户态->内核态唯一途径: 中断/异常/陷入机制  
内核态->用户态: 设置PSW  
特殊指令: 陷入指令(访管指令,因为内核态也称为supervisor mode). 提供给用户程序的接口, 用于调用操作系统的功能(int, trap, syscall, sysenter/sysexit)

异常又分为:  
- 陷入(trap): 有意识安排的. 返回的时候返回到下一条指令.
- 故障(fault): 可恢复的错误. 返回的时候返回到当前指令.
- 终止(abort): 不可恢复的错误. 不会返回.

在每条指令执行周期的最后时刻 扫描中断寄存器 查看是否有中断信号. 
中断向量: 一个内存单元, 存放中断处理程序`入口地址`和程序运行时所需的`处理机状态字`. 


3. X86架构对异常/中断的处理:  
(1) 确定与中断或异常关联的向量i
(2) 通过IDTR寄存器找到IDT表, 获得中断描述符. 进而获得偏移地址.
(3) 从GDTR寄存器获得GDT地址, 结合中断描述符中的段选择符, 在GDT表获取对应的段描述符. 从该段描述符中得到中断或异常处理程序所在的段基址. 
(4) 特权级检查
    - 检查是否发生了特权级的变化. 如果是, 则进行堆栈切换. 必须使用与新的特权级相关的栈. 



4. 系统调用system call
全称操作系统编程调用. 
系统调用,库函数, API, 内核函数的关系:  
通常情况下, 应用程序通过调用C库函数或者API接口, 间接进行系统调用.应用程序也可以直接调用系统 但是比较少见. 内核函数就是系统调用的处理程序. 

5. 用户程序参数传递给内核:  
- 由陷入指令自带参数
- 通过通用寄存器传递参数: 通用寄存器用户程序和内核程序都可以访问
- 在内存中开辟专用堆栈区

`int 0x80  ; 汇编 引发一次系统调用`

6. Intel对中断描述符的分类   
- 任务门(task gate) 
    当中断信号发生时，必须取代当前进程的那个进程的TSS选择符存放在任务门中。
- 中断门(interruptgate) 
    包含段选择符和中断或异常处理程序的段内偏移量.当控制权转移到一个适当的段时，处理器 清IF标志，从而关闭将来会发生的可屏蔽中断.
陷阱门(Trap gate)
    与中断门相似，只是控制权传递到一个适当的段时处理器不修改IF标志. 
 对于中断门，在转移过程中把IF置为0，使得在处理程序执行期间屏蔽掉INTR中断(当然，在中断处理程序中可以人为设置IF标志打开中断，以使得在处理程序执行期间允许响应可屏蔽中断)；对于陷阱门，在转移过程中保持IF位不变，即如果IF位原来是1，那么通过陷阱门转移到处理程序之后仍允许INTR中断。因此，中断门最适宜于处理中断，而陷阱门适宜于处理异常。

 TEST:
 1. 每一个中断或异常向量在这个系统表中有对应的中断或异常处理程序入口地址


## 第三周
1. 多道程序设计(Multiprogramming)
允许多个程序同时进入内存并运行.

2. 并发环境: 一段时间间隔内, 单个CPU上有两个或两个以上程序同时处于开始运行但尚未结束的状态. 并且次序不是事先确定的.

3. 进程(process): 用于刻画并发环境下程序的执行. 进程是具有独立功能的程序关于某个数据集合上的一次运行活动. 是系统进行__资源分配__和__调度__的独立单位. 又称任务.

4. 进程控制块(process control block): 进程描述符, 进程属性. 操作系统用于管理控制进程的一个专门的数据结构. 记录进程的各种属性, 描述进程的动态变化过程. PCB是操作系统感知进程存在的唯一标志. 所有进程的PCB集合称为进程表.  
- 进程描述信息: 进程标识符, 进程名, 用户标识符, 进程组. 
- 进程控制信息: 当前状态, 优先级, 代码执行入口程序, 程序磁盘地址, 运行统计信息, 进程间同步和通信. 
- 所拥有的资源和使用情况: 虚拟地址空间的状况, 打开文件列表.
- CPU现场信息: 寄存器值(通用寄存器, 程序计数器PC, PSW), 指向该进程列表的指针. 

5. 进程的三个基本状态: 
- 运行态(running): 占有CPU,并在CPU上运行.
- 就绪态(ready): 已经具备运行条件, 但由于没有空闲CPU, 而暂时不能运行.
- 等待态(waiting/blocked): 阻塞态 封锁态 睡眠态.因等待某一事件而暂时不能运行. e.g.等待读盘结果

运行态只能由就绪态进入.等待态等待的信号到达后进入就绪态.  
还有其他状态比如创建态和终止态, 还有挂起态. 

6. 进程控制操作完成进程各状态之间的转换, 由具有特定功能的原语完成(primitive). 完成某种特定功能的一段程序, 具有不可分割性和不可中断性. 即原语的执行必须是连续的, 在执行工程中不允许被中断. 

7. 进程创建:
* 给新进程分配一个唯一标识及进程控制块(PCB)
* 为进程分配地址空间
* 初始化进程控制块(设置默认值)
* 设置相应的队列指针(把新进程添加到就绪队列链表中)
* Unix: fork/exec; windows:CreateProcess

8. 收回进程:
* 收回进程所占用的资源
* 撤销该进程的PCB
* Unix: exit; windows: TerminateProcess

9. 进程阻塞: 
- 当被等待的事件尚未发生时, 由进程自己执行阻塞原语, 使自己由运行态变为阻塞态.
- Unix: wait; windows: WaitForSingleObject

10. Unix Fork()函数的实现
- 为子进程分配一个空闲的进程描述符(proc结构)
- 分配给子进程唯一标识符: PID
- 以一次一页的方式 复制父进程地址空间 (Linux采用了写时复制计数copy on write)
- 从父进程那里继承共享资源, 如打开的文件和当前工作目录等
- 将子进程的状态设置为就绪, 插入到就绪队列
- 对子进程返回标识符0
- 对父进程返回标识符PID
- fork执行完后, 进程一分为二

11. 进程分为 系统进程, 用户进程; 前台进程, 后台进程; CPU密集型进程(玩游戏, 画面渲染, 大量计算), I/O密集型进程. Unix进程家族树: init为根. Windows进程地位相同.

12. Context switch 进程切换
将CPU硬件状态从一个进程切换到另一个进程. CPU运行时, 其硬件状态保存在CPU上的寄存器中. 进程不运行时, 这些寄存器的值保存在进程控制块PCB中. 

13. 线程(thread):   
进程可以让程序并发执行, 为什么要再派生出线程:(1)应用的需要 (2)开销的考虑 (3)性能的考虑
有三个线程的字处理软件: (1)输入(2)排版(3)定时保存,存储  
多线程web服务器:(有并发, 阻塞系统调用)  
- 分派线程(dispatcher thread): 监听客户端,分发
- 工作线程(worker thread): 查找

也可以使用单线程进程: 无并发, 阻塞系统调用(速度慢)  或者有限状态机: 有并发, 非阻塞系统调用, 中断 (编程模型复杂)  
进程开销大, 限制了并发度的提高. 线程开销小, 创建撤销速度快, 线程切换时间短, 不需要系统介入, 相互通信无须调用内核.

因此线程是进程中的一个运行实体, 是CPU的调度单位. 线程有标识符ID, 状态及状态转换, 需要保存上下文, 有自己的栈和栈指针, 共享所在进程的地址空间和其他资源, 可以创建和撤销另一个线程.


14. 线程的实现
(1) 用户级线程(user level thread): 内核管理还是进程, 不知道线程的存在. 线程切换不需要内核干预. Unix. 多个线程不能运行在多个CPU上  
(2) 核心级线程(kernel level thread): 内核管理所有线程, 既有进程表, 又有线程表. 内核维护线程进程的上下文. 线程切换需要内核支持. Windows
(3) 混合模型: 线程创建在用户空间完成, 线程调度在内核空间完成. Solaris.

15. 可再入程序(可重入)
可被多个进程同时调用的程序, 它是纯代码的, 即在执行过程中自身不改变, 调用它的进程应该提供数据区. 

16. 测验:
(1) 假设某单处理器计算机系统中有10个进程，则系统中处于等待状态的进程最多有几个？
运行状态最多 1 最少0  
等待状态最多 n 最少0  
就绪状态最多 n-1 最少0  
(2) 进程控制原语: 进程控制原语包括：进程的建立、进程的撤销、进程的等待和进程的唤醒,改变进程优先级。
(3) 当某个正在执行的进程需要进行I/O操作时，可以通过调用挂起(错误, 阻塞)原语将自己从运行状态变为等待状态。
(4) 线程共享的环境包括：进程代码段、进程的公有数据(利用这些共享的数据，线程很容易的实现相互之间的通讯)、进程打开的文件描述符、信号的处理器、进程的当前目录和进程用户ID与进程组ID。   
栈是每个线程独有的，保存其运行状态和局部自动变量的。栈在线程开始的时候初始化，每个线程的栈互相独立，因此，栈是 thread safe 的。
(5) 撤销进程的具体操作过程是：找到要被撤销进程的PCB，将它从所在队列中消去，撤销属于该进程的一切“子孙进程”，释放被撤销进程所占用的全部资源，并消去被撤销进程的PCB。


## 第四周
1. CPU调度: 其任务是控制,协调进程对CPU的竞争. 如果没有就绪进程, 系统会安排空闲进程或者idle进程
2. 上下文切换开销(cost): 直接开销(内核完成切换所需要的时间, 包括保存,恢复寄存器, 切换地址空间). 间接开销(原来进程所使用的高速缓存 cache, 缓冲区缓存 buffer cache, TLB(Translation Lookup Buffer)等的失效)
3. 调度算法衡量指标: 
- 吞吐量(Troughput): 单位时间完成进程的数目
- 周转时间(Turnaround Time, TT): 每个进程从提出请求到运行完成的时间
- 响应时间(Response Time, RT): 从提出请求到第一次响应的时间
4. 占用CPU的方式: 抢占式和非抢占式.
* 可抢占式(Preemptive): 更高优先级的进程就绪时, 系统强行剥夺正在运行的进程的CPU使用. 
* 不可抢占(Non-preemptive): 除非由他自身完成或者主动放弃CPU使用, 不然不能被剥夺对CPU的使用

5. 时间片(Time Slice or quantum 量子的)
6. 主要区别在作业执行过程中，由用户使用操作系统提供操作控制命令。就是给一个命令就执行一个，而批处理就要预编。
7. 批处理系统中采用的调度算法(主要指标: 吞吐量, 响应时间, 平均执行时间)
- 先来先服务(First Come First Service)
非抢占, 按照进程就绪的先后顺序使用CPU
- 最短作业优先(Shortest Job First)
具有最短完成时间的进程优先完成. 可以得到最短的平均周转时间, 不过可能有不公平的现象. 
- 最短剩余时间优先(Shortest Remaining Time Next)
最短作业优先的抢占式算法, 当一个新就绪的进程的运行时间比当前正在运行的进程的剩余时间还要短的情况下, 系统抢占当前进程, 选择新就绪的进程执行
- 最高响应比优先(Highest Response Ration Next)
综合算法, 计算响应比 = 周转时间/处理时间 = (处理时间+等待时间) / 处理时间 


8. 交互式系统中采用的调度算法(指标: 响应时间 公平):
- 轮转调度(Round Robin)
周期性切换, 每个进程分配一个时间片, 通过时钟中断进行轮换. 公平, 由于进程切换, 不合适的时间片造成系统开销过大.
- 最高优先级调度(Highest Priority First)
选择优先级最高的进程进行执行. 抢占式的优先级调度算法会带来优先级翻转的问题, 解决方法包括优先级继承和使用中断禁止等方法.
- 多级反馈队列(Multilevel Feedback Queue)
    * 设置多个就绪队列, 其中第一个的优先级最高;
    * 不同就绪队列的进程分配长度不同的时间片, 第一级队列时间片最少, 随着优先级降低, 时间片增大
    * 调度的时候从高优先级的队列进行调度
    * 当一个新创建的进程就绪后, 进入第一级队列
    * 如果对应的时间片用完还没有执行完, 就放入第二级队列 以此类推.
    * 由于阻塞而放弃CPU的进程放入等待队列, 一旦等待的事件到达, 该进程回到原来的那级就绪队列
    * 抢占或者非抢占式都可以被设计
- 最短进程优先(Shortest Process Next)
类似最短作业优先

9. windows的线程调度算法
调度单位是线程, 采用基于动态优先级的, 抢占式调度, 结合时间配额的调整. 就绪线程按照优先级进入队列,系统选择优先级最高的就绪线程运行, 同一优先级的各线程按时间片轮转进行调度. 多CPU系统中允许多个线程并行执行.  
Windows使用32个优先级: 0系统线程, 1-15可变优先级, 16-31实时优先级(一旦确定就不能改变)

TEST: 优先数决定了优先级. 

## 第五周
1. 并发: 进程的执行是间断性的, 相对执行时间不可预测. 执行的结果和它的执行速度有关, 是不确定的. 
2. 竞争条件: 两个或多个进程读写某些共享数据, 而最后的结果取决于进程运行的精确时序. 临界区: 各个进程中对某个临界资源(互斥资源)实施操作的程序片段.  
所谓优先级翻转就是一个`低优先级`的进程使用`临界区`资源时,即使现在有高优先级的进程想使用临界区资源,也只能被阻塞.可是此时假如有中优先级但不使用临界区资源的进程就绪, CPU就要先处理中优先级的进程, 这就造成了高优先级的持续等待.
3. 进程同步: 系统中多个进程中发生的事件存在某种时序关系, 需要相互合作,共同完成某项任务. 比如一个进程需要另一个进程的消息, 在没有得到消息之前, 该进程进入阻塞态. 
4. 生产者/消费者问题:又称有界缓冲区问题. 指一个或者多个生产者产生某种数据并放置在缓冲区中, 有消费者从缓冲区中取数据, 每次只能有一个生产者或者消费者对缓冲区进行操作.   
要解决的问题: 满的时候不允许生产者再去存放数据. 空的时候不允许消费者再取走数据.  
要避免忙等待: 等待的时候让出CPU, 所以引入睡眠和唤醒原语. 对于生产者, 如果缓冲区满了, 就让他sleep()
5. 信号量: 一个用于进程间传递信息的整数值. 对信号量可以执行的操作只有初始化, P操作(test)和V(increment)操作.  
P操作(Down, SemWait): 信号量减1, 判断如果<0, 进程设置为阻塞态, 放入等待队列.   
V操作(Up, SemSignal): 信号量+1, 判断如果>=0, 从等待队列中唤醒一个进程, 改变其状态为就绪态, 并插入就绪队列. PV操作是原语操作, 关闭中断. 最初提出二元信号量, 解决互斥问题, 后来推广到计数信号量, 解决同步问题. 

PV操作解决互斥问题:  
- 分析并发进程的关键活动, 划分临界区
- 设置信号量mutex, 初值为1
- 临界区前使用P操作
- 临界区之后使用V操作.

## 第六周
1. 管程(Monitors 也称监视器): 信号量机制不足(编写复杂,易出错). 一种高级同步机制. 由关于共享资源的一些数据结构 及 在这之上的操作的一组过程(函数)组成. __进程__只能通过调用管程中的函数来访问数据结构.   
作为一种同步机制, 管程是互斥进入的, 来保证管程中数据结构的数据完整性. 管程中还要设置条件变量及等待唤醒操作来解决同步问题, 可以让一个进程或线程在条件变量上等待, 或者通过发送信号让等待的进程或者线程唤醒. 

2. Hoare管程: 因为管程是互斥进入的, 所以当一个进程试图进入一个已经被占用的管程时, 应当在管程的入口处等待. 因此管程的入口设置一个入口等待队列. 如果P进程唤醒Q进程, 那么P等待Q执行, 如果Q进程执行中又唤醒了进程R, 则Q等待R执行. 如此, 管程内部可能会出现多个等待进程. 因此, 在管程内可以设置一个进程等待队列, 称为紧急等待队列. 紧急等待队列的优先级高于入口等待队列的优先级.   
条件变量: 在管程内部说明和使用的一种特殊类型的变量. 对于条件变量可以执行wait和signal操作. 变量设为c:  
`wait(c)`: 如果紧急等待队列非空, 则唤醒第一个等待者. 否则就要释放进程的互斥权, 执行此操作的进程进入C链(条件变量)末尾.   
`signal(c)`: 如果c链为空, 此操作为空操作, 执行此操作的进程继续执行, 否则唤醒第一个等待者, 执行此操作的进程进入紧急等待队列的末尾. 

3. 管程的应用: 
- 实现: 直接构造(效率高) 或者 间接构造(用某种已经实现的同步机制去构造, 例如用信号量和PV操作构造). 

4. MESA管程: 因为Hoare管程有缺点就是额外的进程切换(两次).   
解决方法: signal -> notify: 当一个正在管程中的进程执行notify时, 它使得紧急等待队列获得通知, 发信号的进程继续执行. 
notify结果: 位于条件队列头的进程在将来合适的时候, 且当处理器可用时恢复执行. 但是应注意, 由于不能保证在它之前没有别的进程进入管程, 因而这个进程必须重新检查条件. 用while循环取代if语句. 
缺点: 导致对条件变量至少多一次额外的检测, 而且对等待进程在notify之后何时运行没有任何限制. 

引入broadcast: 使所有在该条件等待的进程都被释放并进入就绪队列. 
MESA管程优于Hoare管程之处在于更安全. 由于即使在收到信号后也都要重新检查管程变量, 并且由于使用了while结构, 一个进程不正确的broadcast或者notify不会导致收到信号的程序出错. 


5. 通信机制inter-Process Communication: 
- 引入原因: 信号量和管程的不足(不能传递大量信息, 比如数组), 不适用于多处理器情况(管程不支持多处理器). 
- 进程通信机制:包括: 消息传递, 共享内存, 管道, 套接字, 远程过程调用. 
- 消息传递: send 和 receive原语
- 共享内存: 利用读者写者机制解决互斥问题. 
- 管道通信方式: 利用一个缓冲传输介质(内存或者文件)连接两个相互通信的过程. 

6. Linux的IPC机制
- 原子操作: 不可分割, 执行完之前不会被打断. 常用于实现对资源的计数. 
- 屏障(barrier): 一种同步机制, 用于对一组线程进行协调. 应用场景: 一组线程协同完成一项任务, 需要所有线程都到达一个汇合点后再一起向前推进. 




## 第七周
1.地址重定位Relocation: 
- 原因: 进程中的地址不是最终的物理地址, 在进程运行前无法确定物理地址(因为不能确定进程被装载到内存的什么地方), 所以需要地址重定位的支持. 
- 逻辑地址(相对地址, 虚拟地址): 不能用逻辑地址读取信息
- 物理地址(绝对地址, 实地址): 可以直接寻址.
- 通过把逻辑地址转换为物理地址, 称为地址重定位
- 静态重定位: 当用户程序加载到内存时, 一次性实现逻辑地址到物理地址的转换. 
- 动态重定位: 进程执行过程中进行地址转换, 即逐条指令执行时完成地址转换. 需要硬件部件支持.
- 动态重定位需要内存管理单元(Memory Management Unit, MMU)的一个寄存器的基址

2. 物理内存
- 划分: 等长和不等长划分
- 等长划分: 每个区域可以用位图(Bit Map)中的1位表示空闲或占用
- 不等长划分: 空闲区表和已分配区表(表中每一项记录了空闲区的起始地址, 长度, 标志)
- 内存分配算法: 
    + 首次适配(First fit): 在空闲区表中找到第一个满足进程要求的空闲区
    + 下次适配(next fit): 从上次找到的空闲区处继续查找
    + 最佳适配(Best fit): 查找整个空闲区表,找到能够满足进程要求的最小的空闲区
找到后还要重新把该空闲区分为两部分, 一部分供进程使用, 另一部分形成新的空闲区
- 内存回收算法: 当某一块归还后, 前后空闲空间合并, 修改空闲区表

3. 伙伴系统
- 一种经典的内存分配方案. Linux低层内存管理采用.
- 主要思想: 将内存按2的幂进行划分, 组成若干空闲块链表. 查找该链表找到能满足进程需求的最佳匹配块. 
- 算法: 
    + 将整个可用空间看作一块: 2^u
    + 假设进程申请的空间大小为s, 如果满足`2^(u-1) < s <= 2^u`, 则分配整个块. 否则, 将块划分为两个大小相等的伙伴, 大小为2^(u-1). 
    + 一直划分下去直到产生大于或者等于s的最小块

4. 基本内存管理方案:
(1) 连续区: 
- 单一连续区: 一段时间只有一个进程在内存, 简单, 内存利用率低.
- 固定分区: 把内存空间分割成若干区域, 称为分区. 每个分区大小可以相同可以不同, 分区大小固定不变, 每个分区只装一个进程. 
- 可变分区: 根据进程需要, 把空闲的内存空间分割出一个分区, 分配给该进程. 剩余部分成为新的空闲区. 但是会导致内存碎片(进程之间, 称为外碎片). 
- 碎片问题解决: 碎片是指很小的,不易利用的空闲区, 会导致内存利用率下降. 解决方案: 紧缩计数(memory compaction). 在内存移动程序, 将所有小的空闲区合并为大的空闲区. 紧缩时考虑的问题: 系统开销, 移动时机.

(2) 不连续区
- 页式存储管理方案: 用户进程地址空间被划分为大小相等的部分, 称为页(page)或页面, 从0开始编号. 内存空间按同样的大小划分为大小相等的部分, 称为页框. 内存分配规则: 以页为单位进行分配, 并按照进程需要的页数来分配, 逻辑上相邻的页, 物理上不一定相邻. 典型尺寸: 4K, 4M.  
页表项: 记录了逻辑页号与页框号的对应关系.  
地址转换过程: 每个进程一个页表, 存放在内存. 逻辑地址分为页号和页内偏址. 用页号查页表, 得到页框号, 再与页内偏址组合成物理地址.  
- 段式存储方案: 按程序自身的逻辑关系划分为若干个程序段, 每个程序段都有一个段名. 内存空间被动态划分为长度不相等的区域, 称为物理段, 每个物理段由起始地址和长度确定.  内存分配规则, 以段为单位进行分配, 每段在内存中占据连续空间, 但各段之间可以不相连.
- 段页式存储方案: 综合段式, 页式的优点. 用户进程划分时先按段划分, 每一段再按页划分. 段号-段内地址(段内地址又划分为页号-页内偏址). 它既需要段表又需要页表.

5. 交换技术
解决在较小的内存空间运行较大的进程. 例如内存紧缩技术, 覆盖技术, 交换技术, 虚拟存储技术. 
- 覆盖技术(overlaying): 解决了程序大小超过内存总和的问题. 因此程序的执行过程中, 程序的不同部分在内存中互相替代. 因此要求程序员根据其自身的逻辑结构, 将那些不会同时执行的程序段共享同一块内存区域. 要求程序各模块之间有明确的调用结构. 
- 交换技术(swapping): 内存空间紧张时, 系统将内存中的某些进程暂时移到外存, 把外存中某些进程换到内存, 占用前者所占用区域.   
交换区: 一般系统会指定一块特殊的磁盘区域作为交换空间(swap place), 包含连续的磁道, 操作系统可以使用底层的磁盘读写操作对其进行高效的访问.   

## 第八周
1. 虚拟存储技术: 进程运行时, 先将其一部分装入内存, 另一部分暂留在磁盘, 当要执行的指令或访问的数据不在内存上时, 由操作系统自动完成将他们从磁盘掉入内存的工作. 
存储器层次结构: 寄存器 -> 高速缓存CacheL1 -> Cache L2 -> Cache L3 -> 内存(RAM) -> 磁盘(Disk)  
虚拟内存就是把内存和磁盘有机结合起来. 

进程的地址空间(虚存) 分部分存在于寄存器, cache, 内存, 磁盘中.

虚拟存储技术 + 页式存储管理方案: 虚拟页式存储管理系统. 

页：将进程划分的块，对应的大小就叫页面大小。  
页框：将内存划分的块。  
页和页框二者一一对应，一个页放入一个页框，（理论上）页的大小和页框的大小相等。页框数小于页数, 所以要置换.  
页表：就是一个页和页框一一对应的关系表。【存放在内存中】 关系表只是起到一个索引的作用，说白了就是能根据关系表能查到某一个页面和哪一个页框所对应。

页面数目 = 进程大小/页面大小 = 逻辑地址表示的大小/页面大小

2.
虚拟地址空间可以划分为若干个页面, 物理地址空间由若干个页框组成. 由页表记录哪些虚拟地址页面已经加载到内存了(物理地址空间)

页表表项设计:  
- 页表由页表项组成
- 页表项由:  
    + 页框号: 内存块号, 物理页面号, 页帧号
    + 有效位: 驻留位, 中断位, 表明该页是在内存(1)还是在磁盘(0).在内存上, 就可以直接访问得到物理地址以后的空间, 不在内存上, 就要先把该页读入内存, 更新该位, 然后从内存访问.
    + 访问位: 引用位
    + 修改位: 此页在内存中是否被修改过
    + 保护位: 读/可读写

页表项通常由硬件设计. 页表页在内存中如果不连续存放, 则需要引入页表页的地址索引表, 即页目录(page directory). 即多级页表结构. 

虚拟地址 = 页目录偏移(31-22) + 页表偏移(21-12) + 页内偏移(11-0)

引入反转(倒排列表): 从物理地址空间出发, 系统建立一张页表, 页表项记录进程i的某虚拟地址(虚页号)与页框号的映射关系. 

3.
MMU: 内存管理单元, 用于将虚拟地址转换成物理地址.
问题: 访问页表就是要两次或者两次以上的内存访问, CPU速度得不到充分利用. 如何改善:   
利用程序访问的局部性原理 -> 引入快表(TLB)

TLB: Translation Look-ahead Buffer. 在CPU中引入的高速缓存(Cache), 可以匹配CPU的处理速率和内存的访问速度. 除连线寻址机制外, 还有接线逻辑, 能按特定的匹配标志在一个周期内对所有的字同时进行比较. 
相联存储器(Associative Memory): 特点是可以按内容进行查找. 保存正在运行进程的页表的子集. 

4. 页错误(page fault)
页面错误, 页故障, 页面失效. 指地址转换过程中硬件产生的异常.   
具体原因:  
所访问的虚拟页面没有调入物理内存: 缺页异常.  
页面访问违反权限: 读/写, 用户/内核; 错误地访问地址. 

缺页异常: 地址映射过程中, 硬件检查页表发现所要访问的页面不存在, 则产生该异常. 陷入内核态, 由操作系统接管, 执行缺页异常处理程序, 获得磁盘地址, 启动磁盘, 将该页调入内存.


5. 软件相关策略
驻留集: 给每个进程分配的页框.
页框锁定: 采用虚拟内存后, 可能会造成开销, 对于进程的运行时间变得不确定. 想变得稳定, 可以通过给每个页框增加一个锁定位,通过设置相应的锁定位, 不让操作系统将进程使用的页面换出内存,避免产生由交换过程带来的不确定的延迟. 例如操作系统核心代码, 关键数据结构, I/O缓冲区, 特别是正在I/O的进程页面. Windows下的VirtualLock 和 Virtualunlock

6. 页面置换算法
又称页面淘汰算法. 
- 最佳页面置换算法(OPT):置换以后不再需要的页面或者在最远的将来才会用到的页面. 不可实现. 意义是作为一种标准来衡量其他页面置换算法.
- 先进先出算法(FIFO): 选择在内存中驻留时间最长的页面置换他
- 第二次机会算法(SCR): 按照先进先出算法选择某一页面, 检查其访问位R, 如果为0, 则置换该页, 如果为1, 则给第二次机会, 并将访问位置0.
- 时钟算法: 类似第二次机会算法, 但是把所有页框组织成环形, 通过指针来表示下一次替换的页面.
- 最近未使用算法(NRU): 选择在最近一段时间内未使用过的一页并置换. 实现是通过设置页表表项的两位(一般是硬件, 没有的话用软件模拟), 访问位R, 修改位M. 启动一个进程时, R, M位置0. R被定期清零. 
- 最近最少使用页面置换算法(Least Recently Used): 最常用, 最接近OPT. 选择最后一次访问时间距离当前时间最长的一页并置换. 即置换未使用时间最长的一页. 实现的时候需要时间戳, 开销很大. 
- 最不经常使用算法(NFU: Not Frequently Used): 选择访问次数最少的置换掉.
- 老化算法(Aging): 软件模拟LRU, 计数器在加R前先右移一位, 然后把R加到最左端. 随着时间推移, 如果之前有1, 1会越来越靠左, 起的作用越来越小.

Belady现象: FIFO页面置换算法会产生异常现象, 即: 分配给进程的物理页面数增加时, 缺页次数反而变多.

7. 页面置换算法之工作集算法
影响缺页次数的因素: 页面置换算法, 页面本身的大小, 程序的编制方法, 分配给进程的页框数量.
颠簸(Thrashing, 抖动): 虚存中, 页面在内存与磁盘之间频繁调度, 使得调度页面的时间比进程实际运行的时间还多, 这样导致系统效率下降, 称为颠簸或者抖动. 

工作集模型(working set): 如果能为进程提供与活跃页面数相等的物理页面数, 则可减少缺页中断次数. 工作集定义为: 一个进程当前正在使用的页框集合. 
工作集算法: 基本思路是找出一个不在工作集中的页面并置换他. 



8. 内存映射文件技术和写时复制技术
进程通过系统调用(mmap)将一个文件或部分映射到其虚拟地址空间的一部分, 访问这个文件就像访问内存中的一个大数组, 而不是对文件进行读写. 多数情况下, 在映射共享的页面时不会实际读入页面的内容, 而是在访问页面时, 页面才会被每次一页的读入, 磁盘文件则会被当做后备存储. 当进程退出或显式的解除文件映射时, 所有被修改页面会写回文件. 

写时复制技术: 新复制的页面对执行写操作的进程是私有的, 对其他共享写时复制页面的进程是不可见的.


## 第九周 文件系统

1. 
文件是对磁盘的抽象, 文件是指一组带标识(其实就是文件名)的, 在逻辑上有完整意义的信息项的序列.   
信息项: 构成文件内容的基本单位, 单个字节或者多个字节. 各信息项之间是顺序关系.  
文件内容的意义: 由文件建立者和使用者解释. 

文件的分类:  
按文件性质和用途分类(unix): 普通文件, 目录文件, 特殊文件(设备文件), 管道文件, 套接字.   
普通文件: 用户自己建立的文件, 包含了用户的信息, 一般为ASCII或二进制文件.
目录文件: 管理文件系统的系统文件
特殊文件: (1)字符设备文件, 和输入输出有关, 用于模仿串行IO设备, 例如终端, 打印机, 网卡等  
         (2)块设备文件: 磁盘.

典型的文件逻辑结构:   
- 流式文件: 构成文件的基本单位是字符. 文件是有逻辑意义的, 无结构的一串字符的集合. 
- 记录式文件: 文件由若干个记录组成, 可以按记录进行读写,查找等操作. 每条记录有其内部结构. 

对文件的存取(访问)一般是顺序存取(访问)和随机存取(访问)

2. 文件的存储介质
物理块(块block, 簇cluster): 信息存储, 传输, 分配的独立单位. 存储设备划分为大小相等的物理块, 统一编号. 
任何时候只有一个磁头处于活动状态, 输入输出数据流以位串形式出现.   
物理地址形式: 磁头号(盘面号), 磁道号(柱面号), 扇区号(一个扇区一般是512个字节)

磁盘访问:  一次访问请求要知道: 读/写操作, 磁盘地址(设备号, 柱面号, 磁头号, 扇区号), 内存地址(源/目)
完成过程由三个动作组成:  
- 寻道: 磁头移动定位到指定磁道
- 旋转延迟: 等待指定扇区从磁头下旋转经过
- 数据传输: 数据在磁盘与内存之间的实际传输

3. 磁盘空间管理
有关数据结构:  
位图: 用一串二进制位反映磁盘空间中分配使用情况, 每个物理块对应1位, 分配的物理块(分配出去了)为0, 否则为1. 申请物理块时, 可以在位图中查找为1的位, 返回对应的物理块号. 归还时, 将对应的位设为1.

空闲块表: 将所有空闲块记录在表中, 即空闲块表, 主要是两项内容, 起始块号, 块数.   
空闲块链表: 把所有空闲块链成一个链.   

磁盘地址和块号的转换: 已知块号, 则磁盘地址: 柱面号 = 块号/(磁头数*扇区数).   

4. 
文件控制块(FCB, file control block): 为管理文件而设置的数据结构.保存管理文件所需的所有相关信息.   
文件目录: 统一管理每个文件的元数据, 以支持文件名到文件物理地址的转换. 将所有文件的管理信息组织在一起, 即构成文件目录.   
目录文件: 将文件目录以文件形式存在磁盘上.  
目录项: 构成文件目录的基本单位. 目录项可以是FCB, 目录是文件控制块的有序集合. 



5. 文件的物理结构
文件的物理结构是指: 文件在存储介质中的存放方式.  
连续(顺序)结构: 文件存放在若干连续的物理块中.   
优点: 支持顺序存取和随机存取, 磁道寻道时间和次数较少. 可以同时读入多个块, 检索也很容易.  
缺点: 文件不能动态增长(可以预留空间, 但是很难预测, 造成浪费或重新分配和移动). 不利于文件插入和删除, 并且容易产生外部碎片.   

链接结构: 一个文件的信息存放在若干不连续的物理块中, 各块之间通过指针连接, 前一个物理块指向下一个物理块.
优点: 提高了磁盘的空间利用率, 不存在外部碎片问题. 有利于文件插入和删除. 有利于文件的动态扩充.
缺点: 存取速度慢, 不能够随机存取. 可靠性问题, 例如指针出错(某块存放指针的部分出现问题后边的都找不到了). 更多的寻道次数和时间. 链接指针也要占用空间. 

链接结构的一种变形: 文件分配表(FAT, file allocate table). 表项的值有三种: 0(未使用), 下一块块号, -1(结束)

索引结构: 也是存放在若干不连续的空间中. 系统为每个文件建立一个专用的数据结构-索引表, 并将这些物理块的块号存放在索引表中.   
索引表就是磁盘块地址数组, 其中第i个条目指向文件的第i块.  
索引表不等长, 不宜直接放在FCB中, 一般是指定一个字串存放一个地址指向索引表.   
优点: 能顺序存取,随机存取. 满足文件动态增长, 插入删除的要求. 能充分利用磁盘空间. 
缺点: 较多的寻道次数和时间. 索引表本身造成的系统开销, 内存, 磁盘空间, 存取时间. 

索引表的组织方式:   
当索引表很大, 需要多个物理块存放时怎么办?  
- 链接方式: 一个盘块存一个索引表, 多个索引表链接起来. 
- 多级索引方式: 将文件的索引表地址放在另一个索引表中. 
- 综合模式: 直接索引和间接索引相结合.

Unix的三级索引结构即综合模式. 每个文件的主索引表有15个索引项, 每项2个字节. 前12项直接存放文件的物理块号(直接寻址), 文件大于12块, 则利用13项指向另一个物理块(在该块中存放的是1级索引表), 对于更大的文件, 还可以利用14块,15块作为二级和三级索引表.   
假设扇区大小为512个字节, 物理块等于扇区块大小, 一级索引表可以存放256个物理块号.  
一级索引表存放256个块号, 二级索引表可以存放256个一级索引表(就是256^2个块号), 三级索引表同理(256^3).



6. 文件系统的实现
实现文件系统需要考虑磁盘和内存中的内容布局. 

磁盘分区(partition): 把一个物理磁盘的存储空间划分为几个相互独立的部分.   
文件卷(volume): 磁盘上的逻辑分区, 由一个或多个物理块(簇)构成. 一个文件卷可以是整个磁盘, 部分磁盘甚至跨盘raid.  同一文件卷使用同一份管理数据进行分配和磁盘空闲空间管理. 一个文件卷上包含了 文件系统的信息, 用户文件, 目录文件, 未分配空间. 
块, 簇(block, cluster) : 一个或多个连续的扇区, 可寻址数据块.   
格式化(format): 在一个文件卷上建立文件系统, 即建立并初始化用于文件分配和磁盘空闲空间管理的管理数据. 

磁盘上的内容: 
- 引导区: 包含了从该卷引导操作系统所需要的信息. 每个卷(分区)有一个, 通常为第一个扇区. 
- 卷信息: 包含该卷的块数, 块大小, 空闲块数量和指针, 空闲FCB数量和指针. 
- 目录文件: 包括根目录文件和其他目录文件
- 用户文件

内存中所需的数据结构: 
系统打开文件表: 整个系统一张, 放在内存, 用于保存已打开文件的FCB.
用户打开文件表: 每个进程一个, 进程的PCB中记录了用户打开文件表的位置. 



7. Unix文件系统
FCB = 目录项+i节点  
目录项: 文件名+i节点号  I节点: 描述文件的相关信息


## 第十周
1. FAT文件系统
文件系统数据记录在引导扇区中. 表项: 2字节(对于FAT16文件系统来说, 即2个字节16位). 目录项: 32字节, 根目录大小固定. 


2. 文件操作实现
- 创建: 建立系统与文件的联系 最重要的是建立FCB. 在目录中为新文件建立一个目录项, 根据需要填写参数和相关内容. 分配必要的存储空间
- 打开: 根据文件名在文件目录中检索, 并将该文件的目录项读入内存, 建立相应的数据结构, 为后续的文件操作做好准备. 系统返回文件描述符或文件句柄. 
- 指针定位操作: 系统为每个进程打开的每个文件维护一个读写指针, 即相对于文件开头的偏移地址. 读写指针指向每次文件读写的开始位置.
- 读文件:   
`read(文件描述符, 读指针, 要读的长度, 内存目的地址)`  
    - 根据打开文件时得到的文件描述符, 找到相应的文件控制块(目录项), 确定读操作的合法性. 
    - 将文件的逻辑块号转换为物理块号
    - 申请缓冲区
    - 启动磁盘IO操作


3. 文件系统的管理

备份: 
    + 全量转储: 定期将所有文件拷贝到后援存储器
    + 增量转储: 只转储修改过的文件, 即两次备份之间的修改 减少系统开销
    + 物理转储: 从磁盘第0块开始, 将所有磁盘块按序输出到磁带
    + 逻辑转储: 从一个或几个指定目录开始, 递归地转储自给定日期后所有更改的文件和目录


文件系统一致性
产生: 磁盘块 -> 内存 -> 写回磁盘块. 若在写回之前, 系统崩溃, 则文件系统出现不一致. 
解决: 设计一个程序, 当系统再次启动时, 运行该程序, 检查磁盘块和目录系统. 

UNIX一致性检查工作过程: 两张表, 每张对应一个表中的计数器. 初值为0. 表一记录了每块在文件中出现的次数, 表二记录了每块在空闲块表中出现的次数. 

文件系统的写入策略
(考虑文件系统一致性和速度)  
- 通写(write-through): 内存中的修改立即写到磁盘. 缺点: 速度慢 应用: FAT文件系统
- 延迟写(lazy-write): 利用回写(write back)缓存的方法得到高速 缺点: 可恢复性差
- 可恢复写(transaction log): 采用事务日志来实现文件系统的写入. 即考虑安全性 又考虑速度 性能. 应用: NTFS

4. 文件系统的安全性
用户三类: 文件主(owner), 同组用户(group), 其他用户(other)
操作三类: 可读(r), 可写(w), 可执行(x)

对于任意文件 有三组rxw 1表示可以 0表示禁止. 所以如果一个文件时777 表示所有人 可读可写可执行. 711表示owner可读可写可执行, 同组用户和其他用户只可以执行. 

5. 文件系统的性能
磁盘服务: 速度成为系统性能的主要瓶颈之一. 设计文件系统应该尽可能减少磁盘访问次数. 

块高速缓存(block cache): 文件缓存, 磁盘高速缓存, 缓冲区高速缓存. 指内存中为磁盘块设置的一个缓冲区, 保存了磁盘中某些块的副本.   
检查所有读请求, 看所需块是否在块高速缓存区. 如果在, 可以直接进行读操作. 如果不在, 先将数据读入块高速缓存, 再拷贝到所需的地方. 

提前读取(预取): 每次访问磁盘, 都多读入一些磁盘块. 开销较小. 

windows的文件访问方式: 
- 不使用文件缓存
    + 普通方式
    + 通过Windows提供的FlushFileBuffer函数实现
- 使用文件缓存
    + 预读取: 要考虑每次读取的块大小 缓冲区大小 置换方式
    + 写回: 写回时机选择 一致性问题
- 异步模式
    + 不再等待IO操作的完成
    + 使处理器和IO并发工作

用户对磁盘的访问 通过访问文件缓存的方式. 由windows的cache manager实现对缓存的控制.   
write-back机制: 在用户要对磁盘写数据时, 只更改cache中的内容, 由cache manager决定何时将更新反映到磁盘


磁盘调度: 当有多个访盘请求等待时, 采用一定的策略, 对这些请求的服务顺序调整安排, 降低平均磁盘访问时间, 达到公平 高效.   
一次访盘时间 = 寻道时间 + 旋转延迟时间 + 传输时间 (减少寻道时间, 减少延迟时间)

磁盘调度算法: 
- 先来先服务: 按先后次序
- 最短寻道时间优先: 优先选择距当前磁头最近的访问请求. 
- 扫描算法(电梯算法)
- 单向扫描调度算法
- 旋转调度算法: 根据延迟时间来决定执行次序的调度

信息的优化分布:  
记录在磁道上的排列方式也会影响输入输出操作的时间.   
raid技术: 独立磁盘冗余阵列(redundant arrays of Independent disk) 多块磁盘按照一定要求构成一个独立的存储设备. 提高可靠性和性能. 通过把多个磁盘组织在一起, 作为一个逻辑卷提供磁盘跨越功能. 通过把数据分成多个数据块, 并行写入/读出多个磁盘, 提高数据传输率(数据分条stripe). 通过镜像和校验, 提高容错能力.   
最简单的raid方式: 镜像. 最复杂的raid方式: 块交错校验.   
raid 0: 条带化 没有冗余信息性能最好.  raid 1 : 镜像, 磁盘利用率50% 安全性最好. raid 4: 交错块奇偶校验. 带奇偶校验, 校验信息放在一块盘上. 





###十一周
1. 概述
IO设备管理: 逻辑IO, 和具体设备关系不大但是又统一的一些操作组成. 设备驱动程序和中断服务程序. 

分类(按数据组织分):  
- 块设备: 以数据块为单位存储, 传输信息. 传输速率较高, 可寻址, 随机读取. 
- 字符设备: 以字符为单位, 速率低, 不可寻址. 

资源分配角度:   
- 独占设备: 在一段时间内只能由一个进程使用的设备一般为低速IO设备(打印机 磁带)
- 共享设备: 在一段时间可有多个进程共同使用的设备 以交叉方式使用 资源利用率高. 如硬盘. 
- 虚设备: 在一类设备上模拟另一类设备. 常用共享设备模拟独占设备. 用高速设备模拟低速设备. 被模拟的为虚设备. 将慢速的独占设备改造成多个用户可共享的设备. 提高设备的利用率. 

2. 异步IO
应用程序可以启动一个IO操作, 然后再IO请求执行的同时继续处理. 异步IO的基本思想是填充IO操作间等待的CPU时间.   
同步IO指应用程序被阻塞直到IO操作完成. 





### 十二周
1. 死锁概念
一组进程中, 每个进程都无限等待被该组进程中另一进程所占用的资源. 因而永远无法得到滋源. 称为进程死锁. 

可重用资源: 可被多个进程多次使用. 可消耗资源: 只可使用一次, 可创建和销毁的资源.   
活锁: 先加锁, 后轮询. 既无进展, 也无阻塞. 

产生死锁的必要条件:  
- 互斥使用
- 占有且等待
- 不可抢占 
- 循环等待

静态策略(死锁预防): 设计合适的资源分配算法, 不让死锁发生.   
动态策略(死锁避免): 以不让死锁发生为目标, 跟踪并评估资源分配过程. 根据评估结果决策是否分配.   
死锁检测与解除. 

3. 死锁预防
思路: 破坏产生死锁的四个必要条件之一. 





