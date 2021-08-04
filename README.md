# Лабораторная работа. Базовая настройка коммутатора
## Задачи
1. **Проверка конфигурации коммутатора по умолчанию**
2. **Создание сети и настройка основных параметров устройства**
    - Настройте базовые параметры коммутатора
    - Настройте IP-адрес для ПК
3. **Проверка сетевых подключений**
    - Отобразите конфигурацию устройства
    - Протестируйте сквозное соединение, отправив эхо-запрос
    - Протестируйте возможности удаленного управления с помощью Telnet

## Решение
1. В ходе проверки текущих настроек коммутатора 2960 отмечено следующее:
    - На коммутаторе находится пустой файл конфигурации по умолчанию;
    - На коммутаторе 2960 имеется 24 интерфейса FastEhternet;
    - На коммутаторе 2960 имеется 2 интрфейса GigabitEhternet;
    - Диапазон значений, отображаемых в VTY-линиях 0-15;
    - IP-адрес сети VLAN1 не назначен, интерфейс отключен.
2. После создания сети в Cisko Packet Tracer получаем 
    ![](схема сети.png)
