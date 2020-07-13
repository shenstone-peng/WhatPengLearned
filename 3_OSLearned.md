# 3 操作系统
## 3.1操作系统概论
### 3.1.1虚拟内存
1. 虚拟内存是操作系统物理内存和进程之间的中间层，它为进程隐藏了物理内存这一概念，为进程提供了更加简洁和易用的接口以及更加复杂的功能。
2. 操作系统以页为单位管理内存，当进程发现需要访问的数据不在内存时，操作系统可能会将数据以页的方式加载到内存中，这个过程是由MMU完成的。
3. 虚拟内存的关键作用
> 虚拟内存可以利用磁盘起到缓存的作用，提高进程访问磁盘的速度；
> 虚拟内存可以为进程提供独立的内存空间，简化程序的链接、加载过程并通过动态库共享内存
> 虚拟内存可以控制进程对物理内存的访问，隔离不同进程的访问权限，提供系统的安全性。
4. 当用户程序访问未被缓存的虚拟页时（即没有被缓存到物理内存中的数据），硬件就会触发缺页中断（Page Fault,PF），一种情况：被访问的页面已经加载到了物理内存中，但用户程序的页表（Page Table）并不存在该对应关系，这时我们只需要在页表中建立虚拟内存到物理内存的关系；另一种情况：操作系统需要将磁盘上未被缓存的虚拟页加载到物理内存中。
5. 在Linux调用fork创建子进程时，实际上只复制了父进程的页表，当父进程或者子进程对共享的内存进行修改时，共享的内存才会以页为单位进行拷贝，父进程会保留原有的物理空间，而子进程会使用拷贝后的物理空间。
### 3.2 中断
1. 
> 中断又分为：
> 硬中断：外围硬件比如网卡发给CPU的信号
> 软中断：由硬中断处理后对操作系统内核发出信号的中断请求
2. 网卡中断
```bash
cat /proc/interrupts
           CPU0       CPU1
  0:        138          0   IO-APIC-edge      timer
  1:          1          2   IO-APIC-edge      i8042
  8:          1          0   IO-APIC-edge      rtc0
  9:          0          0   IO-APIC-fasteoi   acpi
 12:          3          1   IO-APIC-edge      i8042
 16:          0          0   IO-APIC-fasteoi   uhci_hcd:usb3
 17:          1          0   IO-APIC-fasteoi   radeon
 18:          0          0   IO-APIC-fasteoi   ehci_hcd:usb1, uhci_hcd:usb8, i801_smbus
 19:          0          0   IO-APIC-fasteoi   uhci_hcd:usb5, uhci_hcd:usb7
 21:          0          0   IO-APIC-fasteoi   uhci_hcd:usb4
 23:          0          0   IO-APIC-fasteoi   ehci_hcd:usb2, uhci_hcd:usb6
 24:          0          0   PCI-MSI-edge      aerdrv, PCIe PME
 25:          0          0   PCI-MSI-edge      aerdrv, PCIe PME
 26:          0          0   PCI-MSI-edge      aerdrv, PCIe PME, pciehp
 27:          0          0   PCI-MSI-edge      aerdrv, PCIe PME, pciehp
 28:          0          0   PCI-MSI-edge      aerdrv, PCIe PME, pciehp
 29:          0          0   PCI-MSI-edge      aerdrv, PCIe PME, pciehp
 30:          0          0   PCI-MSI-edge      PCIe PME, pciehp
 31:          0          0   PCI-MSI-edge      PCIe PME, pciehp
 32:          0          0   PCI-MSI-edge      PCIe PME, pciehp
 33:     161714     162023   PCI-MSI-edge      eth0
 35:      53554      53247   PCI-MSI-edge      0000:00:1f.2
 36:          1          0   PCI-MSI-edge      ioat-msi

```
第一列为中断号，查看该中断绑定的CPU中断亲和性。03表示该网卡中断会轮流指定到CPU0和CPU1
```bash
cat /proc/irq/33/smp_affinity
03
```
网卡在接收到ipv4 tcp的数据流时，根据指定的字段计算hash值（例如Toeplitz哈希算法），之后将hash值的后7位数值作为索引，到网卡的对照表中找到相应的队列索引，将数据包添加到此队列中。
【**现场故障**】：网卡驱动只对组播目的端口进行了一次Hash，现网情况下所有组播频道使用相同的目的端口，所有报文需要遍历链表进行查找处理，十分消耗CPU。**改进措施**：进行二次hash，除了使用组播目的端口进行第一次hash外，还对组播目的地址进行第二次hash，查找速度就很快。

### 3.3性能（命令）
1.查看性能的命令
```bash
#1. uptime
#快速展示系统平均负载的方法，这也指出了等待运行进程的数量
#三个数字分别统计过去1、5、15分钟的平均数
[root@localhost ZMSS]# uptime
 15:07:50 up  3:52,  1 user,  load average: 1.51, 1.60, 1.57
#2. dmesg | tail
#这里展示的是最近10条系统消息日志，主要是看由于性能问题导致的错误
[root@localhost ZMSS]# dmesg | tail
[  197.648482] 111111entry tipc_send,msg_sect->iov_len:0
[  197.772422] 111111entry tipc_send,msg_sect->iov_len:0
[  197.897982] 111111entry tipc_send,msg_sect->iov_len:0
[  198.079991] 111111entry tipc_send,msg_sect->iov_len:0
[  198.190386] 111111entry tipc_send,msg_sect->iov_len:0
[  209.443797] perf: interrupt took too long (3145 > 3137), lowering kernel.perf_event_max_sample_rate to 63000
[  211.109880] 111111entry tipc_send,msg_sect->iov_len:28
[  280.512999] perf: interrupt took too long (3943 > 3931), lowering kernel.perf_event_max_sample_rate to 50000
[  434.259920] perf: interrupt took too long (4930 > 4928), lowering kernel.perf_event_max_sample_rate to 40000
[ 5779.905683] hrtimer: interrupt took 5914 ns

#3.vemstat 1
#对虚拟内存统计的简短展示
#r:cpu上的等待运行的可运行进程数，这个指标提供了判断CPU饱和度的数据，因为它不包含IO等待的进程
#free:空闲内存，单位是k。
#si,so：交换进来和交换出去的数据量，如果这两个值非0，那么就说明没有内存了（用到swap了）
#us,sy,id,wa,st:这些是CPU时间的分解，是所有CPU的平均值，分别为用户时间，系统时间，空闲，等待IO时间和被偷的时间
#CPU时间分解可以帮助确定CPU是不是非常忙（通过用户时间和系统时间累加判断）持续的IO等待则表明磁盘是瓶颈
[root@localhost ZMSS]# vmstat 1
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 1  0      0 2196724 131440 4463008    0    0    15   124  959 1001  6 56 36  2  0
 1  0      0 2195960 131444 4463016    0    0     0    28 11144 14342  6 52 41  2  0
#4.mpstat -P ALL 1
#打印各个CPU的时间统计，可以看出整体CPU的使用是不是均衡的。

[root@localhost ZMSS]# mpstat -P ALL 1
Linux 3.10.0-693.21.1.el7.x86_64 (localhost.localdomain)        06/30/20        _x86_64_        (2 CPU)

15:10:29     CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
15:10:30     all    6.03    0.00   50.25    1.01    1.01    0.00    0.00    0.00    0.00   41.71
15:10:30       0    5.10    0.00   45.92    3.06    1.02    0.00    0.00    0.00    0.00   44.90
15:10:30       1    6.86    0.00   53.92    0.00    0.98    0.00    0.00    0.00    0.00   38.24
#5.pidstat 1
#为每个CPU统计信息功能
[root@localhost ZMSS]# pidstat 1
Linux 3.10.0-693.21.1.el7.x86_64 (localhost.localdomain)        06/30/20        _x86_64_        (2 CPU)

15:12:04      UID       PID    %usr %system  %guest    %CPU   CPU  Command
15:12:05        0      6061    0.00    0.87    0.00    0.87     1  kill_abnormal_s
15:12:05        0      6468    0.00    3.48    0.00    3.48     0  ZMSSdu
15:12:05        0      6777    0.00    0.87    0.00    0.87     0  ZMSSRtspOverUDP
15:12:05        0      6778    0.00    0.87    0.00    0.87     1  ZMSSStreamingSe
15:12:05        0     10755    0.87    6.09    0.00    6.96     0  pidstat
15:12:05        0     10824    0.00    7.83    0.00    7.83     1  ps
15:12:05        0     11341    0.00    0.87    0.00    0.87     1  ZMSSCC
15:12:05        0     13626    0.00    0.87    0.00    0.87     0  ZXUSS_CGSL_CDNL
15:12:05        0     13778    0.00    0.87    0.00    0.87     0  ZXUSS_CGSL_USSP
15:12:05        0     13829    0.87    0.00    0.00    0.87     1  ZXUSS_CGSL_CDNC
#6.iostat -xz 1
#r/s,w/s,rkB/s,wkB/s:表示设备上每秒钟的读写次数和读写的字节数，这些可以看出设备的负载情况
#await:I/O等待的平均时间这是应用程序所等待的时间，包含了等待队列中的时间和被调度服务的时间。
[root@localhost ~]# iostat -xz 1
Linux 3.10.0-693.21.1.el7.x86_64 (localhost.localdomain)        06/30/20        _x86_64_        (2 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           6.90    0.00   56.21    1.76    0.00   35.13

Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
sda               0.25    32.59    1.29    5.82    30.23   243.52    77.00     0.29   40.56  102.52   26.77   8.05   5.72

#7.free -m
#buffers:用于块设备I/O缓冲的缓存
#cached:用于文件系统的页缓存
[root@localhost ~]# free -m
              total        used        free      shared  buff/cache   available
Mem:           7470         848        1656          87        4966        5279
Swap:             0           0           0
#8.sar -n DEV 1
#使用这个工具是可以检测网络接口的吞吐：rxkB/s和txkB/s。作为收发数据负载的度量，也是检测是否到达收发极限
#如下例，接收数据2.46kB/s
[root@localhost ~]# sar -n DEV 1
Linux 3.10.0-693.21.1.el7.x86_64 (localhost.localdomain)        06/30/20        _x86_64_        (2 CPU)
16:03:16        IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s
16:03:17         eth0     24.00      5.00      2.46      0.54      0.00      0.00      0.00

#9.sar -n TCP,ETCP 1
#这是对TCP关键指标的统计
#active/s:每秒本地发起的TCP连接数（例如通过connect()发起的链接）
#passive/s：每秒远程发起的连接数（例如通过accept()接收的链接）
#retran/s：每秒TCP重传数
#这种主动和被动统计数通常用作对系统负载的粗略估计
[root@localhost ~]# sar -n TCP,ETCP 1
Linux 3.10.0-693.21.1.el7.x86_64 (localhost.localdomain)        06/30/20        _x86_64_        (2 CPU)

16:08:18     active/s passive/s    iseg/s    oseg/s
16:08:19         2.00      0.00      9.00     19.00

16:08:18     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
16:08:19         0.00      0.00      0.00      0.00      0.00
```
