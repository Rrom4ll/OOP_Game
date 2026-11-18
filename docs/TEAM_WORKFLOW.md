# Git และการรวมงาน

## Branch

| Branch | ผู้รับผิดชอบ | หน้าที่ |
|---|---|---|
| main | James ประสานการรวม | เวอร์ชันรวมที่ compile/run ได้ |
| feat/world-view | A (James) | โลกเกม การควบคุม ภาพ หน้าจอ และเชื่อมระบบ |
| feat/player-combat | B (Jezz) | Player/Enemy/Item อุปกรณ์ ต่อสู้ และ EXP/Level |

ใช้ branch ของแต่ละคนต่อเนื่อง งานย่อยทำเป็น commit ใน branch เดิม ตั้งชื่อ/อีเมล Git ของตนเองก่อน commit และใช้โฟลเดอร์ clone แยกกัน

## เข้า branch ของตัวเอง

หลัง clone:

```bash
git fetch origin
git switch feat/world-view
git merge origin/main
```

Jezz เปลี่ยนคำสั่ง switch เป็น `git switch feat/player-combat` แล้ว merge origin/main เช่นกัน หากยังไม่มี local branch Git จะสร้างจาก remote branch ที่ชื่อเดียวกัน

## ส่งงานหนึ่งรอบ

1. ทำและลองส่วนที่รับผิดชอบใน branch ของตน
2. git add เฉพาะไฟล์ที่แก้จริง แล้ว commit/push
3. แจ้งอีกคนว่ารอบใดพร้อม เมธอดใดเพิ่มหรือเปลี่ยน และส่วนที่ลองได้
4. James รวมส่วนที่เป็นฐานก่อน แล้วรวมส่วนเชื่อมที่ใช้ฐานนั้น
5. ลองผลรวมของรอบให้รันได้ก่อน push main
6. ทั้งคู่ fetch แล้ว merge origin/main เข้า branch ของตนก่อนทำต่อ

ตัวอย่างส่งงาน:

```bash
git add src/Player.java src/Item.java src/Weapon.java src/Armor.java src/Potion.java src/Enemy.java
git commit -m "feat: add player and item foundation"
git push -u origin feat/player-combat
```

รายการไฟล์เป็นตัวอย่าง ใช้เฉพาะไฟล์ที่มีอยู่และแก้ในงานนั้น ก่อนสลับ branch ให้ commit งานค้างของตนเองก่อน

## James รวมงาน

รับฐานของ Jezz ก่อน:

```bash
git fetch origin
git switch main
git pull --ff-only origin main
git merge --no-ff origin/feat/player-combat
```

ถ้างาน James ใช้เมธอดที่เพิ่งรวม ให้นำ main เข้า branch ตัวเอง:

```bash
git switch feat/world-view
git merge main
```

เชื่อมให้ครบ ลองรัน แล้ว commit/push งาน James:

```bash
git add src/Game.java src/Dungeon.java src/Door.java src/Key.java src/AsciiRenderer.java src/TerminalDisplay.java
git commit -m "feat: connect world view and player systems"
git push origin feat/world-view
```

เปลี่ยนรายการ git add ให้ตรงกับไฟล์ที่แก้จริง จากนั้นรวมกลับ main:

```bash
git switch main
git merge --no-ff origin/feat/world-view
javac -encoding UTF-8 -d build src/*.java
java -cp build Main
git push origin main
```

หากรวมแล้วมีปัญหา ให้แก้ใน branch เจ้าของไฟล์แล้วรวมใหม่ก่อน push main

## รับ main กลับไปทำต่อ

Jezz:

```bash
git fetch origin
git switch feat/player-combat
git merge origin/main
```

James ใช้ชุดคำสั่งเดียวกันโดยเปลี่ยนเป็น `feat/world-view`

ถ้ามี conflict ใช้ git status ดูไฟล์ คุยกับเจ้าของส่วนที่ชน แก้เครื่องหมาย conflict แล้ว git add/commit และลองผลที่เกี่ยวข้อง หลีกเลี่ยงการเลือกทับทั้งไฟล์โดยไม่อ่าน ใช้โฟลเดอร์ clone แยกกันของแต่ละคน

## รอบรวมงาน

| รอบ | ผลที่รวมแล้วต้องได้ |
|---|---|
| 1 | เดินสำรวจและหัน พร้อมโครง Player/Item ที่ compile ได้ |
| 2 | เก็บของ Inventory Equip/Use และประตู |
| 3 | ต่อสู้ เกราะ EXP/Level แพ้และเริ่มใหม่ |
| 4 | สองชั้น บันได ทางออก และภาพครบ |

รายละเอียดแต่ละฝั่ง: [James](A_James.md) / [Jezz](B_Jezz.md) เมธอดร่วม: [PROJECT_PLAN](PROJECT_PLAN.md)
