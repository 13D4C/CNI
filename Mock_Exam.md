![image](https://github.com/user-attachments/assets/0376cf79-3628-4a90-aebf-dc6fcb24b7aa)

Ipv4 ![image](https://github.com/user-attachments/assets/93dce1bd-385d-451c-9d57-7a01e7070cf4)

มาตั้ง IPv4 ให้แต่ล่ะอุปกรณ์กัน

สำหรับให้ Ubuntu กัน

 ```bash
 sudo ip addr add 171.51.216.65/29 dev ens2
 ```

ถ้าตั้งผิดจะลบยังไงอ่ะ

```bash
sudo ip addr del 171.51.216.66/29 dev ens2
 ```

add IPv4 Vlan

```bash
interface vlan 2244
ip address 172.51.216.70 255.255.255.248
exit
ip default-gateway 172.51.216.65
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
 ```

IPv6 ![image](https://github.com/user-attachments/assets/e78a762a-8dff-48e8-a7d7-c62eafea1cb0)


