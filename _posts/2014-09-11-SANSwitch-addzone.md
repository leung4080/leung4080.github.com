---
layout: post
title: 光纤交换机添加zone操作记录
description: 博科光纤交换机添加ZONE操作记录
category : SAN
date: 2014/9/11 8:47:04 
tags: [SAN, Storage]

---
{% include JB/setup %}

博科光纤交换机添加ZONE操作记录
==================
***
操作步骤如下：

*	[1. 连接光纤交换机](#p1)
*	[2. 查看交换机配置信息](#p2)
*	[3. 添加ZONE](#p3)
*	[4. 更新配置](#p4)
*	[5. 保存为默认配置](#p5)

* * *



<h3 id="p1">1. 连接光纤交换机 </h3>
>使用串口线（RJ232/DB9）连接到光纤交换机串口

使用SecureCRT等终端工具登陆：
![image1]({{ site.img_path }}/2014/SAN_Seril-com_info.jpg "SecureCRT Com口参数")

登陆默认用户名：admin
默认密码：admin
    
    Fabric OS (sw1)
    
    
    sw1 console login: admin   
    Password: 
    
    -----------------------------------------------------------------
    Please change passwords for switch default accounts now.
    Use Control-C to exit or press 'Enter' key to proceed.
    
    Warning:  Access to  the Root  and Factory accounts may be required  for
    proper  support  of  the switch.  Please  ensure  the Root  and  Factory
    passwords are  documented in a secure location.  Recovery of a lost Root
    or Factory password will result in fabric downtime.
    
    for user - factory
    
    Changing password for factory
    Enter new password:                       直接输入回车，不更新密码
    Password unchanged.
    
    
    
    passwd: Authentication token manipulation error
    Please change passwords for switch default accounts now.
    for user - admin
    Changing password for admin
    Enter new password:                       直接输入回车，不更新密码
    Password unchanged.
    passwd: Authentication token manipulation error
    Please change passwords for switch default accounts now.
    for user - user
    Changing password for user
    Enter new password:                       直接输入回车，不更新密码
    Password unchanged.
    passwd: Authentication token manipulation error
    sw1:admin> 
    sw1:admin> 
    sw1:admin> 

<h3 id="p2">2. 查看交换机配置信息 </h3>
 
>使用switchshow命令查看交换机信息：
>可以看到各个端口的状态，现在使用zone配置名称为sw1cfg

    switchName:     sw1
    switchType:     71.2
    switchState:    Online   
    switchMode:     Native
    switchRole:     Principal
    switchDomain:   1
    switchId:       fffc01
    switchWwn:      10:00:00:05:1e:a0:e6:9c
    zoning:         ON (sw1cfg)
    switchBeacon:   OFF
    
     Area Port Media Speed State     Proto
    =====================================
      0   0   id    N4   Online           F-Port  21:00:00:1b:32:86:40:80 
      1   1   id    N4   Online           F-Port  21:00:00:1b:32:86:f1:7f 
      2   2   id    N4   Online           F-Port  21:00:00:1b:32:13:eb:9f 
      3   3   id    N4   Online           F-Port  21:00:00:1b:32:13:ee:a1 
      4   4   id    N4   Online           F-Port  20:13:00:a0:b8:48:b7:72 
      5   5   id    N4   Online           F-Port  20:05:00:a0:b8:48:67:ab 
      6   6   id    N4   Online           F-Port  20:03:00:a0:b8:48:f7:27 
      7   7   id    N4   Online           F-Port  20:07:00:a0:b8:48:f7:6b 
      8   8   id    N4   Online           F-Port  20:03:00:a0:b8:48:da:e7 
      9   9   id    N4   Online           F-Port  20:03:00:a0:b8:48:da:cb 
     10  10   id    N4   Online           F-Port  20:03:00:a0:b8:48:d1:db 
     11  11   id    N4   Online           F-Port  20:03:00:a0:b8:48:1e:69 
     12  12   id    N4   Online           F-Port  20:05:00:a0:b8:48:57:9f 
     13  13   id    N4   Online           F-Port  21:00:00:1b:32:1a:27:c8 
     14  14   id    N4   No_Light         
     15  15   id    N4   No_Light         
     16  16   id    N4   No_Light         
     17  17   id    N4   No_Light         
     18  18   id    N4   No_Light         
     19  19   id    N4   No_Light         
     20  20   id    N4   No_Light         
     21  21   id    N4   No_Light         
     22  22   id    N4   No_Light         
     23  23   id    N4   No_Light         
    sw1:admin> 2014/07/29-03:00:53, [SNMP-1008], 327, FID 128, INFO, sw1,  The last device change happened at : Tue Jul 29 03:00:48 2014

    2014/07/29-03:01:38, [SNMP-1008], 328, FID 128, INFO, sw1,  The last device change happened at : Tue Jul 29 03:01:38 2014

>使用命令cfgshow查看配置：
>可以看到配置sw1cfg中包含的zone和各个端口的别名：

    sw1:admin> cfgshow                                                                           
    Defined configuration:
     cfg:   sw1cfg  M4000_37_to_ST6140_1; M4000_37_to_ST6140_2;                                                    
                    M4000_38_to_ST6140_3; M4000_38_to_ST6140_4; 
                    M4000_39_to_ST6140_5; M4000_39_to_ST6140_6; 
                    M4000_40_to_ST6140_7; M4000_40_to_ST6140_8; M5000_to_ST6140_0
     zone:  M4000_37_to_ST6140_1
                    NGNIQR1; ST6140_1
     zone:  M4000_37_to_ST6140_2
                    NGNIQR1; ST6140_2
     zone:  M4000_38_to_ST6140_3
                    NGNASE; ST6140_3
     zone:  M4000_38_to_ST6140_4
                    NGNASE; ST6140_4
     zone:  M4000_39_to_ST6140_5
                    NGNIQR2; ST6140_5
     zone:  M4000_39_to_ST6140_6
                    NGNIQR2; ST6140_6
     zone:  M4000_40_to_ST6140_7
                    NGNAPP2; ST6140_7
     zone:  M4000_40_to_ST6140_8
                    NGNAPP2; ST6140_8
     zone:  M5000_to_ST6140_0
                    NGNIQW; ST6140_0
     alias: NGNAPP2 1,3 
     alias: NGNASE  1,1
     alias: NGNIQR1 1,0
     alias: NGNIQR2 1,2
     alias: NGNIQW  1,13
     alias: ST6140_0
                    1,4
     alias: ST6140_1
                    1,5
     alias: ST6140_2
                    1,6
     alias: ST6140_3
                    1,7
     alias: ST6140_4
                    1,8
     alias: ST6140_5
                    1,9
     alias: ST6140_6
                    1,10
     alias: ST6140_7
                    1,11
     alias: ST6140_8
                    1,12
    
    Effective configuration:
     cfg:   sw1cfg                                                                                                                      
     zone:  M4000_37_to_ST6140_1
                    1,0
                    1,5
     zone:  M4000_37_to_ST6140_2
                    1,0
                    1,6
     zone:  M4000_38_to_ST6140_3
                    1,1
                    1,7
     zone:  M4000_38_to_ST6140_4
                    1,1
                    1,8
     zone:  M4000_39_to_ST6140_5
                    1,2
                    1,9
     zone:  M4000_39_to_ST6140_6
                    1,2
                    1,10
     zone:  M4000_40_to_ST6140_7
                    1,3
                    1,11
     zone:  M4000_40_to_ST6140_8
                    1,3
                    1,12
     zone:  M5000_to_ST6140_0
                    1,13
                    1,4

<h3 id="p3">3. 添加ZONE</h3>
>1，为新增的端口设置别名,
添加新的端口到别名HP_2000,对应的端口为1,16和1,20这两个端口

    sw1:admin> alicreate "HP_P2000","1,16;1,20" 

>2，添加两个ZONE
>添加两个zone：M4000_37_to_HP_P2000和M4000_39_to_HP_P2000
>分别为从NGNIQR1到HP_P2000，NGNIQR2到HP_P2000
                                                                                           
    sw1:admin> zonecreate "M4000_37_to_HP_P2000","NGNIQR1;HP_P2000"                                       
    sw1:admin> zonecreate "M4000_39_to_HP_P2000","NGNIQR2;HP_P2000"                                        

>3，将新增的ZONE添加到配置中
>将新增的两个zone加入配置sw1cfg中
    

    sw1:admin> cfgadd "sw1cfg","M4000_37_to_HP_P2000;M4000_39_to_HP_P2000"   


>4，使用命令cfgshow，检查配置是否正确。
                   
    sw1:admin> cfgshow                                                                                                                                          查看更改后的配置。
    Defined configuration:
     cfg:   sw1cfg  M4000_37_to_ST6140_1; M4000_37_to_ST6140_2; 
                    M4000_38_to_ST6140_3; M4000_38_to_ST6140_4; 
                    M4000_39_to_ST6140_5; M4000_39_to_ST6140_6; 
                    M4000_40_to_ST6140_7; M4000_40_to_ST6140_8; M5000_to_ST6140_0; 
                    M4000_37_to_HP_P2000; M4000_39_to_HP_P2000
     zone:  M4000_37_to_HP_P2000
                    NGNIQR1; HP_P2000
     zone:  M4000_37_to_ST6140_1
                    NGNIQR1; ST6140_1
     zone:  M4000_37_to_ST6140_2
                    NGNIQR1; ST6140_2
     zone:  M4000_38_to_ST6140_3
                    NGNASE; ST6140_3
     zone:  M4000_38_to_ST6140_4
                    NGNASE; ST6140_4
     zone:  M4000_39_to_HP_P2000
                    NGNIQR2; HP_P2000
     zone:  M4000_39_to_ST6140_5
                    NGNIQR2; ST6140_5
     zone:  M4000_39_to_ST6140_6
                    NGNIQR2; ST6140_6
     zone:  M4000_40_to_ST6140_7
                    NGNAPP2; ST6140_7
     zone:  M4000_40_to_ST6140_8
                    NGNAPP2; ST6140_8
     zone:  M5000_to_ST6140_0
                    NGNIQW; ST6140_0
     alias: HP_P2000
                    1,16; 1,20
     alias: NGNAPP2 1,3
     alias: NGNASE  1,1
     alias: NGNIQR1 1,0
     alias: NGNIQR2 1,2
     alias: NGNIQW  1,13
     alias: ST6140_0
                    1,4
     alias: ST6140_1
                    1,5
     alias: ST6140_2
                    1,6
     alias: ST6140_3
                    1,7
     alias: ST6140_4
                    1,8
     alias: ST6140_5
                    1,9
     alias: ST6140_6
                    1,10
     alias: ST6140_7
                    1,11
     alias: ST6140_8
                    1,12
    
    Effective configuration:
     cfg:   sw1cfg
     zone:  M4000_37_to_ST6140_1
                    1,0
                    1,5
     zone:  M4000_37_to_ST6140_2
                    1,0
                    1,6
     zone:  M4000_38_to_ST6140_3
                    1,1
                    1,7
     zone:  M4000_38_to_ST6140_4
                    1,1
                    1,8
     zone:  M4000_39_to_ST6140_5
                    1,2
                    1,9
     zone:  M4000_39_to_ST6140_6
                    1,2
                    1,10
     zone:  M4000_40_to_ST6140_7
                    1,3
                    1,11
     zone:  M4000_40_to_ST6140_8
                    1,3
                    1,12
     zone:  M5000_to_ST6140_0
                    1,13
                    1,4

<h3 id="p4">4. 更新配置</h3>
> 修改配置后，需要更新配置，使其生效
> 使用命令:cfgenable

   
    sw1:admin> cfgenable "sw1cfg"                                                                                                       
    You are about to enable a new zoning configuration.
    This action will replace the old zoning configuration with the
    current configuration selected. If the update includes changes 
    to one or more traffic isolation zones, the update may result in  
    localized disruption to traffic on ports associated with
    the traffic isolation zone changes
    Do you want to enable 'sw1cfg' configuration  (yes, y, no, n): [no] y
															输入Y，确认更新配置。        
    sw0 Updating flash ...
    2014/07/29-03:06:47, [ZONE-1022], 329, FID 128, INFO, sw1, The effective configuration has changed to sw1cfg.  
    zone config "sw1cfg" is in effect
    Updating flash ...

>再次检查配置

    sw1:admin> cfgshow                                                                                                                       
    Defined configuration:
     cfg:   sw1cfg  M4000_37_to_ST6140_1; M4000_37_to_ST6140_2; 
                    M4000_38_to_ST6140_3; M4000_38_to_ST6140_4; 
                    M4000_39_to_ST6140_5; M4000_39_to_ST6140_6; 
                    M4000_40_to_ST6140_7; M4000_40_to_ST6140_8; M5000_to_ST6140_0; 
                    M4000_37_to_HP_P2000; M4000_39_to_HP_P2000
     zone:  M4000_37_to_HP_P2000
                    NGNIQR1; HP_P2000
     zone:  M4000_37_to_ST6140_1
                    NGNIQR1; ST6140_1
     zone:  M4000_37_to_ST6140_2
                    NGNIQR1; ST6140_2
     zone:  M4000_38_to_ST6140_3
                    NGNASE; ST6140_3
     zone:  M4000_38_to_ST6140_4
                    NGNASE; ST6140_4
     zone:  M4000_39_to_HP_P2000
                    NGNIQR2; HP_P2000
     zone:  M4000_39_to_ST6140_5
                    NGNIQR2; ST6140_5
     zone:  M4000_39_to_ST6140_6
                    NGNIQR2; ST6140_6
     zone:  M4000_40_to_ST6140_7
                    NGNAPP2; ST6140_7
     zone:  M4000_40_to_ST6140_8
                    NGNAPP2; ST6140_8
     zone:  M5000_to_ST6140_0
                    NGNIQW; ST6140_0
     alias: HP_P2000
                    1,16; 1,20
     alias: NGNAPP2 1,3
     alias: NGNASE  1,1
     alias: NGNIQR1 1,0
     alias: NGNIQR2 1,2
     alias: NGNIQW  1,13
     alias: ST6140_0
                    1,4
     alias: ST6140_1
                    1,5
     alias: ST6140_2
                    1,6
     alias: ST6140_3
                    1,7
     alias: ST6140_4
                    1,8
     alias: ST6140_5
                    1,9
     alias: ST6140_6
                    1,10
     alias: ST6140_7
                    1,11
     alias: ST6140_8
                    1,12
    
    Effective configuration:
     cfg:   sw1cfg
     zone:  M4000_37_to_HP_P2000
                    1,0
                    1,16
                    1,20
     zone:  M4000_37_to_ST6140_1
                    1,0
                    1,5
     zone:  M4000_37_to_ST6140_2
                    1,0
                    1,6
     zone:  M4000_38_to_ST6140_3
                    1,1
                    1,7
     zone:  M4000_38_to_ST6140_4
                    1,1
                    1,8
     zone:  M4000_39_to_HP_P2000
                    1,2
                    1,16
                    1,20
     zone:  M4000_39_to_ST6140_5
                    1,2
                    1,9
     zone:  M4000_39_to_ST6140_6
                    1,2
                    1,10
     zone:  M4000_40_to_ST6140_7
                    1,3
                    1,11
     zone:  M4000_40_to_ST6140_8
                    1,3
                    1,12
     zone:  M5000_to_ST6140_0
                    1,13
                    1,4


<h3 id="p5">

5. 保存为默认配置</h3>
>更新配置后，需要保存为默认配置，以保证设备重启后，会使用当前配置。
>使用cfgsave命令保存
    
    sw1:admin> cfgsave                                                                                                                                      
    You are about to save the Defined zoning configuration. This 
    action will only save the changes on Defined configuration. 
    Any changes made on the Effective configuration will not 
    take effect until it is re-enabled.
    Do you want to save Defined zoning configuration only?  (yes, y, no, n): [no] y
																	输入Y，确认保存配置
    Nothing changed: nothing to save, returning ...

>再次检查交换机信息

    sw1:admin> switchshow
    switchName:     sw1
    switchType:     71.2
    switchState:    Online   
    switchMode:     Native
    switchRole:     Principal
    switchDomain:   1
    switchId:       fffc01
    switchWwn:      10:00:00:05:1e:a0:e6:9c
    zoning:         ON (sw1cfg)
    switchBeacon:   OFF
    
    Area Port Media Speed State     Proto
    =====================================
      0   0   id    N4   Online           F-Port  21:00:00:1b:32:86:40:80 
      1   1   id    N4   Online           F-Port  21:00:00:1b:32:86:f1:7f 
      2   2   id    N4   Online           F-Port  21:00:00:1b:32:13:eb:9f 
      3   3   id    N4   Online           F-Port  21:00:00:1b:32:13:ee:a1 
      4   4   id    N4   Online           F-Port  20:13:00:a0:b8:48:b7:72 
      5   5   id    N4   Online           F-Port  20:05:00:a0:b8:48:67:ab 
      6   6   id    N4   Online           F-Port  20:03:00:a0:b8:48:f7:27 
      7   7   id    N4   Online           F-Port  20:07:00:a0:b8:48:f7:6b 
      8   8   id    N4   Online           F-Port  20:03:00:a0:b8:48:da:e7 
      9   9   id    N4   Online           F-Port  20:03:00:a0:b8:48:da:cb 
     10  10   id    N4   Online           F-Port  20:03:00:a0:b8:48:d1:db 
     11  11   id    N4   Online           F-Port  20:03:00:a0:b8:48:1e:69 
     12  12   id    N4   Online           F-Port  20:05:00:a0:b8:48:57:9f 
     13  13   id    N4   Online           F-Port  21:00:00:1b:32:1a:27:c8 
     14  14   id    N4   No_Light         
     15  15   id    N4   No_Light         
     16  16   id    N4   Online           F-Port  21:70:00:c0:ff:1a:9d:d1 
     17  17   id    N4   No_Light         
     18  18   id    N4   No_Light         
     19  19   id    N4   No_Light         
     20  20   id    N4   Online           F-Port  25:70:00:c0:ff:1a:9d:d1 
     21  21   id    N4   No_Light         
     22  22   id    N4   No_Light         
     23  23   id    N4   No_Light         
    sw1:admin> cfgshow
    Defined configuration:
     cfg:   sw1cfg  M4000_37_to_ST6140_1; M4000_37_to_ST6140_2; 
                    M4000_38_to_ST6140_3; M4000_38_to_ST6140_4; 
                    M4000_39_to_ST6140_5; M4000_39_to_ST6140_6; 
                    M4000_40_to_ST6140_7; M4000_40_to_ST6140_8; M5000_to_ST6140_0; 
                    M4000_37_to_HP_P2000; M4000_39_to_HP_P2000
     zone:  M4000_37_to_HP_P2000
                    NGNIQR1; HP_P2000
     zone:  M4000_37_to_ST6140_1
                    NGNIQR1; ST6140_1
     zone:  M4000_37_to_ST6140_2
                    NGNIQR1; ST6140_2
     zone:  M4000_38_to_ST6140_3
                    NGNASE; ST6140_3
     zone:  M4000_38_to_ST6140_4
                    NGNASE; ST6140_4
     zone:  M4000_39_to_HP_P2000
                    NGNIQR2; HP_P2000
     zone:  M4000_39_to_ST6140_5
                    NGNIQR2; ST6140_5
     zone:  M4000_39_to_ST6140_6
                    NGNIQR2; ST6140_6
     zone:  M4000_40_to_ST6140_7
                    NGNAPP2; ST6140_7
     zone:  M4000_40_to_ST6140_8
                    NGNAPP2; ST6140_8
     zone:  M5000_to_ST6140_0
                    NGNIQW; ST6140_0
     alias: HP_P2000
                    1,16; 1,20
     alias: NGNAPP2 1,3
     alias: NGNASE  1,1
     alias: NGNIQR1 1,0
     alias: NGNIQR2 1,2
     alias: NGNIQW  1,13
     alias: ST6140_0
                    1,4
     alias: ST6140_1
                    1,5
     alias: ST6140_2
                    1,6
     alias: ST6140_3
                    1,7
     alias: ST6140_4
                    1,8
     alias: ST6140_5
                    1,9
     alias: ST6140_6
                    1,10
     alias: ST6140_7
                    1,11
     alias: ST6140_8
                    1,12
    
    Effective configuration:
     cfg:   sw1cfg
     zone:  M4000_37_to_HP_P2000
                    1,0
                    1,16
                    1,20
     zone:  M4000_37_to_ST6140_1
                    1,0
                    1,5
     zone:  M4000_37_to_ST6140_2
                    1,0
                    1,6
     zone:  M4000_38_to_ST6140_3
                    1,1
                    1,7
     zone:  M4000_38_to_ST6140_4
                    1,1
                    1,8
     zone:  M4000_39_to_HP_P2000
                    1,2
                    1,16
                    1,20
     zone:  M4000_39_to_ST6140_5
                    1,2
                    1,9
     zone:  M4000_39_to_ST6140_6
                    1,2
                    1,10
     zone:  M4000_40_to_ST6140_7
                    1,3
                    1,11
     zone:  M4000_40_to_ST6140_8
                    1,3
                    1,12
     zone:  M5000_to_ST6140_0
                    1,13
                    1,4
    
    sw1:admin> timed out waiti
    
    Fabric OS (sw1)
    
    

