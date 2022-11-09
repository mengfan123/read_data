# 		利用三层交换机SVI技术实现VLAN之间通信

1、配置二层交换机Switch-2 设备VLAN和干道信息

```c
Switch(config)#
Switch(config)#hostname Switch-2								//修改交换机名称
Switch-2(config)#vlan 10										//创建VLAN10
Switch-2(config-vlan)#name xiaoshou								//将VLAN10命名为xiaoshou
Switch-2(config-vlan)#vlan 20									//创建VLAN20
Switch-2(config-vlan)#name jishu								//将VLAN20命名为jishu
Switch-2(config-vlan)#exit
Switch-2(config)#interface fa0/5								//进入f0/5子端口
Switch-2(config-if)#switchport access vlan 10						//将f0/5划分为VLAN10
Switch-2(config-if)#exit
Switch-2(config)#interface fa0/10								//进入f0/10子端口
Switch-2(config-if)#switchport access vlan 20					//将  f0/10划分为 VLAN20
Switch-2(config-if)#exit
Switch-2(config)#interface fa 0/1								//进入f0/1子端口
Switch-2(config-if)#switchport mode trunk						//配置f0/1端口模式为trunk
Switch-2(config-if)#exit
Switch-2(config)#exit
```

```c
Switch-2#show vlan

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/2, Fa0/3, Fa0/4, Fa0/6
                                                Fa0/7, Fa0/8, Fa0/9, Fa0/11
                                                Fa0/12, Fa0/13, Fa0/14, Fa0/15
                                                Fa0/16, Fa0/17, Fa0/18, Fa0/19
                                                Fa0/20, Fa0/21, Fa0/22, Fa0/23
                                                Fa0/24
10   xiaoshou                         active    Fa0/5
20   jishu                            active    Fa0/10
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

2、配置三层交换机Switch-3设备VLAN基本信息

```c
Switch#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname Switch-3         //配置交换机名称
Switch-3(config-if)#vlan 10					//创建VLAN10
Switch-3(config-vlan)#vlan 20				//创建VLAN20
Switch-3(config-vlan)#interface f 0/1			//进入f0/1子接口
Switch-3(config-if)#switchport trunk encap dot1q			//配置trunk协议
Switch-3(config-if)#switchport mode trunk						//配置接口模式
Switch-3(config-if)#
```

3、在三层交换机Switch-3上配置SVI端口的虚拟网关

```c
Switch-3#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
Switch-3(config)#interface vlan 10					//激活VLAN10的SVI端口配置IP地址
Switch-3(config-if)#ip address 192.168.20.1 255.255.255.00
Switch-3(config-if)#no shutdown        //开启
Switch-3(config-if)#exit
Switch-3(config)#interface vlan 20 						//激活VLAN20的SVI端口配置IP地址
Switch-3(config-if)#ip address 192.168.10.1 255.255.255.0
Switch-3(config-if)#no shutdow
Switch-3(config-if)#exit
```

4、查看三层交换机Switch-3产生直连路由

```c
Switch-3#show ip route							//查看三层交换机SVI端口产生路由表
Codes: C - connected, S - static, I - IGRP, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

C    192.168.10.0/24 is directly connected, Vlan20
C    192.168.20.0/24 is directly connected, Vlan10
Switch-3#
```

```c
Switch-3#show interfaces vlan 10 						//查看 VLAN10的SVI接口
Vlan10 is up, line protocol is up
  Hardware is CPU Interface, address is 000a.416a.4aed (bia 000a.416a.4aed)
  Internet address is 192.168.20.1/24
  MTU 1500 bytes, BW 100000 Kbit, DLY 1000000 usec,
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation ARPA, loopback not set
  ARP type: ARPA, ARP Timeout 04:00:00
  Last input 21:40:21, output never, output hang never
  Last clearing of "show interface" counters never
  Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
  Queueing strategy: fifo
  Output queue: 0/40 (size/max)
```

5、启用三层交换机

```c
switch-3(config)#ip routing      //启用三层交换机    不启用，默认就为二层
```

6、验证配置 

![image-20221109230017098](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20221109230017098.png)

注：两台交换机之间相连端口，应该设置为“tag VLAN ”模式 

给三层设备的 SVI端口 设置完IP地址后，一定要使用“no shutdown" 命令激活，否则无法 正常使用

如果创建的VLAN内没有激活端口，则三层 交换机上相应的VLAN的SVI端口也将无法被激活，无法正常显示路由表信息

测试PC信息需要设置 网关，网关为相应 VLAN的SVI接口地址

使用“ping”命令测试网络连通时，应该关闭双方PC机自带防火墙 功能，否则会影响连通情况测试

