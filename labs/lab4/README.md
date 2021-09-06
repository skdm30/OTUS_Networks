# Лабораторная работа. Настройка IPv6-адресов на сетевых устройствах
## Задачи
**Часть 1. Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатор** 

**Часть 2. Ручная настройка IPv6-адресов**  

**Часть 3. Проверка сквозного соединения**  

## Ход выполнения работы  
1.1 ***Настроим маршрутизатор.***    
[Конфигурация](config/R1_base_setting) маршрутизатора R1. 

1.2 ***Настроим коммутатор.***   
[Конфигурация](config/S1_base_setting) коммуттатора R1.   


## 2. Ручная настройка IPv6-адресов 
2.1 ***Назначим IPv6-адреса интерфейсам Ethernet на R1*** 

***a)*** Назначим глобальные индивидуальные IPv6-адреса, указанные в таблице адресации обоим интерфейсам Ethernet на R1.
``` R1(config)#int
R1(config)#interface g0/0/0
R1(config-if)#ipv6 address 2001:db8:acad:a::1/64
R1(config-if)#no sh
R1(config-if)#no shutdown 

R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/0, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/0, changed state to up

R1(config-if)#interface g0/0/1
R1(config-if)#ipv6 address 2001:db8:acad:1::1/64
R1(config-if)#no sh
R1(config-if)#no shutdown 

R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up

R1(config-if)#end
R1# 
```   

***b)*** Введем команду show ipv6 interface brief, чтобы проверить, назначен ли каждому интерфейсу корректный индивидуальный IPv6-адрес.    
```
R1#show ipv6 interface brief 
GigabitEthernet0/0/0       [up/up]
    FE80::2D0:FFFF:FE79:2401
    2001:DB8:ACAD:A::1
GigabitEthernet0/0/1       [up/up]
    FE80::2D0:FFFF:FE79:2402
    2001:DB8:ACAD:1::1
Vlan1                      [administratively down/down]
    unassigned
R1#
```   

***с)*** Чтобы обеспечить соответствие локальных адресов канала индивидуальному адресу, вручную введем локальные адреса канала на каждом интерфейсе Ethernet на R1.   
```
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#int
R1(config)#interface g0/0/0
R1(config-if)#ip
R1(config-if)#ipv
R1(config-if)#ipv6 ad
R1(config-if)#ipv6 address fe80::1 li
R1(config-if)#ipv6 address fe80::1 link-local 
R1(config-if)#interface g0/0/1
R1(config-if)#ipv6 address fe80::1 link-local 
R1(config-if)#end
R1#
%SYS-5-CONFIG_I: Configured from console by console

R1#
```     

***d)*** Убедимся, что локальный адрес связи ихменен на FE80::1
```
R1#show ipv6 interface brief 
GigabitEthernet0/0/0       [up/up]
    FE80::1
    2001:DB8:ACAD:A::1
GigabitEthernet0/0/1       [up/up]
    FE80::1
    2001:DB8:ACAD:1::1
Vlan1                      [administratively down/down]
    unassigned
```

