

|Имя устройства | IPv4 | IPv6 |
|------------|------|------|
| CLI | 172\.16.13.2/28 - к ISP |  2001::3:2/120 — к ISP |
| ISP | 172\.16.13.1/24 - к CLI<br />10\.10.0.1/30 - к HQ-R<br />192\.168.12.2/30 - к BR-R | 2001::3:1/120 — к CLI<br />2001::7:2/126 — к HQ-R<br />2001::7:6/126 - к BR-R |
| HQ-R | 10\.10.0.2/26  — к HQ-SRV  <br />172\.16.13.3/24  — к ISP  | 2001::1:1/122 — к HQ-SRV<br />2001::7:1/126 к — ISP |
| BR-R | 192\.168.12.3/28 — к BR-SRV 172.16.13.5/30 — к ISP | 2001::2:1/124 — к BR-SRV 2001::7:5/126 — к ISP |
| HQ-SRV | 10\.10.0.5/26 — к HQ-R  | 2001::1:2/122 — к HQ-R |
| BR-SRV | 192\.168.12.5/28 — к BR-R  | 2001::2:2/124 — к BR-R |


|Имя устройства | IPv4 | IPv6 |
|------------|------|------|
| ISP 1 | 172.16.3.1/28 |2001::3:1/120|
| ISP к HQ | 10.10.0.1/30 |2001::7:2/126|
| ISP к BR | 10.10.0.3/30 |2001::7:6/126|
| BR-R к SRV | 172.16.2.1/28 |2001::2:1/124|
| BR-R к ISP | 10.10.0.4/30 |2001::7:5/126|
| HQ-R к ISP | 10.10.0.2/30 |2001::7:1/126|
| HQ к SRV | 172.16.1.1/28 |2001::1:1/122|
| HQ-SRV | 172.16.1.2/28 |2001::1:2/122|
| BR-SRV | 172.16.2.2/28 |2001::2:2/124|
| CLI | 172.16.3.2/28 |2001::3:2/120|

Ospf |router-id 1.1.1.1\2.2.2.2 \3.3.3.3
|------------|------------|
| HQ – |Network 172.16.1.0/24 area 0  |
| HQ – |Network 10.10.0.0/26 area 1 |
| ISP – |network 10.10.0.0/30 area 1 | 
| ISP – |network 172.16.3.0/24 area 0 |
| ISP – |network 10.10.0.0/30 area 2 |
| BR – |network 172.16.13.5/30 area 0 |
| BR – | network 192.168.12.3/28 area 2 |

Ospf6 | router-id 0.0.0.1\0.0.0.2\0.0.0.3
|------------|------------|
HQ – | Area 0.0.0.0 range 2001::7:1/126
HQ – | Area 0.0.0.0 range 2001::1:1/122
ISP – |area 0.0.0.0 range 2001::3:1/120
ISP – |area 0.0.0.0 range 2001::7:2/126
ISP – |area 0.0.0.0 range 2001::7:6/126
BR – |area 0.0.0.0 range 2001::2:1/124
BR – |area 0.0.0.0 range 2001::7:5/126

![image](https://github.com/Revenant262626/demka/assets/159104311/85b5a694-4dcd-42aa-ba70-1a0a01c623c7)

Если закачка не идет то нужно вводить эти команды пока не заработает:
Dhclient -r
Dhclient -v

МОДУЛЬ 1

Теперь пока все не испорчено или правильно сделано, скачаем все что нужно для выполнения заданий в дальнейшем:
Apt install frr(для frr, на всех роутерах(HQ, BR, ISP)
Apt install isc-dhcp-server(для dhcp, только на HQ)
Apt install radvd(настройка маршрутизации)
Apt install iperf3(для измерения пропускной способности на 2х маршрутизаторах HQ и ISP)
Apt install bind9 dnsutils(пакеты для днс будет стоять на сервере)(не надо)
apt install iptables-persistent(SRV для ssh)

1. FRR

apt install frr
nano /etc/frr/daemons
меняем ipv4 и ipv6 на да
systemctl restart frr (ОБЯЗАТЕЛЬНО РЕСТАРТНИ СУК)
vtysh 
Conf t
router ospf
ospf router-id 0.0.0.0
ospf router-id 1.1.1.1
ospf router-id 2.2.2.2
ospf router-id 3.3.3.3
(НА HQ-R)
network 10.10.0.2/30 area 0
network 172.16.1.1/28 area 1
exit
exit
write
exit
nano /etc/sysctl.conf
раскоменчиваем ![image](https://github.com/Revenant262626/demka/assets/159104311/cf24a689-9430-4799-8caf-f0f1456cd5bf) ![image](https://github.com/Revenant262626/demka/assets/159104311/e6f0a087-c233-4cfb-adf8-bbb3ce46f30d)
sysctl -p
vtysh
conf t
router ospf6
ospf6 router-id 0.0.0.0
ospf6 router-id 0.0.0.1
ospf6 router-id 0.0.0.2
ospf6 router-id 0.0.0.3
area 0.0.0.0 range 2001::7:1/126
area 0.0.0.0 range 2001::1:1/122
exit
interface ens224
ipv6 ospf6 area 0.0.0.0
exit
interface ens256
ipv6 ospf6 area 0.0.0.0
exit
exit
write
exit
(НА BR-R)
network 10.10.0.4/30 area 0
network 172.16.2.1 area 2
exit
exit
write
exit
nano /etc/sysctl.conf
раскоменчиваем ![image](https://github.com/Revenant262626/demka/assets/159104311/cf24a689-9430-4799-8caf-f0f1456cd5bf) ![image](https://github.com/Revenant262626/demka/assets/159104311/e6f0a087-c233-4cfb-adf8-bbb3ce46f30d)
sysctl -p
vtysh
conf t
router ospf6
ospf6 router-id 0.0.0.0
ospf6 router-id 0.0.0.1
ospf6 router-id 0.0.0.2
ospf6 router-id 0.0.0.3
area 0.0.0.0 range 2001::7:5/126
area 0.0.0.0 range 2001::2:1/124
exit
interface ens224
ipv6 ospf6 area 0.0.0.0
exit
interface ens256
ipv6 ospf6 area 0.0.0.0
exit
exit
write
exit
(НА ISP)
network 172.16.3.1/28 area 3
network 10.10.0.1/30 area 0
network 10.10.0.3/30 area 0
exit
exit
write
exit
nano /etc/sysctl.conf
раскоменчиваем ![image](https://github.com/Revenant262626/demka/assets/159104311/cf24a689-9430-4799-8caf-f0f1456cd5bf) ![image](https://github.com/Revenant262626/demka/assets/159104311/e6f0a087-c233-4cfb-adf8-bbb3ce46f30d)
sysctl -p
vtysh
conf t
router ospf6
ospf6 router-id 0.0.0.0
ospf6 router-id 0.0.0.1
ospf6 router-id 0.0.0.2
ospf6 router-id 0.0.0.3
area 0.0.0.0 range 2001::3:1/120
area 0.0.0.0 range 2001::7:2/126
area 0.0.0.0 range 2001::7:6/126
exit
interface ens224
ipv6 ospf6 area 0.0.0.0
exit
interface ens256
ipv6 ospf6 area 0.0.0.0
exit
exit
write
exit



