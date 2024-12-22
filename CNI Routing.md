	
Routing On-Stick

![[Pasted image 20241222162052.png]]

ก็ขั้นแรก เรามาทำการเตรียม Vlan กับ Port Switch กันก่อนนะ

## Switch 1

```
Switch(config)# vlan Y1 
Switch(config-vlan)# name VLAN_Y1 
Switch(config-vlan)# exit 
Switch(config)# vlan Y2 
Switch(config-vlan)# name VLAN_Y2 
Switch(config-vlan)# exit 
Switch(config)# interface f0/1 
Switch(config-if)# switchport mode access 
Switch(config-if)# switchport access vlan Y1 
Switch(config-if)# exit Switch(config)#interface f0/2 
Switch(config-if)# switchport mode access 
Switch(config-if)# switchport access vlan Y2 
Switch(config-if)# exit 
Switch(config)# interface f0/3 
Switch(config-if)# switchport mode trunk 
Switch(config-if)# switchport trunk allowed vlan Y1,Y2
Switch(config)# interface f0/4 
Switch(config-if)# switchport mode trunk 
Switch(config-if)# switchport trunk allowed vlan Y1,Y2
Switch(config-if)# exit Switch1(config)#interface f0/24 
Switch1(config-if)# switchport mode trunk 
Switch1(config-if)# switchport trunk allowed vlan Y1,Y2 
Switch1(config-if)# exit
```

Router กับ Switch เป็น Trunk ด้วยน้าาา

ต่อมามา Config ที่ Router กัน

```
Router(config)#interface f0/0 
Router(config-if)#no shutdown // เปิดใช้งาน Interface หลักก่อน 
Router(config-if)#exit 
Router(config)#interface f0/0.Y1 // สร้าง Subinterface สำหรับ VLAN Y1 
Router(config-subif)#encapsulation dot1Q Y1 // กำหนด VLAN ID 
Router(config-subif)#ip address 192.168.Y1.1 255.255.255.0 // กำหนด IP Address    
Router(config-subif)#exit Router(config)#interface f0/0.Y2 // สร้าง Subinterface สำหรับ VLAN Y2 Router(config-subif)#encapsulation dot1Q Y2 // กำหนด VLAN ID 
Router(config-subif)#ip address 192.168.Y2.1 255.255.255.0 // กำหนด IP Address 
Router(config-subif)#exit
```

หมายเหตุ
- Vlan กับ Sub-interface ควรจะเป็น ID 

- encapsulation dot1Q Y1 หมายถึง Subinterface นี้จะรับส่ง Traffic ของ VLAN Y1
    
- IP Address ของ Subinterface จะเป็น Default Gateway ของ PC ใน VLAN นั้นๆ