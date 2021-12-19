## 1 Подключитесь к публичному маршрутизатору в интернет. Найдите маршрут к вашему публичному IP

```
route-views>show ip route 188.143.142.181 255.255.255.0
Routing entry for 188.143.142.0/24
  Known via "bgp 6447", distance 20, metric 0
  Tag 6939, type external
  Last update from 64.71.137.241 04:13:33 ago
  Routing Descriptor Blocks:
  * 64.71.137.241, from 64.71.137.241, 04:13:33 ago
      Route metric is 0, traffic share count is 1
      AS Hops 2
      Route tag 6939
      MPLS label: none
			
BGP routing table entry for 188.143.142.0/24, version 1828902429
Paths: (4 available, best #4, table default)
  Not advertised to any peer
  Refresh Epoch 1
  1351 6939 44050
    132.198.255.253 from 132.198.255.253 (132.198.255.253)
      Origin IGP, localpref 100, valid, external
      path 7FE13D97DA88 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  20130 6939 44050
    140.192.8.16 from 140.192.8.16 (140.192.8.16)
      Origin IGP, localpref 100, valid, external
      path 7FE0D5DD4760 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 3
  3303 6939 44050
    217.192.89.50 from 217.192.89.50 (138.187.128.158)
      Origin IGP, localpref 100, valid, external
      Community: 3303:1006 3303:1021 3303:1030 3303:3067 6939:7154 6939:8233 6939:9002
      path 7FE0E1735030 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  6939 44050
    64.71.137.241 from 64.71.137.241 (216.218.252.164)
      Origin IGP, localpref 100, valid, external, best
      path 7FE0AA024898 RPKI State valid
      rx pathid: 0, tx pathid: 0x0
			
```
## 2 Создайте dummy0 интерфейс в Ubuntu. Добавьте несколько статических маршрутов. Проверьте таблицу маршрутизации.

	#modprobe -v dummy numdummies=1
	#ifconfig dummy0 up
	#ip a add 10.1.1.1/24 dev dummy0
	#ip r a 10.2.2.0/24 via 10.1.1.2
	#ip r a 10.3.3.0/24 via 10.1.1.3
	#ip r l
	default via 192.168.22.199 dev eth0 proto dhcp src 192.168.22.102 metric 100
	10.1.1.0/24 dev dummy0 proto kernel scope link src 10.1.1.1
	10.2.2.0/24 via 10.1.1.2 dev dummy0
	10.3.3.0/24 via 10.1.1.3 dev dummy0
	192.168.22.0/24 dev eth0 proto kernel scope link src 192.168.22.102
	192.168.22.199 dev eth0 proto dhcp scope link src 192.168.22.102 metric 100
	
## 3 Проверьте открытые TCP порты в Ubuntu, какие протоколы и приложения используют эти порты? Приведите несколько примеров.

```
# ss -tanp
State  Recv-Q Send-Q  Local Address:Port      Peer Address:Port  Process
LISTEN 0      4096          0.0.0.0:19999          0.0.0.0:*      users:(("netdata",pid=913,fd=4))
LISTEN 0      4096    127.0.0.53%lo:53             0.0.0.0:*      users:(("systemd-resolve",pid=752,fd=13))
LISTEN 0      128           0.0.0.0:22             0.0.0.0:*      users:(("sshd",pid=945,fd=3))
LISTEN 0      4096        127.0.0.1:8125           0.0.0.0:*      users:(("netdata",pid=913,fd=34))
ESTAB  0      36     192.168.22.102:22      192.168.22.104:62920  users:(("sshd",pid=1864,fd=4),("sshd",pid=1752,fd=4))
LISTEN 0      4096                *:9100                 *:*      users:(("node_exporter",pid=813,fd=3))
LISTEN 0      128              [::]:22                [::]:*      users:(("sshd",pid=945,fd=4))
LISTEN 0      4096            [::1]:8125              [::]:*      users:(("netdata",pid=913,fd=33))
```

На 19999 порту запущен процесс netdata

на 22 порту запущен sshd, протокол ssh

## 4 Проверьте используемые UDP сокеты в Ubuntu, какие протоколы и приложения используют эти порты?

```
# ss -uanp
State  Recv-Q Send-Q                      Local Address:Port Peer Address:PortProcess
UNCONN 0      0                               127.0.0.1:8125      0.0.0.0:*    users:(("netdata",pid=913,fd=32))
UNCONN 0      0                           127.0.0.53%lo:53        0.0.0.0:*    users:(("systemd-resolve",pid=752,fd=12))
UNCONN 0      0                     192.168.22.102%eth0:68        0.0.0.0:*    users:(("systemd-network",pid=460,fd=19))
UNCONN 0      0                                10.1.1.1:123       0.0.0.0:*    users:(("ntpd",pid=930,fd=26))
UNCONN 0      0                          192.168.22.102:123       0.0.0.0:*    users:(("ntpd",pid=930,fd=19))
UNCONN 0      0                               127.0.0.1:123       0.0.0.0:*    users:(("ntpd",pid=930,fd=18))
UNCONN 0      0                                 0.0.0.0:123       0.0.0.0:*    users:(("ntpd",pid=930,fd=17))
UNCONN 0      0                                   [::1]:8125         [::]:*    users:(("netdata",pid=913,fd=15))
UNCONN 0      0      [fe80::e4b7:b6ff:fe6e:46d2]%dummy0:123          [::]:*    users:(("ntpd",pid=930,fd=25))
UNCONN 0      0         [fe80::215:5dff:fe16:6602]%eth0:123          [::]:*    users:(("ntpd",pid=930,fd=21))
UNCONN 0      0                                   [::1]:123          [::]:*    users:(("ntpd",pid=930,fd=20))
UNCONN 0      0                                    [::]:123          [::]:*    users:(("ntpd",pid=930,fd=16))
```

Порт 123 использует ntpd
Порт 8125 использует процесс netdata

## 5 Используя diagrams.net, создайте L3 диаграмму вашей домашней сети или любой другой сети, с которой вы работали.

![image](https://user-images.githubusercontent.com/93075740/146674911-3e02d617-1b2a-4794-86a4-842f7dc03336.png)

## 6 Установите Nginx, настройте в режиме балансировщика TCP или UDP

	apt install nginx
	vi /etc/nginx/modules-available/ntp

```
	stream {
  upstream ntp_backends {
    server 10.10.10.11:123;
    server 10.10.10.12:123;
  }
  server {
    listen 10123 udp;
    proxy_pass ntp_backends;
    proxy_responses 1;
  }
}
```

	cd /etc/nginx/modules-enabled/
	ln -s /etc/nginx/modules-available/ntp 55-ntp.conf
	
## 7 Установите bird2, настройте динамический протокол маршрутизации RIP

	apt install bird2
	vi /etc/bird/bird.conf
	
```
protocol rip {
        ipv4 {
                # Export direct, static routes and ones from RIP itself
                import all;
                export all;
#               export where source ~ [ RTS_DEVICE, RTS_STATIC, RTS_RIP ];
        };
        interface "eth*";
}
```
