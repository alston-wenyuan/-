# 性能调优工具

## 常用工具简单介绍与操作演示（可自行补充删减，流畅的解读工具输出的信息含义）

### Linux工具/命令

- **top**：常用的性能分析工具，能够实时显示系统中各个进程的资源占用状况

  ![img](D:\工作目录\工作日志\能力提升\phase_1\performance_optimization\调优工具_002.png)

  **第一行，任务队列信息，同 uptime 命令的执行结果**

  ```
  系统时间：07:27:05
  
  运行时间：up 1:57 min,
  
  当前登录用户： 3 user
  
  负载均衡(uptime) load average: 0.00, 0.00, 0.00
  
     average后面的三个数分别是1分钟、5分钟、15分钟的负载情况。
  
  load average数据是每隔5秒钟检查一次活跃的进程数，然后按特定算法计算出的数值。如果这个数除以逻辑CPU的数量，结果高于5的时候就表明系统在超负荷运转了
  ```

  **第二行，Tasks — 任务（进程）**

  ```
  总进程:150 total, 运行:1 running, 休眠:149 sleeping, 停止: 0 stopped, 僵尸进程: 0 zombie
  ```

  **第三行，cpu状态信息**

  ```
  0.0%us【user space】— 用户空间占用CPU的百分比。
  
  0.3%sy【sysctl】— 内核空间占用CPU的百分比。
  
  0.0%ni【】— 改变过优先级的进程占用CPU的百分比
  
  99.7%id【idolt】— 空闲CPU百分比
  
  0.0%wa【wait】— IO等待占用CPU的百分比
  
  0.0%hi【Hardware IRQ】— 硬中断占用CPU的百分比
  
  0.0%si【Software Interrupts】— 软中断占用CPU的百分比
  ```

  **第四行,内存状态**

  ```
  1003020k total,   234464k used,   777824k free,    24084k buffers【缓存的内存量】
  ```

  **第五行，swap交换分区信息**

  ```
  2031612k total,      536k used,  2031076k free,   505864k cached【缓冲的交换区总量】
  
  备注：
  
  可用内存=free + buffer + cached
  
  对于内存监控，在top里我们要时刻监控第五行swap交换分区的used，如果这个数值在不断的变化，说明内核在不断进行内存和swap的数据交换，这是真正的内存不够用了。
  
  第四行中使用中的内存总量（used）指的是现在系统内核控制的内存数，
  
  第四行中空闲内存总量（free）是内核还未纳入其管控范围的数量。
  
  纳入内核管理的内存不见得都在使用中，还包括过去使用过的现在可以被重复利用的内存，内核并不把这些可被重新使用的内存交还到free中去，因此在linux上free内存会越来越少，但不用为此担心。
  ```

  **第六行，空行**

  **第七行以下：各进程（任务）的状态监控**

  ```
  PID — 进程id
  USER — 进程所有者
  PR — 进程优先级
  NI — nice值。负值表示高优先级，正值表示低优先级
  VIRT — 进程使用的虚拟内存总量，单位kb。VIRT=SWAP+RES
  RES — 进程使用的、未被换出的物理内存大小，单位kb。RES=CODE+DATA
  SHR — 共享内存大小，单位kb
  S —进程状态。D=不可中断的睡眠状态 R=运行 S=睡眠 T=跟踪/停止 Z=僵尸进程
  %CPU — 上次更新到现在的CPU时间占用百分比
  %MEM — 进程使用的物理内存百分比
  TIME+ — 进程使用的CPU时间总计，单位1/100秒
  COMMAND — 进程名称（命令名/命令行）
  
  详解：
  VIRT：virtual memory usage 虚拟内存
  1、进程“需要的”虚拟内存大小，包括进程使用的库、代码、数据等
  2、假如进程申请100m的内存，但实际只使用了10m，那么它会增长100m，而不是实际的使用量
  
  RES：resident memory usage 常驻内存
  1、进程当前使用的内存大小，但不包括swap out
  2、包含其他进程的共享
  3、如果申请100m的内存，实际使用10m，它只增长10m，与VIRT相反
  4、关于库占用内存的情况，它只统计加载的库文件所占内存大小
  
  SHR：shared memory 共享内存
  1、除了自身进程的共享内存，也包括其他进程的共享内存
  2、虽然进程只使用了几个共享库的函数，但它包含了整个共享库的大小
  3、计算某个进程所占的物理内存大小公式：RES – SHR
  4、swap out后，它将会降下来
  
  DATA
  1、数据占用的内存。如果top没有显示，按f键可以显示出来。
  2、真正的该程序要求的数据空间，是真正在运行中要使用的。
  
  top 运行中可以通过 top 的内部命令对进程的显示方式进行控制。内部命令如下：
  s – 改变画面更新频率
  l – 关闭或开启第一部分第一行 top 信息的表示
  t – 关闭或开启第一部分第二行 Tasks 和第三行 Cpus 信息的表示
  m – 关闭或开启第一部分第四行 Mem 和 第五行 Swap 信息的表示
  N – 以 PID 的大小的顺序排列表示进程列表
  P – 以 CPU 占用率大小的顺序排列进程列表
  M – 以内存占用率大小的顺序排列进程列表
  h – 显示帮助
  n – 设置在进程列表所显示进程的数量
  q – 退出 top
  s – 改变画面更新周期
  ```

  # **top使用方法**

  ```
  使用格式：
  
  top [-] [d] [p] [q] [c] [C] [S] [s] [n]
  
  参数说明：
  
  d：指定每两次屏幕信息刷新之间的时间间隔。当然用户可以使用s交互命令来改变之。
  
  p:通过指定监控进程ID来仅仅监控某个进程的状态。
  
  q:该选项将使top没有任何延迟的进行刷新。如果调用程序有超级用户权限，那么top将以尽可能高的优先级运行。
  
  S：指定累计模式。
  
  s：使top命令在安全模式中运行。这将去除交互命令所带来的潜在危险。
  
  i：使top不显示任何闲置或者僵死进程。
  
  c:显示整个命令行而不只是显示命令名。
  
  常用命令说明
  Ctrl+L：擦除并且重写屏幕
  
  K：终止一个进程。系统将提示用户输入需要终止的进程PID，以及需要发送给该进程什么样的信号。一般的终止进程可以使用15信号；如果不能正常结束那就使用信号9强制结束该进程。默认值是信号15。在安全模式中此命令被屏蔽。
  
  i：忽略闲置和僵死进程。这是一个开关式命令。
  
  q：退出程序
  
  r:重新安排一个进程的优先级别。系统提示用户输入需要改变的进程PID以及需要设置的进程优先级值。输入一个正值将使优先级降低，反之则可以使该进程拥有更高的优先权。默认值是10。
  
  S：切换到累计模式。
  
  s：改变两次刷新之间的延迟时间。系统将提示用户输入新的时间，单位为s。如果有小数，就换算成m s。输入0值则系统将不断刷新，默认值是5 s。需要注意的是如果设置太小的时间，很可能会引起不断刷新，从而根本来不及看清显示的情况，而且系统负载也会大大增加。
  
  f或者F：从当前显示中添加或者删除项目。
  
  o或者O：改变显示项目的顺序
  
  l：切换显示平均负载和启动时间信息。
  
  m:切换显示内存信息。
  
  t:切换显示进程和CPU状态信息。
  
  c:切换显示命令名称和完整命令行。
  
  M:根据驻留内存大小进行排序。
  
  P:根据CPU使用百分比大小进行排序。
  
  T:根据时间/累计时间进行排序。
  
  W:将当前设置写入~/.toprc文件中。
  ```

  

- **vmstat**：Virtual Meomory Statistics（虚拟内存统计）的缩写，可对操作系统的虚拟内存、进程、CPU活动进行监控。是对系统的整体情况进行统计，不足之处是无法对某个进程进行深入分析。是最常见的Linux/Unix监控工具，可以展现给定时间间隔的服务器的状态值,包括服务器的CPU使用率，内存使用，虚拟内存交换情况,IO读写情况。相比top，可以看到整个机器的CPU,内存,IO的使用情况，而不是单单看到各个进程的CPU使用率和内存使用率(使用场景不一样)。

  ```
  一般vmstat工具的使用是通过两个数字参数来完成的，第一个参数是采样的时间间隔数，单位是秒，第二个参数是采样的次数，如:
  
  root@ubuntu:~# vmstat 2 1
  procs -----------memory---------- ---swap-- -----io---- -system-- ----cpu----
   r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa
   1  0      0 3498472 315836 3819540    0    0     0     1    2    0  0  0 100  0
  ```

  ```
  每个参数的意思。
  r 表示运行队列(就是说多少个进程真的分配到CPU)。这个也和top的负载有关系，一般负载超过了3就比较高，超过了5就高，超过了10就不正常了，服务器的状态很危险。top的负载类似每秒的运行队列。如果运行队列过大，表示你的CPU很繁忙，一般会造成CPU使用率很高。
  
  b 表示阻塞的进程,这个不多说，进程阻塞，大家懂的。
  
  swpd 虚拟内存已使用的大小，如果大于0，表示你的机器物理内存不足了，如果不是程序内存泄露的原因，那么你该升级内存了或者把耗内存的任务迁移到其他机器。
  
  free   空闲的物理内存的大小，我的机器内存总共8G，剩余3415M。
  
  buff   Linux/Unix系统是用来存储，目录里面有什么内容，权限等的缓存，我本机大概占用300多M
  
  cache cache直接用来记忆我们打开的文件,给文件做缓冲，我本机大概占用300多M(这里是Linux/Unix的聪明之处，把空闲的物理内存的一部分拿来做文件和目录的缓存，是为了提高 程序执行的性能，当程序使用内存时，buffer/cached会很快地被使用。)
  
  si  每秒从磁盘读入虚拟内存的大小，如果这个值大于0，表示物理内存不够用或者内存泄露了，要查找耗内存进程解决掉。我的机器内存充裕，一切正常。
  
  so  每秒虚拟内存写入磁盘的大小，如果这个值大于0，同上。
  
  bi  块设备每秒接收的块数量，这里的块设备是指系统上所有的磁盘和其他块设备，默认块大小是1024byte，我本机上没什么IO操作，所以一直是0，但是我曾在处理拷贝大量数据(2-3T)的机器上看过可以达到140000/s，磁盘写入速度差不多140M每秒
  
  bo 块设备每秒发送的块数量，例如我们读取文件，bo就要大于0。bi和bo一般都要接近0，不然就是IO过于频繁，需要调整。
  
  in 每秒CPU的中断次数，包括时间中断
  
  cs 每秒上下文切换次数，例如我们调用系统函数，就要进行上下文切换，线程的切换，也要进程上下文切换，这个值要越小越好，太大了，要考虑调低线程或者进程的数目,例如在apache和nginx这种web服务器中，我们一般做性能测试时会进行几千并发甚至几万并发的测试，选择web服务器的进程可以由进程或者线程的峰值一直下调，压测，直到cs到一个比较小的值，这个进程和线程数就是比较合适的值了。系统调用也是，每次调用系统函数，我们的代码就会进入内核空间，导致上下文切换，这个是很耗资源，也要尽量避免频繁调用系统函数。上下文切换次数过多表示你的CPU大部分浪费在上下文切换，导致CPU干正经事的时间少了，CPU没有充分利用，是不可取的。
  
  us 用户CPU时间，我曾经在一个做加密解密很频繁的服务器上，可以看到us接近100,r运行队列达到80(机器在做压力测试，性能表现不佳)。
  
  sy 系统CPU时间，如果太高，表示系统调用时间长，例如是IO操作频繁。
  
  id  空闲 CPU时间，一般来说，id + us + sy = 100,一般我认为id是空闲CPU使用率，us是用户CPU使用率，sy是系统CPU使用率。
  
  wt 等待IO CPU时间。
  ```

  

- **iostat**：是I/O statistics（输入/输出统计）的缩写，iostat工具将对系统的磁盘操作活动进行监视。它的特点是汇报磁盘活动统计情况，同时也会汇报出CPU使用情况。同vmstat一样，iostat也有一个弱点，就是它不能对某个进程进行深入分析，仅对系统的整体情况进行分析。

  ![img](https://images2018.cnblogs.com/blog/1278921/201807/1278921-20180718112206624-965391863.png)

  ```
  输出含义：
  
      avg-cpu: 总体cpu使用情况统计信息，对于多核cpu，这里为所有cpu的平均值。重点关注iowait值，表示CPU用于等待io请求的完成时间。
  
      Device: 各磁盘设备的IO统计信息。各列含义如下：
  
  Device: 以sdX形式显示的设备名称
  tps: 每秒进程下发的IO读、写请求数量
  KB_read/s: 每秒从驱动器读入的数据量，单位为K。
  KB_wrtn/s: 每秒从驱动器写入的数据量，单位为K。
  KB_read: 读入数据总量，单位为K。
  KB_wrtn: 写入数据总量，单位为K。
  ```

  

### JDK内置命令行工具

- jps (JVM Process Status）: 类似 UNIX 的 ps 命令。用户查看所有 Java 进程的启动类、传入参数和 Java 虚拟机参数等信息；

  ```
  命令格式：jps [ options ] [ hostid ]
  
      -q 只显示pid，不显示class名称,jar文件名和传递给main 方法的参数
  
      -m 输出传递给main 方法的参数，在嵌入式jvm上可能是null
  
      -l 输出应用程序main class的完整package名 或者 应用程序的jar文件完整路径名
  
      -v 输出传递给JVM的参数
  ```

  

- jstat（ JVM Statistics Monitoring Tool）: 用于收集 HotSpot 虚拟机各方面的运行数据;

  ```
  命令格式: jstat -<option> [-t] [-h<lines>] <vmid> [<interval> [<count>]]
  
  参数解释：
  Option — 选项，我们一般使用 -gcutil 查看gc情况
  vmid — VM的进程号，即当前运行的java进程号
  interval– 间隔时间，单位为秒或者毫秒
  count — 打印次数，如果缺省则打印无数次
  
  option
  选项option代表这用户希望查询的虚拟机信息，主要分为3类：类装载、垃圾收集和运行期编译状况，具体选项及作用如下：
  
  -class 监视类装载、卸载数量、总空间及类装载所耗费的时间 
  
  -gc 监视Java堆状况，包括Eden区、2个Survivor区、老年代、永久代等的容量 
  
  -gccapacity 监视内容与-gc基本相同，但输出主要关注Java堆各个区域使用到的最大和最小空间
  
  -gcutil监视内容与-gc基本相同，但输出主要关注已使用空间占总空间的百分比 
  
  -gccause 与-gcutil功能一样，但是会额外输出导致上一次GC产生的原因 
  
  -gcnew 监视新生代GC的状况 
  
  -gcnewcapacity 监视内容与-gcnew基本相同，输出主要关注使用到的最大和最小空间
  
   -gcold 监视老年代GC的状况
  
   -gcoldcapacity 监视内容与——gcold基本相同，输出主要关注使用到的最大和最小空间
  
  -gcpermcapacity 输出永久代使用到的最大和最小空间 
  
  -compiler 输出JIT编译器编译过的方法、耗时等信息 -printcompilation 输出已经被JIT编译的方法
  
  常见术语
  1、jstat –class<pid> : 显示加载class的数量，及所占空间等信息。
  Loaded 装载的类的数量 
  
  Bytes 装载类所占用的字节数 
  
  Unloaded 卸载类的数量 
  
  Bytes 卸载类的字节数 
  
  Time装载和卸载类所花费的时间
  
  2、jstat -compiler <pid>显示VM实时编译的数量等信息。
  Compiled 编译任务执行数量 
  
  Failed 编译任务执行失败数量 
  
  Invalid 编译任务执行失效数量 
  
  Time 编译任务消耗时间 
  
  FailedType 最后一个编译失败任务的类型 
  
  FailedMethod 最后一个编译失败任务所在的类及方法
  
  3、jstat -gc <pid>: 可以显示gc的信息，查看gc的次数，及时间。
  S0C 年轻代中第一个survivor（幸存区）的容量 (字节) 
  
  S1C年轻代中第二个survivor（幸存区）的容量 (字节) 
  
  S0U 年轻代中第一个survivor（幸存区）目前已使用空间 (字节) 
  
  S1U 年轻代中第二个survivor（幸存区）目前已使用空间 (字节) 
  
  EC 年轻代中Eden（伊甸园）的容量 (字节) 
  
  EU 年轻代中Eden（伊甸园）目前已使用空间 (字节) 
  
  OC Old代的容量 (字节) 
  
  OU Old代目前已使用空间 (字节) 
  
  PC Perm(持久代)的容量 (字节) 
  
  PUPerm(持久代)目前已使用空间 (字节) 
  
  YGC 从应用程序启动到采样时年轻代中gc次数 
  
  YGCT 从应用程序启动到采样时年轻代中gc所用时间(s) 
  
  FGC 从应用程序启动到采样时old代(全gc)gc次数 
  
  FGCT 从应用程序启动到采样时old代(全gc)gc所用时间(s) 
  
  GCT 从应用程序启动到采样时gc用的总时间(s)
  
  4、jstat -gccapacity <pid>:可以显示，VM内存中三代（young,old,perm）对象的使用和占用大小
  NGCMN 年轻代(young)中初始化(最小)的大小(字节)
  
  NGCMX 年轻代(young)的最大容量 (字节) 
  
  NGC 年轻代(young)中当前的容量 (字节) 
  
  S0C 年轻代中第一个survivor（幸存区）的容量 (字节) 
  
  S1C 年轻代中第二个survivor（幸存区）的容量 (字节) 
  
  EC 年轻代中Eden（伊甸园）的容量 (字节) 
  
  OGCMN old代中初始化(最小)的大小 (字节) 
  
  OGCMX old代的最大容量(字节) 
  
  OGC old代当前新生成的容量 (字节) 
  
  OC Old代的容量 (字节) 
  
  PGCMN perm代中初始化(最小)的大小 (字节) 
  
  PGCMX perm代的最大容量 (字节)
  PGC perm代当前新生成的容量 (字节) 
  
  PC Perm(持久代)的容量 (字节) 
  
  YGC 从应用程序启动到采样时年轻代中gc次数 
  
  FGC从应用程序启动到采样时old代(全gc)gc次数
  
  5、jstat -gcutil <pid>:统计gc信息
  S0 年轻代中第一个survivor（幸存区）已使用的占当前容量百分比 
  
  S1 年轻代中第二个survivor（幸存区）已使用的占当前容量百分比 
  
  E 年轻代中Eden（伊甸园）已使用的占当前容量百分比 
  
  O old代已使用的占当前容量百分比 
  
  P perm代已使用的占当前容量百分比 
  
  YGC 从应用程序启动到采样时年轻代中gc次数 
  
  YGCT 从应用程序启动到采样时年轻代中gc所用时间(s) 
  
  FGC从应用程序启动到采样时old代(全gc)gc次数 
  
  FGCT 从应用程序启动到采样时old代(全gc)gc所用时间(s) 
  
  GCT 从应用程序启动到采样时gc用的总时间(s)
  
  6、jstat -gcnew <pid>:年轻代对象的信息。
  S0C 年轻代中第一个survivor（幸存区）的容量 (字节) 
  
  S1C年轻代中第二个survivor（幸存区）的容量 (字节) 
  
  S0U 年轻代中第一个survivor（幸存区）目前已使用空间 (字节) 
  
  S1U 年轻代中第二个survivor（幸存区）目前已使用空间 (字节) 
  
  TT 持有次数限制 
  
  MTT 最大持有次数限制 
  
  EC 年轻代中Eden的容量 (字节) 
  
  EU 年轻代中Eden目前已使用空间 (字节) 
  
  YGC 从应用程序启动到采样时年轻代中gc次数 
  
  YGCT 从应用程序启动到采样时年轻代中gc所用时间(s)
  
  7、jstat -gcnewcapacity<pid>: 年轻代对象的信息及其占用量。
  NGCMN 年轻代(young)中初始化(最小)的大小(字节) 
  
  NGCMX 年轻代(young)的最大容量 (字节) 
  
  NGC 年轻代(young)中当前的容量 (字节) 
  
  S0CMX 年轻代中第一个survivor（幸存区）的最大容量 (字节) 
  
  S0C 年轻代中第一个survivor（幸存区）的容量 (字节) 
  
  S1CMX 年轻代中第二个survivor（幸存区）的最大容量 (字节) 
  
  S1C 年轻代中第二个survivor（幸存区）的容量 (字节) 
  
  ECMX 年轻代中Eden（伊甸园）的最大容量 (字节) 
  
  EC 年轻代中Eden（伊甸园）的容量 (字节) 
  
  YGC 从应用程序启动到采样时年轻代中gc次数 
  
  FGC 从应用程序启动到采样时old代(全gc)gc次数
  
  8、jstat -gcold <pid>：old代对象的信息。
  
  PC Perm(持久代)的容量 (字节) 
  
  PU Perm(持久代)目前已使用空间 (字节) 
  
  OC Old代的容量 (字节) 
  
  OU Old代目前已使用空间 (字节) 
  
  YGC 从应用程序启动到采样时年轻代中gc次数 
  
  FGC 从应用程序启动到采样时old代(全gc)gc次数 
  
  FGCT 从应用程序启动到采样时old代(全gc)gc所用时间(s) 
  
  GCT 从应用程序启动到采样时gc用的总时间(s)
  
  9、stat -gcoldcapacity <pid>: old代对象的信息及其占用量。
  
  OGCMN old代中初始化(最小)的大小 (字节) 
  
  OGCMX old代的最大容量(字节) 
  
  OGC old代当前新生成的容量 (字节) 
  
  OC Old代的容量 (字节) 
  
  YGC 从应用程序启动到采样时年轻代中gc次数 
  
  FGC 从应用程序启动到采样时old代(全gc)gc次数 
  
  FGCT 从应用程序启动到采样时old代(全gc)gc所用时间(s) 
  
  GCT 从应用程序启动到采样时gc用的总时间(s)
  
  10、jstat -gcpermcapacity<pid>: perm对象的信息及其占用量。
  
  PGCMN perm代中初始化(最小)的大小 (字节) 
  
  PGCMX perm代的最大容量 (字节)
  PGC perm代当前新生成的容量 (字节) 
  
  PC Perm(持久代)的容量 (字节) 
  
  YGC 从应用程序启动到采样时年轻代中gc次数 
  
  FGC从应用程序启动到采样时old代(全gc)gc次数 
  
  FGCT 从应用程序启动到采样时old代(全gc)gc所用时间(s) 
  
  GCT 从应用程序启动到采样时gc用的总时间(s)
  
  11、jstat -printcompilation <pid>：当前VM执行的信息。
  
  Compiled 编译任务的数目 
  
  Size 方法生成的字节码的大小 
  
  Type 编译类型 
  
  Method 类名和方法名用来标识编译的方法。类名使用/做为一个命名空间分隔符。方法名是给定类中的方法。上述格式是由-XX:+PrintComplation选项进行设置的
  ```

  

- jinfo (Configuration Info for Java) : Configuration Info forJava,显示虚拟机配置信息;

  ```
  jinfo是jdk自带的命令，可以用来查看正在运行的Java应用程序的扩展参数，甚至支持在运行时，修改部分参数。
  
  通常会先使用jps查看java进程的id，然后使用jinfo查看指定pid的jvm信息.
  
  jps #通过jps来查看当前运行状态的虚拟机进程
  查看jvm的参数
  jinfo -flags process_id
  
  查看java系统参数
  jinfo -sysprops process_id
  虚拟机的这些参数可以通过下面的命令查看：
  java -XX:+PrintFlagsFinal -version | grep manageable
  
  除了通过启动脚本可以设置参数，PrintGC默认是打开的，因此我们只需要打开PrintGCDetails参数。
  jinfo -flag +PrintGC 27250
  jinfo -flag +PrintGCDetails 27250
  
  如果需要关闭GC日志的打印，使用下面的命令：
  jinfo -flag -PrintGC 27250
  jinfo -flag -PrintGCDetails 27250
  
  查看是否开启了GC日志的打印：
  jinfo -flag PrintGC 27250
  jinfo -flag PrintGCDetails 27250
  ```

  

- jmap (Memory Map for Java) : 生成堆转储快照;

  ```
  命令格式： jmap [option] vmid
  
      -dump：生成Java堆转储快照。格式为：-dump:[live,]format=b,file=<filename>，其中live子参数说明是否只dump出存活的对象。
  
      -finalizerinfo：显示在F-Queue中等待Finalizer线程执行finalize方法的对象。只在Linux/Solaris平台下有效。
  
      -heap：显示Java堆详细信息，如使用哪种回收器、参数配置、分代状况等。只在Linux/Solaris平台下有效。
  
      -histo：显示堆中对象统计信息，包括类、实例数量和合计容量。
  
      -permstat：以ClassLoader为统计口径显示永久代内存状态。只在Linux/Solaris平台下有效。
  
      -F：当虚拟机进程对-dump选项没有响应时，可使用这个选项强制生成dump快照。只在Linux/Solaris平台下有效。
   
  示例
   
  jmap -dump:format=b,file=eclipse.bin 3500
  ```

  

- jhat (JVM Heap Dump Browser ) : 用于分析 heapdump 文件，它会建立一个 HTTP/HTML 服务器，让用户可以在浏览器上查看分析结果;

  ```
  是jdk内置的工具之一。主要是用来分析java堆的命令，可以将堆中的对象以html的形式显示出来，包括对象的数量，大小等等，并支持对象查询语言。
  使用jmap等方法生成java的堆文件后，使用其进行分析。
  
  第一步：导出堆
  #jmap -dump:live,file=a.log pid
  
  第二步：分析堆文件
  #jhat -J-Xmx512M a1.log
  
  第三步：查看html
  http://ip:7000/
  ```

  

- jstack (Stack Trace for Java) : 生成虚拟机当前时刻的线程快照，线程快照就是当前虚拟机内每一条线程正在执行的方法堆栈的集合。

  ```
  命令格式： jstack [-l] pid
  
  使用方式
  
  说明
  
  jstack [ option ] pid
  
  	查看当前时间点，指定进程的dump堆栈信息。
  
  jstack [ option ] pid > 文件
  
  	将当前时间点的指定进程的dump堆栈信息，写入到指定文件中。注:若该文件不存在，则会自动生成;若该文件存在，则会覆盖源文件。
  
  jstack [ option ] executable core
  
  	查看当前时间点，core文件的dump堆栈信息。
  
  jstack [ option ] [server_id@]<remote server IP or hostname>
  
  	查看当前时间点，远程机器的dump堆栈信息。
  	
  	
  	-F : 当进程挂起了，此时'jstack [-l] pid'是没有相应的，这时候可使用此参数来强制打印堆栈信息,强制jstack），一般情况不需要使用。
  	
  	-m : 打印java和native c/c++框架的所有栈信息。可以打印JVM的堆栈，以及Native的栈帧，一般应用排查不需要使用。
  	
  	-l : 长列表. 打印关于锁的附加信息。例如属于java.util.concurrent的ownable synchronizers列表，会使得JVM停顿得长久得多（可能会差很多倍，比如普通的jstack可能几毫秒和一次GC没区别，加了-l 就是近一秒的时间），-l 建议不要用。一般情况不需要使用。
  	
  	-h or -help : 打印帮助信息。
  	
  ```

  

### 可视化分析工具

- JConsole

  ![image-20210904181635054](https://mmbiz.qpic.cn/mmbiz_png/yqdSticVI7RPDaHNbIKZuACwlI5DuvJibeXOQehA6CMoRZSjKrgmpPwOvpYwhSFh8rW8oYQ7SWD4uLhKUdl5odjQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

  ```
  点击JDK/bin 目录下面的jconsole.exe 即可启动
  
  然后会自动自动搜索本机运行的所有虚拟机进程。
  
  选择其中一个进程可开始进行监控
  
  
  JConsole 基本包括以下基本功能：概述、内存、线程、类、VM概要、MBean
  
  	内存页签相对于可视化的jstat 命令，用于监视受收集器管理的虚拟机内存。
  	
  	如果上面的“内存”页签相当于可视化的jstat命令的话，“线程”页签的功能相当于可视化的jstack命令，遇到线程停顿时可以使用这个页签进行监控分析。线程长时间	停顿的主要原因主要有：等待外部资源（数据库连接、网络资源、设备资
  	源等）、死循环、锁等待（活锁和死锁）
  	
  	红线是加载的类的总数（包括随后卸载的类），蓝线是当前加载的类的数量。
  	“详细信息”部分显示自Java VM启动以来加载的类的总数，当前加载的数量和卸载的数量。
  	通过选中右上角的复选框将类加载跟踪设置为详细输出
  	
  	VisualVM（All-in-One Java Troubleshooting Tool）;功能最强大的运行监视和故障处理程序
  	显示虚拟机进程以及进程的配置、环境信息（jps、jinfo）。
      监视应用程序的CPU、GC、堆、方法区(1.7及以前)，元空间（JDK1.8及以后）以及线程的信息（jstat、jstack）。
      dump以及分析堆转储快照（jmap、jhat）。
      方法级的程序运行性能分析，找出被调用最多、运行时间最长的方法。
      离线程序快照：收集程序的运行时配置、线程dump、内存dump等信息建立一个快照
      
      左边的树形结构显示了所有的MBean,选择了一个MBean之后，其属性、操作、通知和其他信息会在右边显示
  ```

  

- Visual VM

- JProfiler

  ```
  JProfiler是由ej-technologies GmbH公司开发的一款性能瓶颈分析工具(该公司还开发部署工具)。
  其特点:
      使用方便
      界面操作友好
      对被分析的应用影响小
      CPU,Thread,Memory分析功能尤其强大
      支持对jdbc,noSql, jsp, servlet, socket等进行分析
      支持多种模式(离线，在线)的分析
      跨平台
  ```

  

- GC Viewer

  ```
  一款离线的GC日志分析器。有两个部分，其中显示了日志分析的结果。一个是图表的左侧，另一个是右侧的数据面板。下面对这些部分的内容进行说明。
  ```

  [详情参考Github](https://codechina.csdn.net/mirrors/chewiebug/gcviewer?utm_source=csdn_github_accelerator)

- MAT

  ```
  Eclipse Memory Analyzer是一个快速且功能丰富的Java堆分析器，可帮助您查找内存泄漏并减少内存消耗。使用Memory Analyzer分析具有数亿个对象的高效堆转储，快速计算对象的保留大小，查看谁阻止垃圾收集器收集对象，运行报告以自动提取泄漏嫌疑者。
  ```

  

- GCeasy

  ```
  官网地址：https://gceasy.io/，GCeasy是一款在线的GC日志分析器，可以通过GC日志分析进行内存泄露检测、GC暂停原因分析、JVM配置建议优化等功能，而且是可以免费使用的（有一些服务是收费的）。
  
      JVM的各个分代区域分配的内存及使用峰值的内存
  
      关键性能指标：吞吐量及GC暂停平均时间、最大时间、各个时间段的比例。
  
      GC发生的原因、次数、时间等
  ```

  

- 公司内部APM系统

---

## JVM常见参数与配置（基于公司内部运维平台操作）

- **-Duser.timezone**：时区设置

- **-Xms**：初始堆大小，默认为物理内存的1/64(<1GB)；默认(MinHeapFreeRatio参数可以调整)空余堆内存小于40%时，JVM就会增大堆直到-Xmx的最大限制

- **-Xmx**：最大堆大小，默认(MaxHeapFreeRatio参数可以调整)空余堆内存大于70%时，JVM会减少堆直到 -Xms的最小限制

- **-Xmn**：新生代的内存空间大小，注意：此处的大小是（eden+ 2 survivor space)。与jmap -heap中显示的New gen是不同的。整个堆大小=新生代大小 + 老生代大小 + 永久代大小。在保证堆大小不变的情况下，增大新生代后,将会减小老生代大小。此值对系统性能影响较大,Sun官方推荐配置为整个堆的3/8。

- **-XX:SurvivorRatio**：新生代中Eden区域与Survivor区域的容量比值，默认值为8。两个Survivor区与一个Eden区的比值为2:8,一个Survivor区占整个年轻代的1/10。

- **-Xss**：每个线程的堆栈大小。JDK5.0以后每个线程堆栈大小为1M,以前每个线程堆栈大小为256K。应根据应用的线程所需内存大小进行适当调整。在相同物理内存下,减小这个值能生成更多的线程。但是操作系统对一个进程内的线程数还是有限制的，不能无限生成，经验值在3000~5000左右。一般小的应用， 如果栈不是很深， 应该是128k够用的，大的应用建议使用256k。这个选项对性能影响比较大，需要严格的测试。和threadstacksize选项解释很类似,官方文档似乎没有解释,在论坛中有这样一句话:"-Xss is translated in a VM flag named ThreadStackSize”一般设置这个值就可以了。

- **-XX:PermSize**：设置永久代(perm gen)初始值。默认值为物理内存的1/64。

- **-XX:MaxPermSize**：设置持久代最大值。物理内存的1/4。

- **-XX:+UseG1GC**：设置垃圾回收器为G1

- **-XX:G1HeapRegionSize**:JVM对堆期望划分的REGION数量，而不是实际划分的REGION数量

- **-XX:MaxGCPauseMillis**：表示每次GC最大的停顿毫秒数，VM将调整Java堆大小和其他与GC相关的参数，以使GC引起的暂停时间短于x毫秒，尽可能地保证内存回收花费时间不超过设定值。请注意，这可能会导致VM降低整体吞吐量(吞吐量=运行用户代码时间/VM总运行时间)，并且在某些情况下，VM将无法达到所需的暂停时间目标。默认情况下，VM没有暂停时间目标值。GC的暂停时间主要取决于堆中实时数据的数量与实时数据量。该参数应谨慎使用。太小的值将导致系统花费过多的时间进行垃圾回收。原因是为满足最大暂停时间，VM将设置更小的堆，以存储相对少量的对象，来提升回收速率，会导致更高频率的GC。

- **-XX:+UnlockExperimentalVMOptions** :解锁实验参数，允许使用实验性参数，JVM中有些参数不能通过-XX直接复制需要先解锁，比如要使用某些参数的时候，可能不会生效，需要设置这个参数来解锁；

- **-XX:G1NewSizePercent**:新生代比例下限

- **-XX:G1MaxNewSizePercent**:新生代比例上限

- **-XX:InitiatingHeapOccupancyPercent**:指定触发全局并发标记的老年代使用占比，默认值45%，也就是老年代占堆的比例超过45%。如果Mixed GC周期结束后老年代使用率还是超过45%,那么会再次触发全局并发标记过程，这样就会导致频繁的老年代GC，影响应用吞吐量。同时老年代空间不大，Mixed GC回收的空间肯定是偏少的。可以适当调高IHOP的值，当然如果此值太高，很容易导致年轻代晋升失败而触发Full GC，所以需要多次调整测试。

- **-XX:G1MixedGCCountTarget**:指定一个周期内触发Mixed GC最大次数，默认值8。一次全局并发标记后，最多接着8次Mixed GC，把全局并发标记阶段生成的Cset里的Region拆分为最多8部分，然后在每轮Mixed GC里收集一部分。

- **-XX:ConcGCThreads**：指定并发GC线程数，默认是-XX:ParallelGCThreads/4，也就是在非STW期间的GC工作线程数，当然其他的线程很多工作在应用上。当并发周期时间过长时，可以尝试调大GC工作线程数，但是这也意味着此期间应用所占的线程数减少，会对吞吐量有一定影响。

- **-XX:ParallelGCThreads**：指定并行GC线程数，也就是在STW阶段工作的GC线程数，其值遵循以下原则：

  - 如果用户显示指定了ParallelGCThreads，则使用用户指定的值。
  - 否则需要根据实际的CPU所能够支持的线程数来计算ParallelGCThreads的值。
  - 如果物理CPU所能够支持线程数小于8，则ParallelGCThreads的值为CPU所支持的线程数。这里的阀值为8，是因为JVM中调用nof_parallel_worker_threads接口所传入的switch_pt的值均为8。
  - 如果物理CPU所能够支持线程数大于8，则ParallelGCThreads的值为8加上一个调整值，调整值的计算方式为：物理CPU所支持的线程数减去8所得值的5/8或者5/16，JVM会根据实际的情况来选择具体是乘以5/8还是5/16。**ParallelGCThreads= 8 + (N - 8) \* 5 / 8**

  比如，在64线程的x86 CPU上，如果用户未指定ParallelGCThreads的值，则默认的计算方式为：ParallelGCThreads = 8 + (64 - 8) * (5/8) = 8 + 35 = 43。

- **-XX:MaxTenuringThreshold**：晋升年龄阈值，默认值15。一般新生对象经过15次Young GC会晋升到老年代，巨型对象会直接分配在老年代，同时在Young GC时，如果相同age的对象占Survivors空间的比例超过 -XX:TargetSurvivorRatio的值(默认50%)，则会自动将此次晋升年龄阈值设置为此age的值，所有年龄超过此值的对象都会被晋升到老年代，此举可能会导致老年代需要不少空间应对此种晋升。一般这个值不需要额外调整。

- **-Xloggc**:把相关日志信息记录到文件以便分析

---

## 模拟环境实战演练（可自行补充删减，使用相应工具排查解读存在的问题）

### CPU 占满

``` java
	/**
     * 模拟CPU占满
     */
    @GetMapping("/cpu/loop")
    public void testCPULoop() throws InterruptedException {
        System.out.println("请求cpu死循环");
        Thread.currentThread().setName("loop-thread-cpu");
        int num = 0;
        while (true) {
            num++;
            if (num == Integer.MAX_VALUE) {
                System.out.println("reset");
            }
            num = 0;
        }

    }
```

### 内存泄露

``` java
    /**
     * 模拟内存泄漏
     */
    @GetMapping(value = "/memory/leak")
    public String leak() {
        System.out.println("模拟内存泄漏");
        ThreadLocal<Byte[]> localVariable = new ThreadLocal<Byte[]>();
        localVariable.set(new Byte[4096 * 1024]);// 为线程添加变量
        return "ok";
    }
```

### 内存溢出

``` java
    public static List<Object> data = new ArrayList<>();
    
    /**
     * 模拟内存溢出
     */
    @GetMapping(value = "/memory/overflow")
    public String overflow() {
        System.out.println("模拟内存溢出");
        for (int i = 0; i < 1000; i++) {
            data.add(new char[1024 * 1024 * 1024]);
        }
        return "ok";
    }
```

### 死锁

``` java
    ExecutorService service = new ThreadPoolExecutor(4, 10,
            0, TimeUnit.SECONDS, new LinkedBlockingQueue<Runnable>(1024),
            Executors.defaultThreadFactory(),
            new ThreadPoolExecutor.AbortPolicy());
    /**
     * 模拟死锁（循环请求接口 2000 次）
     */
    @GetMapping("/cpu/test")
    public String testCPU() throws InterruptedException {
        System.out.println("请求cpu");
        Object lock1 = new Object();
        Object lock2 = new Object();
        service.submit(new DeadLockThread(lock1, lock2), "deadLookThread-" + new Random().nextInt());
        service.submit(new DeadLockThread(lock2, lock1), "deadLookThread-" + new Random().nextInt());
        return "ok";
    }
    
    

public class DeadLockThread implements Runnable {
    private Object lock1;
    private Object lock2;

    public DeadLockThread1(Object lock1, Object lock2) {
        this.lock1 = lock1;
        this.lock2 = lock2;
    }

    @Override
    public void run() {
        synchronized (lock2) {
            System.out.println(Thread.currentThread().getName()+"get lock2 and wait lock1");
            try {
                TimeUnit.MILLISECONDS.sleep(2000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            synchronized (lock1) {
                System.out.println(Thread.currentThread().getName()+"get lock1 and lock2 ");
            }
        }
    }
}
```

### 锁对性能影响（锁导致并行变串行，影响性能，解决方式）

``` java
    public static ExecutorService service = new ThreadPoolExecutor(4, 10,
            0, TimeUnit.SECONDS, new LinkedBlockingQueue<Runnable>(1024),
            Executors.defaultThreadFactory(),
            new ThreadPoolExecutor.AbortPolicy());

    @GetMapping(value = "/thread/lock")
    public String theadLock(int num) {
        for (int i = 0; i < 10; i++) {
            service.execute(new Runnable() {
                @Override
                public void run() {
                    sleep();
                }
            });
        }
        return "ok";
    }

    public static void sleep(){
        try {
            System.out.printf("[%s] todo sleep...\n", Thread.currentThread().toString());
            Thread.sleep(3000L);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
```

### 线程频繁切换

``` java
    @GetMapping(value = "/thread/swap")
    public String theadSwap(int num) {
        System.out.println("模拟线程切换");
        for (int i = 0; i < num; i++) {
            new Thread(new ThreadSwap1(new AtomicInteger(0)),"thread-swap"+i).start();
        }
        return "ok";
    }
    
public class ThreadSwap1 implements Runnable {
    private AtomicInteger integer;

    public ThreadSwap1(AtomicInteger integer) {
        this.integer = integer;
    }

    @Override
    public void run() {
        while (true) {
            integer.addAndGet(1);
            Thread.yield(); //让出CPU资源
        }
    }
}
```

---
