---
layout: post
title: IBM x3650 M4服务器安装Red Hat Enterprise Linux问题
permalink: x3650m4-install-RedHat5
description: x3650m4安装RedHat5.5
category : lessons
date: 2013-08-12 21:35:30 +08:00
tags: "Linux"

---

IBM x3650 M4服务器安装Red Hat Enterprise Linux问题
---

##问题1：由于驱动问题，安装程序无法找磁盘 ##



1. 下载Raid驱动。

Red Hat Enterprise Linux 5 版本的驱动:

ibm_dd_sraidmr_00.00.06.19_rhel5_32-64.tgz  [下载地址](http://www-947.ibm.com/support/entry/portal/docdisplay?lndocid=MIGR-5073142)

2.解压，并将文件拷贝到U盘。

3.插入U盘，使用光盘引导，在引导界面boot: 输入 linux dd 回车；

	boot: linux dd

4.安装过程中根据linux内核版本选择相应的ISO文件（在disks目录下）
例如：64位的RedHat 5.5选择disks/dud-2.6.18-194-64.RHEL5.iso

5.安装程序应该正确识别磁盘，进行系统安装。

----------

##问题2：lspci显示设备为Unknown device##
如下所示：

    # lspci
    00:00.0 Host bridge: Intel Corporation Unknown device 3c00 (rev 07)
    00:01.0 PCI bridge: Intel Corporation Unknown device 3c02 (rev 07)
    00:04.0 System peripheral: Intel Corporation Unknown device 3c20 (rev 07)
    00:04.1 System peripheral: Intel Corporation Unknown device 3c21 (rev 07)
	...... 
    80:05.2 System peripheral: Intel Corporation Unknown device 3c2a (rev 07)

### 解决方法： ###

更新pci-ids文件：
从[pciids.sourceforge.net](http://pciids.sourceforge.net/v2.2/pci.ids)下载，并上传到服务器/root/目录下

备份原来的pci.ids文件：

	
	[root@localhost ~]#cd /usr/share/hwdata/
	[root@localhost hwdata]#mv pci.ids pci.ids_BAK_20130812
	[root@localhost hwdata]#mv /root/pci.ids /usr/share/hwdata/
    [root@localhost hwdata]# lspci|more
    00:00.0 Host bridge: Intel Corporation Xeon E5/Core i7 DMI2 (rev 07)
    00:01.0 PCI bridge: Intel Corporation Xeon E5/Core i7 IIO PCI Express Root Port 1a (rev 07)
    00:02.0 PCI bridge: Intel Corporation Xeon E5/Core i7 IIO PCI Express Root Port 2a (rev 07)
    00:02.2 PCI bridge: Intel Corporation Xeon E5/Core i7 IIO PCI Express Root Port 2c (rev 07)
    00:03.0 PCI bridge: Intel Corporation Xeon E5/Core i7 IIO PCI Express Root Port 3a in PCI Express Mode (rev 07)
    00:03.2 PCI bridge: Intel Corporation Xeon E5/Core i7 IIO PCI Express Root Port 3c (rev 07)
    00:04.0 System peripheral: Intel Corporation Xeon E5/Core i7 DMA Channel 0 (rev 07)
    00:04.1 System peripheral: Intel Corporation Xeon E5/Core i7 DMA Channel 1 (rev 07)
    00:04.2 System peripheral: Intel Corporation Xeon E5/Core i7 DMA Channel 2 (rev 07)
    00:04.3 System peripheral: Intel Corporation Xeon E5/Core i7 DMA Channel 3 (rev 07)
    00:04.4 System peripheral: Intel Corporation Xeon E5/Core i7 DMA Channel 4 (rev 07)
    00:04.5 System peripheral: Intel Corporation Xeon E5/Core i7 DMA Channel 5 (rev 07)
    00:04.6 System peripheral: Intel Corporation Xeon E5/Core i7 DMA Channel 6 (rev 07)
    00:04.7 System peripheral: Intel Corporation Xeon E5/Core i7 DMA Channel 7 (rev 07)
    00:05.0 System peripheral: Intel Corporation Xeon E5/Core i7 Address Map, VTd_Misc, System Management (rev 07)
    00:05.2 System peripheral: Intel Corporation Xeon E5/Core i7 Control Status and Global Errors (rev 07)
    00:11.0 PCI bridge: Intel Corporation C600/X79 series chipset PCI Express Virtual Root Port (rev 06)
    00:1a.0 USB controller: Intel Corporation C600/X79 series chipset USB2 Enhanced Host Controller #2 (rev 06)
    00:1c.0 PCI bridge: Intel Corporation C600/X79 series chipset PCI Express Root Port 1 (rev b6)
    00:1c.7 PCI bridge: Intel Corporation C600/X79 series chipset PCI Express Root Port 8 (rev b6)

已经可以正常识别所有PCI设备。

----------

###问题3：双网卡绑定无法正常工作，系统无法正常识别断开的链路###

由于intel网卡驱动问题，需要更新网卡驱动：
1,下载网卡驱动：根据型号从Intel官网下载，82575/6, 82580, I350, I210等型号的网卡可以[点击这里](https://downloadcenter.intel.com/confirm.aspx?httpDown=http://downloadmirror.intel.com/13663/eng/igb-4.3.0.tar.gz&lang=eng&Dwnldid=13663)下载。

2,上传并解压：igb-4.3.0.tar.gz

    [root@localhost ~]# tar -zxvf igb-4.3.0.tar.gz 
    igb-4.3.0/
    igb-4.3.0/igb.spec
    igb-4.3.0/igb.7
    igb-4.3.0/pci.updates
    igb-4.3.0/SUMS
    igb-4.3.0/src/
    igb-4.3.0/src/e1000_api.h
    igb-4.3.0/src/e1000_nvm.h
    igb-4.3.0/src/e1000_manage.c
    igb-4.3.0/src/igb_vmdq.h
    igb-4.3.0/src/igb_procfs.c
    igb-4.3.0/src/e1000_i210.c
    igb-4.3.0/src/e1000_phy.c
    igb-4.3.0/src/kcompat.c
    igb-4.3.0/src/igb.h
    igb-4.3.0/src/igb_hwmon.c
    igb-4.3.0/src/e1000_82575.h
    igb-4.3.0/src/e1000_nvm.c
    igb-4.3.0/src/e1000_defines.h
    igb-4.3.0/src/e1000_82575.c
    igb-4.3.0/src/e1000_phy.h
    igb-4.3.0/src/kcompat.h
    igb-4.3.0/src/e1000_mac.c
    igb-4.3.0/src/Module.supported
    igb-4.3.0/src/e1000_mbx.h
    igb-4.3.0/src/e1000_mac.h
    igb-4.3.0/src/igb_ptp.c
    igb-4.3.0/src/e1000_i210.h
    igb-4.3.0/src/igb_regtest.h
    igb-4.3.0/src/e1000_api.c
    igb-4.3.0/src/e1000_mbx.c
    igb-4.3.0/src/e1000_osdep.h
    igb-4.3.0/src/igb_ethtool.c
    igb-4.3.0/src/igb_vmdq.c
    igb-4.3.0/src/e1000_hw.h
    igb-4.3.0/src/e1000_regs.h
    igb-4.3.0/src/igb_param.c
    igb-4.3.0/src/e1000_manage.h
    igb-4.3.0/src/igb_main.c
    igb-4.3.0/src/kcompat_ethtool.c
    igb-4.3.0/src/Makefile
    igb-4.3.0/COPYING
    igb-4.3.0/README
    [root@localhost ~]# cd igb-4.3.0
    [root@localhost igb-4.3.0]# cd src/

3,进行安装：

    [root@localhost src]# make install
    make -C /lib/modules/2.6.18-194.el5/build SUBDIRS=/root/igb-4.3.0/src modules
    make[1]: Entering directory `/usr/src/kernels/2.6.18-194.el5-x86_64'
      CC [M]  /root/igb-4.3.0/src/igb_main.o
      CC [M]  /root/igb-4.3.0/src/e1000_82575.o
      CC [M]  /root/igb-4.3.0/src/e1000_i210.o
      CC [M]  /root/igb-4.3.0/src/e1000_mac.o
      CC [M]  /root/igb-4.3.0/src/e1000_nvm.o
      CC [M]  /root/igb-4.3.0/src/e1000_phy.o
      CC [M]  /root/igb-4.3.0/src/e1000_manage.o
      CC [M]  /root/igb-4.3.0/src/igb_param.o
      CC [M]  /root/igb-4.3.0/src/igb_ethtool.o
      CC [M]  /root/igb-4.3.0/src/kcompat.o
      CC [M]  /root/igb-4.3.0/src/e1000_api.o
      CC [M]  /root/igb-4.3.0/src/e1000_mbx.o
      CC [M]  /root/igb-4.3.0/src/igb_vmdq.o
      CC [M]  /root/igb-4.3.0/src/igb_procfs.o
      CC [M]  /root/igb-4.3.0/src/igb_hwmon.o
      LD [M]  /root/igb-4.3.0/src/igb.o
      Building modules, stage 2.
      MODPOST
      CC  /root/igb-4.3.0/src/igb.mod.o
      LD [M]  /root/igb-4.3.0/src/igb.ko
    make[1]: Leaving directory `/usr/src/kernels/2.6.18-194.el5-x86_64'
    gzip -c ../igb.7 > igb.7.gz
    # remove all old versions of the driver
    find /lib/modules/2.6.18-194.el5 -name igb.ko -exec rm -f {} \; || true
    find /lib/modules/2.6.18-194.el5 -name igb.ko.gz -exec rm -f {} \; || true
    install -D -m 644 igb.ko /lib/modules/2.6.18-194.el5/kernel/drivers/net/igb/igb.ko
    /sbin/depmod -a || true
    install -D -m 644 igb.7.gz /usr/share/man/man7/igb.7.gz
    man -c -P'cat > /dev/null' igb || true

4，删除旧的驱动，加载新驱动（网络可能会中断几十秒），最好是在本地操作，远程操作可以使用如下的方法。

    [root@localhost src]# rmmod igb; modprobe igb

5,查看新驱动的相关信息：

    [root@localhost src]# lsmod |grep igb
    igb   205672  0 
    i2c_algo_bit   42185  1 igb
    i2c_core   56641  3 igb,i2c_algo_bit,i2c_ec
    8021q  57425  2 igb,cxgb3
    dca41221  1 igb
    [root@localhost src]# modinfo igb
    filename:   /lib/modules/2.6.18-194.el5/kernel/drivers/net/igb/igb.ko
    version:4.3.0
    license:GPL
    description:Intel(R) Gigabit Ethernet Network Driver
    author: Intel Corporation, <e1000-devel@lists.sourceforge.net>
    srcversion: 91C113BAC8EE9277766AEAD
    alias:  pci:v00008086d000010D6sv*sd*bc*sc*i*
    alias:  pci:v00008086d000010A9sv*sd*bc*sc*i*
    alias:  pci:v00008086d000010A7sv*sd*bc*sc*i*
    alias:  pci:v00008086d000010E8sv*sd*bc*sc*i*
    alias:  pci:v00008086d00001526sv*sd*bc*sc*i*
    alias:  pci:v00008086d0000150Dsv*sd*bc*sc*i*
    alias:  pci:v00008086d000010E7sv*sd*bc*sc*i*
    alias:  pci:v00008086d000010E6sv*sd*bc*sc*i*
    alias:  pci:v00008086d00001518sv*sd*bc*sc*i*
    alias:  pci:v00008086d0000150Asv*sd*bc*sc*i*
    alias:  pci:v00008086d000010C9sv*sd*bc*sc*i*
    alias:  pci:v00008086d00000440sv*sd*bc*sc*i*
    alias:  pci:v00008086d0000043Csv*sd*bc*sc*i*
    alias:  pci:v00008086d0000043Asv*sd*bc*sc*i*
    alias:  pci:v00008086d00000438sv*sd*bc*sc*i*
    alias:  pci:v00008086d00001516sv*sd*bc*sc*i*
    alias:  pci:v00008086d00001511sv*sd*bc*sc*i*
    alias:  pci:v00008086d00001510sv*sd*bc*sc*i*
    alias:  pci:v00008086d00001527sv*sd*bc*sc*i*
    alias:  pci:v00008086d0000150Fsv*sd*bc*sc*i*
    alias:  pci:v00008086d0000150Esv*sd*bc*sc*i*
    alias:  pci:v00008086d00001524sv*sd*bc*sc*i*
    alias:  pci:v00008086d00001523sv*sd*bc*sc*i*
    alias:  pci:v00008086d00001522sv*sd*bc*sc*i*
    alias:  pci:v00008086d00001521sv*sd*bc*sc*i*
    alias:  pci:v00008086d00001539sv*sd*bc*sc*i*
    alias:  pci:v00008086d0000157Csv*sd*bc*sc*i*
    alias:  pci:v00008086d0000157Bsv*sd*bc*sc*i*
    alias:  pci:v00008086d00001538sv*sd*bc*sc*i*
    alias:  pci:v00008086d00001537sv*sd*bc*sc*i*
    alias:  pci:v00008086d00001536sv*sd*bc*sc*i*
    alias:  pci:v00008086d00001533sv*sd*bc*sc*i*
    depends:i2c-core,dca,i2c-algo-bit,8021q
    vermagic:   2.6.18-194.el5 SMP mod_unload gcc-4.1
    parm:   InterruptThrottleRate:Maximum interrupts per second, per vector, (max 100000), default 3=adaptive (array of int)
    parm:   IntMode:Change Interrupt Mode (0=Legacy, 1=MSI, 2=MSI-X), default 2 (array of int)
    parm:   Node:set the starting node to allocate memory on, default -1 (array of int)
    parm:   LLIPort:Low Latency Interrupt TCP Port (0-65535), default 0=off (array of int)
    parm:   LLIPush:Low Latency Interrupt on TCP Push flag (0,1), default 0=off (array of int)
    parm:   LLISize:Low Latency Interrupt on Packet Size (0-1500), default 0=off (array of int)
    parm:   RSS:Number of Receive-Side Scaling Descriptor Queues (0-8), default 1, 0=number of cpus (array of int)
    parm:   VMDQ:Number of Virtual Machine Device Queues: 0-1 = disable, 2-8 enable, default 0 (array of int)
    parm:   max_vfs:Number of Virtual Functions: 0 = disable, 1-7 enable, default 0 (array of int)
    parm:   MDD:Malicious Driver Detection (0/1), default 1 = enabled. Only available when max_vfs is greater than 0 (array of int)
    parm:   QueuePairs:Enable Tx/Rx queue pairs for interrupt handling (0,1), default 1=on (array of int)
    parm:   EEE:Enable/disable on parts that support the feature (array of int)
    parm:   DMAC:Disable or set latency for DMA Coalescing ((0=off, 1000-10000(msec), 250, 500 (usec)) (array of int)
    parm:   LRO:Large Receive Offload (0,1), default 0=off (array of int)
    parm:   debug:Debug level (0=none, ..., 16=all) (int)
    



2013/8/12 21:35:30 
