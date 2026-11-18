# ตัวอย่าง ASCII

ภาพนิ่งสำหรับเริ่มจัดหน้าจอและออกแบบ sprite เปิดด้วยฟอนต์ monospace และปิด word wrap ภาพเกมจริงต้องประกอบจากสถานะด่านและผู้เล่น

| ไฟล์ | ขนาด | เนื้อหา |
|---|---|---|
| [มือเปล่า](gameplay-unarmed-120x40.txt) | 120×40 | ไม่มีดาบ ATK 2, DEF 0, Goblin HP 12/12 |
| [สวมอุปกรณ์](gameplay-equipped-120x40.txt) | 120×40 | ATK 7, DEF 2, Goblin HP 5/12 หลังตีหนึ่งครั้ง |
| [โหมดละเอียด](gameplay-equipped-160x50.txt) | 160×50 | การจัดพื้นที่เมื่อเพิ่มคอลัมน์และแถว |
| [Goblin](enemy-goblin-24x18.txt) | 24×18 | sprite มอน |
| [Sword](weapon-sword-18x20.txt) | 18×20 | ดาบด้านหน้า |
| [Door](door-22x16.txt) | 22×16 | ประตู |

ขนาดเป็นคอลัมน์ × แถว ใช้ printable ASCII และ space ทุกบรรทัดกว้างเท่ากัน ช่องว่างท้ายบรรทัดเป็นส่วนของขนาดภาพ ห้าม trim ออก

กติกาค่าพลังดู [GAME_DESIGN](../GAME_DESIGN.md) การวางภาพและกรอบดู [ART_GUIDE](../ART_GUIDE.md)
