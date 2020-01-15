---
title: Linux-MTD-Subsystem
date: 2019-12-25 18:08:18
tags:
 - "Linux"
 - "MTD Subsystem"
categories: Linux
---

[资料来源](http://www.linux-mtd.infradead.org/doc/general.html)

MTD(Memory Technology Devices)子系统提供了一个原始Flash设备的抽象层。对于不同类型的Flash设备，MTD子系统向上提供了相同的API。比如：NAND、OneNAND、NOR、AG-AND、ECC'd NOR 等等。

MTD子系统并不管理块设备，比如：MMC, eMMC, SD, CompactFlash 等等都属于块设备。这些块设备内部也有原始的Flash，但芯片内部有FTL(Flash Translation Layer)，这让这些设备对外呈现出块设备的特性。

更多关于[Raw Flash 和 FTL Devices](http://www.linux-mtd.infradead.org/doc/ubifs.html#L_raw_vs_ftl) 的对比。

注意：SSD, MMC, eMMC, RS-MMC, SD, mini-SD, micro-SD, USB flash drive, CompactFlash, MemoryStick, MemoryStick Micro, and other FTL devices are **block devices**, not raw flash devices.

# MTD device & block device & char device

> **MTD is neither a block nor a char device.** 

在老的Unix系统中，只认识块设备和字符设备，**字符设备**是键盘和鼠标之类的东西。你能从字符设备中读取当前的数据，但是字符设备没有大小，不能查找（seek）。**块设备**有固定的大小，并且可以被查找，块设备被组织为一个个的块，一个块通常是512字节。

Flash既不是字符设备也不是块设备，它虽然看起来更像块设备，但是与块设备还是有区别：**块设备不区分写和擦**。所以有了MTD设备。

**块设备和MTD设备的区别**：

| **Block device**                                             | **MTD device**                                               |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Consists of sectors                                          | Consists of eraseblocks                                      |
| Sectors are small (512, 1024 bytes)                          | Eraseblocks are larger (typically 128KiB)                    |
| Maintains 2 main operations: **read sector** and **write sector** | Maintains 3 main operations: **read from eraseblock**, **write to eraseblock**, and **erase eraseblock** |
| Bad sectors are re-mapped and hidden by hardware (at least in modern LBA hard drives); in case of FTL devices it is the responsibility of FTL to provide this | Bad eraseblocks are not hidden and should be dealt with in software |
| Sectors are devoid of the wear-out property (in FTL devices it is the responsibility of FTL to provide this) | Eraseblocks wear-out and become bad and unusable after about 103 (for MLC NAND) - 105 (NOR, SLC NAND) erase cycles |

**MTD subsystem has the following interfaces**.

- MTD character devices - usually referred to as `/dev/mtd0`, `/dev/mtd1`, and so on. These character devices provide I/O access to the raw flash. They support a number of `ioctl` calls for erasing eraseblocks, marking them as bad or checking if an eraseblock is bad, getting information about MTD devices, etc.
- The `sysfs` interface is relatively newer and it provides full information about each MTD device in the system. This interface is easily extensible and developers are encouraged to use the `sysfs` interface instead of older `ioctl` or `/proc/mtd` interfaces, when possible. The `sysfs` interface for the mtd subsystem is documentated in the kernel, and currently can be found at `Documentation/ABI/testing/sysfs-class-mtd`.
- The `/proc/mtd` proc file system file provides general MTD information. 这是遗留下来的接口，sysfs接口提供了更多的信息。

**The mtdblock driver**

The `mtdblock` driver 在MTD设备之上模拟块设备。它甚至没有坏块处理，因此它实际上不适用于NAND Flash。它的工作方式是将整个Flash块缓存在RAM中，有修改的请求时，先擦除整个块，并写回修改后的内容。这意味着the `mtdblock` driver不会尝试进行任何优化，并且在读、擦、回写的过程中，如果突然断电，将丢失大量数据。而且，the `mtdblock` driver不执行任何耗损均衡或位翻转处理。

人们通常将mtdblock视为FTL层，并尝试使用mtdblock在Raw Flash之上使用基于块设备的文件系统。在大多数情况下，这是错误的。换句话说，请不要使用mtdblock，除非您确切知道自己在做什么。就我自己在实际使用中而言，我在mtdblock设备上挂载过jffs2和squashfs。

There is also a read-only version of this driver, 主要用在内存紧张的场合. However, just like the R/W version of the driver, there is no wear-levelling and bit-flips handling.

Instead of using this old driver, you may check the R/O block device emulation provided by `UBI` useful. Please refer to the [UBI section](http://www.linux-mtd.infradead.org/doc/ubi.html#L_ubiblock) for more details.



# MTD NAND Driver Programming Interface

[Online Home Page](http://www.linux-mtd.infradead.org/tech/mtdnand/book1.html)

