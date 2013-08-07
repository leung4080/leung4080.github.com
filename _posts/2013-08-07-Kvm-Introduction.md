---
layout: post
category : lessons
tagline: "Supporting tagline"
tags : [KVM, 虚拟化, Linux, RedHat ]
---
{% include JB/setup %}

#RHEL安装KVM；
1，放入RHEL5.5的安装光盘

2，挂载安装光盘到/mnt/ ，命令
    mount -o loop /dev/cdrom /mnt

3，修改yum源配置文件 /etc/yum.repos.d/rhel-debuginfo.repo 内容查看附件

4，更新yum数据文件，命令:
    yum makecache

5，安装kvm，命令: 
    yum groupinstall KVM （根据提示输入Y）

6，安装后检查KVM组件是否成功安装，有以下这些组件（使用rpm -qa 命令查看是否有这些组件）
    
Mandatory Packages:
   celt051
   etherboot-zroms
   etherboot-zroms-kvm
   kmod-kvm
   kvm
   kvm-qemu-img
   qcairo
   qffmpeg-libs
   qpixman
   qspice-libs
 Default Packages:
   Virtualization-en-US
   libvirt
   virt-manager
   virt-viewer
 Optional Packages:
   celt051-devel
   etherboot-pxes
   etherboot-roms
   etherboot-roms-kvm
   gpxe-roms-qemu
   iasl
   kvm-tools
   libcmpiutil
   libvirt-cim
   qcairo-devel
   qffmpeg-devel
   qpixman-devel
   qspice
   qspice-libs-devel
    
7，重启机器

8，检查kvm_intel模块是否已加载（如果是AMD的CPU,则为kvm-amd） 
      [root@xxgserver01 ~]# lsmod|grep kvm 
      kvm_intel              86920  0 
      kvm                   226208  2 ksm,kvm_intel

9，查看libvirtd服务是否已启动，是否已设置为开机启动
    [root@xxgserver01 ~]# service libvirtd status
    libvirtd (pid  5515) 正在运行...
    [root@xxgserver01 ~]# chkconfig --list|grep libvirtd
    libvirtd        0:关闭  1:关闭  2:关闭  3:启用  4:启用  5:启用  6:关闭

10，设置libvirtd服务为开机启动
    #chkconfig libvirtd on

11，启动libvirtd服务
    # service libvirtd start

12，使用管理工具进行虚拟机安装
命令行管理工具 virsh
或
图形化管理工具 virt-manager

