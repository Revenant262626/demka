![image](https://github.com/Revenant262626/demka/assets/159104311/a60cbce1-0f47-47af-a7dd-8d11d381a6c0)


|Имя устройства | IPv4 | IPv6 |
|------------|------|------|
| CLI | 172\.16.13.2/28 - к ISP |  2001::3:2/120 — к ISP |
| ISP | 172\.16.13.1/24 - к CLI<br />10\.10.0.1/30 - к HQ-R<br />192\.168.12.2/30 - к BR-R | 2001::3:1/120 — к CLI<br />2001::7:2/126 — к HQ-R<br />2001::7:6/126 - к BR-R |
| HQ-R | 10\.10.0.2/26  — к HQ-SRV  <br />172\.16.13.3/24  — к ISP  | 2001::1:1/122 — к HQ-SRV<br />2001::7:1/126 к — ISP |
| BR-R | 192\.168.12.3/28 — к BR-SRV 172.16.13.5/30 — к ISP | 2001::2:1/124 — к BR-SRV 2001::7:5/126 — к ISP |
| HQ-SRV | 10\.10.0.5/26 — к HQ-R  | 2001::1:2/122 — к HQ-R |
| BR-SRV | 192\.168.12.5/28 — к BR-R  | 2001::2:2/124 — к BR-R |

|айпи маски | маска |
|--------|----------|
|255.255.255.0|24|
|255.255.255.218|25|
|255.255.255.192|26|
|255.255.255.224|27|
|255.255.255.240|28|
|255.255.255.248|29|
|255.255.255.252|30|

|Имя устройства | IPv4 | IPv6 |
|------------|------|------|
| ISP 1 | 172.16.3.1/28 |2001::3:1/120|
| ISP к HQ | 10.10.0.1/30 |2001::7:2/126|
| ISP к BR | 10.10.0.3/30 |2001::7:6/126|
| BR-R к SRV | 172.16.2.1/29 |2001::2:1/124|
| BR-R к ISP | 10.10.0.4/30 |2001::7:5/126|
| HQ-R к ISP | 10.10.0.2/30 |2001::7:1/126|
| HQ к SRV | 172.16.1.1/27 |2001::1:1/122|
| HQ-SRV | 172.16.1.2/27 |2001::1:2/122|
| BR-SRV | 172.16.2.2/29 |2001::2:2/124|
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

Если закачка не идет то нужно вводить эти команды пока не заработает:
Dhclient -r
Dhclient -v

<center><h1>МОДУЛЬ 1</h1></center>

<p>Теперь пока все не испорчено или правильно сделано, скачаем все что нужно для выполнения заданий в дальнейшем:</p>

```
Apt install frr(для frr, на всех роутерах(HQ, BR, ISP)
```
```
Apt install isc-dhcp-server(для dhcp, только на HQ)
```

```
Apt install radvd(настройка маршрутизации)
```

```
Apt install iperf3(для измерения пропускной способности на 2х маршрутизаторах HQ и ISP)
```

```
Apt install bind9 dnsutils(пакеты для днс будет стоять на сервере)(не надо)<
```

```
apt install iptables-persistent(SRV для ssh)<
```

1. FRR

<p>apt install frr</p>
<p>nano /etc/frr/daemons</p>
<p>меняем ipv4 и ipv6 на да</p>
<p>systemctl restart frr (ОБЯЗАТЕЛЬНО РЕСТАРТНИ СУК)</p>
<p>vtysh</p>
<p>Conf t</p>
<p>router ospf</p>
<p>ospf router-id 0.0.0.0</p>
<p>ospf router-id 1.1.1.1</p>
<p>ospf router-id 2.2.2.2</p>
<p>ospf router-id 3.3.3.3</p>

<p>(НА HQ-R)</p>

<p>network 10.10.0.2/30 area 0</p>
<p>network 172.16.1.1/28 area 1</p>
<p>exit</p>
<p>exit</p>
<p>write</p>
<p>exit</p>
<p>nano /etc/sysctl.conf</p>
<p>раскоменчиваем там две команды</p>
<p>sysctl -p</p>
<p>vtysh</p>
<p>conf t</p>
<p>router ospf6</p>
<p>ospf6 router-id 0.0.0.0</p>
<p>ospf6 router-id 0.0.0.1</p>
<p>ospf6 router-id 0.0.0.2</p>
<p>ospf6 router-id 0.0.0.3</p>
<p>area 0.0.0.0 range 2001::7:1/126</p>
<p>area 0.0.0.0 range 2001::1:1/122</p>
<p>exit</p>
<p>interface ens224</p>
<p>ipv6 ospf6 area 0.0.0.0</p>
<p>exit</p>
<p>interface ens256</p>
<p>ipv6 ospf6 area 0.0.0.0</p>
<p>exit</p>
<p>exit</p>
<p>write</p>
<p>exit</p>

<p>(НА BR-R)</p>

<p>network 10.10.0.4/30 area 0</p>
<p>network 172.16.2.1 area 2</p>
<p>exit</p>
<p>exit</p>
<p>write</p>
<p>exit</p>
<p>nano /etc/sysctl.conf</p>
<p>раскоменчиваем там две команды</p>
<p>sysctl -p</p>
<p>vtysh</p>
<p>conf t</p>
<p>router ospf6</p>
<p>ospf6 router-id 0.0.0.0</p>
<p>ospf6 router-id 0.0.0.1</p>
<p>ospf6 router-id 0.0.0.2</p>
<p>ospf6 router-id 0.0.0.3</p>
<p>area 0.0.0.0 range 2001::7:5/126</p>
<p>area 0.0.0.0 range 2001::2:1/124</p>
<p>exit</p>
<p>interface ens224</p>
<p>ipv6 ospf6 area 0.0.0.0</p>
<p>exit</p>
<p>interface ens256</p>
<p>ipv6 ospf6 area 0.0.0.0</p>
<p>exit</p>
<p>exit</p>
<p>write</p>
<p>exit</p>

<p>(НА ISP)</p>

<p>network 172.16.3.1/28 area 3</p>
<p>network 10.10.0.1/30 area 0</p>
<p>network 10.10.0.3/30 area 0</p>
<p>exit</p>
<p>exit</p>
<p>write</p>
<p>exit</p>
<p>nano /etc/sysctl.conf</p>
<p>раскоменчиваем там две команды</p>
<p>sysctl -p</p>
<p>vtysh</p>
<p>conf t</p>
<p>router ospf6</p>
<p>ospf6 router-id 0.0.0.0</p>
<p>ospf6 router-id 0.0.0.1</p>
<p>ospf6 router-id 0.0.0.2</p>
<p>ospf6 router-id 0.0.0.3</p>
<p>area 0.0.0.0 range 2001::3:1/120</p>
<p>area 0.0.0.0 range 2001::7:2/126</p>
<p>area 0.0.0.0 range 2001::7:6/126</p>
<p>exit</p>
<p>interface ens224</p>
<p>ipv6 ospf6 area 0.0.0.0</p>
<p>exit</p>
<p>interface ens256</p>
<p>ipv6 ospf6 area 0.0.0.0</p>
<p>exit</p>
<p>exit</p>
<p>write</p>
<p>exit</p>

2. RADVD

<p>/etc/radvd.conf</p> 
<p>interface ens224 {</p>
<p>MinRtvAdvInterval 3; (надо смотреть по заданию)</p>
<p>MaxRtvAdvInterval 60; (надо смотреть по заданию)</p>
<p>AdvSendAdvert on;</p>
<p>};</p>
<p>выходим</p>
<p>systemctl stop radvd</p>
<p>systemctl start radvd</p>
<p>systemctl status radvd</p>

3. Пользователи

<p>adduser имя_пользователя(Admin, Branchadmin, Networkadmin)</p>
<p>Пароль: P@ssw0rd</p>
<p>(На CLI = admin)</p>
<p>(На BR-R = branchadmin, networkadmin)</p>
<p>(На HQ-R = admin, networkadmin)</p>
<p>(На BR-SRV = branchadmin, networkadmin)</p>
<p>(Ha HQ-SRV = admin)</p>
<p>visudo</p>
<p># User privilege specification</p>
<p>root ALL=(ALL:ALL) ALL</p>
<p>admin ALL=(ALL:ALL) ALL</p>

4.IPERF 3

<p>apt install iperf3</p>
<p>жмем yes</p>
<p>пишем iperf3 -c (пишем айпи машины) -i1 -t20</p>
<p>всо)</p>
