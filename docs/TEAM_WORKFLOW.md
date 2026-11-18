# การแบ่งงานทีมและ Git

## 1. เอกสารงานรายคน

| สมาชิก | Branch | งานหลัก | รายละเอียด |
|---|---|---|---|
| A (James) | `feat/world-view` | โลกเกม การเดิน ประตู กุญแจ บันได ภาพ ASCII รับคำสั่ง หน้าจอ และรวมระบบ | [งาน James](A_James.md) |
| B (Jezz) | `feat/player-combat` | Player, Enemy, Item, Weapon, Armor, Potion กระเป๋า Equip/Use และ EXP/Level | [งาน Jezz](B_Jezz.md) |

James ดูแล Main, Game, Dungeon, Door, Key, AsciiRenderer และ TerminalDisplay ส่วน Jezz ดูแล Player, Enemy, Item, Weapon, Armor และ Potion

## 2. ใช้สาม branch

- `main`: เวอร์ชันรวมที่รันได้ ใช้ร่วมกัน โดย James ประสานการ merge
- `feat/world-view`: งานทั้งหมดของ James
- `feat/player-combat`: งานทั้งหมดของ Jezz

ใช้สอง branch ของสมาชิกต่อเนื่องตลอดโปรเจกต์ งานย่อยเป็น commit ภายใน branch เดิม

## 3. Flow รวมงาน

```text
                    main
              โครงเริ่มต้นที่รันได้
                 /       \
                /         \
   feat/world-view     feat/player-combat
        James                 Jezz
                \         /
                 \       /
                    main
              รวมส่วนที่พร้อมเป็นช่วง
                 /       \
        ทั้งคู่ merge origin/main
        กลับเข้า branch เดิมแล้วทำต่อ
```

1. ใช้ Main.java บนฐานเริ่มต้นที่รันได้ แล้วตกลงชื่อเมธอดที่จะเรียกกันก่อนเพิ่มระบบ
2. แต่ละคนเข้า branch ที่เตรียมจาก main แล้ว ทำงานและ commit ใน branch ของตน
3. push รอบที่พร้อม แล้วแจ้งอีกคนว่าเพิ่มอะไรและเรียกใช้อย่างไร
4. James fetch งานใหม่และรวม branch ที่พร้อมเข้า main ถ้างาน James ต้องใช้ข้อมูลใหม่ของ Jezz ให้รวม Jezz ก่อน แล้ว James นำ main เข้า branch ของตัวเองเพื่อเชื่อมให้ครบ
5. รวมงาน James ที่เชื่อมแล้ว ลองเล่นผลของรอบนั้น แล้ว push main
6. ทั้งคู่ fetch แล้ว merge origin/main กลับเข้า branch ของตน ก่อนทำรอบต่อไป

ถ้ามีปัญหาให้แก้ใน branch เจ้าของไฟล์แล้วรวมใหม่ ใช้ working directory แยกกันและ commit งานค้างก่อนสลับ branch

## 4. รอบส่งงาน

| รอบ | James | Jezz | ผลที่ต้องเห็น |
|---|---|---|---|
| 1: สำรวจ | แผนที่ทดลอง W/A/D ภาพและ STATUS | Player พิกัด/ทิศ/HP และ Item พื้นฐาน | เปิดเกมแล้วเดินสำรวจได้ |
| 2: ของและประตู | จุดเก็บของ Key/Door หน้า Inventory และภาพดาบ | กระเป๋า Weapon/Armor/Potion และ Equip/Use | เก็บของ ใช้ยา สวมของ เปิดทางได้ |
| 3: ต่อสู้ | เชื่อม W กับการตีและ combat log | Enemy ดาเมจ เกราะ HP และ EXP/Level | ตีแล้วอยู่ที่เดิม มอนตายไม่สวน และ Level ไม่ Heal |
| 4: เกมครบ | สองชั้น บันได จุดจบ เริ่มใหม่ และอาร์ตทั้งหมด | ปรับค่าพลังและแก้บั๊กข้อมูล/กติกา | เล่นตั้งแต่เริ่มจนชนะหรือแพ้ได้ |

## 5. จุดเชื่อม

- Jezz ทำ Player/Item พื้นฐานให้ James ใช้ก่อน ไม่ต้องรอระบบต่อสู้ทั้งหมดเสร็จ
- Game ของ James เลือกว่า W ต้องเดินหรือตี ส่วน Player/Enemy ของ Jezz คำนวณการโจมตีและเปลี่ยน HP
- Inventory UI ของ James เรียก Equip/Use ของ Jezz ไม่บวกโบนัสหรือแก้ HP อีกชุดในหน้าจอ
- Key ของ James สืบทอด Item ของ Jezz การจับคู่ประตูอยู่ใน Door ส่วนกระเป๋ารองรับ Item ทั่วไป
- ถ้าต้องเปลี่ยนชื่อเมธอดหรือข้อมูลที่อีกคนใช้อยู่ ให้ตกลงก่อนแล้วปรับส่วนเชื่อมให้ครบ

คำสั่ง Git และรายละเอียดเมธอดของแต่ละคนอยู่ใน A_James.md และ B_Jezz.md กติกาที่ยังต้องเลือกให้ยึด [GAME_DESIGN](GAME_DESIGN.md)
