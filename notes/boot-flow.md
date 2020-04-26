### boot starting flow

```flow
st=>start: Start
op_run_boot=>operation: CPU上电自动读取一级boot，并运行。
以下操作和判断均在一级boot中
op_get_start_flag=>operation: 获取boot启动标记，
（其值为A、B、0，三者之一）
cd_is_last_start_fail=>condition: 判断boot启动标记
是否为B？
op_read_bootimage=>operation: read 二级boot镜像，
if flag is 0, read boota,
if flag is A, read bootb,
op_set_start_flag=>operation: switch boot启动标记，
if flag is 0, set to A,
if flag is A, set to B,
op_go_sec_boot=>operation: 运行二级boot，
以下操作和判断均在二级boot中
op_sec_boot_set_flag=>operation: sysctrl_init中，
set boot启动标记为 0，
标志着二级boot成功运行
e1=>end: 二级boot启动失败，进入一级boot命令行
e2=>end: End
st->op_run_boot->op_get_start_flag->cd_is_last_start_fail(yes)->e1
cd_is_last_start_fail(no)->op_read_bootimage->op_set_start_flag->op_go_sec_boot->op_sec_boot_set_flag->e2
```



- **如果二级boot启动失败，进入一级boot命令行后怎么办？**

  进入一级boot命令行后，由于一级boot里网口并没有初始化，所以只能使用`loady` 命令，通过串口将二级boot的镜像导入到内存，使用`go` 命令，手动运行二级boot，这样就可以利用网口升级二级boot的镜像。

- **boot 启动标记保存在哪？**

  boot启动标记保存在CPU的寄存器中，该寄存器上电复位值为 0，掉电丢失；但是设备复位重启并不会影响到该寄存器的值。




### booting kernel flow without security

```flow
st=>start: Start
op_scan_main=>operation: scan main pt
op_get_bootflag=>operation: get kernel flag
op_sw_bootflag=>operation: switch kernel flag
op_read_kernel=>operation: read kernel image
op_boot_kernel=>operation: booting kernel
op_set_kernel_fail=>operation: set kernel flag failed
cd_is_kernel_fail=>condition: 判断内核上一次
是否启动失败？
e=>end: End
st->op_scan_main->op_get_bootflag->cd_is_kernel_fail
cd_is_kernel_fail(yes)->op_sw_bootflag->op_read_kernel->op_boot_kernel->e
cd_is_kernel_fail(no)->op_read_kernel
```

- **boot阶段如何判断上一次内核启动是否失败？**

  内核启动成功或者失败的状态保存在一个CPU内部的一个寄存器中，该寄存器的特性是：上电复位值为 0，掉电丢失；但是设备复位重启并不会影响到该寄存器的值。

  在内核启动成功后，在挂载rootfs前，内核代码回去设置该寄存器，将内核启动的状态设置为成功。在启动内核前，在boot代码中该寄存器的值设置为失败。

  如果内核启动异常，导致看门狗复位重启，这个状态寄存器的值没有设置为成功，那么在boot阶段就可以知道上一次内核启动异常。

- **kernel、rootfs、app、frk的启动标记存放在何处？**

  这些标记存放在main分区中的ubi卷中，卷名分别为flaga, flagb，flaga 与 flagb 两个卷中的标记数据互为备份。

- **切换标记的动作是否保存到了flash中？**

  boot中切换标记后，并没有将新的标记写到main分区的卷中。

- **hi_boot 中 bootflag 和 bootstate 有啥区别？**

  bootflag 和 bootstate 两个结构体中各个标记数据的初始值是从main分区中卷中读取的。但是bootflag中的标记值是不会更改的，boot代码中切换标记的操作是切换bootstate中的标记。bootflag 和 bootstate中的数据会通过 bootargs 传给内核。

- **hi_boot 中 bootflag、bootstate 和 系统起来后的activeflag、bootflag的区别？**

  boot中的bootflag和bootstate中的内容会通过bootargs传给内核，内核启动后，挂载rootfs如果发生回滚，标记的更改也在bootstate中。内核会创建两个proc文件，activeflag文件对应bootstate，bootflag文件对应bootflag。

### booting kernel flow with security

```flow
st=>start: Start
e1=>end: rootfs主备校验都失败，退出
e2=>end: app主备校验都失败，退出
e=>end: End
op_scan_main=>operation: scan main pt，获取启动标记信息
op_verify_rootfs_1=>operation: 校验rootfs签名
op_verify_rootfs_2=>operation: 校验bkup rootfs签名
op_sw_rootfs_state=>operation: 切换rootfs标记信息

op_verify_app_1=>operation: 校验app签名
op_verify_app_2=>operation: 校验bkup app签名
op_sw_app_state=>operation: 切换app标记信息

cd_verifyr_is_fail_1=>condition: 校验是否失败？
cd_verifyr_is_OK_2=>condition: 校验是否成功？
cd_verifya_is_fail_1=>condition: 校验是否失败？
cd_verifya_is_OK_2=>condition: 校验是否成功？

st->op_scan_main->op_verify_rootfs_1->cd_verifyr_is_fail_1(no)->op_verify_app_1
cd_verifyr_is_fail_1(yes)->op_sw_rootfs_state->op_verify_rootfs_2->cd_verifyr_is_OK_2(no)->e1
cd_verifyr_is_OK_2(yes)->op_verify_app_1->cd_verifya_is_fail_1(no)->e
cd_verifya_is_fail_1(yes)->op_sw_app_state->op_verify_app_2->cd_verifya_is_OK_2(yes)->e
cd_verifya_is_OK_2(no)->e2

```

### mount rootfs flow

```flow
st=>start: Start
op_get_rootfsflag=>operation: get rootfs flag
op_mount_as_squashfs=>operation: mount rootfs as squashfs
op_mount_as_ubifs=>operation: mount rootfs as ubifs
op_sw_rootfsflag=>operation: switch rootfs flag
op_mount_bkup_as_squashfs=>operation: mount bkup as squashfs
op_mount_bkup_as_ubifs=>operation: mount bkup as ubifs
cd_is_mount_fail=>condition: 挂载失败?
cd_is_mount_ubi_fail=>condition: 挂载失败?
cd_is_bkup_mount_fail=>condition: 挂载失败?
e1=>end: 挂载成功退出
e2=>end: End
e3=>end: 挂载成功退出
e4=>end: 挂载成功退出
st->op_get_rootfsflag->op_mount_as_squashfs->cd_is_mount_fail
cd_is_mount_fail(no)->e1
cd_is_mount_fail(yes)->op_mount_as_ubifs->cd_is_mount_ubi_fail
cd_is_mount_ubi_fail(no)->e3
cd_is_mount_ubi_fail(yes)->op_sw_rootfsflag->op_mount_bkup_as_squashfs->cd_is_bkup_mount_fail
cd_is_bkup_mount_fail(no)->e4
cd_is_bkup_mount_fail(yes)->op_mount_bkup_as_ubifs->e2
```




