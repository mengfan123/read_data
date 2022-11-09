# 				利用单臂路由实现VLAN间通信（可选）

1、配置二层交换机Switch-2设备基本VLAN信息

```c
Switch>enable								//进入特权模式
Switch#configure terminal					//进入全局配置模式
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname switch-2				//修改交换机名称
switch-2(config)#vlan 10						//创建vlan10
switch-2(config-vlan)#name xiaoshou				//将vlan10  命名为xiaoshou
switch-2(config-vlan)#vlan 20					//创建vlan20
switch-2(config-vlan)#name jishu				//将vlan20  命名为jishu
switch-2(config-vlan)#exit
switch-2(config)#interface fa0/2				//进入fa0/2子接口模式
switch-2(config-if)#switchport access vlan 10		//将fa0/2划分为 vlan 10
switch-2(config-if)#exit
switch-2(config)#interface fa0/3				//进入fa0/3子接口模式
switch-2(config-if)#switchport access vlan 20			//将fa0/3划分为 vlan 20
switch-2(config-if)#exit
switch-2(config-if)#interface fa0/1					//进入fa0/1子接口模式
switch-2(config-if)#switchport mode trunk			//配置接口模式
switch-2(config-if)#end
switch-2#
```

```c
switch-2#show vlan

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/4, Fa0/5, Fa0/6, Fa0/7
                                                Fa0/8, Fa0/9, Fa0/10, Fa0/11
                                                Fa0/12, Fa0/13, Fa0/14, Fa0/15
                                                Fa0/16, Fa0/17, Fa0/18, Fa0/19
                                                Fa0/20, Fa0/21, Fa0/22, Fa0/23
                                                Fa0/24
10   xiaoshou                         active    Fa0/2
20   jishu                            active    Fa0/3
1002 fddi-default                     act/unsup 
1003 token-ring-default               act/unsup 
1004 fddinet-default                  act/unsup 
1005 trnet-default                    act/unsup 

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------
1    enet  100001     1500  -      -      -        -    -        0      0
10   enet  100010     1500  -      -      -        -    -        0      0
20   enet  100020     1500  -      -      -        -    -        0      0
1002 fddi  101002     1500  -      -      -        -    -        0      0   
 --More-- 
```

2、配置路由器设备 单臂路由技术

```c
Router(config)#
Router(config)#interface fa 0/0    //进入fa0/0子端口模式
Router(config-if)#no shutdown 			//开启端口
Router(config-if)#exit
Router(config)#interface fa0/0.10		//进入子接口fa0/0.10 子接口名称自定义
Router(config-subif)#encapsulation dot1Q 10			//指定子接口fa0/0.10对应vlan10，并配置干道模式
Router(config-subif)#ip address 192.168.10.1 255.255.255.0    //配置子接口fa0/0.10  的IP 地址
Router(config-subif)#exit
Router(config)#interface fa 0/0.20				//进入子接口fa0/0.20 子接口名称自定义
Router(config-subif)#encapsulation dot1Q 20			//指定子接口fa0/0.20对应vlan20，并配置干道模式
Router(config-subif)#ip address 192.168.20.1  255.255.255.0		//配置子接口fa0/0.20  的IP 地址
Router(config-subif)#end
```

3、查看路由器产生的路由表

```c
Router#show ip route				//查看路由表
Codes: C - connected, S - static, I - IGRP, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

C    192.168.10.0/24 is directly connected, FastEthernet0/0.10
C    192.168.20.0/24 is directly connected, FastEthernet0/0.20
```

4、验证配置

![image-20221109221140340](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20221109221140340.png)

不同VLAN 可以 ping通

注：

给路由器子接口配置IP地址之前 ，一定要先封装dot1q协议

各个VLAN内主机，要以相应VLAN子接口IP地址作为网关

