# Развертывание коммутируемой сети с резервными каналами

### Цели:
1. Создание сети и настройка основных параметров устройства
2. Выбор корневого моста
3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов
4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов

### Топология:
![](https://raw.githubusercontent.com/mineralka-sudo/otus/main/labs/stp/topology.PNG)

### Таблица адресации:
| Устройство | Интерфейс | IP          | Маска подсети |
|------------|-----------|-------------|---------------|
| S1         | VLAN 1    | 192.168.1.1 | 255.255.255.0 |
| S2         | VLAN 1    | 192.168.1.2 | 255.255.255.0 |
| S3         | VLAN 1    | 192.168.1.3 | 255.255.255.0 |

### Решение:
#### 1. Создаем сеть и настраиваем основные параметры устройств
```
hostname S1
!
no ip domain-lookup
!
enable secret class
!
line vty 0 15
password cisco
login
!
line console 0
password cisco
login
!
logging synchronous
!
banner motd «This is a secure system. Authorized Access Only!»
!
interface vlan 1
ip address 192.168.1.1 255.255.255.0
```
#### 2. Определяемой корневой мост
```
interface range e0/0-3
shutdown
!
interface range e0/0-3
switchport trunk encapsulation dot1q
switchport mode trunk
!
interface range e0/1, e0/3
no shutdown
```
```
show spanning-tree
```
![](https://github.com/mineralka-sudo/otus/blob/main/labs/stp/S1_2.PNG?raw=true)
![](https://github.com/mineralka-sudo/otus/blob/main/labs/stp/S2_2.PNG?raw=true)
![](https://github.com/mineralka-sudo/otus/blob/main/labs/stp/S3_2.PNG?raw=true)
- S2 является корневым мостом, так как его MAC наименьший 
- Порт S3 e0/3 является альтернативным, так как..
![](https://github.com/mineralka-sudo/otus/blob/main/labs/stp/t.png?raw=true)
#### 3. Наблюдаем за процессом выбора протоколом STP порта, исходя из стоимости портов
```
interface e0/1
spanning-tree cost 18
```
```
show spanning-tree
```
![](https://github.com/mineralka-sudo/otus/blob/main/labs/stp/S1_3.PNG?raw=true)
![](https://github.com/mineralka-sudo/otus/blob/main/labs/stp/S3_3.PNG?raw=true)
Почему протокол spanning-tree заменяет ранее заблокированный порт на назначенный порт и блокирует порт, который был назначенным портом на другом коммутаторе?
#### 4. Наблюдаем за процессом выбора протоколом STP порта, исходя из приоритета портов
```
interface range e0/0, e0/2
no shutdown
```
```
show spanning-tree
```
![](https://github.com/mineralka-sudo/otus/blob/main/labs/stp/S1_4.PNG?raw=true)
![](https://github.com/mineralka-sudo/otus/blob/main/labs/stp/S2_4.PNG?raw=true)
![](https://github.com/mineralka-sudo/otus/blob/main/labs/stp/S3_4.PNG?raw=true)
Какой порт выбран протоколом STP в качестве порта корневого моста на каждом коммутаторе некорневого моста? _________________________________
Почему протокол STP выбрал эти порты в качестве портов корневого моста на этих коммутаторах?
