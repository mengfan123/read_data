# 								配置交换机VLAN功能

1、测试网络联通情况

```c
ping 命令可以测试网络的连接情况
```

2、在交换机上按照端口创建VLAN

```c
Switch>enable
Switch#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#vlan 10										//创建vlan10
Switch(config-vlan)#name test10								//将vlan10 命名为test10
Switch(config-vlan)#exit
Switch(config)#vlan 20										//创建vlan20
Switch(config-vlan)#name test20								//将vlan20 命名为test20
Switch(config-vlan)#exit
Switch(config)#show vlan
Switch(config)#exit
Switch#
Switch#show vlan										//查看配置完成的vlan
```

```c

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1, Fa0/2, Fa0/3, Fa0/4
                                                Fa0/5, Fa0/6, Fa0/7, Fa0/8
                                                Fa0/9, Fa0/10, Fa0/11, Fa0/12
                                                Fa0/13, Fa0/14, Fa0/15, Fa0/16
                                                Fa0/17, Fa0/18, Fa0/19, Fa0/20
                                                Fa0/21, Fa0/22, Fa0/23, Fa0/24
10   test10                           active    
20   test20                           active    
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

3、将接口分配到不同vlan中

```c
Switch#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#interface fastethernet 0/5        
Switch(config-if)#switchport access vlan 10       //将f5端口  加入vlan10
Switch(config-if)#no shutdown					//开启端口
Switch(config-if)#interface fastethernet 0/1
Switch(config-if)#switchport access vlan 20       //将f1端口  加入vlan20
Switch(config-if)#no shutdown        			//开启端口
Switch(config-if)#exit
Switch(config)#exit
Switch#
%SYS-5-CONFIG_I: Configured from console by console
Switch#
Switch#show vlan
```

```c

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1, Fa0/2, Fa0/3, Fa0/4
                                                Fa0/6, Fa0/7, Fa0/8, Fa0/9
                                                Fa0/11, Fa0/12, Fa0/13, Fa0/14
                                                Fa0/15, Fa0/16, Fa0/17, Fa0/18
                                                Fa0/19, Fa0/20, Fa0/21, Fa0/22
                                                Fa0/23, Fa0/24
10   test10                           active    Fa0/5
20   test20                           active    Fa0/10
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

4、测试网络联通情况

```c
不同vlan之间会产生隔离，不同vlan之间也无法互通
```

5、清除vlan中的IP地址

```c
Switch(config)#interface vlan 10				
Switch(config-if)#no ip address				//清除该vlan上的IP地址
Switch(config-if)#exit
Switch(config)#no interface vlan 10				//释放该vlan子接口
Switch(config)#no vlan 10					//删除vlan
Switch(config)#
```

