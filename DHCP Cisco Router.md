# DHCP ROUTER CISCO

**1. การตั้งค่า DHCP บน Router ตามรูป**

![image](https://github.com/user-attachments/assets/41b2019f-215e-416b-9457-6aa5039430fc)

*   **เข้าสู่โหมด config:**
    ```
    Router# en
    Router# conf t
    Router# ip dhcp pool ITKMITL-1
    ```

*   **สร้าง DHCP pool ชื่อ `ITKMITL-1` (หรือชื่ออื่นตามต้องการ):**
    ```
    Router# ip dhcp pool ITKMITL-1
    ```
*   **กำหนด network ที่จะแจก IP:**
    ```
    Router(dhcp-config)# network 192.168.3.0 255.255.255.0
    ```
*   **กำหนด default gateway:**
    ```
    Router(dhcp-config)# default-router 192.168.3.1
    ```
*   **กำหนด DNS server (ถ้ามี):**
    ```
    Router(dhcp-config)# dns-server 8.8.8.8 8.8.4.4
    ```
*   **ออกจากโหมด DHCP pool:**
    ```
    Router(dhcp-config)# exit
    ```
*   **ยกเว้น IP ที่ไม่ต้องการแจก (ถ้าต้องการ):**
    ```
    Router(config)# ip dhcp excluded-address 192.168.3.1 192.168.3.10
    ```
    (ตัวอย่างนี้ยกเว้น IP ตั้งแต่ 192.168.1.1 ถึง 192.168.1.10)
*   **บันทึกการตั้งค่า:**
    ```
    ISR4331(config)# exit
    ISR4331# write memory
    ```

# อย่าลือตั้งขา Gateway ด้วยน้าาา
