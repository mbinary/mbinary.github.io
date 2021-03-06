---
title: 『现代操作系统』IO设备
tags: [操作系统]
mathjax: false
date: 2018-06-17 20:57:27
categories:
        - 操作系统
keywords:
description: "IO 设备详细介绍, 包括盘, 时钟, 用户界面等组成以及原理"
top:
---


<!-- TOC -->

- [盘](#盘)
    - [硬件](#硬件)
        - [磁盘](#磁盘)
        - [RAID](#raid)
        - [CD-ROM](#cd-rom)
    - [磁盘格式化](#磁盘格式化)
    - [磁盘臂调度算法](#磁盘臂调度算法)
    - [错误处理](#错误处理)
    - [稳定存储器](#稳定存储器)
        - [目标](#目标)
        - [模型](#模型)
        - [原理](#原理)
- [时钟](#时钟)
    - [时钟硬件](#时钟硬件)
        - [构成](#构成)
        - [模式](#模式)
    - [时钟软件](#时钟软件)
    - [软定时器](#软定时器)
- [用户界面](#用户界面)
    - [键盘](#键盘)
        - [键盘软件](#键盘软件)
        - [模式,](#模式)
        - [回显](#回显)
        - [规范模式下的特殊字符](#规范模式下的特殊字符)
    - [鼠标](#鼠标)
        - [硬件](#硬件-1)
        - [原理](#原理-1)
    - [X Windows System](#x-windows-system)
- [瘦客户机(thin client)](#瘦客户机thin-client)
- [电源管理](#电源管理)
    - [思路](#思路)
    - [硬件问题](#硬件问题)
    - [OS 问题](#os-问题)
        - [显示器](#显示器)
        - [硬盘](#硬盘)
        - [CPU](#cpu)
        - [内存](#内存)

<!-- /TOC -->
<a id="markdown-盘" name="盘"></a>
# 盘
<a id="markdown-硬件" name="硬件"></a>
## 硬件
如磁盘, 硬盘, 软盘, 常作为辅助存储器.

磁记录, 根据每个`小磁针`的极性记录 0, 1. 写的时候, 改变电流方向利用电流的磁效应感性去磁性. 读的时候,利用电磁感应判断极性.

<a id="markdown-磁盘" name="磁盘"></a>
### 磁盘
磁盘被组织成柱面, 每个柱面包含若干磁道,磁道数与垂直堆叠的磁头个数相同. 磁道被分成若干扇区.

重叠寻道(overlapped seek): 控制器同时操控多个驱动器进行寻道.

![IO-device-1](https://raw.githubusercontent.com/mbinary/mbinary.github.io/hexo/source/images/IO-device-1.png)
大多数磁盘都有一个虚拟的几何规格呈现给 OS, 控制器可以将虚拟的几何规格映射到实际的物理位置

<a id="markdown-raid" name="raid"></a>
### RAID
(Redundant Array of Inexpensive Disk)

CPU 性能提升快于磁盘, 出现 `并行 I/O` 的思想--RAID ( 相对的,  SLED(single large expensive disk)
RAID 背后的思想是将一个装满了的磁盘盒子安装到计算机上, 用RAID 控制器替换磁盘控制器卡,将数据复制到整个RAID 上, 然后继续常规的操作

对 RAID 的并行操作, 目前有0级到7级 RAID. 层级这个名称或许用词不当, 这里没有分层结构,只是不同的组织形式而已

![IO-device-2](https://raw.githubusercontent.com/mbinary/mbinary.github.io/hexo/source/images/IO-device-2.png)
* 0

  * 组成: 将 RAID 模拟的虚拟单个磁盘划分成 Stripe , 每个 stripe 带有 K个扇区, `0`~ `k-1 `扇区为 条带0, `k`~`2k-1`为条带1...  注意还未引入冗余, 实际上不是正真的 RAID
  * 原理: 软件发出一条命令,如读取一个由四个连续 stripe 组成的数据块, 那么 RAID 控制器将命令分解为四个单独的命令, 每条命令对应每个磁盘块, 并行操作. 而软件并不知道这些
  * 特点: 对于大量数据量的请求性能最好
* 1

真正的 RAID, 复制了所有的磁盘. 执行写, 每个 stripe 都被写了两次 执行读,可以任选一个副本. 具有很好的容错性(即时一个驱动器崩溃,有副本). 恢复简单:,安装一个新的驱动器复制到其上就可以了.
* 2

0和1操作的扇区条带, 而 2 是工作在字(甚至字节)的基础上. 如 将每个字节分割成 4位半字节对, 并形成 7 位的汉明码. 然后同步读写.
效率高,(即时损失一位, 汉明码可以轻松处理). 
但是这要求所有驱动器旋转必须同步.
* 3

3 是 2 的简化, 为每个数据字计算奇偶校验位并写入即可.
* 4,5

使用stripe ,但是同样写有奇偶校验字., 然而计算 奇偶校验会降低效率.

<a id="markdown-cd-rom" name="cd-rom"></a>
### CD-ROM

![IO-device-3](https://raw.githubusercontent.com/mbinary/mbinary.github.io/hexo/source/images/IO-device-3.png)

<a id="markdown-磁盘格式化" name="磁盘格式化"></a>
## 磁盘格式化
在磁盘使用之前,每个盘片必须经由软件完成低级格式化(low-level format). 格式化的磁盘容量约为物理容量 70%.
<a id="markdown-磁盘臂调度算法" name="磁盘臂调度算法"></a>
## 磁盘臂调度算法
时间, 主要是寻到时间较长.
* 寻道时间
* 旋转延迟
* 实际数据传输时间


算法
* FCFS 先来先服务: 很难优化寻道时间,
* SSF(Shortest Seek First) 最短寻道优先: 很可能在中间往返, 而不能处理靠边的请求, 响应时间很长
* 电梯算法(elevator algorithm): 电梯也是用的这种算法. 保存向一个方向移动直到向那个方向再没有服务请求到来.

![电梯算法](https://raw.githubusercontent.com/mbinary/mbinary.github.io/hexo/source/images/电梯算法.png)
 

由于寻道和旋转延迟太影响性能了, 所以一次只读一两个扇区效率低下,. 许多磁盘控制器常读出多个扇区并进行高速缓存(独立于操作系统的 高速缓存).

<a id="markdown-错误处理" name="错误处理"></a>
## 错误处理
制造时的瑕疵可能出现坏扇区, 厂商需要设置控制器来处理坏区, 有如下方法, 控制器需要维护一个映射表来替换坏区
![IO-device-4](https://raw.githubusercontent.com/mbinary/mbinary.github.io/hexo/source/images/IO-device-4.png)

也可以由操作系统软件来处理, 首先要获得坏区列表,然后建立重映射表.

另外的问题: `备份`
操作系统要隐藏坏块, 使对备份应用程序不可见.

**AV盘🙈😮**
![IO-device-5](https://raw.githubusercontent.com/mbinary/mbinary.github.io/hexo/source/images/IO-device-5.png)

<a id="markdown-稳定存储器" name="稳定存储器"></a>
## 稳定存储器
<a id="markdown-目标" name="目标"></a>
### 目标
为了不丢失或损坏数据,保持磁盘的移植性,稳定存储器有必要的

它们是: 在写操作到来时, 要么成功执行, 要么对现有数据没有影响, 即时发生了磁盘或者 CPU 错误.并且是`在软件中实现的 `

<a id="markdown-模型" name="模型"></a>
### 模型
* 在磁盘写一个块时, 写操作要么正确,要么错误,并且该错误可以在随后的读操作中通过检查 ECC 域检查出来
* 一个被正确写入的扇区可能会自发地变坏并且变得不可读(但是概率很小, 可以忽略)
* CPU 可能出现故障, 这时只能停机.


<a id="markdown-原理" name="原理"></a>
### 原理
使用一对完全相同的磁盘,对应的块一同工作形成一个无差错的块. 定义如下三种操作
![IO-device-6](https://raw.githubusercontent.com/mbinary/mbinary.github.io/hexo/source/images/IO-device-6.png)

<a id="markdown-时钟" name="时钟"></a>
# 时钟
既不是块设备, 也不是字符设备
<a id="markdown-时钟硬件" name="时钟硬件"></a>
## 时钟硬件
<a id="markdown-构成" name="构成"></a>
### 构成
晶体震荡器, 计数器, 存储寄存器
![IO-device-7](https://raw.githubusercontent.com/mbinary/mbinary.github.io/hexo/source/images/IO-device-7.png)

<a id="markdown-模式" name="模式"></a>
### 模式
* 完成模式(one-shot mode): 当时钟启动时它把存储寄存器的值复制到计数器中，然后，来自晶体的每一个脉冲使计数器减1。当计數器变为0时．产生一个中断．并停止工作，直到软件再一次显式地启动它。
* 方波模式(square-wavemode): 当计数器变为0并且产生中断之后，存储寄存器的值自动复制到计数器中，并且整个过程无限期地再次重复下去。这些周期性的中断称为时忡滴答(clocktick).


可编程时钟的优点是其中断頻率可以由软件控制。可编程时钟芯片通常包含两个或三个独立的可编程时钟.

为了防止计算机的电源被切断时丢失当前时间，大多数计算机具有一个由电池供电的备份时钟，它是由在数字手表中使用的那种类型的低功耗电路实現的。电池时钟可以在系统启动的时候读出，如果不存在备份时钟，软件可能会向用户询问当前日期和时f对于一个连人网络的系统而言还有一种从远程主机获取当前时间的标群方法。无论是哪种情况，当前时间都要像UNIX所做的那样转换成自 1970 年 1 月 1 日 12 时 的UTC滴答数.

<a id="markdown-时钟软件" name="时钟软件"></a>
## 时钟软件
时间硬件所做的只是根据已知时间间隔产生中断,其他与时间有关的工作都是软件--时钟驱动程序完成的. 如
* 维护日时间

因为 32 位的寄存器以滴答计数最多计数 2 年
![IO-device-8](https://raw.githubusercontent.com/mbinary/mbinary.github.io/hexo/source/images/IO-device-8.png)

* 防止进程超时运行

每启动一个进程,调度程序将一个计数器初始化为以`滴答`为单位的该进程的时间片取值. 每次时钟中断时,时钟驱动程序将时间片计数器减少1,当为 0 , 时钟驱动程序就 `调用调度程序`激活另一个进程
* 对 CPU 的使用情况记账

每启动一个程序, 需要对它使用的 CPU 时间计时,
  * 使用一个辅助定时器, 
  * 全局变量维护一个指针(不太精确),指向进程表中当前运行的进程的表项,每一个 滴答, 就加 1
* 处理用户进程提出的 alarm 系统调用
* 为系统本身的各个部分提供监视定时器
* 完成概要剖析,监视和统计信息收集


注意: 在时钟中断期间, 时钟驱动程序需要做: 
* 将实际时间加 1
* 将时间片减 1 并检查是否为0
* 对 CPU 记账
* 将报警计数器减 1
* ...

做很多事, 所以要仔细安排以加快速度

<a id="markdown-软定时器" name="软定时器"></a>
## 软定时器
大多数计算机有辅助可编程时钟, 可以设置它以程序需要的任何速率引发定时器中断.
<a id="markdown-用户界面" name="用户界面"></a>
# 用户界面
<a id="markdown-键盘" name="键盘"></a>
## 键盘
键盘包含一个嵌入式微处理器. 每按下一个键, 产生一个中断,释放键, 也产生一个中断.
<a id="markdown-键盘软件" name="键盘软件"></a>
### 键盘软件
I/O 端口中段数字是键标号, 称为 扫描码(scan code),而不是 ASCII 码. 
键盘不超过128个, 所以只需7 个表示键编号, 第 8位为0 表示按下, 为 1 表示释放
<a id="markdown-模式" name="模式"></a>
### 模式, 
* 非规范模式: 直接将扫描码传递给用户程序, 如 1<backspace>2  (这样过于原始, 且依赖机器
* 规范模式: 处理行内编辑, 如上面只需传递 2 


<a id="markdown-回显" name="回显"></a>
### 回显
键盘和监视器本来是两种 I/O 设备, 但用户的使用将他们联系起来, 即用户习惯 键入 , 然后在监视器上显示出来, 称为 `echoing 回显` .  
这里就需考虑回显带来的问题,
* 一行超过 80 个字符, 换行?
* 制表符的处理


<a id="markdown-规范模式下的特殊字符" name="规范模式下的特殊字符"></a>
### 规范模式下的特殊字符
![IO-device-9](https://raw.githubusercontent.com/mbinary/mbinary.github.io/hexo/source/images/IO-device-9.png)

<a id="markdown-鼠标" name="鼠标"></a>
## 鼠标
<a id="markdown-硬件-1" name="硬件-1"></a>
### 硬件
光学鼠标在其底部装备有一个或多个发光二极管和光电探瀏器。现代光学鼠标在其中有图像处理芯片并且获取处于它们下方的连续的低分辨率照片，寻找从图像到图像的变化。

鼠标可能具有一个，两个或者三个按钮,某些鼠标具有滚轮，可将额外的数据发送回计算机.

无线鼠标使用低功率无线电，例如使 Bluetooth将數据发这回计算机，而有线鼠标是通过导线将数据发送回。

<a id="markdown-原理-1" name="原理-1"></a>
### 原理
当鼠标在随便哪个方向移动了一个确定的最小距离，或者按钮被接下或释放时。都会有一条消息发送给计算机。最小距离大约是0.1mm（尽管它可以在软件中设置）。有些人将这一单位称为一个鼠标步(mickey) 

发送的消息为 `(Δx,Δy,button)`. 通常，消息占3字节, 速度最多每秒40次．
注意，鼠标仅仅指出位上的变化，而不是绝对位置本身。如果轻轻地拿起鼠标并且轻轻地放下而
不导致橡皮球旋转．那么就不会有消息发出。
某些GUI 区分单击与双击鼠标接钮。如果两次点击在空间上〈鼠标步）足够接近，并且在时间上(亳秒)也足够接近，那么就会发出双击信号。最大的“足够接近”是软件的事情，并且这两个参数通常是用户可设置的。
<a id="markdown-x-windows-system" name="x-windows-system"></a>
## X Windows System
几乎所有 UNIX 系统的用户界面都以 X 为基础. 如 GNOME, KDE

当 X 在一台机器上运行时, 采集键盘与鼠标输入并且输出到屏幕上的软件称为 `X server`
X server 常位于用户计算机的内部, 而 X 客户可能在远程计算服务器上.

![Client and Server](https://raw.githubusercontent.com/mbinary/mbinary.github.io/hexo/source/images/Client and Server.png)

注意 X 只是一个窗口系统, 不是一个完全的 GUI. 要获得完全的 GUI, 需要在器上运行其他软件层.

<a id="markdown-瘦客户机thin-client" name="瘦客户机thin-client"></a>
# 瘦客户机(thin client)
![IO-device-10](https://raw.githubusercontent.com/mbinary/mbinary.github.io/hexo/source/images/IO-device-10.png)

<a id="markdown-电源管理" name="电源管理"></a>
# 电源管理
<a id="markdown-思路" name="思路"></a>
## 思路
* 当计算机的某些部件(主要是 I/O 设备)不同的时候让 OS 关闭他们.
* 应用程序使用较少的能量


<a id="markdown-硬件问题" name="硬件问题"></a>
## 硬件问题
计算机状态: 工作, 睡眠, 休眠, 关闭
权衡: 消耗电量从左到右递减, 关闭状态不耗电量. 但是从睡眠, 休眠状态恢复到工作状态, 后者需要更多的时间和电量.

<a id="markdown-os-问题" name="os-问题"></a>
## OS 问题
通过算法或者试探, 让 OS 对关于关闭什么设备以及何时关闭能够作出良好的决策
<a id="markdown-显示器" name="显示器"></a>
### 显示器
一段时间后可以关闭屏幕(是睡眠,可以立即唤醒))
改进: 将屏幕分成多个区域, 可以关闭当前窗口(或者用户自己定义)未覆盖的区域. 窗口管理器还可以使窗口与区域对齐, 进一步地, 部分照亮关闭的区域
![IO-device-11](https://raw.githubusercontent.com/mbinary/mbinary.github.io/hexo/source/images/IO-device-11.png)
<a id="markdown-硬盘" name="硬盘"></a>
### 硬盘
即时不存在存取操作,硬盘也消耗大量的能量以保持高速旋转.(不然加速需要很长时间 呢(●ˇ∀ˇ●)). 但是注意 停止硬盘是休眠不是睡眠.
此外,重新硬盘启动会消耗更多的能量. 
因此, 每个硬盘有一个特征时间 T, 为它的盈亏平衡点. 如果能预测将来多久才用到硬盘(可以基于存取历史), 那么如果将来时间讲个 ΔT > T, 就可以关闭.
<a id="markdown-cpu" name="cpu"></a>
### CPU
睡眠中的 CPU 几乎不耗电, 只需等待中断的到来才唤醒. 
CPU 电压可以用软件降低,但会降低时钟速度. 由于电能消耗与电压的平方成正比, 而电压与时钟速度成正比, 所以可以有降低的平衡点来盈利

<a id="markdown-内存" name="内存"></a>
### 内存
* 刷新然后关闭高速缓存

cache 可以重新加载且不损失信息, 而且速度快, 
* 将主存内容写到磁盘上, 然后关闭主存本身.
