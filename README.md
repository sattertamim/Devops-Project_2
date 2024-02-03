# Devops-Project_2
tamimrimon@Ubuntu:~$ sudo ip link

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

tamimrimon@Ubuntu:~$ sudo ip netns exec ns2 ip link set lo up
tamimrimon@Ubuntu:~$ sudo ip netns exec ns2 ip link 

tamimrimon@Ubuntu:~$ sudo ip link add br0 type bridge
tamimrimon@Ubuntu:~$ sudo ip link set br0 up
tamimrimon@Ubuntu:~$ sudo ip link

tamimrimon@Ubuntu:~$ sudo ip addr add 192.168.1.1/24 dev br0
tamimrimon@Ubuntu:~$ sudo ip addr
   
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

tamimrimon@Ubuntu:~$ sudo ip netns exec ns1 ip link set ceth0 up
tamimrimon@Ubuntu:~$ sudo ip link

tamimrimon@Ubuntu:~$ sudo ip netns exec ns1 ip link

tamimrimon@Ubuntu:~$ sudo ip link add veth1 type veth peer name ceth1
tamimrimon@Ubuntu:~$ sudo ip link set veth1 master br0
tamimrimon@Ubuntu:~$ sudo ip link set veth1 up
tamimrimon@Ubuntu:~$ sudo ip link set ceth1 netns ns2
tamimrimon@Ubuntu:~$ sudo ip netns exec ns2 ip link set ceth1 up
tamimrimon@Ubuntu:~$ sudo ip link

tamimrimon@Ubuntu:~$ sudo ip netns exec ns2 ip link

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
