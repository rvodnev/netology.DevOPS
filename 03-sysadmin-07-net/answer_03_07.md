# Решение домашней работы

1. Проверьте список доступных сетевых интерфейсов на вашем компьютере. Какие команды есть для этого в Linux и в Windows?

Windows
```
ipconfig /all
netsh interface show interface
```
Linux  - `ip a`

---
2.Какой протокол используется для распознавания соседа по сетевому интерфейсу? Какой пакет и команды есть в Linux для этого?
```
Link Layer Discovery Protocol (LLDP) — протокол канального уровня, который позволяет сетевым устройствам анонсировать в сеть информацию о себе и о своих возможностях, а также собирать эту информацию о соседних устройствах.

```
В Linux есть пакет `lldpd`, поднимать для демонстрации не буду.
```bash
vagrant@vagrant:~$ sudo apt-get install lldpd
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following additional packages will be installed:
  libmysqlclient21 libsensors-config libsensors5 libsnmp-base libsnmp35 mysql-common
Suggested packages:
  lm-sensors snmp-mibs-downloader snmpd
The following NEW packages will be installed:
  libmysqlclient21 libsensors-config libsensors5 libsnmp-base libsnmp35 lldpd mysql-common
0 upgraded, 7 newly installed, 0 to remove and 0 not upgraded.
Need to get 1,228 kB/2,607 kB of archives.
After this operation, 12.5 MB of additional disk space will be used.
Do you want to continue? [Y/n] Y
Ign:1 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 libmysqlclient21 amd64 8.0.26-0ubuntu0.20.04.2
Err:1 http://security.ubuntu.com/ubuntu focal-updates/main amd64 libmysqlclient21 amd64 8.0.26-0ubuntu0.20.04.2
  404  Not Found [IP: 91.189.88.142 80]
E: Failed to fetch http://security.ubuntu.com/ubuntu/pool/main/m/mysql-8.0/libmysqlclient21_8.0.26-0ubuntu0.20.04.2_amd64.deb  404  Not Found [IP: 91.189.88.142 80]
E: Unable to fetch some archives, maybe run apt-get update or try with --fix-missing?
```

Тут можно все команды с примерами почитать [ЛИНК](http://xgu.ru/wiki/LLDP#LLDP_.D0.B2_Linux)

---
4. Какая технология используется для разделения L2 коммутатора на несколько виртуальных сетей? Какой пакет и команды есть в Linux для этого? Приведите пример конфига.


Технология VLAN (Virtual Local Area Network). 

В [Linux](http://xgu.ru/wiki/VLAN_%D0%B2_Debian) пакет `vlan`. Для настройки `sudo nano /etc/network/interfaces`. 
```
auto vlan1400
iface vlan1400 inet static
        address 192.168.1.1
        netmask 255.255.255.0
        vlan_raw_device eth0
```
---
5. Какие типы агрегации интерфейсов есть в Linux? Какие опции есть для балансировки нагрузки? Приведите пример конфига.

Типы агрегации интерфейсов в Linux:

* `Mode-0(balance-rr)` – Данный режим используется по умолчанию. Balance-rr обеспечивается балансировку нагрузки и отказоустойчивость. В данном режиме сетевые пакеты отправляются “по кругу”, от первого интерфейса к последнему. Если выходят из строя интерфейсы, пакеты отправляются на остальные оставшиеся. Дополнительной настройки коммутатора не требуется при нахождении портов в одном коммутаторе. При разностных коммутаторах требуется дополнительная настройка.
* `Mode-1(active-backup)` – Один из интерфейсов работает в активном режиме, остальные в ожидающем. При обнаружении проблемы на активном интерфейсе производится переключение на ожидающий интерфейс. Не требуется поддержки от коммутатора.
* `Mode-2(balance-xor)` – Передача пакетов распределяется по типу входящего и исходящего трафика по формуле ((MAC src) XOR (MAC dest)) % число интерфейсов. Режим дает балансировку нагрузки и отказоустойчивость. Не требуется дополнительной настройки коммутатора/коммутаторов.
* `Mode-3(broadcast` – Происходит передача во все объединенные интерфейсы, тем самым обеспечивая отказоустойчивость. Рекомендуется только для использования MULTICAST трафика.
* `Mode-4(802.3ad)` – динамическое объединение одинаковых портов. В данном режиме можно значительно увеличить пропускную способность входящего так и исходящего трафика. Для данного режима необходима поддержка и настройка коммутатора/коммутаторов.
* `Mode-5(balance-tlb)` – Адаптивная балансировки нагрузки трафика. Входящий трафик получается только активным интерфейсом, исходящий распределяется в зависимости от текущей загрузки канала каждого интерфейса. Не требуется специальной поддержки и настройки коммутатора/коммутаторов.
* `Mode-6(balance-alb)` – Адаптивная балансировка нагрузки. Отличается более совершенным алгоритмом балансировки нагрузки чем Mode-5). Обеспечивается балансировку нагрузки как исходящего так и входящего трафика. Не требуется специальной поддержки и настройки коммутатора/коммутаторов.

Конфиг:
```bash
iface bond0 inet static
        address 192.10.0.11
        netmask 255.255.255.0
        gateway 192.10.0.1
        bond-slaves eth0 eth1
        bond-mode balance-alb # Адаптивная балансировка нагрузки.
bond-miimon 200
bond-downdelay 100
```
---
6. Сколько IP адресов в сети с маской /29 ? Сколько /29 подсетей можно получить из сети с маской /24. Приведите несколько примеров /29 подсетей внутри сети 10.10.10.0/24.

Ставим `ipcalc`

```bash
vagrant@vagrant:~$ sudo apt-get install ipcalc
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following NEW packages will be installed:
  ipcalc
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 25.9 kB of archives.
After this operation, 64.5 kB of additional disk space will be used.
Get:1 http://archive.ubuntu.com/ubuntu focal/universe amd64 ipcalc all 0.41-5 [25.9 kB]
Fetched 25.9 kB in 1s (51.2 kB/s)
Selecting previously unselected package ipcalc.
(Reading database ... 75304 files and directories currently installed.)
Preparing to unpack .../archives/ipcalc_0.41-5_all.deb ...
Unpacking ipcalc (0.41-5) ...
Setting up ipcalc (0.41-5) ...
Processing triggers for man-db (2.9.1-1) ...
```
Считаем
```bash
vagrant@vagrant:~$ ipcalc 192.168.1.1/29
Address:   192.168.1.1          11000000.10101000.00000001.00000 001
Netmask:   255.255.255.248 = 29 11111111.11111111.11111111.11111 000
Wildcard:  0.0.0.7              00000000.00000000.00000000.00000 111
=>
Network:   192.168.1.0/29       11000000.10101000.00000001.00000 000
HostMin:   192.168.1.1          11000000.10101000.00000001.00000 001
HostMax:   192.168.1.6          11000000.10101000.00000001.00000 110
Broadcast: 192.168.1.7          11000000.10101000.00000001.00000 111
Hosts/Net: 6                     Class C, Private Internet
```
* IP 8
* Хостов 6

```bash
vagrant@vagrant:~$ ipcalc 10.10.10.0/24
Address:   10.10.10.0           00001010.00001010.00001010. 00000000
Netmask:   255.255.255.0 = 24   11111111.11111111.11111111. 00000000
Wildcard:  0.0.0.255            00000000.00000000.00000000. 11111111
=>
Network:   10.10.10.0/24        00001010.00001010.00001010. 00000000
HostMin:   10.10.10.1           00001010.00001010.00001010. 00000001
HostMax:   10.10.10.254         00001010.00001010.00001010. 11111110
Broadcast: 10.10.10.255         00001010.00001010.00001010. 11111111
Hosts/Net: 254                   Class A, Private Internet
```
* IP 256
* Хостов 254

Из сети `/24` можно получить 32 `/29` подсети. 

Примеры `/29` подсетей:
* 192.168.0.0/29 
* 192.168.0.8/29 
* 192.168.0.16/29

---
7. Задача: вас попросили организовать стык между 2-мя организациями. Диапазоны 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 уже заняты. Из какой подсети допустимо взять частные IP адреса? Маску выберите из расчета максимум 40-50 хостов внутри подсети.

Если брать в расчет только `IPv4`

Частные диапазоны IP-адресов:
* `10.0.0.0 — 10.255.255.255` (маска подсети для бесклассовой (CIDR) адресации: 255.0.0.0 или /8)
* `100.64.0.0 — 100.127.255.255` (маска подсети 255.192.0.0 или /10) - Данная подсеть рекомендована согласно RFC 6598 для использования в качестве адресов для CGN (Carrier-Grade NAT).
* `172.16.0.0 — 172.31.255.255` (маска подсети: 255.240.0.0 или /12)
* `192.168.0.0 — 192.168.255.255` (маска подсети: 255.255.0.0 или /16)
```bash
vagrant@vagrant:~$ ipcalc 100.64.0.0/26
Address:   100.64.0.0           01100100.01000000.00000000.00 000000
Netmask:   255.255.255.192 = 26 11111111.11111111.11111111.11 000000
Wildcard:  0.0.0.63             00000000.00000000.00000000.00 111111
=>
Network:   100.64.0.0/26        01100100.01000000.00000000.00 000000
HostMin:   100.64.0.1           01100100.01000000.00000000.00 000001
HostMax:   100.64.0.62          01100100.01000000.00000000.00 111110
Broadcast: 100.64.0.63          01100100.01000000.00000000.00 111111
Hosts/Net: 62                    Class A
```
---
8. Как проверить ARP таблицу в Linux, Windows? Как очистить ARP кеш полностью? Как из ARP таблицы удалить только один нужный IP?

Ставим утилиту в `Linux`
```bash
vagrant@vagrant:~$ sudo apt-get install net-tools
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following NEW packages will be installed:
  net-tools
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 196 kB of archives.
After this operation, 864 kB of additional disk space will be used.
Get:1 http://archive.ubuntu.com/ubuntu focal/main amd64 net-tools amd64 1.60+git20180626.aebd88e-1ubuntu1 [196 kB]
Fetched 196 kB in 1s (276 kB/s)
Selecting previously unselected package net-tools.
(Reading database ... 75316 files and directories currently installed.)
Preparing to unpack .../net-tools_1.60+git20180626.aebd88e-1ubuntu1_amd64.deb ...
Unpacking net-tools (1.60+git20180626.aebd88e-1ubuntu1) ...
Setting up net-tools (1.60+git20180626.aebd88e-1ubuntu1) ...
Processing triggers for man-db (2.9.1-1) ...
```
* Проверить ARP-таблицу: `arp -n`
* Очистить ARP кеш полностью: `ip neigh flush`
* Из ARP таблицы удалить только один нужный IP: `arp -d <address>`

В `Windows`
* `arp -a` -проверить ARP-таблицу
* `-d` — удаление IP-адреса (например, arp -d 192.168.100.10)
* `-d -a` — удаление всех записей в таблице ARP
* `-s` — добавление записи в таблицу ARP (команда arp -s АДРЕС MAC-АДРЕС, где АДРЕС — это адрес, который нужно добавить, а MAC-АДРЕС — MAC-адрес компьютера)
---
