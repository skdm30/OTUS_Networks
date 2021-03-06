# Лабораторная работа. Доступ к сетевым устройствам по протоколу SSH
## Задачи
1. Настройка основных параметров устройства
2. Настройка маршрутизатора для доступа по протоколу SSH
3. Настройка коммутатора для доступа по протоколу SSH
4. SSH через интерфейс командной строки (CLI) коммутатора     


  **Таблица адресации**   
  
| Устройство |Интерфейс    | IP-адрес     | Маска подсети  | Шлюз по умолчанию|
|------------|-------------|--------------|----------------|------------------|
|    R1      | G 0/0/1     | 192.168.1.1  |255.255.255.0   |        -         |
|    S1      | VLAN1       | 192.168.1.11 |255.255.255.0   |    192.168.1.1   |
|    PC      | NIC         | 192.168.1.3  |255.255.255.0   |    192.168.1.1   |

## Ход выполнения работы    
### 1. Настройка основных параметров устройств    
Создали сеть согласно заданию:  
![](pic/topology.png) 

Провели настройку основных параметров маршрутизатора R1. Конфигурацию можно посмотреть 
[здесь](config/base_setting_R1).  

Также провели настройку компьютера PC0 и проверили связь с маршрутизатором R1 c помощью команды ping:   


![](pic/base_setting_PC.png)  

### 2. Настройка маршрутизатора для доступа по протоколу SSH   
#### 2.1 Настройка аутентификации устройств 
***a)*** Имя устройства было задано при первоначальной настройке маршрутизатора.  
***b)*** Зададим имя домена для устройства:  
``` 
R1(config)#ip domain-name cisco.com 
``` 

#### 2.2 Создадим ключ шифрования с указанием его длины и укажем версию SSH
```
R1(config)#crypto key ?
  generate  Generate new keys
  zeroize   Remove keys
R1(config)#crypto key z
R1(config)#crypto key zeroize rsa
% All RSA keys will be removed.
% All router certs issued using these keys will also be removed.
Do you really want to remove these keys? [yes/no]: y
R1(config)#cr
*Mar 2 19:38:17.218: %SSH-5-DISABLED: SSH 1.5 has been disabled
R1(config)#crypto k
R1(config)#crypto key ge
R1(config)#crypto key generate rsa
The name for the keys will be: R1.cisco.com
Choose the size of the key modulus in the range of 360 to 2048 for your
  General Purpose Keys. Choosing a key modulus greater than 512 may take
  a few minutes.

How many bits in the modulus [512]: 1024
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]

R1(config)#
R1(config)#ip ssh ?
  authentication-retries  Specify number of authentication retries
  time-out                Specify SSH time-out interval
  version                 Specify protocol version to be supported
R1(config)#ip ssh ver
R1(config)#ip ssh version 2
R1(config)#
```  
#### 2.3 Создадим имя пользователя в локальной базе учетных записей.  
```
R1(config)#username admin privilege 15 secret Adm1nP@55
```  

#### 2.4 Активируем протокол SSH на линиях VTY.  
***a)*** Активируем протоколы Telnet и SSH на входящих линиях VTY:  
```
R1(config)#line vty 0 4
R1(config-line)#tra
R1(config-line)#transport in
R1(config-line)#transport input te
R1(config-line)#transport input telnet ss
R1(config-line)#transport input telnet ssh
                                       ^
% Invalid input detected at '^' marker.
	
R1(config-line)#transport input ssh
R1(config-line)#transport input tel
R1(config-line)#transport input telnet 
R1(config-line)#
```  

***b)*** Изменим способ входа в систему так, чтобы использовалась проверка пользователей по локальной базе учетных записей:	
```	
R1(config-line)#login local			
```

#### 2.5 Сохраним текущую конфигурацию.		
```

R1#copy running-config startup-config 
Destination filename [startup-config]? 
Building configuration...
[OK]
R1#	
```

#### 2.6 Установим соединение с маршрутизатором по протоколу SSH.		
![](pic/ssh_R1.png) 

### 3. Настройка коммутатора для доступа по протоколу SSH   	
#### 3.1 Настроим основные параметры коммутатора.		
Конфигурацию можно посмотреть [здесь](config/base_setting_S1)		


#### 3.2 Настроим коммутатор для соединения по протоколу SSH.		
***a)*** Имя устройство было задано при начальной настройке.		
***b)*** Зададим имя домена для устройства.
```
S1(config)#ip domain-name cisco.com
```
***c)*** Создадим ключ шифрования с указанием его длины, а также применим версию 2 для SSH.
```
S1(config)#crypto key generate rsa
The name for the keys will be: S1.cisco.com
Choose the size of the key modulus in the range of 360 to 2048 for your
  General Purpose Keys. Choosing a key modulus greater than 512 may take
  a few minutes.

How many bits in the modulus [512]: 1024
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]
S1(config)#ip ?
  access-list      Named access-list
  arp              IP ARP global configuration
  default-gateway  Specify default gateway (if not routing IP)
  dhcp             Configure DHCP server and relay parameters
  domain           IP DNS Resolver
  domain-lookup    Enable IP Domain Name System hostname translation
  domain-name      Define the default domain name
  ftp              FTP configuration commands
  host             Add an entry to the ip hostname table
  name-server      Specify address of name server to use
  scp              Scp commands
  ssh              Configure ssh options
S1(config)#ip s
S1(config)#ip ss
S1(config)#ip ssh ?
  authentication-retries  Specify number of authentication retries
  time-out                Specify SSH time-out interval
  version                 Specify protocol version to be supported
S1(config)#ip ssh ve
S1(config)#ip ssh version ?
  <1-2>  Protocol version
S1(config)#ip ssh version 2
S1(config)#
```
***d)*** Создадим имя пользователя в локальной базе учетных записей:		
```
R1(config)#username admin privilege 15 secret Adm1nP@55
```
***e)*** Активируем протоколы Telnet и SSH на линиях VTY:	
```
S1(config-line)#transport input ssh
S1(config-line)#transport input telnet
```
***f)*** Изменим способ входа в систему так, чтобы использовалась проверка пользователей по локальной базе учетных записей:	
```	
R1(config-line)#login local			

```

#### 3.3 Установим соединение с маршрутизатором по протоколу SSH.		
![](pic/ssh_S1.png) 


### 4. Настройка протокола SSH с использованием интерфейса командной строки (CLI) коммутатора
```
S1#ssh -l admin 192.168.1.1

Password: 
% Login invalid


Password: 

Unauthorized access is strictly prohibited. 

R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#
R1(config)#
R1(config)#
R1(config)#
R1(config)#
R1(config)#
S1#
S1#
[Resuming connection 1 to 192.168.1.1 ... ]

R1(config)#exit
R1#exit

[Connection to 192.168.1.1 closed by foreign host]
S1#
```

### Вопрос для повторения.		
Как предоставить доступ к сетевому устройству нескольким пользователям, у каждого из которых есть собственное имя пользователя?			
***Ответ:*** Необходимо добавить требуемых пользователей в локальный список учетных записей ( команда username).
