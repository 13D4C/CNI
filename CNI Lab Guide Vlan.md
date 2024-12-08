
# VLAN
	VLAN ย่อมาจาก Virtual Local Area Network หรือเครือข่ายเสมือน มันเป็นวิธีการแบ่งเครือข่ายทางกายภาพ (physical network) ออกเป็นเครือข่ายย่อย (subnetworks) หรือเครือข่ายเสมือน (virtual networks) หลาย ๆ เครือข่าย ซึ่งอุปกรณ์ในแต่ละ VLAN จะสามารถสื่อสารกันได้เสมือนว่าเชื่อมต่อกันบนเครือข่ายเดียวกัน แต่จริงๆ แล้วอาจเชื่อมต่อผ่านสวิตช์ (switch) หลายตัวหรืออยู่คนละตำแหน่งทางกายภาพกันก็ได้

## **วิธีการทำงานของ VLAN:**
	VLAN ทำงานโดยการใช้ "แท็ก" (tags) หรือ "ID" เพื่อระบุว่าเฟรม (frame) ของข้อมูลนั้นอยู่ใน VLAN ใด สวิตช์ (switch) ที่รองรับ VLAN จะอ่านแท็กเหล่านี้และส่งต่อเฟรมไปยังพอร์ตที่ถูกต้องตาม VLAN ID ที่ระบุ


## Config Vlan

	ตัวอย่างโจทย์ที่ 1 : การทำ Vlan เบื้องต้น 2 วง

![[Pasted image 20241209001952.png]]

จากรูปให้เราทำ 2 Vlan โดยเราจะไป Config ที่ Switch ก่อนนะ

เราสามารถดู Vlan ของ Switch ได้โดยการใช้คำสั่ง

```
show vlan
```
![[Pasted image 20241209002458.png]]

**เรามาทำ Vlan ที่ 1 กับ 2 กัน**
```
#เข้าสู่ Privileged EXEC Mode
en 
Cont f
```

**สร้าง VLAN**
```
vlan 10 #กรณีนี้คือต้องการสร้าง Vlan no.10

name Area1 #ตั้งชื่อ Vlan

exit 

vlan 20 #กรณีนี้คือต้องการสร้าง Vlan no.20

name Area2  #ตั้งชื่อ Vlan

exit
```

พอผมมาลองตรวจสอบ Vlan ก็จะถูกสร้างแล้ว

![[Pasted image 20241209002827.png]]

**ต่อมากำหนด Port ให้กับ VLAN**

```
interface FastEthernet0/1 #เลือก Port
switchport mode access #เลือก Mode 
switchport access vlan 10 #เลือก Vlan no.

interface FastEthernet0/2 #เลือก Port
switchport mode access #เลือก Mode 
switchport access vlan 10 #เลือก Vlan no.

interface FastEthernet0/3 #เลือก Port
switchport mode access #เลือก Mode 
switchport access vlan 20 #เลือก Vlan no.

interface FastEthernet0/4 #เลือก Port
switchport mode access #เลือก Mode 
switchport access vlan 20 #เลือก Vlan no.
```

พอผมมาลองตรวจสอบ Vlan ก็จะพบว่า Port ถูกเชื่อม

![[Pasted image 20241209003735.png]]

ทีนี้ Device ที่อยู่คนล่ะ Vlan ก็จะคุยกันไม่ได้แล้ว แต่ๆๆๆถ้าผมอยากให้มันคุยด้วยกันได้ล่ะ จำทำยังไงดี

**ก็ใช้ Router มาช่วย**

![[Pasted image 20241209005323.png]]

แบบนี้นะ

**ทำการ Config ที่ Switch ก่อนนะ**

```
interface FastEthernet0/24 # Port ที่เราจะใช้เชื่อมกับ Router(Port ของ Switch นะ)
switchport mode trunk
```

**จากนั้นไป Config ที่ Router**

```
interface GigabitEthernet0/0 # Port ที่เราใช้เชื่อมกับ Switch (Port ของ Router นะ)
no shutdown

interface GigabitEthernet0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0 #gateway ที่ใช้

interface GigabitEthernet0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0 #gateway ที่ใช้

```

จากนั้นก็ Ping ติดกันแล้ว (อย่าลืมตั้ง Gateway ที่ Device ด้วยนะ)