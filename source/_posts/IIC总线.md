---
title: IIC总线
date: 2019-12-21 23:05:18
tags:
 - "嵌入式"
 - "IIC"
categories: 嵌入式
---


[资料来源](http://www.ti.com/lit/an/slva704/slva704.pdf)

## IIC总线上的三种信号类型

### 开始和结束信号

![iic-start-stop](https://raw.githubusercontent.com/zhangyaaoo/ImageBed/master/iic/iic-start-stop.png)

### 应答信号

![iic-ack](https://raw.githubusercontent.com/zhangyaaoo/ImageBed/master/iic/iic-ack.png)



### 一个字节的传输

![iic-single-byte-transfer](https://raw.githubusercontent.com/zhangyaaoo/ImageBed/master/iic/iic-single-byte-transfer.jpg)





> 看着这样的时序图的时候，有一个暗线：时间。既然叫“时序图”，那么时间是隐藏在图中的很重要变量，



![iic-single-byte-transfer-t](https://raw.githubusercontent.com/zhangyaaoo/ImageBed/master/iic/iic-single-byte-transfer-t.png)



**所以从左到右，随着时间的流逝，首先发送的数据是MSB，最后发送的是LSB。**





## IIC协议内容

首先区分下面几个名词

- MASTER (主机)
- SLAVE (从机)
- TRANSMITTER (发送方)
- RECEIVER (接收方)

==协议要求：==

1. 开始和结束信号只能由主机发送。
2. 应答信号由数据接收方发送，数据接收方可以是主机，也可以是从机。
3. SCL上的时钟信号始终都是由主机发送的。
4. 在传输数据时：SDA上传输的数据必须在SCL为高电平期间保持稳定，SDA上的数据只能在SCL为低电平期间变化。在SCL为高电平期间改变SDA上的数据将被视为开始或者结束信号。
5. IIC传输的字节必须是8位。每个字节传输后都要跟随一个应答信号。对每次数据传输的字节数没有严格限制。
6. 先发送高位字节(MSB)。



## SDA/SCL线如何输出高低电平

### 拉低

![iic-pulling-down](https://raw.githubusercontent.com/zhangyaaoo/ImageBed/master/iic/iic-pulling-down.jpg)



**当主机或从机想输出一个低电平，通过使FET导通，将引脚线接地，输出一个低电平。**

The logic wanting to transmit a low will activate the pull-down FET, which will provide a short to ground, pulling the line low.



### 拉高

![iic-release-high](https://raw.githubusercontent.com/zhangyaaoo/ImageBed/master/iic/iic-release-high.jpg)

**当主机或从机想输出一个高电平(只能通过释放总线)，通过使FET截止，将引脚线悬空，由于上拉电阻的作用，引脚将输出一个高电平。**

When the slave or master wishes to transmit a logic high, it may only release the bus by turning off the pull-down FET. This leaves the bus floating, and the pull-up resistor will pull the voltage up to the voltage rail, which will be interpreted as a high. The figure shows the flow of current through the pull-up resistor, which pulls the bus high.


### SDA、SCL线上的上拉电阻
![iic-pin-internal](https://raw.githubusercontent.com/zhangyaaoo/ImageBed/master/iic/iic-pin-internal.jpg)


最近，一些工程师在处理IIC单片机接口的工作问题时，对外部接上拉电阻的做法感到疑惑。由于单片机内部已经设置了上拉电阻，对于外部是否还需要接上拉的情况业界一直存在争议。针对I2C是否有必要接上拉的问题，就让牛人来为我们进行深入解读。

由于一些单片机型号内部就设置了上拉电阻，因此有些上拉能力够了，是可以不加上拉电阻，有些不够，那就必须在外部加上拉电阻。在这种情况下，主要是取决于工程师所使用的单片机是否有标准的IIC标准接口。如果单片机使用了标准的IIC接口，那么接口在使能时引脚将进入漏极开路模式，可以省去外部接入的上拉电阻。但如果是使用单片机的引脚模拟IIC协议的话，就需要结合单片机引脚是否支持漏极开路模式或者上拉模式来进行判断，这种情况下一般是需要接入一个外部的上拉电阻的。

除此之外，在IIC接口接入上拉电阻，也可以起到保护作用。由于I2C接口在工作时主要负责的是对高低电平检测的作用，一旦没有了上拉电阻的保护而直接接电源，出现器件拉低时整个系统就非常危险。根据I2C总线规范，总线空闲时两根线都必须为高。根据IIC总线规范的要求，总线空闲时两根线都必须为高。但由于IIC接口采用Open Drain机制，本身只能输出低电平而无法主动输出高电平，所以只能通过外部上拉电阻RP将信号线拉至高电平。因此I2C总线上的上拉电阻是必须要接入的。

结语

工程师需要依据IIC接口的具体情况，判断是否有必要在外部单独接入一个上拉电阻。如果没有使用标准的I2C接口，为了系统的稳定和安全，在外部进行上拉电阻的接入就是必须的工作了。[摘抄自这里](https://www.dianyuan.com/article/30297.html)

## 一次完整的传输
![iic-data-transfer](https://raw.githubusercontent.com/zhangyaaoo/ImageBed/master/iic/iic-data-transfer.jpg)
主机发送开始信号后，随后发送7-bit的从机地址，第8位发送读写位，读写位标志着：主机接下来是写数据到从设备，还是从从设备读数据。

然后，**主机释放SDA线 (释放以后，由于上拉电阻的作用，SDA线上的电平状态被上拉电阻拉高)，等待从设备的应答**。每一个字节的数据传送完成后都会有一个位的应答。

应答时：从机将SDA线上的电平拉低，并在第9个时钟周期SCL为高电平时一直保持拉低状态，保证主机能够正常读取从机的应答。

**发送开始信号后，可以不发送结束信号，再一次发送开始信号。**

![iic-writemode-7-addr](https://raw.githubusercontent.com/zhangyaaoo/ImageBed/master/iic/iic-writemode-7-addr.jpg)

**主机读写从机寄存器中的数值：**
![iic-single-byte-write](https://raw.githubusercontent.com/zhangyaaoo/ImageBed/master/iic/iic-single-byte-write.jpg)


![iic-single-byte-read](https://raw.githubusercontent.com/zhangyaaoo/ImageBed/master/iic/iic-single-byte-read.jpg)
==注意==：主机接收器在接收到最后一个字节后，不会发出ACK信号。于是从机释放SDA线，以允许主机发出P信号结束传输. 

![iic-read-register](https://raw.githubusercontent.com/zhangyaaoo/ImageBed/master/iic/iic-read-register.jpg)

---

为什么向从机寄存器写数据时，不需要重新发送开始信号，而从从机寄存器读数据时，需要重新发送开始信号？

在发送开始信号后，主机需要发送从机地址和读写位，可以认为读写位规定了这次开始传输的数据流向，写：表示数据由主机给出，从机接收数据；读：表示数据由从机给出，主机接收数据。

当向从机寄存器写数据时，数据的流向一直是从主机流向从机；

当从从机寄存器读数据时，我们首先需要向从机给出寄存器地址，然后从机给主机发送寄存器中的值，数据的流向所有改变，所以我们需要在向从机写入寄存器地址后，重新发送开始信号，此时读写位为1，指明接下来从机发送数据给主机。

----

## 总线的状态

### 空闲状态

SDA、SCL都为高电平，开始信号发出前，结束信号发出后。

### 忙碌状态

开始信号发出后，结束信号发出前都视为忙碌状态。

## 实际波形分析
![iic-real-wave](https://raw.githubusercontent.com/zhangyaaoo/ImageBed/master/iic/iic-real-wave.jpg)**上图是一个实际的IIC传输的波形.(黄色:SCL 绿色:SDA)**

![iic-real-wave-one-byte](https://raw.githubusercontent.com/zhangyaaoo/ImageBed/master/iic/iic-real-wave-one-byte.jpg)
**实际的波形和上面理论分析的波形有所不同，不同的地方是**: SDA线上的数据是在时钟负跳变时几乎马上输出的，如序号2和3标记的地方。但是这个符合：SDA上传输的数据必须在SCL为高电平期间保持稳定，SDA上的数据只能在SCL为低电平期间变化。

 - 数据传输起始信号,(序号1标记的地方): 时钟为高时,数据线上电平的负跳变.
 - 数据传输过程中,时钟为高时,数据线上保持电平稳定. 如序号4和5标记的地方.
 - 起始位(6)，地址传输(7)，读写位(8)，应答(9)


![iic-real-wave-parse](https://raw.githubusercontent.com/zhangyaaoo/ImageBed/master/iic/iic-real-wave-parse.jpg)
