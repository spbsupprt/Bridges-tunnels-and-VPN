# Bridges-tunnels-and-VPN

Что нужно сделать?

- Настроить VPN между двумя ВМ в tun/tap режимах, замерить скорость в туннелях, сделать вывод об отличающихся показателях

- Поднять RAS на базе OpenVPN с клиентскими сертификатами, подключиться с локальной машины на ВМ

---

Дано:

![image](https://github.com/user-attachments/assets/3432dae8-03f9-429a-ac4f-44c82b37f084)


### Настроить VPN между двумя ВМ в tun/tap режимах, замерить скорость в туннелях, сделать вывод об отличающихся показателях

Применим плейбук:

https://github.com/spbsupprt/Bridges-tunnels-and-VPN/blob/main/tun.yml


![image](https://github.com/user-attachments/assets/a848aeef-4961-4e91-80a8-f2292d8f5abf)



И сделаем замер:

```
iperf3 -c 10.10.10.1 -t 40 -i 5

   client: Connecting to host 10.10.10.1, port 5201
   client: [  5] local 10.10.10.2 port 58504 connected to 10.10.10.1 port 5201
   client: [ ID] Interval           Transfer     Bitrate         Retr  Cwnd
   client: [  5]   0.00-5.00   sec  8.22 MBytes  13.8 Mbits/sec    0    408 KBytes
   client: [  5]   5.00-10.00  sec  9.94 MBytes  16.7 Mbits/sec    8    722 KBytes
   client: [  5]  10.00-15.00  sec  6.23 MBytes  10.5 Mbits/sec    0    835 KBytes
   client: [  5]  15.00-20.00  sec  9.07 MBytes  15.2 Mbits/sec    0    863 KBytes
   client: [  5]  20.00-25.00  sec  8.27 MBytes  13.9 Mbits/sec    0    983 KBytes
   client: [  5]  25.00-30.00  sec  9.91 MBytes  16.6 Mbits/sec   56    982 KBytes
   client: [  5]  30.00-35.00  sec  11.2 MBytes  18.9 Mbits/sec   15    996 KBytes
   client: [  5]  35.00-40.01  sec  12.5 MBytes  20.9 Mbits/sec   11    784 KBytes
   client: - - - - - - - - - - - - - - - - - - - - - - - - -
   client: [ ID] Interval           Transfer     Bitrate         Retr
   client: [  5]   0.00-40.01  sec  75.4 MBytes  15.8 Mbits/sec   90             sender
   client: [  5]   0.00-40.52  sec  73.6 MBytes  15.2 Mbits/sec                  receiver
   client: 
    client: iperf Done.
```


Применим плейбук:

https://github.com/spbsupprt/Bridges-tunnels-and-VPN/blob/main/tap.yml


![image](https://github.com/user-attachments/assets/db5cec74-8766-4e30-879e-2bfcb517c74b)

И сделаем замер:

```
iperf3 -c 10.10.10.1 -t 40 -i 5

    client: Connecting to host 10.10.10.1, port 5201
    client: [  5] local 10.10.10.2 port 57186 connected to 10.10.10.1 port 5201
    client: [ ID] Interval           Transfer     Bitrate         Retr  Cwnd
    client: [  5]   0.00-5.00   sec  7.15 MBytes  12.0 Mbits/sec    0    345 KBytes
    client: [  5]   5.00-10.01  sec  9.23 MBytes  15.5 Mbits/sec   28    492 KBytes
    client: [  5]  10.01-15.00  sec  6.50 MBytes  10.9 Mbits/sec   12    464 KBytes
    client: [  5]  15.00-20.00  sec  5.57 MBytes  9.35 Mbits/sec    0    563 KBytes
    client: [  5]  20.00-25.01  sec  8.05 MBytes  13.5 Mbits/sec    0    597 KBytes
    client: [  5]  25.01-30.00  sec  8.05 MBytes  13.5 Mbits/sec   13    603 KBytes
    client: [  5]  30.00-35.01  sec  10.7 MBytes  17.8 Mbits/sec    5    538 KBytes
    client: [  5]  35.01-40.00  sec  10.5 MBytes  17.6 Mbits/sec    0    559 KBytes
    client: - - - - - - - - - - - - - - - - - - - - - - - - -
    client: [ ID] Interval           Transfer     Bitrate         Retr
    client: [  5]   0.00-40.00  sec  65.7 MBytes  13.8 Mbits/sec   58             sender
    client: [  5]   0.00-40.22  sec  64.8 MBytes  13.5 Mbits/sec                  receiver
    client: 
    client: iperf Done.
```

Анализ результатов

Параметр:

TUN (L3)	Скорость	15.2 Mbps

TAP (L2) Скорость	13.5 Mbps



Выводы:
- TUN быстрее на 8-10% из-за:

Меньших накладных расходов (работает на уровне IP)

Отсутствия обработки Ethernet-заголовков

- TAP требует больше CPU:

Эмуляция Ethernet-фреймов

Обработка ARP-запросов

- Когда использовать:

TUN: VPN для интернета, маршрутизация

TAP: Мостовые подключения, L2-туннели
