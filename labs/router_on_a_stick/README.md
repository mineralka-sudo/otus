# Router-on-a-Stick

### Задачи:
1: Создать сеть и настроить основные параметры устройств  
2: Создать VLAN и настроить порты коммутаторов  
3: Настроить trunk 802.1Q между коммутаторами  
4: Настроить маршрутизацию между VLAN    

### Топология
![](https://raw.githubusercontent.com/mineralka-sudo/otus/main/labs/lab1/topology.png)

### Таблица адрессации
| Device | Interface | IP Address   | Subnet Mask   | Default Gateway |
|--------|-----------|--------------|---------------|-----------------|
| R1     | G0/0/1.3  | 192.168.3.1  | 255.255.255.0 | N/A             |
|        | G0/0/1.4  | 192.168.4.1  | 255.255.255.0 |                 |
|        | G0/0/1.8  | N/A          | N/A           |                 |
| S1     | VLAN 3    | 192.168.3.11 | 255.255.255.0 | 192.168.3.1     |
| S2     | VLAN 3    | 192.168.3.12 | 255.255.255.0 | 192.168.3.1     |
| PC-A   | NIC       | 192.168.3.3  | 255.255.255.0 | 192.168.3.1     |
| PC-B   | NIC       | 192.168.4.3  | 255.255.255.0 | 192.168.4.1     |

### VLAN 
| VLAN | Name       | Interface Assigned            |
|------|------------|-------------------------------|
| 3    | Management | S1: VLAN 3                    |
|      |            | S2: VLAN 3                    |
|      |            | S1: F0/6                      |
| 4    | Operations | S2: F0/18                     |
| 7    | ParkingLot | S1: F0/24, F0/7-24, G0/1-2    |
|      |            | S2: f0/2-17, f0/19-24, G0/1-2 |
| 8    | Native     | N/A                           |

### Решение
1: Создаем сеть и настраиваем основные параметры устройств:  
    - Отключаем поиск DNS  
    - Назначаем class в качестве привилегированного зашифрованного пароля EXEC  
    - Назначаем cisco в качестве пароля консоли    
    - Назначаем cisco в качестве пароля VTY   
    - Шифруем открытые пароли   
    - Создаем баннер, предупреждающий любого, кто получает доступ к устройству, о том, что несанкционированный доступ запрещен   
    - Настраиваем часы   
    - Сохраняем текущую конфигурацию в файл начальной конфигурации   
```
no ip domain lookup
!
enable secret class
!
line console 0
password cisco
login
!
line vty 0 15
password cisco
login
!
service password-encryption
!
banner motd «This is a secure system. Authorized Access Only!»
!
exit
!
clock set 19:03:00 13 feb 2022
!
copy running-config startup-config
```
2: Создаем VLAN:
```
vlan 3
name Management
!
interface vlan 3
ip address 192.168.3.11 255.255.255.0
no shutdown
```
3: Настроим trunk 802.1Q между коммутаторами:
```
interface f0/1
switchport mode trunk
switchport trunk native vlan 8
switchport trunk allowed vlan 3,4,8
```
4: Настроим маршрутизацию между VLAN на маршрутизаторе:
```
interface g0/1.3
description Management
encapsulation dot1Q 3
ip address 192.168.3.1 255.255.255.0
```
