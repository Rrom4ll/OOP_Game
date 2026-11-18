# B (Jezz)

Branch: `feat/player-combat`

รับผิดชอบ Player, Enemy, Item, Weapon, Armor และ Potion รวมข้อมูล ค่าพลัง กระเป๋า อุปกรณ์ การโจมตี และ EXP/Level

อ่าน [กติกาเกม](GAME_DESIGN.md) และ [constructor/API ที่ใช้ร่วมกัน](PROJECT_PLAN.md) ก่อนเขียน งานของ James อยู่ใน [A_James](A_James.md)

## ไฟล์ที่ดูแล

| ไฟล์ | หน้าที่ |
|---|---|
| Player.java | พิกัด/ทิศ HP/EXP/Level กระเป๋า อุปกรณ์ โจมตี รับดาเมจ Heal |
| Enemy.java | ชื่อ HP/ATK/EXP reward ตรวจมีชีวิต โจมตี และรับดาเมจ |
| Item.java | abstract class เก็บชื่อและ getDescription() |
| Weapon.java | extends Item เก็บ attackBonus |
| Armor.java | extends Item เก็บ defenseBonus |
| Potion.java | extends Item เก็บ healAmount |

James เขียน Key ให้สืบทอด Item และทำเมนู/หน้าจอ/ประตูทั้งหมด กระเป๋าของ Jezz รับ Item ทั่วไปได้โดยไม่ต้องรู้จัก Key โดยตรง

## เริ่มทำอะไรก่อน

1. เข้า feat/player-combat
2. เริ่ม Item แล้วทำ Weapon/Armor/Potion ที่มี constructor, getter และ getDescription()
3. ทำโครง Enemy ให้มี constructor และข้อมูลพื้นฐาน
4. ทำ Player ที่มีค่าตั้งต้น พิกัด/ทิศ/HP และ getter ตาม API
5. ให้ทั้งหกคลาส compile ร่วมกับ Main ได้ ไม่ส่ง Player ที่อ้างถึงชนิดที่ยังไม่มี
6. ส่งฐานนี้ให้ James ก่อน แล้วทำกระเป๋า Equip/Use และการต่อสู้ในรอบถัดไป

พฤติกรรมที่ยังไม่ถึงรอบไม่ต้องเปิดใช้ในเกม แต่คลาสและชนิดข้อมูลที่ถูกอ้างถึงต้องมีครบตั้งแต่ฐานรอบแรก

## งานที่ต้องทำ

### 1. Player และการคุมค่า

- field สำคัญเป็น private ใช้เมธอดอ่าน/เปลี่ยนข้อมูล
- Player() เริ่ม Level 1, HP 20/20, base ATK 2, EXP 0 กระเป๋าว่าง และไม่สวมของ
- เก็บพิกัดเริ่ม (0,0) หันเหนือ Game จะย้ายไปจุดเริ่มด่าน
- moveTo/setDirection เปลี่ยนข้อมูลหลัง Game ตรวจด่านแล้ว ไม่ตรวจแผนที่เอง
- takeDamage() ไม่ให้ HP ต่ำกว่า 0 และ heal() ไม่ให้เกิน Max HP
- เมธอดโจมตี/ใช้ของไม่เปลี่ยนพิกัด ไม่รับปุ่ม และไม่พิมพ์หน้าจอ

### 2. Item และกระเป๋า

- Item มี protected constructor รับชื่อ, getName() และ abstract getDescription()
- คลาสลูกเก็บค่าพลังเฉพาะและ override รายละเอียด
- ใช้ `ArrayList<Item>` ภายใน Player และส่ง getItems() แบบอ่านอย่างเดียว
- addItem/removeItem บอกผลด้วย boolean ไม่เพิ่มชิ้นเดิมซ้ำและไม่ลบชิ้นที่กำลัง Equip
- มีของหลายชิ้นได้แต่ไม่ใช้/สวมให้อัตโนมัติ
- James ใช้รายการเดียวกันแสดง Inventory และหา matching key ใน Door

### 3. Equip และยา

- equipWeapon/equipArmor ต้องรับชิ้นที่มีอยู่ในกระเป๋าจริง
- เก็บอาวุธ/เกราะที่สวมอย่างละหนึ่งชิ้น Equip ซ้ำไม่บวกโบนัสซ้ำ เปลี่ยนชิ้นแล้วแทนชิ้นเก่า
- getAttack() คำนวณ `2 + (Level - 1) + weaponBonus`
- getDefense() คืน armorBonus หรือ 0 เมื่อไม่สวม
- ส่ง equipped weapon/armor เป็นออบเจ็กต์หรือ null ให้ James แสดงผล
- usePotion() ตรวจว่ามีชีวิต มียาชิ้นนั้น และ HP ไม่เต็ม ก่อน Heal แล้วนำยาออก
- ถ้าขาดเงื่อนไขให้คืน false โดยไม่เปลี่ยนค่า เลือดเต็มแล้วไม่เสียยา

### 4. Enemy และการโจมตี

- Enemy เก็บชื่อ HP/Max HP, ATK และ EXP reward ตาม constructor
- isAlive() คืน HP > 0 ไม่เก็บสถานะตายซ้ำกับ HP
- Player.attack(enemy) หัก HP มอนเท่าที่เหลือและคืนค่าที่หักจริง
- Enemy.attack(player) หัก `max(1, ATK - player.DEF)` ไม่เกิน HP ที่เหลือ และคืนค่าที่หักจริง
- ฝ่ายโจมตีหรือเป้าหมายตายแล้วให้คืน 0 ไม่เปลี่ยนข้อมูล
- เมธอดโจมตีไม่วนสู้ ไม่สั่งสวน ไม่ให้ EXP และไม่ย้ายตำแหน่ง
- Game ของ James เป็นผู้ตรวจมอนรอด/ตาย แล้วเรียกสวนหรือ gainExp() ตามลำดับ

ตัวอย่างค่าตั้งต้น:

```text
Player ATK: 2 + Sword 5 = 7
Goblin HP: 12 -> 5
Goblin damage: 5 - Armor 2 = 3
Player HP: 20 -> 17
```

ถ้าตีอีกครั้ง Goblin เหลือ 5 จึงหักจริง 5 คืนดาเมจ 5 ให้หน้าจอ และ Game จะไม่เรียกมอนสวน

### 5. EXP และ Level

- Game ส่ง EXP เมื่อฆ่ามอนสำเร็จครั้งเดียว
- getExp() เป็น EXP ของ Level ปัจจุบัน getExpToNextLevel() คืน `10 + 5 × (Level - 1)`
- เมื่อครบเกณฑ์ หัก EXP เกณฑ์นั้น เพิ่ม Level และ Max HP +5 แล้วตรวจซ้ำถ้ายังเหลือพอ
- Current HP คงเดิม เช่น 12/20 -> 12/25
- โบนัส ATK มาจาก Level ใน getAttack() ไม่เพิ่ม base ATK ซ้ำอีกครั้ง
- ส่งค่า Level/EXP/HP ล่าสุดให้ Game/หน้าจออ่าน ไม่พิมพ์ข้อความ Level Up เอง

## API ที่ส่งให้ James

ใช้ชื่อ ชนิดข้อมูล และผลลัพธ์ใน [PROJECT_PLAN](PROJECT_PLAN.md) เป็นสัญญาร่วม โดยเฉพาะ:

- Player: พิกัด/ทิศ HP/ATK/DEF/Level/EXP, กระเป๋า, Equip/Use และ attack/gainExp
- Enemy: getter, isAlive(), takeDamage(), attack()
- Item: constructor, getName(), getDescription() และ getter ค่าพลังของคลาสลูก

ไม่เปลี่ยนชื่อเมธอดที่ใช้อยู่โดยแก้เพียงฝั่งเดียว ให้แจ้ง James เพื่อปรับจุดเรียกพร้อมกัน

## ความรู้ที่ใช้

| แนวคิด | ใช้กับงาน |
|---|---|
| Class / Constructor | สร้างผู้เล่น มอน และของจากค่าตั้งต้น |
| Encapsulation / Guard | คุม HP และตรวจเงื่อนไขก่อนแก้ข้อมูล |
| Inheritance / Overriding | ชนิดไอเทมสืบทอด Item และแสดงรายละเอียดต่างกัน |
| Polymorphism | วนรายการ Item โดยไม่ต้องแสดงผลแยกด้วยชนิดทุกชิ้น |
| ArrayList / Association | กระเป๋ามีหลายชิ้น อุปกรณ์ที่สวมอย่างละ 0..1 |
| if / Loop / Method | การโจมตี Heal และตรวจเกณฑ์ EXP ซ้ำ |
| OOAD / Information Expert | ให้คลาสคำนวณพฤติกรรมจากข้อมูลของตน |
| Sequence / Layering | ทำเมธอดให้ Game เรียกตามลำดับ โดยแยกจากหน้าจอ |

รายละเอียดแนวคิดเพิ่มเติมอยู่ใน [COURSE_SCOPE](COURSE_SCOPE.md)

## รอบส่งงาน

| รอบ | งาน Jezz | James นำไปใช้กับ |
|---|---|---|
| 1 | โครงหกคลาส constructor/getter, Player พิกัด/ทิศ/HP, Item พื้นฐาน | Game เดิน/หัน STATUS และ Key |
| 2 | กระเป๋า Equip/Use และค่าพลังอุปกรณ์ | เก็บของ Inventory UI และประตู |
| 3 | Enemy โจมตี HP เกราะ และ EXP/Level | W ต่อสู้ log แพ้/เริ่มใหม่ |
| 4 | ปรับค่าสมดุลและแก้บั๊กข้อมูล | เกมสองชั้นครบ |

## Git ของ Jezz

```bash
git fetch origin
git switch feat/player-combat
git merge origin/main
```

ก่อนทำต่อหลัง James ส่ง main ใหม่:

```bash
git fetch origin
git merge origin/main
```

ตัวอย่างส่งฐานหกคลาส:

```bash
git add src/Player.java src/Enemy.java src/Item.java src/Weapon.java src/Armor.java src/Potion.java
git commit -m "feat: add player and item foundation"
git push -u origin feat/player-combat
```

เลือกเฉพาะไฟล์ที่แก้จริง แจ้ง James ว่ารอบใดพร้อมและเพิ่มเมธอดอะไร James จะประสานรวม main ใช้ branch เดิมทำงานรอบต่อไป ดู flow ร่วมใน [TEAM_WORKFLOW](TEAM_WORKFLOW.md)

## งาน Jezz ทำ

- constructor/getter ใช้กับ Game/Key/หน้าจอได้ตาม API
- HP อยู่ในช่วง 0 ถึง Max HP
- Equip ซ้ำไม่เพิ่มโบนัสทับ ของที่ยังไม่ Equip ไม่มีผล
- ใช้ยาสำเร็จจึงนำยาออก เลือดเต็มไม่เสียยา
- attack() ไม่ขยับ ไม่สวนเอง และส่งจำนวน HP ที่หักจริง
- EXP/Level ถูกเกณฑ์ Current HP ไม่เพิ่มตอน Level Up
