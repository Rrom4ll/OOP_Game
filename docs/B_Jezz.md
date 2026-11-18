# B (Jezz) — ผู้เล่น ไอเทม และการต่อสู้

Branch: `feat/player-combat`

Jezz รับผิดชอบข้อมูลผู้เล่น กระเป๋า อุปกรณ์ มอน การคำนวณโจมตี การลด HP การฟื้นเลือด และ EXP/Level ส่งเมธอดกับข้อมูลให้ James ใช้ในเกม ดูกติกาใน [GAME_DESIGN](GAME_DESIGN.md) และงานอีกฝั่งใน [A (James)](A_James.md)

## 1. Branch ที่ใช้

| Branch | เจ้าของ | หน้าที่ |
|---|---|---|
| `main` | ใช้ร่วมกัน โดย James ประสานการรวม | เวอร์ชันที่รวมระบบและรันได้ |
| `feat/world-view` | James | โลกเกม การเดิน ประตู กุญแจ บันได ภาพ รับคำสั่ง และเมนู |
| `feat/player-combat` | Jezz | ข้อมูลและพฤติกรรมของผู้เล่น ไอเทม มอน และการพัฒนาตัวละคร |

ใช้ branch ของตนต่อเนื่อง ไม่ต้องแยก branch ใหม่สำหรับยา อาวุธ หรือ EXP

## 2. ไฟล์ที่รับผิดชอบ

| ไฟล์ | ต้องทำอะไร |
|---|---|
| `Player.java` | ค่าสถานะ พิกัด/ทิศ กระเป๋า อุปกรณ์ โจมตี รับดาเมจ Heal และเพิ่ม EXP/Level |
| `Enemy.java` | ชื่อ HP, Max HP, ATK, EXP reward การรับดาเมจ โจมตี และตรวจว่าตายหรือยัง |
| `Item.java` | ข้อมูลร่วมของไอเทม เช่น ชื่อ และเมธอด getDescription() |
| `Weapon.java` | สืบทอด Item เก็บโบนัส ATK |
| `Armor.java` | สืบทอด Item เก็บโบนัส DEF |
| `Potion.java` | สืบทอด Item เก็บปริมาณ Heal |

James เขียน Key ให้สืบทอด Item และดูแลการจับคู่กับ Door ส่วน Jezz ทำกระเป๋าที่รับ Item ได้ทั่วไป จึงไม่ต้องเขียน UI หรือเงื่อนไขประตู

## 3. งานที่ต้องทำโดยละเอียด

### 3.1 ข้อมูล Player และการคุมค่า

- เก็บ HP, Max HP, EXP, Level, ค่าพลังพื้นฐาน พิกัด ทิศ รายการของ และอุปกรณ์ที่สวม
- เริ่มไม่มีดาบ/เกราะที่ Equip มีพลังโจมตีมือเปล่าตามค่าที่ตกลง
- ใช้ private สำหรับ field สำคัญ และให้ส่วนอื่นอ่าน/เปลี่ยนผ่านเมธอด
- takeDamage() ทำให้ HP ต่ำสุดเป็น 0 ส่วน heal() ทำให้ HP สูงสุดไม่เกิน Max HP
- เตรียม getX/getY/getDirection และ moveTo/setDirection ให้ James ควบคุมการเดินหลังตรวจด่านแล้ว
- เมธอดโจมตี/ใช้ของไม่เปลี่ยนตำแหน่งผู้เล่น

### 3.2 Item และกระเป๋า

- เริ่ม Item ด้วย constructor และข้อมูลที่ใช้ร่วมกัน เช่น name กับ getDescription()
- ใช้ ArrayList<Item> เก็บของ และมีเมธอดเพิ่ม อ่าน และนำของออกเมื่อกติกาต้องใช้
- Weapon/Armor/Potion สืบทอด Item แล้ว override รายละเอียดให้ตรงชนิด
- ตัวอย่าง description: `Iron Sword: ATK +5`, `Leather Armor: DEF +2`, `Healing Potion: Heal 10 HP` โดยค่าจริงกำหนดในตารางสมดุล
- ให้ James อ่านรายการเพื่อสร้างหน้า Inventory และตรวจ Key ไม่ให้ UI แก้ field ของกระเป๋าโดยตรง
- การเก็บของเข้ากระเป๋ายังไม่ Equip หรือ Use ให้เอง

### 3.3 อุปกรณ์และยา

- equipWeapon() เปลี่ยนอาวุธที่สวม แล้ว getAttack() คืน ATK ปัจจุบันตามอาวุธนั้น
- equipArmor() เปลี่ยนเกราะที่สวม แล้ว getDefense() คืนค่าป้องกันปัจจุบัน
- การ Equip ซ้ำไม่บวกโบนัสทับหลายรอบ การเปลี่ยนชิ้นต้องคำนวณจากอุปกรณ์ที่สวมจริง
- เปิดให้ James อ่าน equipped weapon/armor เพื่อแสดงชื่อและภาพดาบ
- usePotion() ฟื้น Current HP ไม่เกิน Max HP และจัดการจำนวน/การนำยาออกตามกติกาที่เลือก
- ตรวจว่าของที่เลือกใช้/สวมเป็นของในกระเป๋าจริง และส่งผลสำเร็จหรือไม่สำเร็จให้หน้าจอแสดงได้
- เงื่อนไขการใช้ยาต่อหน้ามอนและการเสียยาเมื่อเลือดเต็มยังต้องเลือกก่อนเขียนส่วนนั้น

### 3.4 Enemy และการโจมตี

- เก็บชื่อ HP/current และ max, ATK และ EXP reward ตามค่าตายตัว
- ไม่เขียนการเดินไล่ผู้เล่นหรือระบบสุ่มศัตรู
- Player.attack(enemy) คำนวณ ATK จากค่าพื้นฐาน โบนัส Level และอาวุธที่ Equip แล้วลด HP มอน
- Enemy.attack(player) คำนวณดาเมจหลังหักเกราะที่ Equip แล้วลด HP ผู้เล่น
- เตรียม isAlive() เพื่อให้ Game ตรวจว่าควรสวนหรือให้ EXP
- เมธอดเหล่านี้ทำเฉพาะการโจมตี ไม่รับปุ่ม W ไม่สั่งเดิน และไม่วนต่อสู้อัตโนมัติ
- Game ของ James เป็นผู้เรียกผู้เล่นตีก่อน ถ้ามอนรอดจึงเรียกมันสวน ถ้ามอนตายจึงเรียกเพิ่ม EXP ครั้งเดียว
- คืนค่าดาเมจที่หน้าจอต้องใช้ และให้ James อ่าน HP ล่าสุดได้ รูปแบบ overkill กับดาเมจขั้นต่ำรอตกลงตาม GAME_DESIGN

ตัวอย่างเพื่ออธิบายผลลัพธ์ ไม่ใช่ตัวเลขที่ล็อกแล้ว:

```text
Player ATK: 2 + Sword Bonus 5 = 7
Goblin HP: 12 - 7 = 5
Goblin ATK: 5 - Armor DEF 2 = 3 damage
Player HP: 20 - 3 = 17
```

ตัวเลขส่งออกที่ James ต้องนำไปแสดงคือดาเมจ 7, Goblin HP 5/12, ดาเมจสวน 3 และ Player HP 17/20

### 3.5 EXP และ Level

- gainExp() รับ EXP ที่ Game ส่งมาเมื่อฆ่ามอน
- ตรวจเกณฑ์ Level เพิ่ม max HP และ ATK ตามตาราง
- Current HP ต้องคงเดิม เช่น 12/20 กลายเป็น 12/25
- เลือกวิธีเพิ่ม ATK ให้ชัด: เพิ่มค่าพื้นฐานโดยตรง หรือเก็บโบนัส Level แยก อย่าบวกสองวิธีซ้ำกัน
- เปิดให้ James อ่าน EXP, Level และค่าสถานะก่อน/หลัง เพื่อแสดงข้อความ Level Up
- รูปแบบ EXP สะสม ส่วนเกิน และการข้ามหลาย Level ยังต้องตกลงให้ตรงกันก่อนเขียนรายละเอียด

## 4. ความรู้ที่ใช้กับงานนี้

| เนื้อหา | ใช้ตรงไหน | ตัวอย่างเรียนที่เกี่ยวข้อง |
|---|---|---|
| Class / Object / Constructor | สร้าง Player, Enemy และไอเทมจากค่าตั้งต้น | DispatchDemo.java และคลาสใน Assignment1 |
| Encapsulation / Guard condition | คุม HP และตรวจเงื่อนไขก่อนใช้ของ | Lab_4_Encapsulation.java |
| Inheritance | Weapon/Armor/Potion สืบทอด Item | PayrollApp.java |
| Overriding / Polymorphism | วน Item เรียก getDescription() ของแต่ละชนิด | PayrollApp.java และ DispatchDemo.java |
| ArrayList | เก็บรายการของใน Inventory | Employee.java ใน Assignment1_Java |
| Composition / Association | Player มีกระเป๋าและอ้างถึงอุปกรณ์ที่สวม | Employee.java / Division.java |
| if / Loop / Method | ตรวจตาย ใช้ยา คำนวณโบนัส และตรวจเกณฑ์ EXP | ตัวอย่าง Java พื้นฐานและแลบ OOP |

ดูรายละเอียดแหล่งเรียนใน [COURSE_SCOPE](COURSE_SCOPE.md) ใช้คลาสและเมธอดเรียบง่ายที่อธิบายได้ก่อน ไม่ต้องเพิ่ม framework สำหรับงานเหล่านี้

## 5. เมธอดที่ส่งให้ James ใช้

ตกลงชื่อและชนิดคืนค่าก่อนเริ่มเชื่อม นี่เป็นสัญญาที่เสนอ ยังไม่ใช่โค้ดที่มีอยู่แล้ว

| กลุ่ม | เมธอด/ข้อมูล |
|---|---|
| พิกัดและทิศ | getX(), getY(), getDirection(), moveTo(x,y), setDirection(direction) |
| สถานะ | getHp(), getMaxHp(), getAttack(), getDefense(), getExp(), getLevel() |
| กระเป๋า | getItems(), addItem(item), removeItem(item) |
| อุปกรณ์/ยา | equipWeapon(weapon), equipArmor(armor), usePotion(potion), ข้อมูลอุปกรณ์ที่สวม |
| ต่อสู้ | Player.attack(enemy), Enemy.attack(player), takeDamage(amount) |
| มอน | isAlive(), getHp(), getMaxHp(), getExpReward() |
| Level | gainExp(amount) |
| Item | constructor ข้อมูลร่วม, getName(), getDescription() และค่าพลังเฉพาะชนิด |

James รับผิดชอบ Game, Door/Key และหน้าจอ หากต้องเปลี่ยนชื่อเมธอดที่ James เรียกใช้ ให้แจ้งและรวมการเปลี่ยนส่วนเชื่อมให้ครบ

## 6. ลำดับส่งงานเข้า main

| รอบ | Jezz ทำ | James นำไปเชื่อมกับอะไร | ผลเมื่อรวมแล้ว |
|---|---|---|---|
| 1: สำรวจ | Player พื้นฐาน พิกัด/ทิศ/HP และ Item โครงข้อมูลร่วม | Game การเดิน ภาพและ STATUS | เดินสำรวจและเห็นค่าสถานะได้ |
| 2: ของและประตู | กระเป๋า Weapon/Armor/Potion และ Equip/Use | หน้า Inventory จุดเก็บของ Key/Door และภาพดาบ | เก็บและใช้ของ เปิดทางได้ |
| 3: ต่อสู้ | Enemy สูตรโจมตี เกราะ HP และ EXP/Level | การกด W และ combat log | ตีทีละครั้ง ตายไม่สวน เลเวลขึ้นไม่ Heal |
| 4: เกมครบ | ปรับตารางตัวเลขและแก้บั๊กข้อมูล/กติกา | สองชั้น บันได และจุดจบเกม | เล่นครบตั้งแต่เริ่มจนชนะหรือแพ้ได้ |

ส่ง Player และ Item พื้นฐานให้เร็ว ไม่ต้องรอให้การต่อสู้ทั้งชุดเสร็จ James จึงเริ่มใช้เมธอดและสร้าง Key ต่อได้ ขณะ James ทำภาพ/ด่าน Jezz ทำอุปกรณ์และสูตรจากข้อมูลตัวอย่างได้

## 7. Git flow ของ Jezz

ใช้ repo ของตนเอง ก่อนสลับ branch ให้ commit งานค้างของตนให้เรียบร้อย James เป็นผู้ประสานการรวม main

### เข้า branch ครั้งแรกหลัง clone

ใช้ branch ของ Jezz ที่เตรียมจากฐาน main:

```bash
git fetch origin
git switch --track origin/feat/player-combat
```

ถ้ามี local branch อยู่แล้วหรือกลับมาทำงานครั้งต่อไป ใช้ `git switch feat/player-combat`

### ทำงานและส่งรอบที่พร้อม

ตัวอย่างรอบ Player/Item พื้นฐาน เลือก git add เฉพาะไฟล์ที่แก้จริง:

```bash
git add src/Player.java src/Item.java
git commit -m "feat: add player state and base item"
git push -u origin feat/player-combat
```

แจ้ง James ว่ารอบนี้พร้อม อธิบายเมธอดที่เพิ่มและผลที่ต้องเห็น เช่น Player มีตำแหน่ง/ทิศ/HP และ Game เรียก moveTo() ได้ James จะนำ branch นี้ไป merge เข้า main

### หลัง James ส่ง main รอบใหม่

```bash
git fetch origin
git switch feat/player-combat
git merge origin/main
```

ทำรอบถัดไปบน branch เดิม ตัวอย่างเมื่อระบบต่อสู้พร้อม:

```bash
git add src/Player.java src/Enemy.java
git commit -m "feat: add combat damage and level progression"
git push origin feat/player-combat
```

ชื่อไฟล์เป็นตัวอย่าง ให้ stage เฉพาะไฟล์ที่มีอยู่และแก้จริงในรอบนั้น ไม่ต้อง push main หรือแก้ไฟล์ Game/หน้าจอแทน James

### ถ้ามี conflict

ใช้ git status ดูไฟล์ที่ชน คุยกับ James ว่าจะเก็บพฤติกรรมใด แก้เครื่องหมาย conflict แล้ว git add ไฟล์ที่แก้และ git commit จากนั้นลองผลที่เกี่ยวข้องก่อน push อย่าเลือกทับทั้งไฟล์โดยไม่อ่าน

## 8. ผลที่งาน Jezz ต้องทำได้

- HP ไม่ติดลบ Heal ไม่เกิน Max HP
- ของที่ยังไม่ Equip ไม่เพิ่ม ATK/DEF และเปลี่ยนอุปกรณ์แล้วไม่บวกโบนัสซ้ำ
- เมธอดโจมตีไม่ขยับผู้เล่น และ James เรียกสวนเฉพาะมอนที่รอดได้
- ข้อมูล HP มอนกับดาเมจส่งให้หน้าจอแสดงได้ครบ
- EXP/Level เพิ่มถูกเกณฑ์ โดย Level Up ไม่เติม Current HP
- Player/Item/Enemy ใช้ร่วมกับ Game และหน้าจอของ James ได้ผ่านเมธอดที่ตกลง

เมื่อมี src/*.java ครบตามโครงสร้างและยังไม่ใช้ package สามารถลองผลรวมด้วย:

```powershell
javac -encoding UTF-8 -d build src/*.java
java -cp build Main
```

ฐาน main มี Main.java ที่ compile/run ได้แล้ว ใช้ branch ของตนพัฒนาคลาสระบบเกมตามรอบงานข้างต้น
