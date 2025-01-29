![image](https://github.com/user-attachments/assets/547cc0d0-24a0-49ef-9db8-7308aaefe319)

Config OSPF
```
en
conf t
router ospf <process-ID>
network <Network address วงที่เราอยากไป> <wildcard> area <ID>
#แล้วก็ทำงี้ทุก Network ID ที่เราอยากไป ก็เสร็จแล้ว
```
