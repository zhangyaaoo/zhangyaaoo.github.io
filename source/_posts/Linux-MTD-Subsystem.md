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

MTD子系统并不管理块设备，比如：MMC, eMMC, SD, CompactFlash 等等都属于块设备。这些块设备内部也有原始的Flash，另外芯片内部也有FTL(Flash Translation Layer)，让这些设备对外呈现出块设备的特性。

更多关于[Raw Flash 和 FTL Devices](http://www.linux-mtd.infradead.org/doc/ubifs.html#L_raw_vs_ftl) 的对比。

注意：SSD, MMC, eMMC, RS-MMC, SD, mini-SD, micro-SD, USB flash drive, CompactFlash, MemoryStick, MemoryStick Micro, and other FTL devices are **block devices**, not raw flash devices.

块设备和MTD设备的区别：

| **Block device**                                             | **MTD device**                                               |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Consists of sectors                                          | Consists of eraseblocks                                      |
| Sectors are small (512, 1024 bytes)                          | Eraseblocks are larger (typically 128KiB)                    |
| Maintains 2 main operations: **read sector** and **write sector** | Maintains 3 main operations: **read from eraseblock**, **write to eraseblock**, and **erase eraseblock** |
| Bad sectors are re-mapped and hidden by hardware (at least in modern LBA hard drives); in case of FTL devices it is the responsibility of FTL to provide this | Bad eraseblocks are not hidden and should be dealt with in software |
| Sectors are devoid of the wear-out property (in FTL devices it is the responsibility of FTL to provide this) | Eraseblocks wear-out and become bad and unusable after about 103 (for MLC NAND) - 105 (NOR, SLC NAND) erase cycles |

