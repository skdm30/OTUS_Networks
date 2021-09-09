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
Интерфейсу G0/0/0 назначены группы многоадресной рассылки:  
FF02::1     
FF02::1:FF00:1      


2.2 ***Активируйте IPv6-маршрутизацию на R1.***      
***a)*** В командной строке на PC-1 введем команду ipconfig, чтобы получить данные IPv6-адреса, назначенного интерфейсу ПК.         
![](pic/PC1.png     
Видим интерфейсу назаначаен адрес IPv6 LincLocal, который служит для связи внутри локальной сети.       
***b)*** Активируем IPv6 маршрутизацию на R1.       
```
R1 # configure terminal
R1(config)# ipv6 unicast-routing
R1(config)# exit        
```
***с)*** Проверим данные IPv6 адреса на PC-1        
![](pic/PC1.png     
Почему PC-B получил глобальный префикс маршрутизации и идентификатор подсети, которые вы настроили на R1?       

2.3 ***Назначим IPv6-адреса интерфейсу управления (SVI) на S1.***      
***a)*** Назначим адрес IPv6 для S1.        
```
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#inter
S1(config)#interface vlan1
S1(config-if)#ipv6 address 2001:db8:acad:1::b/64
S1(config-if)#no shu
S1(config-if)#no shutdown 

S1(config-if)#
%LINK-5-CHANGED: Interface Vlan1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up

S1(config-if)#ipv6 address fe80::1 lin
S1(config-if)#ipv6 address fe80::1 link-local 
S1(config-if)#end       
```     

***b)*** Проверим правильность назначения IPv6 адресов интерфейсу управления        
```
S1#show ipv6 interface vlan 1
Vlan1 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::1
  No Virtual link-local address(es):
  Global unicast address(es):
    2001:DB8:ACAD:1::B, subnet is 2001:DB8:ACAD:1::/64
  Joined group address(es):
    FF02::1
    FF02::1:FF00:1
    FF02::1:FF00:B
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  Output features: Check hwidb
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
  ND NS retransmit interval is 1000 milliseconds
S1#
```     

2.4 ***Назначим компьютерам статические IPv6 адреса.***         
***a)*** Настройки PC-0     
картинка        
Настройка PC-2      
картинка            

## 3. Проверка сквозного подключения.       
Для проверки правильности работы сети отправили эхо-запрос на FE80::1, на интерфейс управления S1  и на PC-2 c PC1
rfhnbyrf 4          

Также с PC-2 отправили эхо-запрос в обратном направлении к PC-0 и на локальный адрес канала G0/0/0 на R1
            
## Вопросы.     
1.	Почему обоим интерфейсам Ethernet на R1 можно назначить один и тот же локальный адрес канала — FE80::1?     

Такое возможно, т.к. оба интерфейса задействованы в одной локальной сети.       

2.	Какой идентификатор подсети в индивидуальном IPv6-адресе 2001:db8:acad::aaaa:1234/64?       
2001:db8:acad::/64

