# ACL
- ACL (Access Control List) คือ รายการคำสั่งที่ใช้ในการควบคุมการไหลของทราฟฟิกเครือข่าย โดยการอนุญาตหรือปฏิเสธแพ็กเก็ตตามเงื่อนไขที่กำหนด
    
- ACL ใช้เพื่อเพิ่มความปลอดภัย, กรองทราฟฟิกที่ไม่ต้องการ, และจัดการการใช้งานแบนด์วิธ
    
- ACL แบ่งออกเป็น 2 ประเภทหลักๆ คือ:
    
    - **Standard ACL:** กรองทราฟฟิกตาม **Source IP Address** เท่านั้น (หมายเลข 1-99 และ 1300-1999)
        
    - **Extended ACL:** กรองทราฟฟิกตาม **Source IP Address, Destination IP Address, Protocol (TCP/UDP), และ Port Number** (หมายเลข 100-199 และ 2000-2699)
        
- ACL ทำงานแบบ **Top-Down** คือ อุปกรณ์จะตรวจสอบเงื่อนไขใน ACL ทีละบรรทัดจากบนลงล่าง หากตรงกับเงื่อนไขใดก็จะดำเนินการตามคำสั่งในบรรทัดนั้น (permit/deny) ทันที และจะไม่ตรวจสอบบรรทัดถัดไป

คำสั่งที่ควรจะรุ้ของ ACL คือ
	Deny = ห้าม
	Permit = อนุญาต


**คำสั่งพื้นฐานในการสร้าง ACL**

1. **เข้าสู่โหมด Global Configuration:**

    ```
    Router> enable
    Router# configure terminal
    ```

2. **สร้าง Standard ACL:**

    ```
    Router(config)# access-list <access-list-number> {permit | deny} <source> [source-wildcard]
    ```

    *   `access-list-number`:  ตัวเลข ACL (1-99 หรือ 1300-1999)
    *   `permit | deny`:  อนุญาตหรือปฏิเสธ
    *   `source`:  ที่อยู่ IP ต้นทาง
    *   `source-wildcard`:  Wildcard mask (optional)

3. **สร้าง Extended ACL:**

    ```
    Router(config)# access-list <access-list-number> {permit | deny} <protocol> <source> [source-wildcard] <destination> [destination-wildcard] [operator] [port]
    ```

    *   `access-list-number`:  ตัวเลข ACL (100-199 หรือ 2000-2699)
    *   `permit | deny`:  อนุญาตหรือปฏิเสธ
    *   `protocol`:  โปรโตคอล (เช่น tcp, udp, ip)
    *   `source`:  ที่อยู่ IP ต้นทาง
    *   `source-wildcard`:  Wildcard mask (optional)
    *   `destination`:  ที่อยู่ IP ปลายทาง
    *   `destination-wildcard`:  Wildcard mask (optional)
    *   `operator`:  ตัวดำเนินการ (เช่น eq, gt, lt, neq) (optional)
    *   `port`:  หมายเลขพอร์ต (optional)

**Wildcard Mask คืออะไร?**

Wildcard mask ใช้เพื่อระบุช่วงของ IP addresses  มันทำงานตรงกันข้ามกับ subnet mask:

*   `0` หมายถึง "ตรวจสอบบิตนี้"
*   `255` หมายถึง "ไม่ต้องสนใจบิตนี้"

**ตัวอย่าง:**

*   `0.0.0.0`  ตรงกับ IP address ที่ระบุเท่านั้น
*   `0.0.0.255`  ตรงกับ IP addresses ใดๆ ใน 8 บิตสุดท้าย
*   `0.0.255.255` ตรงกับ IP addresses ใดๆ ใน 16 บิตสุดท้าย
*   `255.255.255.255` หรือ `any`  ตรงกับ IP address ใดๆ

**การนำ ACL ไปใช้กับ Interface**

1. **เข้าสู่โหมด Interface Configuration:**

    ```
    Router(config)# interface <interface-type> <interface-number>
    ```

2. **ใช้คำสั่ง `ip access-group`:**

    ```
    Router(config-if)# ip access-group <access-list-number> {in | out}
    ```

    *   `access-list-number`:  ตัวเลข ACL
    *   `in`:  ใช้กับทราฟฟิกขาเข้า
    *   `out`:  ใช้กับทราฟฟิกขาออก

**ตัวอย่างการใช้งาน**

**ตัวอย่างที่ 1:  บล็อกการเข้าถึง telnet จากเครือข่าย 192.168.1.0/24 ไปยังเซิร์ฟเวอร์ 10.1.1.1**

```
Router(config)# access-list 101 deny tcp 192.168.1.0 0.0.0.255 host 10.1.1.1 eq 23
Router(config)# access-list 101 permit ip any any
Router(config)# interface fastEthernet 0/0
Router(config-if)# ip access-group 101 out
```

**ตัวอย่างที่ 2:  อนุญาตเฉพาะ HTTP (port 80) และ HTTPS (port 443) จากเครือข่ายใดๆ ไปยังเว็บเซิร์ฟเวอร์ 172.16.1.100**

```
Router(config)# access-list 102 permit tcp any host 172.16.1.100 eq 80
Router(config)# access-list 102 permit tcp any host 172.16.1.100 eq 443
Router(config)# access-list 102 deny ip any any
Router(config)# interface GigabitEthernet 0/1
Router(config-if)# ip access-group 102 in
```

**ตัวอย่างที่ 3:  บล็อกโฮสต์ 192.168.1.10 ไม่ให้เข้าถึงเครือข่าย**

```
Router(config)# access-list 1 deny host 192.168.1.10
Router(config)# access-list 1 permit any
Router(config)# interface fastEthernet 0/0
Router(config-if)# ip access-group 1 in
```

**ข้อควรจำ:**

*   ACL จะถูกประมวลผลตามลำดับจากบนลงล่าง
*   มี `implicit deny` (ปฏิเสธโดยปริยาย) ที่ส่วนท้ายของ ACL ทุกรายการ ดังนั้นหากไม่มีกฎใดตรงกัน ทราฟฟิกจะถูกปฏิเสธ
*   ควรวางกฎที่เฉพาะเจาะจงไว้ด้านบนของ ACL และกฎทั่วไปไว้ด้านล่าง
*   สามารถใช้คำสั่ง `show access-lists` เพื่อดูรายการ ACL ที่กำหนดค่าไว้
