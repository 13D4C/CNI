![image](https://github.com/user-attachments/assets/0376cf79-3628-4a90-aebf-dc6fcb24b7aa)

Ipv4 ![image](https://github.com/user-attachments/assets/93dce1bd-385d-451c-9d57-7a01e7070cf4)

มาตั้ง IPv4 ให้แต่ล่ะอุปกรณ์กัน

สำหรับให้ Ubuntu กัน

 ```bash
 sudo ip addr add 172.51.216.65/29 dev ens2
 ```

ถ้าตั้งผิดจะลบยังไงอ่ะ

```bash
sudo ip addr del 172.51.216.66/29 dev ens2
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

ต่อมา มาตั้งให้ Ubuntu 2 รับ dhcp
```bash
sudo vim /etc/netplan/50-cloud-init.yaml
แล้วปรับให้มันเป็น dhcp: true
sudo netplan apply
 ```
IPv6 ![image](https://github.com/user-attachments/assets/e78a762a-8dff-48e8-a7d7-c62eafea1cb0)


