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
Switch#hostname S1
S1#no ip domain-lookup
S1#ip default-gateway 192.168.10.1    
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


