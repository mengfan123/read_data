# 					配置交换机Trunk干道技术

交换机上的一个端口只能属于一个VLAN。默认情况下，该端口也被称为Access接入口



如果实现一个端口能属于多个VLAN所共有的通信接口，需要在该接口上启用IEEE802.1q协议配置，也称为Trunk接入口

1、配置Switch-1交换机设备VLAN信息

```c
Switch(config)#
Switch(config)#hostname Switch-1          //修改交换机名称
Switch-1(config)#vlan 10					//创建vlan10
Switch-1(config-vlan)#name xiaoshou				//将VLAN10 命名为 xiaoshou
Switch-1(config-vlan)#vlan 20    				// 创建VLAN20
Switch-1(config-vlan)#name jishou				//将VLAN20  命名为   技术
Switch-1(config-vlan)#exit           			//退出接口配置模式
Switch-1(config)#
Switch-1(config)#interface range fastEthernet 0/2-6     //端口范围选择   如果不是连续的用,号分隔
Switch-1(config-if-range)#switchport access vlan 10		//将0/2-0/6端口划分到vlan10
Switch-1(config-if-range)#exit
Switch-1(config)#
Switch-1(config)#interface range fastEthernet 0/10-15    //端口范围选择   如果不是连续的用,号分隔
Switch-1(config-if-range)#switchport access vlan 20		//将0/10-0/15端口划分到vlan20
Switch-1(config-if-range)#end
```

```

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/16, Fa0/17, Fa0/18, Fa0/19
                                                Fa0/20, Fa0/21, Fa0/22, Fa0/23
                                                Fa0/24
10   xiaoshou                         active    Fa0/2, Fa0/3, Fa0/4, Fa0/5
                                                Fa0/6
20   jishou                           active  
                                                Fa0/10, Fa0/11, Fa0/12, Fa0/13
                                                Fa0/14, Fa0/15
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

2、设置交换机Switch-1的Trunk链路

```c
Switch-1(config)#interface fastEthernet 0/3        //进入0/3子端口模式
Switch-1(config-if)#switchport mode trunk          //将端口模式设置为trunk
Switch-1(config-if)#exit
Switch-1(config)#
```

3、配置Switch-2 交换机设备的VLAN信息

```c
Switch(config)#hostname Switch-2  				//配置交换机名称
Switch-2(config)#vlan 10						//创建VLAN10
Switch-2(config-vlan)#name xiaoshou				//将VLAN10 命名为xiaoshou
Switch-2(config-vlan)#exit
Switch-2(config)#interface range fastEthernet 0/2-6       //指定端口范围
Switch-2(config-if-range)#switchport access vlan 10			//将端口划分给VLAN10
Switch-2(config-if-range)#end
Switch-2#
```

4、设置交换机的Trunk链路

```c
Switch-2(config)#interface fastEthernet 0/1      //进入端口模式    0/1
Switch-2(config-if)#switchport mode trunk		//配置端口模式
Switch-2(config-if)#end
```

5、查看Trunk的配置信息

```c
Switch-2#show interface fastEthernet 0/1 switchport     //查看Trunk的配置信息
Name: Fa0/1
Switchport: Enabled
Administrative Mode: trunk
Operational Mode: trunk
Administrative Trunking Encapsulation: dot1q
Operational Trunking Encapsulation: dot1q
Negotiation of Trunking: On
Access Mode VLAN: 1 (default)
Trunking Native Mode VLAN: 1 (default)
Voice VLAN: none
Administrative private-vlan host-association: none
Administrative private-vlan mapping: none
Administrative private-vlan trunk native VLAN: none
Administrative private-vlan trunk encapsulation: dot1q
Administrative private-vlan trunk normal VLANs: none
Administrative private-vlan trunk private VLANs: none
Operational private-vlan: none
Trunking VLANs Enabled: ALL
Pruning VLANs Enabled: 2-1001
Capture Mode Disabled
Capture VLANs Allowed: ALL
Protected: false
 --More-- 
```

6、使用ping名称测试配置

PC2

![image-20221106231432740](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20221106231432740.png)

PC1

![image-20221106231518939](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20221106231518939.png)

![image-20221106231533192](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20221106231533192.png)

相同VLAN之间可以互通，不同VLAN之间无法互通