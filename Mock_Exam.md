![image](https://github.com/user-attachments/assets/0376cf79-3628-4a90-aebf-dc6fcb24b7aa)

Ipv4 ![image](https://github.com/user-attachments/assets/93dce1bd-385d-451c-9d57-7a01e7070cf4)

มาตั้ง IPv4 ให้แต่ล่ะอุปกรณ์กัน

สำหรับให้ Ubuntu กัน

 ```bash
 sudo ip addr add 171.51.216.66/29 dev ens2
sudo ip route add default via 171.51.216.65 dev ens2
 ```

version netplan
 ```bash
network:
  version: 2
  ethernets:
    ens2:
      addresses:
        - 172.51.216.66/29
      routes:
        - to: default
          via: 172.51.216.65
      nameservers:
        addresses:
          - 172.51.216.65
 ```

ถ้าตั้งผิดจะลบยังไงอ่ะ

```bash
sudo ip addr del 171.51.216.66/29 dev ens2
 ```

add IPv4 Vlan และทำ switch

```bash
interface vlan 2244
ip address 172.51.216.70 255.255.255.248
exit
ip default-gateway 172.51.216.65
```

```bash
interface eแต่ล่ะขา
switchport mode access
switchport mode access vlan <ID>
intrerface ขาไป Router
switchport mode trunk
switchport mode trunk encapsulation dot1Q
```

ต่อมา Router

```bash
en
conf t
interface e0/0.2244
encapsulation dot1Q 2244
ip address 171.51.216.66 255.255.255.248
no shut
exit
interface e0/0.2751
encapsulation dot1Q 2751
ip address 172.51.216.97 255.255.255.224
no shut
exit
interface e0/1
ip address 10.30.6.223	255.255.255.0
no shut
exit
ip default-gateway 10.30.6.1
ip dhcp pool ITKMITL-1
network 172.51.216.96 255.255.255.224
default-router 172.51.216.97
dns-server 172.51.216.97
 ```

ต่อมาจะมาทำ ACL กับ Nat
```
en
conf t
access-list 1 permit 172.51.216
ขาออก
ip nat outside
ขาเข้า
ipnat inside
และ
ip nat inside source list 1 int <ขาออก> overload
แล้วก็ทำการ
ip route 0.0.0.0 0.0.0.0 <next hop>
```
ต่อไปทำ Route

```
ip route 10.30.6.0 255.255.255.0 10.30.6.1
ipv6 route 2001:db8:bacd::/64 2001:db8:dada:aaaa::1000
```

ต่อมา มาตั้งให้ Ubuntu 2 รับ dhcp
```bash
sudo vim /etc/netplan/50-cloud-init.yaml
แล้วปรับให้มันเป็น dhcp: true
sudo netplan apply
 ```
IPv6 ![image](https://github.com/user-attachments/assets/e78a762a-8dff-48e8-a7d7-c62eafea1cb0)

ตั้ง Ipv6
```bash
sudo vim /etc/netplan/50-cloud-init.yaml

  version: 2
  ethernets:
    ens2:
      addresses:
        - 172.51.216.66/29
        - 2001:51:216:2244::2/64
        - fe80::2/10
      routes:
        - to: default
          via: 172.51.216.65
        - to: "::/0"
          via: fe80::1
      nameservers:
        addresses:
          - 172.51.216.65
          - fe80::1
          - 1.1.1.1

ส่วน ubuntu 2 set แบบนี้นะ
sudo vim /etc/netplan/50-cloud-init.yaml

network:
  version: 2
  ethernets:
    ens2:
      dhcp4: true
      dhcp6: true
      ipv6-address-generation: eui64
 ```

ต่อมาตั้ง router

ไปทีล่ะ interfaceแล้ว add Ip

 ```
ipv6 address <ipv6>/64
จากนั้นก็
ipv6 address <link local> link-local
แล้วก็
Unicast-routing
 ```

ต่อมาทำ ssh ให้ Device กัน




Part ACL

