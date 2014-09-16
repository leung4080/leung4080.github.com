---
layout: post
category : Linux
title: RHEL multipath配置操作记录
description:linux系统连接SAN存储，多路径配置
tagline:  
date: 2014/9/16 11:18:04 
tags: [SAN, Linux, RedHat, multipath]
---
{% include JB/setup %}

###RHEL multipath配置操作记录###


***
操作步骤如下：

*	[1. 描述](#p1)
*	[2. 规划](#p2)
*	[3. 配置操作记录](#p3)
    *	[3.1 配置多路径](#p3d1)
	*	[3.2 创建lv及文件系统](#p3d2)

* * *

 
<h2 id="p1">描述</h2>

新增4台服务器,主机名分别为san_host1、san_host2、san_host3、san_host4，每台机器已划分有独立的存储（3T/3T/3T/5T）目前已经接好HBA卡并正确识别硬件多路径，需要按以下方式挂载文件系统:

1、san_host1 单独挂3T文件系统,文件系统目录名称/data01;

2、san_host2 单独挂3T文件系统,文件系统目录名称/data01;

3、san_host3 单独挂3T文件系统,文件系统目录名称/data01;

4、san_host4 划分4T空间用于数据库裸设备，另外1T空间建立文件系统挂载目录为/data01。
 
<h2 id="p2">规划</h2>

![image1](http://leung4080.github.io/assets/images/2014/storage1.jpg "存储规划")
 
 
<h2 id="p3">配置操作记录</h2>
 
<h3 id="p3d1">配置多路径</h3>
 
 
###分别在四台服务器上执行：###

    [root@localhost ~]# multipath -ll
    May 13 10:00:50 | /etc/multipath.conf does not exist, blacklisting all devices.
    May 13 10:00:50 | A sample multipath.conf file is located at
    May 13 10:00:50 | /usr/share/doc/device-mapper-multipath-0.4.9/multipath.conf
    May 13 10:00:50 | You can run /sbin/mpathconf to create or modify /etc/multipath.conf
    May 13 10:00:50 | DM multipath kernel driver not loaded
    [root@localhost ~]# service multipathd status
    multipathd 已停
    [root@localhost ~]# service multipathd start
    正在启动守护进程multipathd：[确定]
    [root@localhost ~]# mpathconf --enable --find_multipaths y --with_module y
    [root@localhost ~]# mpathconf 
    multipath is enabled
    find_multipaths is enabled
    user_friendly_names is enabled
    dm_multipath module is loaded
    multipathd is chkconfiged off
    [root@localhost ~]# multipath -l                             #检查是否有多路径信息输出
    mpathc (3600507680180861ce0000000000000a3) dm-5 IBM,2145
    size=1.0T features='1 queue_if_no_path' hwhandler='0' wp=rw
    |-+- policy='round-robin 0' prio=-1 status=active
    | |- 1:0:1:1 sdf 8:80   active undef running
    | |- 1:0:0:1 sdc 8:32   active undef running
    | |- 3:0:1:1 sdr 65:16  active undef running
    | `- 3:0:0:1 sdo 8:224  active undef running
    `-+- policy='round-robin 0' prio=-1 status=enabled
      |- 1:0:2:1 sdi 8:128  active undef running
      |- 1:0:3:1 sdl 8:176  active undef running
      |- 3:0:2:1 sdu 65:64  active undef running
      `- 3:0:3:1 sdx 65:112 active undef running
    mpathb (3600507680180861ce0000000000000a2) dm-4 IBM,2145
    size=1.0T features='1 queue_if_no_path' hwhandler='0' wp=rw
    |-+- policy='round-robin 0' prio=-1 status=active
    | |- 1:0:2:0 sdh 8:112  active undef running
    | |- 1:0:3:0 sdk 8:160  active undef running
    | |- 3:0:2:0 sdt 65:48  active undef running
    | `- 3:0:3:0 sdw 65:96  active undef running
    `-+- policy='round-robin 0' prio=-1 status=enabled
      |- 1:0:1:0 sde 8:64   active undef running
      |- 1:0:0:0 sdb 8:16   active undef running
      |- 3:0:0:0 sdn 8:208  active undef running
      `- 3:0:1:0 sdq 65:0   active undef running
    mpatha (3600507680180861ce0000000000000a4) dm-3 IBM,2145
    size=1.0T features='1 queue_if_no_path' hwhandler='0' wp=rw
    |-+- policy='round-robin 0' prio=-1 status=active
    | |- 1:0:2:2 sdj 8:144  active undef running
    | |- 1:0:3:2 sdm 8:192  active undef running
    | |- 3:0:2:2 sdv 65:80  active undef running
    | `- 3:0:3:2 sdy 65:128 active undef running
    `-+- policy='round-robin 0' prio=-1 status=enabled
      |- 1:0:0:2 sdd 8:48   active undef running
      |- 1:0:1:2 sdg 8:96   active undef running
      |- 3:0:0:2 sdp 8:240  active undef running
      `- 3:0:1:2 sds 65:32  active undef running
 
###在主机san_host1上执行：###
 
#### 修改wwid与设备对应关系： ####

	[root@localhost ~]# vi /etc/multipath/bindings
	mpatha 3600507680180861ce0000000000000a2
	mpathb 3600507680180861ce0000000000000a3
	mpathc 3600507680180861ce0000000000000a4
 


 
####删除原多路径映表，并重建：####

	[root@localhost ~]# multipath -F
	[root@localhost ~]# service multipathd restart
	正在关闭multipathd 端口监控程序：[确定]
	正在启动守护进程multipathd：[确定]
	[root@localhost ~]# multipath -ll
	mpathc (3600507680180861ce0000000000000a4) dm-3 IBM,2145
	size=1.0T features='1 queue_if_no_path' hwhandler='0' wp=rw
	|-+- policy='round-robin 0' prio=50 status=active
	| |- 1:0:2:2 sdj 8:144  active ready running
	| |- 1:0:3:2 sdm 8:192  active ready running
	| |- 3:0:2:2 sdv 65:80  active ready running
	| `- 3:0:3:2 sdy 65:128 active ready running
	`-+- policy='round-robin 0' prio=10 status=enabled
	|- 1:0:0:2 sdd 8:48   active ready running
	|- 1:0:1:2 sdg 8:96   active ready running
	|- 3:0:0:2 sdp 8:240  active ready running
	`- 3:0:1:2 sds 65:32  active ready running
	mpathb (3600507680180861ce0000000000000a3) dm-5 IBM,2145
	size=1.0T features='1 queue_if_no_path' hwhandler='0' wp=rw
	|-+- policy='round-robin 0' prio=50 status=active
	| |- 1:0:1:1 sdf 8:80   active ready running
	| |- 1:0:0:1 sdc 8:32   active ready running
	| |- 3:0:1:1 sdr 65:16  active ready running
	| `- 3:0:0:1 sdo 8:224  active ready running
	`-+- policy='round-robin 0' prio=10 status=enabled
	|- 1:0:2:1 sdi 8:128  active ready running
	|- 1:0:3:1 sdl 8:176  active ready running
	|- 3:0:2:1 sdu 65:64  active ready running
	`- 3:0:3:1 sdx 65:112 active ready running
	mpatha (3600507680180861ce0000000000000a2) dm-4 IBM,2145
	size=1.0T features='1 queue_if_no_path' hwhandler='0' wp=rw
	|-+- policy='round-robin 0' prio=50 status=active
	| |- 1:0:2:0 sdh 8:112  active ready running
	| |- 1:0:3:0 sdk 8:160  active ready running
	| |- 3:0:2:0 sdt 65:48  active ready running
	| `- 3:0:3:0 sdw 65:96  active ready running
	`-+- policy='round-robin 0' prio=10 status=enabled
	|- 1:0:1:0 sde 8:64   active ready running
	|- 1:0:0:0 sdb 8:16   active ready running
	|- 3:0:0:0 sdn 8:208  active ready running
	`- 3:0:1:0 sdq 65:0   active ready running
 
 
 
 
###在主机san_host2上执行：###

	[root@localhost ~]# vi /etc/multipath/bindings 
 
	# Multipath bindings, Version : 1.0
	# NOTE: this file is automatically maintained by the multipath program.
	# You should not need to edit this file in normal circumstances.
	#
	# Format:
	# alias wwid
	#
	mpatha 3600507680180861ce000000000000097
	mpathb 3600507680180861ce000000000000098
	mpathc 3600507680180861ce000000000000099
 
 
	[root@localhost ~]# multipath -F
	[root@localhost ~]# service multipathd restart
	正在关闭multipathd 端口监控程序：[确定]
	正在启动守护进程multipathd：[确定]
	[root@localhost ~]# multipath -ll
	mpathc (3600507680180861ce000000000000099) dm-4 IBM,2145
	size=1.0T features='1 queue_if_no_path' hwhandler='0' wp=rw
	|-+- policy='round-robin 0' prio=50 status=active
	| |- 1:0:0:2 sdd 8:48   active ready running
	| |- 1:0:1:2 sdg 8:96   active ready running
	| |- 3:0:0:2 sdp 8:240  active ready running
	| `- 3:0:1:2 sds 65:32  active ready running
	`-+- policy='round-robin 0' prio=10 status=enabled
	|- 1:0:2:2 sdj 8:144  active ready running
	|- 1:0:3:2 sdm 8:192  active ready running
	|- 3:0:2:2 sdv 65:80  active ready running
	`- 3:0:3:2 sdy 65:128 active ready running
	mpathb (3600507680180861ce000000000000098) dm-3 IBM,2145
	size=1.0T features='1 queue_if_no_path' hwhandler='0' wp=rw
	|-+- policy='round-robin 0' prio=50 status=active
	| |- 1:0:2:1 sdi 8:128  active ready running
	| |- 1:0:3:1 sdl 8:176  active ready running
	| |- 3:0:2:1 sdu 65:64  active ready running
	| `- 3:0:3:1 sdx 65:112 active ready running
	`-+- policy='round-robin 0' prio=10 status=enabled
	|- 1:0:0:1 sdc 8:32   active ready running
	|- 1:0:1:1 sdf 8:80   active ready running
	|- 3:0:0:1 sdo 8:224  active ready running
	`- 3:0:1:1 sdr 65:16  active ready running
	mpatha (3600507680180861ce000000000000097) dm-5 IBM,2145
	size=1.0T features='1 queue_if_no_path' hwhandler='0' wp=rw
	|-+- policy='round-robin 0' prio=50 status=active
	| |- 1:0:1:0 sde 8:64   active ready running
	| |- 1:0:0:0 sdb 8:16   active ready running
	| |- 3:0:0:0 sdn 8:208  active ready running
	| `- 3:0:1:0 sdq 65:0   active ready running
	`-+- policy='round-robin 0' prio=10 status=enabled
	|- 1:0:2:0 sdh 8:112  active ready running
	|- 1:0:3:0 sdk 8:160  active ready running
	|- 3:0:2:0 sdt 65:48  active ready running
	`- 3:0:3:0 sdw 65:96  active ready running
	
	
###在主机san_host3上执行：###
	
	[root@localhost ~]# vi /etc/multipath/bindings 
	
	# Multipath bindings, Version : 1.0
	# NOTE: this file is automatically maintained by the multipath program.
	# You should not need to edit this file in normal circumstances.
	#
	# Format:
	# alias wwid
	#
	mpatha 3600507680180861ce00000000000009a
	mpathb 3600507680180861ce00000000000009b
	mpathc 3600507680180861ce00000000000009c
	
	[root@localhost ~]# multipath -F
	[root@localhost ~]# service multipathd restart
	正在关闭multipathd 端口监控程序：[确定]
	正在启动守护进程multipathd：[确定]
	[root@localhost ~]# multipath -ll
	mpathc (3600507680180861ce00000000000009c) dm-3 IBM,2145
	size=1.0T features='1 queue_if_no_path' hwhandler='0' wp=rw
	|-+- policy='round-robin 0' prio=50 status=active
	| |- 1:0:2:2 sdj 8:144  active ready running
	| |- 1:0:3:2 sdm 8:192  active ready running
	| |- 3:0:2:2 sdv 65:80  active ready running
	| `- 3:0:3:2 sdy 65:128 active ready running
	`-+- policy='round-robin 0' prio=10 status=enabled
	|- 1:0:0:2 sdd 8:48   active ready running
	|- 1:0:1:2 sdg 8:96   active ready running
	|- 3:0:0:2 sdp 8:240  active ready running
	`- 3:0:1:2 sds 65:32  active ready running
	mpathb (3600507680180861ce00000000000009b) dm-5 IBM,2145
	size=1.0T features='1 queue_if_no_path' hwhandler='0' wp=rw
	|-+- policy='round-robin 0' prio=50 status=active
	| |- 1:0:1:1 sdf 8:80   active ready running
	| |- 1:0:0:1 sdc 8:32   active ready running
	| |- 3:0:1:1 sdr 65:16  active ready running
	| `- 3:0:0:1 sdo 8:224  active ready running
	`-+- policy='round-robin 0' prio=10 status=enabled
	|- 1:0:2:1 sdi 8:128  active ready running
	|- 1:0:3:1 sdl 8:176  active ready running
	|- 3:0:2:1 sdu 65:64  active ready running
	`- 3:0:3:1 sdx 65:112 active ready running
	mpatha (3600507680180861ce00000000000009a) dm-4 IBM,2145
	size=1.0T features='1 queue_if_no_path' hwhandler='0' wp=rw
	|-+- policy='round-robin 0' prio=50 status=active
	| |- 1:0:3:0 sdk 8:160  active ready running
	| |- 1:0:2:0 sdh 8:112  active ready running
	| |- 3:0:2:0 sdt 65:48  active ready running
	| `- 3:0:3:0 sdw 65:96  active ready running
	`-+- policy='round-robin 0' prio=10 status=enabled
	|- 1:0:1:0 sde 8:64   active ready running
	|- 1:0:0:0 sdb 8:16   active ready running
	|- 3:0:1:0 sdq 65:0   active ready running
	`- 3:0:0:0 sdn 8:208  active ready running
	
	
####在主机san_host4上执行：####
	
	[root@localhost ~]# vi /etc/multipath/bindings 
	
	# Multipath bindings, Version : 1.0
	# NOTE: this file is automatically maintained by the multipath program.
	# You should not need to edit this file in normal circumstances.
	#
	# Format:
	# alias wwid
	#
	mpatha 3600507680180861ce00000000000009d
	mpathb 3600507680180861ce00000000000009e
	mpathc 3600507680180861ce00000000000009f
	mpathd 3600507680180861ce0000000000000a0
	mpathe 3600507680180861ce0000000000000a1
	
	[root@localhost ~]# multipath -F
	[root@localhost ~]# service multipathd restart
	正在关闭multipathd 端口监控程序：[确定]
	正在启动守护进程multipathd：[确定]
	[root@localhost ~]# multipath -ll
	mpathe (3600507680180861ce0000000000000a1) dm-5 IBM,2145
	size=1.0T features='1 queue_if_no_path' hwhandler='0' wp=rw
	|-+- policy='round-robin 0' prio=50 status=active
	| |- 1:0:0:4 sdf  8:80   active ready running
	| |- 1:0:1:4 sdk  8:160  active ready running
	| |- 3:0:0:4 sdz  65:144 active ready running
	| `- 3:0:1:4 sdae 65:224 active ready running
	`-+- policy='round-robin 0' prio=10 status=enabled
	|- 1:0:2:4 sdp  8:240  active ready running
	|- 1:0:3:4 sdu  65:64  active ready running
	|- 3:0:3:4 sdao 66:128 active ready running
	`- 3:0:2:4 sdaj 66:48  active ready running
	mpathd (3600507680180861ce0000000000000a0) dm-4 IBM,2145
	size=1.0T features='1 queue_if_no_path' hwhandler='0' wp=rw
	|-+- policy='round-robin 0' prio=50 status=active
	| |- 1:0:2:3 sdo  8:224  active ready running
	| |- 1:0:3:3 sdt  65:48  active ready running
	| |- 3:0:3:3 sdan 66:112 active ready running
	| `- 3:0:2:3 sdai 66:32  active ready running
	`-+- policy='round-robin 0' prio=10 status=enabled
	|- 1:0:0:3 sde  8:64   active ready running
	|- 1:0:1:3 sdj  8:144  active ready running
	|- 3:0:0:3 sdy  65:128 active ready running
	`- 3:0:1:3 sdad 65:208 active ready running
	mpathc (3600507680180861ce00000000000009f) dm-3 IBM,2145
	size=1.0T features='1 queue_if_no_path' hwhandler='0' wp=rw
	|-+- policy='round-robin 0' prio=50 status=active
	| |- 1:0:0:2 sdd  8:48   active ready running
	| |- 1:0:1:2 sdi  8:128  active ready running
	| |- 3:0:0:2 sdx  65:112 active ready running
	| `- 3:0:1:2 sdac 65:192 active ready running
	`-+- policy='round-robin 0' prio=10 status=enabled
	|- 1:0:2:2 sdn  8:208  active ready running
	|- 1:0:3:2 sds  65:32  active ready running
	|- 3:0:2:2 sdah 66:16  active ready running
	`- 3:0:3:2 sdam 66:96  active ready running
	mpathb (3600507680180861ce00000000000009e) dm-2 IBM,2145
	size=1.0T features='1 queue_if_no_path' hwhandler='0' wp=rw
	|-+- policy='round-robin 0' prio=50 status=active
	| |- 1:0:2:1 sdm  8:192  active ready running
	| |- 1:0:3:1 sdr  65:16  active ready running
	| |- 3:0:2:1 sdag 66:0   active ready running
	| `- 3:0:3:1 sdal 66:80  active ready running
	`-+- policy='round-robin 0' prio=10 status=enabled
	|- 1:0:0:1 sdc  8:32   active ready running
	|- 1:0:1:1 sdh  8:112  active ready running
	|- 3:0:0:1 sdw  65:96  active ready running
	`- 3:0:1:1 sdab 65:176 active ready running
	mpatha (3600507680180861ce00000000000009d) dm-6 IBM,2145
	size=1.0T features='1 queue_if_no_path' hwhandler='0' wp=rw
	|-+- policy='round-robin 0' prio=50 status=active
	| |- 1:0:0:0 sdb  8:16   active ready running
	| |- 1:0:1:0 sdg  8:96   active ready running
	| |- 3:0:0:0 sdv  65:80  active ready running
	| `- 3:0:1:0 sdaa 65:160 active ready running
	`-+- policy='round-robin 0' prio=10 status=enabled
	|- 1:0:2:0 sdl  8:176  active ready running
	|- 1:0:3:0 sdq  65:0   active ready running
	|- 3:0:2:0 sdaf 65:240 active ready running
	`- 3:0:3:0 sdak 66:64  active ready running
	
	
	
	
<h3 id="p3d2">2,创建lv及文件系统</h3>
	
####在san_host1/san_host2/san_host3这三台主机上执行如下操作：####
	
	
	pvcreate /dev/mapper/mpatha 
	pvcreate /dev/mapper/mpathb 
	pvcreate /dev/mapper/mpathc
	
	
	vgcreate datavg /dev/mapper/mpatha /dev/mapper/mpathb /dev/mapper/mpathc  
	
	vgdisplay datavg 
	
	lvcreate -n lv_data01 -l 786400 datavg
	
	lvs
	
	mkfs.ext3 /dev/mapper/datavg-lv_data01 
	
	
	mkdir /data01
	
	mount /dev/mapper/datavg-lv_data01 /data01/
	
	vi /etc/fstab 
	加入一行：
	/dev/mapper/datavg-lv_data01    /data01         ext3    defaults        1 3
	
	同步磁盘数据并重启
	[root@localhost ~]# sync
	[root@localhost ~]# reboot
	
重启后查看检查mulitpath设备及wwid是否对应，df查看文件系统/data01是否正常挂载
	
	
####在san_host4执行如下操作：####
	
	pvcreate /dev/mapper/mpatha 
	
	vgcreate datavg /dev/mapper/mpatha
	
	vgdisplay datavg 
	
	lvcreate -n lv_data01 -l 262100 datavg
	
	lvs
	
	mkfs.ext3 /dev/mapper/datavg-lv_data01 
	
	
	mkdir /data01
	
	mount /dev/mapper/datavg-lv_data01 /data01/
	
	vi /etc/fstab 
	加入
	/dev/mapper/datavg-lv_data01    /data01         ext3    defaults        1 3
	
	
	同步磁盘数据并重启
	[root@localhost ~]# sync
	[root@localhost ~]# reboot
	
重启后查看检查mulitpath设备及wwid是否对应，df查看文件系统/data01是否正常挂载
 
