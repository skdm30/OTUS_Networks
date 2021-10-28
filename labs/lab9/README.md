# Лабораторная работа - Конфигурация безопасности коммутатора   


**Часть 1. Настройка основного сетевого устройства**    
***Создадим сеть согласно топологии:*** 
![](pic/network.png) 
***Настроим маршрутизатор R1***   
Загрузим конфигурацию из методического указания.    
Проверим текущую конфигурацию:  
``` 
R1#show ip interface brief 
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0/0   unassigned      YES unset  administratively down down 
GigabitEthernet0/0/1   192.168.10.1    YES manual up                    up 
Loopback0              10.10.1.1       YES manual up                    up 
Vlan1                  unassigned      YES unset  administratively down down
R1#   
```   
***Настройка и проверка основных параметров коммутатора***    
Настроим имя хоста, запретим нежелательный поиск в DNS и установим шлюз по умолчанию
```
Switch(config)#hostname S1
S1(config)#no ip domain-lookup
S1(config)#ip default-gateway 192.168.10.1    
```   

**Часть 2. Настройка сетей VLAN на коммутатораха**      
***Сконфигурируем и настроим VLAN 10, 333, 999***    
``` 
S1# show vlan brief 

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    
10   MANAGEMENT                       active    Fa0/5, Fa0/6
333  NATIVE                           active    
999  PARKINGLOT                       active    Fa0/2, Fa0/3, Fa0/4, Fa0/7
                                                Fa0/8, Fa0/9, Fa0/10, Fa0/11
                                                Fa0/12, Fa0/13, Fa0/14, Fa0/15
                                                Fa0/16, Fa0/17, Fa0/18, Fa0/19
                                                Fa0/20, Fa0/21, Fa0/22, Fa0/23
                                                Fa0/24, Gig0/1, Gig0/2
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    
S1#
```
  
```
S2#show vlan brief 

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/2, Fa0/3, Fa0/4, Fa0/5
                                                Fa0/6, Fa0/7, Fa0/8, Fa0/9
                                                Fa0/10, Fa0/11, Fa0/12, Fa0/13
                                                Fa0/14, Fa0/15, Fa0/16, Fa0/17
                                                Fa0/19, Fa0/20, Fa0/21, Fa0/22
                                                Fa0/23, Fa0/24, Gig0/1, Gig0/2
10   MANAGEMENT                       active    Fa0/18
333  NATIVE                           active    
999  PARKINGLOT                       active    
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    
S2#
```
**Часть 3. Настройки безопасности коммутатора.**    
Настроим все магистральные порты Fa0/1 на обоих коммутаторах для использования VLAN 333 в качестве native VLAN. 
``` 
S1#show interfaces trunk 
Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      333

Port        Vlans allowed on trunk
Fa0/1       1,10,333,999

Port        Vlans allowed and active in management domain
Fa0/1       1,10,333,999

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       1,10,333,999

S1#
```   
```
S2#show int trunk 
Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      333

Port        Vlans allowed on trunk
Fa0/1       1,10,333,999

Port        Vlans allowed and active in management domain
Fa0/1       1,10,333,999

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       1,10,333,999

S2#
```   
***Отключим согласование DTP f0/1 на коммутаторах:***
```
S1(config)#int f0/1
S1(config-if)#sw
S1(config-if)#switchport n
S1(config-if)#switchport nonegotiate 
```
***Настроим порты доступа***  
На S1 настроим f0/5, f0/6 и свяжем их с VLAN 10.  
На S2 портом доступа сделаем f0/18 и также свяжем с VLAN 10.  
На обоих коммутаторах все неиспользуемые порты переместим в VLAN 999 и отклюим их:    
```
S1#show interfaces status 
Port      Name               Status       Vlan       Duplex  Speed Type
Fa0/1                        connected    1          auto    auto  10/100BaseTX
Fa0/2                        disabled 999        auto    auto  10/100BaseTX
Fa0/3                        disabled 999        auto    auto  10/100BaseTX
Fa0/4                        disabled 999        auto    auto  10/100BaseTX
Fa0/5                        connected    10         auto    auto  10/100BaseTX
Fa0/6                        connected    10         auto    auto  10/100BaseTX
Fa0/7                        disabled 999        auto    auto  10/100BaseTX
Fa0/8                        disabled 999        auto    auto  10/100BaseTX
Fa0/9                        disabled 999        auto    auto  10/100BaseTX
Fa0/10                       disabled 999        auto    auto  10/100BaseTX
Fa0/11                       disabled 999        auto    auto  10/100BaseTX
Fa0/12                       disabled 999        auto    auto  10/100BaseTX
Fa0/13                       disabled 999        auto    auto  10/100BaseTX
Fa0/14                       disabled 999        auto    auto  10/100BaseTX
Fa0/15                       disabled 999        auto    auto  10/100BaseTX
Fa0/16                       disabled 999        auto    auto  10/100BaseTX
Fa0/17                       disabled 999        auto    auto  10/100BaseTX
Fa0/18                       disabled 999        auto    auto  10/100BaseTX
Fa0/19                       disabled 999        auto    auto  10/100BaseTX
Fa0/20                       disabled 999        auto    auto  10/100BaseTX
Fa0/21                       disabled 999        auto    auto  10/100BaseTX
Fa0/22                       disabled 999        auto    auto  10/100BaseTX
Fa0/23                       disabled 999        auto    auto  10/100BaseTX
Fa0/24                       disabled 999        auto    auto  10/100BaseTX
Gig0/1                       disabled 999        auto    auto  10/100BaseTX
Gig0/2                       disabled 999        auto    auto  10/100BaseTX
``` 
```
S2(config-if)#do sh in sta
Port      Name               Status       Vlan       Duplex  Speed Type
Fa0/1                        connected    1          auto    auto  10/100BaseTX
Fa0/2                        disabled 999        auto    auto  10/100BaseTX
Fa0/3                        disabled 999        auto    auto  10/100BaseTX
Fa0/4                        disabled 999        auto    auto  10/100BaseTX
Fa0/5                        disabled 999        auto    auto  10/100BaseTX
Fa0/6                        disabled 999        auto    auto  10/100BaseTX
Fa0/7                        disabled 999        auto    auto  10/100BaseTX
Fa0/8                        disabled 999        auto    auto  10/100BaseTX
Fa0/9                        disabled 999        auto    auto  10/100BaseTX
Fa0/10                       disabled 999        auto    auto  10/100BaseTX
Fa0/11                       disabled 999        auto    auto  10/100BaseTX
Fa0/12                       disabled 999        auto    auto  10/100BaseTX
Fa0/13                       disabled 999        auto    auto  10/100BaseTX
Fa0/14                       disabled 999        auto    auto  10/100BaseTX
Fa0/15                       disabled 999        auto    auto  10/100BaseTX
Fa0/16                       disabled 999        auto    auto  10/100BaseTX
Fa0/17                       disabled 999        auto    auto  10/100BaseTX
Fa0/18                       connected    10         auto    auto  10/100BaseTX
Fa0/19                       disabled 999        auto    auto  10/100BaseTX
Fa0/20                       disabled 999        auto    auto  10/100BaseTX
Fa0/21                       disabled 999        auto    auto  10/100BaseTX
Fa0/22                       disabled 999        auto    auto  10/100BaseTX
Fa0/23                       disabled 999        auto    auto  10/100BaseTX
Fa0/24                       disabled 999        auto    auto  10/100BaseTX
Gig0/1                       disabled 999        auto    auto  10/100BaseTX
Gig0/2                       disabled 999        auto    auto  10/100BaseTX
```
***Настроим безопасность портов f0/6 S1, f0/18 S2***    
На S1 введем команду *show port-security interface f0/6* для просмотра настроек по умолчанию: 
``` 
S1#show port-security int f0/6
Port Security              : Disabled
Port Status                : Secure-down
Violation Mode             : Shutdown
Aging Time                 : 0 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 1
Total MAC Addresses        : 0
Configured MAC Addresses   : 0
Sticky MAC Addresses       : 0
Last Source Address:Vlan   : 0000.0000.0000:0
Security Violation Count   : 0
S1#
```   
На S1 включим защиту порта на F0 / 6 со следующими настройками:
-	Максимальное количество записей MAC-адресов: 3  
-	
-	Режим безопасности: restrict  
-	
-	Aging time: 60 мин. 
-	
-	Aging type: неактивный      
-	
``` 
S1#show port-security int f0/6
Port Security              : Enabled
Port Status                : Secure-up
Violation Mode             : Restrict
Aging Time                 : 60 mins
Aging Type                 : Inactivity
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 3
Total MAC Addresses        : 0
Configured MAC Addresses   : 0
Sticky MAC Addresses       : 0
Last Source Address:Vlan   : 0000.0000.0000:0
Security Violation Count   : 0
S1#
```

``` 
S1#show port-security address 
			Secure Mac Address Table
-------------------------------------------------------------------------------
Vlan	Mac Address	Type			Ports		Remaining Age
								(mins)
----	-----------	----			-----		-------------
10	0090.2BC6.4C44	DynamicConfigured	FastEthernet0/6		-
------------------------------------------------------------------------------
Total Addresses in System (excluding one mac per port)     : 0
Max Addresses limit in System (excluding one mac per port) : 1024
S1#
``` 
***Реализуем безопасность DHCP snooping***		
На S2 включим DHCP snooping и настроим DHCP snooping во VLAN 10
```	
S2(config)#ip dhcp snooping
S2(config-if)#ip dhcp snooping vlan 10
```	
Магистральный порт f0/1 настроим как доверенный:	
```	
S2(config-if)ip dhcp snooping trust	
```	
 Ограничим порт f0/18 пятью DHCP-пакетами в секунду:	
 ```	
 S2(config-if)ip dhcp snooping limit rate 5	
 ```	
 Для проверки dhcp snooping выполним команду *show ip dhcp snooping*
 
 ```	
 S2#show ip dhcp snooping 
Switch DHCP snooping is enabled
DHCP snooping is configured on following VLANs:
10
Insertion of option 82 is enabled
Option 82 on untrusted port is not allowed
Verification of hwaddr field is enabled
Interface                  Trusted    Rate limit (pps)
-----------------------    -------    ----------------
FastEthernet0/1            yes        unlimited       
FastEthernet0/18           no         5          
```	
Проверим привязку отслеживания DHCP с помощью команды *show ip dhcp snooping binding*	
```	
S2#show ip dhcp snooping binding 
MacAddress          IpAddress        Lease(sec)  Type           VLAN  Interface
------------------  ---------------  ----------  -------------  ----  -----------------
00:D0:FF:06:B8:73   192.168.10.11    0           dhcp-snooping  10    FastEthernet0/18
Total number of bindings: 1
S2#
```
***Реализация PortFast и BPDU Guard***	
Настроим PortFast и BPDU на портах доступа на обоих коммутаторах:	
```	
S1(config)#int f0/5
S1(config-if)#spanning-tree portfast
S1(config-if)# spanning-tree bpduguard enable
``` 
Убедимся, что защита BPDU и PortFast включены на соответствующих портах:
```	
S1#show spanning-tree int f0/5 detail 

Port 5 (FastEthernet0/5) of VLAN0010 is designated forwarding
  Port path cost 19, Port priority 128, Port Identifier 128.5
  Designated root has priority 32778, address 0001.968C.D681
  Designated bridge has priority 32778, address 0060.3E24.56D8
  Designated port id is 128.5, designated path cost 19
  Timers: message age 16, forward delay 0, hold 0
  Number of transitions to forwarding state: 1
  The port is in the portfast mode
  Link type is point-to-point by default

```

Итоговые конфиги  [S1](config/S1) и [S2](config/S2)  		

***Проверим наличие сквозного ⁪подключения***
![](pic/PC-A.png) 	
![](pic/PC-B.png) 	
На скриншотах видно, что связь между двумя компьютерами работает.
