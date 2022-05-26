## Part 1. Инструмент **ipcalc**
- `sudo apt install ipcalc`
#### 1.1. Сети и маски

##### 1) Адрес сети 

- **192.167.38.54/13**:
  + **192.167.38.54**

##### 2) Перевод маски

- **255.255.255.0**:
  + **/24**
  + **11111111.11111111.11111111.00000000**
- 
- **/15**:
  + **255.254.0.0**
  + **11111111.11111110.00000000.00000000**
- 
- **11111111.11111111.11111111.11110000**:
  + **255.255.255.240**
  + **/28**
  
##### 3) Минимальный и максимальный хост в сети 

**12.167.38.4**:
- 
- **/8**:
  + **12.0.0.1** | **12.255.255.254**
- 
- **11111111.11111111.00000000.00000000**:
  + **12.167.0.1** | **12.167.255.254**
- 
- **255.255.254.0**:
  + **12.167.38.1** | **12.167.39.254**
- 
- **/4**:
  + **0.0.0.1** | **15.255.255.254**

#### 1.2. localhost

- **194.34.23.100/16** - <img src=../misc/images/task/1_2a.png>
- **127.0.0.2/24** - <img src=../misc/images/task/1_2b.png>
- **127.1.0.1/8** - <img src=../misc/images/task/1_2b.png>
- **128.0.0.1/8** - <img src=../misc/images/task/1_2b.png>
#### 1.3. Диапазоны и сегменты сетей
##### 1) 
- **10.0.0.45/8** - <img src=../misc/images/task/A_Private.png>
- **134.43.0.2/16** - <img src=../misc/images/task/B.png>
- **192.168.4.2/16** - <img src=../misc/images/task/C_Private.png>
- **172.20.250.4/12** - <img src=../misc/images/task/B_Private.png>
- **172.0.2.1/12** - <img src=../misc/images/task/B.png>
- **192.172.0.1/12** - <img src=../misc/images/task/C_In_Private.png>
- **172.68.0.2/12** - <img src=../misc/images/task/B.png>
- **172.16.255.255/12** - <img src=../misc/images/task/B_Private.png>
- **10.10.10.10/8** - <img src=../misc/images/task/A_Private.png>
- **192.169.168.1/16** - <img src=../misc/images/task/B.png>
##### 2) 
<img src=../misc/images/task/minmax.png>

- **10.10.0.0/18**: 
  + **10.0.0.1** - нет
  + **10.10.0.2** - да
  + **10.10.10.10** - да 
  + **10.10.100.1** - нет
  + **10.10.1.255** - нет

## Part 2. Статическая маршрутизация между двумя машинами

##### `ip a`

- <img src=../misc/images/task/ip-a.png>
##### `nano /etc/netplan/*.yaml`:

- <img src=../misc/images/task/cat_yaml.png>

  + ws1 - **192.168.100.10**, маска **/16**, ws2 - **172.24.116.8**, маска **/12**
##### `netplan apply`

- <img src=../misc/images/task/ip-r.png>

  + `ip r` - ws1, ws2

#### 2.1. Добавление статического маршрута вручную

- ws1 - `ip r add 172.24.116.8/32 via 192.168.100.10`
- ws2 - `ip r add 192.168.100.10/31 via 172.24.116.8`
- `ping 172.24.116.8`
- `ping 192.168.100.10`

  + <img src=../misc/images/task/iproadd.png>

#### 2.2. Добавление статического маршрута с сохранением

- `sudo nano etc/netplan/00-installer-config.yaml`
  + `sudo netplan apply`
  + `sudo reboot`

- <img src=../misc/images/task/cat_yaml_route.png>

  + `cat etc/netplan/00-installer-config.yaml`

- <img src=../misc/images/task/ping_route.png>

  + ws1 - `ping 172.24.116.8`
  + ws2 - `ping 192.168.100.10`

## Part 3. `sudo apt install iperf3`

#### 3.1. Скорость соединения
- ##### 8 Mbps = 1 MB/s
- ##### 100 MB/s = 800000 Kbps
- ##### 1 Gbps = 1000 Mbps

> по чек листу другие цифры - они тоже верны - здесь дело в том, что идёт разность в системе исчисления, то есть и так и так будет верно
+  > но к слову, то что написано в чек листе больше подходит для измерения частоты (например скорость оперативки)

#### 3.2. Утилита **iperf3**
##### ws1 - `sudo iperf3 -c 172.24.116.8` и ws2 - `sudo iperf3 -s`

- <img src=../misc/images/task/iperf3.png>

## Part 4. Сетевой экран

#### 4.1. `sudo apt-get install iptables`
- `sudo nano /etc/firewall.sh`

- <img src=../misc/images/task/firewall.png>
##### `sudo chmod +x /etc/firewall.sh` и `sudo /etc/firewall.sh`

- <img src=../misc/images/task/fw.png>

  + ws1 - INPUT для пакетов на ping-reply DROP - не работает т.е. пакеты отправляет, но не принемает.
  + ws2 - INPUT для пакетов на ping-reply ACCEPT - работает.

#### 4.2. `sudo snap install nmap`

> подключить *nmap* к сетевому управлению:
```shell
  sudo snap connect nmap:network-control
```

- <img src=../misc/images/task/nmap.png>

## Part 5. Статическая маршрутизация сети

#### 5.1. Настройка адресов машин

##### Настроика конфигурации

- <img src=../misc/images/task/ws11_yaml.png>

- <img src=../misc/images/task/ws21_yaml.png>

- <img src=../misc/images/task/ws22_yaml.png>

- <img src=../misc/images/task/r1_yaml.png>

- <img src=../misc/images/task/r2_yaml.png>

##### `ip -4 a`

- <img src=../misc/images/task/ws11_ip4a.png>

- <img src=../misc/images/task/ws21_ip4a.png>

- <img src=../misc/images/task/ws22_ip4a.png>

- <img src=../misc/images/task/r1_ip4a.png>

- <img src=../misc/images/task/r2_ip4a.png>

##### ping ws22 с ws21

- <img src=../misc/images/task/ws21-ws22_ping.png>

##### ping r1 с ws11

- <img src=../misc/images/task/ws11-r1_ping.png>

#### 5.2. Включение переадресации IP-адресов.

- `sudo sysctl -w net.ipv4.ip_forward=1`

- <img src=../misc/images/task/sysctl-r1r2.png>

- `sudo nano /etc/sysctl.conf`

- <img src=../misc/images/task/ipv_frwd.png>

#### 5.3. Установка маршрута по-умолчанию

##### Настроика маршрута по-умолчанию (шлюз) для рабочих станций.

- <img src=../misc/images/task/gws11.png>

- <img src=../misc/images/task/gws21.png>

- <img src=../misc/images/task/gws22.png>

##### `ip r`

- <img src=../misc/images/task/iprws11.png>

- <img src=../misc/images/task/iprws21.png>

- <img src=../misc/images/task/iprws22.png>

##### пинг ws11 - r2; `sudo tcpdump -tn -i enp0s8`

- <img src=../misc/images/task/tcpdump-r2.png>

#### 5.4. Добавление статических маршрутов

##### r1 и r2 статические маршруты. 

- <img src=../misc/images/task/stc-r1.png>

- <img src=../misc/images/task/stc-r2.png>

##### `ip r`

- <img src=../misc/images/task/iprr1.png>

- <img src=../misc/images/task/iprr2.png>

##### ws11 - `ip r list 10.10.0.0/18` и `ip r list 0.0.0.0/0`

- <img src=../misc/images/task/iprlistws11.png>
- для 10.10.0.0 выбран не 0.0.0.0/0 потому, что маршрутизатор выбирает путь с большей маской.

#### 5.5. `sudo apt install inetutils-traceroute`
##### на рабочих станциях перед установкой нужно отключать gateway4

- <img src=../misc/images/task/tr_ws11-ws21.png>
  
  + `traceroute 10.20.0.10`

- <img src=../misc/images/task/r1_tcpd.png>
  
  + r1 - `sudo tcpdump -tnv -i enp0s8`

- 1 - стучимся на r1
- 2 - переадресация направлена на r2
- 3 - А с r2 уже по указанному адресу на w22

#### 5.6. Использование протокола **ICMP** при маршрутизации

- `sudo tcpdump -n -i enp0s8 icmp`


- <img src=../misc/images/task/nping.png>
  
  + `ping -c 1 10.30.0.111`

## Part 6. Динамическая настройка IP с помощью **DHCP**

- <img src=../misc/images/task/ws11_mac.png>

- `sudo nano /etc/dhcp/dhcpd.conf`
##### 1)

- <img src=../misc/images/task/r2-dhconf.png>

- <img src=../misc/images/task/r1-dhconf.png>

##### 2)

- <img src=../misc/images/task/r2-resolv.png>

- <img src=../misc/images/task/r1-resolv.png>

##### `sudo apt install isc-dhcp-server` 
##### `sudo systemctl restart isc-dhcp-server`

- <img src=../misc/images/task/restart-dhcpserv.png>

##### ws21 - `reboot`

- <img src=../misc/images/task/ws21-ripa.png>

  + `ip a`

- <img src=../misc/images/task/ws22-ws21-ping.png>

  + `ping 10.20.0.10`

##### ws21 обновление ip адреса

- <img src=../misc/images/task/mac.png>

  + `dhclient -r`
  + `dhclient -v`

- <img src=../misc/images/task/dhclient-v.png>

## Part 7. **NAT**

##### `sudo apt install apache2` на ws22 и r1

- <img src=../misc/images/task/ports.png>

##### `sudo service apache2 start` на ws22 и r1

- <img src=../misc/images/task/ap.png>

#####  на r2 следующие правила:

- <img src=../misc/images/task/fire.png>

##### ws22 и r1 `ping`
*При запуске файла с этими правилами, ws22 не должна "пинговаться" с r1*

- <img src=../misc/images/task/noping.png>

##### Добавить в файл ещё одно правило:

- <img src=../misc/images/task/fire2.png>

##### ws22 и r1 `ping`
*При запуске файла с этими правилами, ws22 должна "пинговаться" с r1*

- <img src=../misc/images/task/yeping.png>

##### Добавить в файл ещё два правила:

- <img src=../misc/images/task/nat.png>

- <img src=../misc/images/task/tel1.png>

  + `telnet 10.100.0.12 8080`

- <img src=../misc/images/task/tel2.png>

  + `telnet 10.100.0.11 8080`

## Part 8. Дополнительно. Знакомство с **SSH Tunnels**

##### ws22 - `sudo systemctl start apache2`

- <img src=../misc/images/task/apa.png>

##### c ws21 до ws22 - `sudo ssh -L 22:localhost:80 wsybil@10.20.0.20`

- <img src=../misc/images/task/p-8connect.png>

##### c ws11 до ws22 - `sudo ssh -R 22:localhost:80 wsybil@10.20.0.20`

- <img src=../misc/images/task/p8.png>

- <img src=../misc/images/task/p-8connect2.png>
