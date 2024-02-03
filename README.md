# Devops-Project_2
tamimrimon@Ubuntu:~$ sudo ip link
[sudo] password for tamimrimon: 
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 08:00:27:bd:d2:82 brd ff:ff:ff:ff:ff:ff
tamimrimon@Ubuntu:~$ sudo route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.0.2.2        0.0.0.0         UG    100    0        0 enp0s3
10.0.2.0        0.0.0.0         255.255.255.0   U     100    0        0 enp0s3
169.254.0.0     0.0.0.0         255.255.0.0     U     1000   0        0 enp0s3
tamimrimon@Ubuntu:~$ sudo ip netns add ns1
tamimrimon@Ubuntu:~$ sudo ip netns add ns2
tamimrimon@Ubuntu:~$ sudo ip netns list
ns2
ns1
tamimrimon@Ubuntu:~$ sudo ls /var/run/netns/
ns1  ns2
tamimrimon@Ubuntu:~$ sudo ip netns exec ns1 ip link set lo up
tamimrimon@Ubuntu:~$ sudo ip netns exec ns1 ip link 
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
tamimrimon@Ubuntu:~$ sudo ip netns exec ns2 ip link set lo up
tamimrimon@Ubuntu:~$ sudo ip netns exec ns2 ip link 
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
tamimrimon@Ubuntu:~$ sudo ip link add br0 type bridge
tamimrimon@Ubuntu:~$ sudo ip link set br0 up
tamimrimon@Ubuntu:~$ sudo ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 08:00:27:bd:d2:82 brd ff:ff:ff:ff:ff:ff
3: br0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN mode DEFAULT group default qlen 1000
    link/ether c2:ce:2f:39:d5:ec brd ff:ff:ff:ff:ff:ff
tamimrimon@Ubuntu:~$ sudo ip addr add 192.168.1.1/24 dev br0
tamimrimon@Ubuntu:~$ sudo ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:bd:d2:82 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic noprefixroute enp0s3
       valid_lft 86040sec preferred_lft 86040sec
    inet6 fe80::d14e:a2a2:57aa:2011/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
3: br0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    link/ether c2:ce:2f:39:d5:ec brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.1/24 scope global br0
       valid_lft forever preferred_lft forever
tamimrimon@Ubuntu:~$ ping -c 2 192.168.1.1
PING 192.168.1.1 (192.168.1.1) 56(84) bytes of data.
64 bytes from 192.168.1.1: icmp_seq=1 ttl=64 time=0.076 ms
64 bytes from 192.168.1.1: icmp_seq=2 ttl=64 time=0.054 ms

--- 192.168.1.1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1007ms
rtt min/avg/max/mdev = 0.054/0.065/0.076/0.011 ms
tamimrimon@Ubuntu:~$ sudo ip link add veth0 type veth peer name ceth0
tamimrimon@Ubuntu:~$ sudo ip link set veth0 master br0
tamimrimon@Ubuntu:~$ sudo ip link set veth0 up
tamimrimon@Ubuntu:~$ sudo ip link set ceth0 netns ns1
tamimrimon@Ubuntu:~$ sudo ip netns exec ns1 ip link ceth0 up
Command "ceth0" is unknown, try "ip link help".
tamimrimon@Ubuntu:~$ sudo ip netns exec ns1 ip link set ceth0 up
tamimrimon@Ubuntu:~$ sudo ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 08:00:27:bd:d2:82 brd ff:ff:ff:ff:ff:ff
3: br0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default qlen 1000
    link/ether c2:ce:2f:39:d5:ec brd ff:ff:ff:ff:ff:ff
5: veth0@if4: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master br0 state UP mode DEFAULT group default qlen 1000
    link/ether 0a:62:fd:1c:68:a1 brd ff:ff:ff:ff:ff:ff link-netns ns1
tamimrimon@Ubuntu:~$ sudo ip netns exec ns1 ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
4: ceth0@if5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default qlen 1000
    link/ether ba:74:a4:5f:65:0d brd ff:ff:ff:ff:ff:ff link-netnsid 0
tamimrimon@Ubuntu:~$ sudo ip link add veth1 type veth peer name ceth1
tamimrimon@Ubuntu:~$ sudo ip link set veth1 master br0
tamimrimon@Ubuntu:~$ sudo ip link set veth1 up
tamimrimon@Ubuntu:~$ sudo ip link set ceth1 netns ns2
tamimrimon@Ubuntu:~$ sudo ip netns exec ns2 ip link set ceth1 up
tamimrimon@Ubuntu:~$ sudo ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 08:00:27:bd:d2:82 brd ff:ff:ff:ff:ff:ff
3: br0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default qlen 1000
    link/ether c2:ce:2f:39:d5:ec brd ff:ff:ff:ff:ff:ff
5: veth0@if4: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master br0 state UP mode DEFAULT group default qlen 1000
    link/ether 0a:62:fd:1c:68:a1 brd ff:ff:ff:ff:ff:ff link-netns ns1
7: veth1@if6: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master br0 state UP mode DEFAULT group default qlen 1000
    link/ether 6a:dc:92:f2:16:63 brd ff:ff:ff:ff:ff:ff link-netns ns2
tamimrimon@Ubuntu:~$ sudo ip netns exec ns2 ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
6: ceth1@if7: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default qlen 1000
    link/ether 4a:78:c6:b3:b8:96 brd ff:ff:ff:ff:ff:ff link-netnsid 0
tamimrimon@Ubuntu:~$ sudo ip netns exec ns1 ip addr add 192.168.1.10/24 dev ceth0
tamimrimon@Ubuntu:~$ sudo ip netns exec ns1 ping -c 2 192.168.1.10
PING 192.168.1.10 (192.168.1.10) 56(84) bytes of data.
64 bytes from 192.168.1.10: icmp_seq=1 ttl=64 time=0.098 ms
64 bytes from 192.168.1.10: icmp_seq=2 ttl=64 time=0.061 ms

--- 192.168.1.10 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1018ms
rtt min/avg/max/mdev = 0.061/0.079/0.098/0.018 ms
tamimrimon@Ubuntu:~$ sudo ip netns exec ns1 ip route
192.168.1.0/24 dev ceth0 proto kernel scope link src 192.168.1.10 
tamimrimon@Ubuntu:~$ sudo ip netns exec ns1 ping -c 2 192.168.1.1
PING 192.168.1.1 (192.168.1.1) 56(84) bytes of data.
64 bytes from 192.168.1.1: icmp_seq=1 ttl=64 time=0.084 ms
64 bytes from 192.168.1.1: icmp_seq=2 ttl=64 time=0.073 ms

--- 192.168.1.1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1018ms
rtt min/avg/max/mdev = 0.073/0.078/0.084/0.005 ms
tamimrimon@Ubuntu:~$ sudo ip netns exec ns2 ip addr add 192.168.1.11/24 dev ceth1
tamimrimon@Ubuntu:~$ sudo ip netns exec ns2 ping -c 2 192.168.1.11
PING 192.168.1.11 (192.168.1.11) 56(84) bytes of data.
64 bytes from 192.168.1.11: icmp_seq=1 ttl=64 time=0.037 ms
64 bytes from 192.168.1.11: icmp_seq=2 ttl=64 time=0.189 ms

--- 192.168.1.11 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1205ms
rtt min/avg/max/mdev = 0.037/0.113/0.189/0.076 ms
tamimrimon@Ubuntu:~$ sudo ip netns exec ns2 ip route
192.168.1.0/24 dev ceth1 proto kernel scope link src 192.168.1.11 
tamimrimon@Ubuntu:~$ sudo ip netns exec ns2 ping -c 2 192.168.1.1
PING 192.168.1.1 (192.168.1.1) 56(84) bytes of data.
64 bytes from 192.168.1.1: icmp_seq=1 ttl=64 time=0.156 ms
64 bytes from 192.168.1.1: icmp_seq=2 ttl=64 time=0.077 ms

--- 192.168.1.1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1147ms
rtt min/avg/max/mdev = 0.077/0.116/0.156/0.039 ms
tamimrimon@Ubuntu:~$ sudo nsenter --net=/var/run/netns/ns1
root@Ubuntu:/home/tamimrimon# ping -c 2 192.168.1.11
PING 192.168.1.11 (192.168.1.11) 56(84) bytes of data.
64 bytes from 192.168.1.11: icmp_seq=1 ttl=64 time=0.220 ms
64 bytes from 192.168.1.11: icmp_seq=2 ttl=64 time=0.086 ms

--- 192.168.1.11 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1017ms
rtt min/avg/max/mdev = 0.086/0.153/0.220/0.067 ms
