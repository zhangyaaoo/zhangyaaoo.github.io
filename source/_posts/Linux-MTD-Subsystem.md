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

