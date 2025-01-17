![image](https://github.com/user-attachments/assets/0376cf79-3628-4a90-aebf-dc6fcb24b7aa)
Ipv4 ![image](https://github.com/user-attachments/assets/93dce1bd-385d-451c-9d57-7a01e7070cf4)
**การกำหนดค่า IPv4**

1.  **การกำหนดค่า IPv4 บน Ubuntu (วิธีที่ 1 - คำสั่ง)**

    ```bash
    # กำหนด IP address และ subnet mask ให้กับ interface ens2
    sudo ip addr add 171.51.216.66/29 dev ens2

    # กำหนด default gateway สำหรับ interface ens2
    sudo ip route add default via 171.51.216.65 dev ens2
    ```
    *   `171.51.216.66/29`: IP address และ subnet mask สำหรับเครื่อง Ubuntu
    *   `ens2`: ชื่อ interface ของเครือข่าย
    *   `171.51.216.65`: IP address ของ default gateway

2.  **การกำหนดค่า IPv4 บน Ubuntu (วิธีที่ 2 - Netplan ควรใช้วิธีนี้เพราะมันจะถาวร)**

    ```yaml
    network:
      version: 2
      ethernets:
        ens2:
          addresses:
            - 172.51.216.66/29  # IP address และ subnet mask
          routes:
            - to: default        # กำหนด default gateway
              via: 172.51.216.65 # IP address ของ default gateway
          nameservers:
            addresses:
              - 172.51.216.65    # IP address ของ nameserver
    ```
    *   Netplan เป็นเครื่องมือสำหรับการจัดการเครือข่ายใน Ubuntu
    *   ไฟล์นี้จะถูกบันทึกใน `/etc/netplan/`
    *   ****NOTE****
    *   1.) sudo nano /etc/netplan/[file .yaml]
    *   2.) configure ตามภาพ
    *   3.) หลัง configure ให้ sudo netplan apply เพื่อให้เอาที่ configure มาปรับใช้ 

3.  **การลบ IP address ที่กำหนดไว้ (กรณีตั้งค่าผิดพลาด)**

    ```bash
    sudo ip addr del 171.51.216.66/29 dev ens2
    ```

**การตั้งค่า VLAN และ Switch**

1.  **การสร้าง VLAN และกำหนด IP address บน Switch**

    ```bash
    interface vlan 2244
    ip address 172.51.216.70 255.255.255.248
    exit
    ip default-gateway 172.51.216.65
    ```
    *   `vlan 2244`: สร้าง VLAN ID 2244
    *   `172.51.216.70/29`: IP address ของ VLAN interface
    *   `172.51.216.65`: IP address ของ default gateway

2.  **การตั้งค่า Port ของ Switch**

    ```bash
    interface <ชื่อ Interface>
      no shutdown
      switchport mode access            # ตั้งค่า interface เป็น access port
      switchport access vlan <ID>      # กำหนด VLAN ที่ interface จะเข้าถึง
    interface <ชื่อ Interface ที่เชื่อมต่อกับ Router>
      switchport mode trunk             # ตั้งค่า interface เป็น trunk port
      switchport trunk encapsulation dot1Q  # กำหนด encapsulation เป็น dot1Q
    no ip routing
    ```
    *   `access port`: อนุญาตเฉพาะ traffic จาก VLAN เดียว
    *   `trunk port`: อนุญาต traffic จากหลาย VLAN

**การตั้งค่า Router (Cisco)**

1.  **การสร้าง Sub-interface บน Router**

    ```bash
    en
    conf t

    interface e0/0.2244              # สร้าง sub-interface สำหรับ VLAN 2244
      encapsulation dot1Q 2244       # กำหนด encapsulation เป็น dot1Q และ VLAN ID
      ip address 172.51.216.66 255.255.255.248 # กำหนด IP address
      no shut                       # เปิดใช้งาน interface
      exit

    interface e0/0.2751              # สร้าง sub-interface สำหรับ VLAN 2751
      encapsulation dot1Q 2751
      ip address 172.51.216.97 255.255.255.224
      no shut
      exit

    interface e0/1                  # สร้าง interface สำหรับเครือข่าย 10.30.6.0/24
      ip address 10.30.6.223 255.255.255.0
      no shut
      exit

    ip default-gateway 10.30.6.1
    ```
    *   `e0/0.2244`, `e0/0.2751`: Sub-interfaces สำหรับ VLAN
    *   `e0/1`: Interface สำหรับเครือข่าย 10.30.6.0/24

2.  **การตั้งค่า DHCP Pool**

    ```bash
    ip dhcp pool ITKMITL-1            # สร้าง DHCP pool ชื่อ ITKMITL-1
      network 172.51.216.96 255.255.255.224 # กำหนด network range
      default-router 172.51.216.97    # กำหนด default router
      dns-server 172.51.216.97        # กำหนด DNS server
      ip dhcp excluded-address 172.51.216.96 172.51.216.100
    ```

**การตั้งค่า ACL และ NAT**

1.  **การกำหนดค่า ACL (Access Control List)**

    ```bash
    en
    conf t
    access-list 1 permit 172.51.216.0 0.0.0.255  # อนุญาต traffic จาก network 172.51.216.0/24
    ```
2.  **การกำหนดค่า NAT (Network Address Translation)**

    ```bash
    # กำหนด interface เป็น outside (สำหรับ NAT)
    interface <ขาออก>
    ip nat outside
    # กำหนด interface เป็น inside (สำหรับ NAT)
    interface <ขาเข้า>
    ip nat inside
    # ทำ NAT โดยใช้ access-list และ overload (PAT)
    ทำที่ global config
    ip nat inside source list 1 int <ขาออก> overload
    ```
    *   `overload`: ใช้ Port Address Translation (PAT) เพื่อแปลง private IP เป็น public IP

3.  **การกำหนดค่า Route**

    ```bash
    ip route 0.0.0.0 0.0.0.0 <next hop>  # กำหนด default route
    ```

**การตั้งค่า Routing**

```bash
ip route 10.30.6.0 255.255.255.0 10.30.6.1      # กำหนด static route สำหรับเครือข่าย 10.30.6.0/24
ip route 172.17.0.0 255.255.0.0 10.30.6.1
ipv6 route 2001:db8:bacd::/64 2001:db8:dada:aaaa::1000 # กำหนด static route สำหรับ IPv6
```

**การรับ DHCP บน Ubuntu 2**

```bash
sudo vim /etc/netplan/50-cloud-init.yaml
# แก้ไขไฟล์ให้ dhcp4: true
network:
  version: 2
  ethernets:
    ens2:
      dhcp4: true
      # เพิ่ม dhcp6: true หากต้องการรับ ipv6
      # dhcp6: true
sudo netplan apply
```
*  `dhcp4: true`: กำหนดให้ Ubuntu รับ IP address จาก DHCP server

IPv6 ![image](https://github.com/user-attachments/assets/e78a762a-8dff-48e8-a7d7-c62eafea1cb0)
**การกำหนดค่า IPv6**

1.  **การกำหนดค่า IPv6 บน Ubuntu (Netplan)**

    ```yaml
    network:
      version: 2
      ethernets:
        ens2:
          addresses:
            - 172.51.216.66/29        # IPv4 address
            - 2001:51:216:2244::2/64  # IPv6 address
            - fe80::2/10            # Link-local address
          routes:
            - to: default
              via: 172.51.216.65      # IPv4 default gateway
            - to: "::/0"
              via: fe80::1            # IPv6 default gateway (link-local address)
          nameservers:
            addresses:
              - 172.51.216.65        # IPv4 nameserver
              - fe80::1            # IPv6 nameserver (link-local address)
    ```
2.  **การกำหนดค่า IPv6 DHCP บน Ubuntu 2 (Netplan)**

    ```yaml
    network:
      version: 2
      ethernets:
        ens2:
          dhcp4: true
          dhcp6: true
          ipv6-address-generation: eui64 # สร้าง IPv6 Address จาก MAC Address
    ```
3.  **การกำหนดค่า IPv6 บน Router (Cisco)**
    ```bash
    # ไปที่ interface
    interface <ชื่อ Interface>
        ipv6 address <ipv6>/64       # กำหนด IPv6 Address
        ipv6 address <link local> link-local # กำหนด link-local address
        exit
    # เปิด IPv6 Unicast routing
    ipv6 unicast-routing
    ```
### Part : Service
    DNS Router : 
        Router
        en
        conf t
        ip dns server
        ip domain lookup
        ip dns primary itkmitl.lab soa ns1.itkmitl.lab <อะไรก็ได้แนะนำชื่อ Router>
        ไปในขา Router ก่อน
        ip host SW.itkmitl.lab <IP>
        ip name server 8.8.8.8

Part : ACL
 
