# แผนพัฒนาและสัญญาระหว่างงาน

กติกาและค่าตั้งต้นใช้จาก [GAME_DESIGN](GAME_DESIGN.md) ไฟล์นี้กำหนดหน้าที่คลาส ชื่อเมธอด และลำดับเชื่อมงาน

## ผู้รับผิดชอบ

| James — feat/world-view | Jezz — feat/player-combat |
|---|---|
| Main, Game, Dungeon, Door, Key, AsciiRenderer, TerminalDisplay และอาร์ต | Player, Enemy, Item, Weapon, Armor, Potion |

ใช้ src/ แบบไม่มี package ในรอบแรก เพื่อต่อไฟล์และใช้คำสั่ง compile เดียวกัน

## ข้อตกลงข้อมูล

- พิกัด x เพิ่มไปขวา y เพิ่มลงล่าง อาร์เรย์ใช้ `[y][x]`
- ทิศเป็น int: 0 เหนือ, 1 ตะวันออก, 2 ใต้, 3 ตะวันตก
- เวกเตอร์เดินตามทิศ: `(0,-1)`, `(1,0)`, `(0,1)`, `(-1,0)`
- ค่า Floor แสดงเป็น 1 และ 2; ตำแหน่งมอน/ของอยู่ใน Dungeon ไม่อยู่ใน Enemy/Item
- หนึ่งช่องมีวัตถุหลักได้อย่างเดียว: ของ มอน ประตู บันได หรือทางออก
- ตรวจ null เมื่อช่องไม่มีมอน/ของ หรือผู้เล่นยังไม่สวมอุปกรณ์
- คำสั่งจากผู้เล่นที่ไม่ถูกต้องให้ Game ปฏิเสธโดยไม่แก้ข้อมูล constructor ที่รับค่าผิด รวมถึง moveTo/setDirection ที่รับค่าผิดให้โยน IllegalArgumentException ส่วนเมธอดใช้ของ/โจมตีปฏิเสธด้วย false หรือ 0 ตามตาราง

## Constructor ที่ใช้ร่วมกัน

| คลาส | Constructor | ข้อมูล |
|---|---|---|
| Player | `public Player()` | ค่าตั้งต้นใน GAME_DESIGN กระเป๋าว่าง พิกัดเริ่ม (0,0) หันเหนือ; Game ย้ายไปจุดเริ่มด่าน |
| Enemy | `public Enemy(String name, int maxHp, int attack, int expReward)` | name ไม่ว่าง, maxHp > 0, attack > 0, expReward >= 0 |
| Item | `protected Item(String name)` | abstract class ชื่อไม่ว่าง ใช้ร่วมกับคลาสลูก |
| Weapon | `public Weapon(String name, int attackBonus)` | โบนัส >= 0 |
| Armor | `public Armor(String name, int defenseBonus)` | โบนัส >= 0 |
| Potion | `public Potion(String name, int healAmount)` | ปริมาณ Heal > 0 |
| Key | `public Key(String name, String keyId)` | James เขียนให้ extends Item; keyId ไม่ว่าง |
| Door | `public Door(String name, String requiredKeyId)` | James เขียน ชื่อและรหัสกุญแจไม่ว่าง เริ่มล็อก |

## API ของ Player — Jezz

| Signature | ผลและเงื่อนไข |
|---|---|
| `int getX()`, `int getY()`, `int getDirection()` | อ่านพิกัด/ทิศ |
| `void moveTo(int x, int y)` | เปลี่ยนพิกัดที่ไม่ติดลบ Game ต้องตรวจด่านก่อนเรียก |
| `void setDirection(int direction)` | รับ 0–3 Game คำนวณการวนทิศซ้าย/ขวาก่อนเรียก |
| `int getHp()`, `int getMaxHp()` | เลือดปัจจุบันและเพดาน |
| `int getAttack()`, `int getDefense()` | ค่าปัจจุบันรวมอุปกรณ์ตาม GAME_DESIGN |
| `int getLevel()`, `int getExp()`, `int getExpToNextLevel()` | EXP เป็นส่วนของ Level ปัจจุบัน |
| `List<Item> getItems()` | รายการอ่านอย่างเดียว ห้ามแก้ List จากภายนอก |
| `boolean addItem(Item item)` | เพิ่มออบเจ็กต์ที่ไม่ใช่ null และยังไม่มีชิ้นเดียวกันในกระเป๋า สำเร็จคืน true |
| `boolean removeItem(Item item)` | นำชิ้นที่มีอยู่ออก สำเร็จคืน true; ปฏิเสธชิ้นที่กำลัง Equip |
| `Weapon getEquippedWeapon()` | อาวุธที่สวม หรือ null |
| `Armor getEquippedArmor()` | เกราะที่สวม หรือ null |
| `boolean equipWeapon(Weapon weapon)` | ต้องมีชิ้นนี้ในกระเป๋า แทนชิ้นเดิม ไม่บวกโบนัสซ้ำ |
| `boolean equipArmor(Armor armor)` | ต้องมีชิ้นนี้ในกระเป๋า แทนชิ้นเดิม ไม่บวกโบนัสซ้ำ |
| `boolean usePotion(Potion potion)` | ผู้เล่นต้องมีชีวิต มีชิ้นนี้ในกระเป๋า และ HP ยังไม่เต็ม จึง Heal แล้วนำยาออก |
| `void takeDamage(int amount)` | amount <= 0 ไม่เปลี่ยนค่า; HP ต่ำสุด 0 |
| `boolean heal(int amount)` | amount > 0, HP > 0 และยังไม่เต็ม จึงฟื้นได้ คืน true เมื่อ HP เพิ่มจริง |
| `int attack(Enemy enemy)` | enemy ต้องไม่เป็น null และผู้เล่น/มอนต้องมีชีวิต หัก HP มอนและคืนจำนวนที่หักจริง ไม่เดิน ไม่สวน ไม่ให้ EXP เอง |
| `void gainExp(int amount)` | รับค่าที่ไม่ติดลบ เพิ่ม EXP/Level และ max HP โดยไม่เพิ่ม Current HP |

ใช้ List/ArrayList จาก java.util เก็บ baseAttack เริ่มต้นแล้วคำนวณโบนัสจาก Level ตามสูตร ไม่เพิ่มค่าพื้นฐานและโบนัส Level ซ้ำสองรอบ

## API ของ Enemy และ Item — Jezz

| คลาส | Signature / หน้าที่ |
|---|---|
| Enemy | `String getName()`, `int getHp()`, `int getMaxHp()`, `int getAttack()`, `int getExpReward()` |
| Enemy | `boolean isAlive()` อ่านจาก HP > 0 |
| Enemy | `void takeDamage(int amount)` ไม่ให้ HP ต่ำกว่า 0; amount <= 0 ไม่เปลี่ยนข้อมูล |
| Enemy | `int attack(Player player)` player ต้องไม่เป็น null และมอน/ผู้เล่นต้องมีชีวิต หักดาเมจหลังเกราะ คืน HP ที่หักจริง ไม่มีการเดิน |
| Item | `String getName()` และ `public abstract String getDescription()` |
| Weapon | `int getAttackBonus()` และ override getDescription() |
| Armor | `int getDefenseBonus()` และ override getDescription() |
| Potion | `int getHealAmount()` และ override getDescription() |

เมธอดที่ปฏิเสธการใช้/โจมตีคืน false หรือ 0 ตามชนิดผลลัพธ์ โดยข้อมูลไม่เปลี่ยน ไม่พิมพ์ข้อความหรือรับคำสั่งภายในคลาสเหล่านี้

## จุดเชื่อมโลกและหน้าจอ — James

| คลาส | Signature | หน้าที่ |
|---|---|---|
| Key | `String getKeyId()` | คืนรหัสกุญแจ และ override getDescription() จาก Item |
| Door | `boolean isOpen()`, `String getName()` | อ่านสถานะและชื่อ |
| Door | `boolean tryOpen(Player player)` | อ่าน getItems() หา Key ที่รหัสตรง ตรวจครบก่อน removeItem() แล้วเปิด สำเร็จครั้งแรกคืน true |
| Dungeon | `Enemy getEnemyAt(int x,int y)`, `Item getItemAt(int x,int y)`, `Door getDoorAt(int x,int y)` | คืนออบเจ็กต์หรือ null |
| Dungeon | `boolean isWalkable(int x,int y)` | false ถ้านอกด่าน กำแพง ประตูปิด หรือมอนมีชีวิต |
| Dungeon | `void removeEnemyAt(int x,int y)`, `void removeItemAt(int x,int y)` | นำสิ่งที่ตาย/ใช้แล้วออก ไม่เปลี่ยนผู้เล่น |
| Dungeon | `boolean goDown(Player player)` | สำเร็จเมื่อยืนบนบันไดชั้น 1 ย้ายไปจุดเริ่มชั้น 2 โดยใช้ Player เดิม กรณีอื่นคืน false |
| Dungeon | `int getFloor()` | คืนชั้นปัจจุบัน 1 หรือ 2 |
| AsciiRenderer | `String render(Dungeon dungeon, Player player)` | คืนภาพฉาก ไม่เปลี่ยนสถานะ |
| TerminalDisplay | `String readCommand()` | คืนหนึ่งบรรทัด หรือ null เมื่อ EOF |
| TerminalDisplay | `void show(String scene, Player player, Enemy enemyAhead, int floor, List<String> messages)` | พิมพ์ฉาก/สถานะ/log; enemyAhead เป็น null ได้ |
| TerminalDisplay | `void showInventory(Player player)` | แสดงรายการอย่างเดียว Game แปลหมายเลขและเรียก Equip/Use |

James เลือกรายละเอียดภายใน Dungeon/renderer ได้ โดยคง API ของ Jezz และกติกาพิกัดให้ตรงกัน Game เป็นตัวเดียวที่ประสานคำสั่งจากผู้เล่น ไม่ให้ TerminalDisplay หัก HP/เพิ่ม EXP อีกชุด

## Flow ต่อสู้ที่ Game ต้องทำ

```text
พบมอนมีชีวิตตรงหน้า
  -> player.attack(enemy)
  -> บันทึกดาเมจและ HP มอน
  -> ถ้ามอนรอด: enemy.attack(player) แล้วบันทึก HP ผู้เล่น
  -> ถ้ามอนตาย: player.gainExp(enemy.getExpReward()) แล้วนำมอนออก
  -> ตรวจ Game Over
  -> แสดงภาพและ log
  -> จบคำสั่งโดยไม่เปลี่ยนตำแหน่ง
```

## สี่รอบพัฒนา

| รอบ | James | Jezz | main ต้องทำได้ |
|---|---|---|---|
| 1: สำรวจ | แผนที่ทดลอง รับ W/A/D ภาพและ STATUS | โครงหกคลาสที่ compile ร่วมกันได้; constructor, getter, พิกัด/ทิศ/HP และ Item พื้นฐาน | เดินสำรวจและหันได้ |
| 2: ของและประตู | Key/Door จุดเก็บของ Inventory UI และภาพดาบ | กระเป๋า Equip/Use Weapon/Armor/Potion | เก็บของ ใช้ยา สวมอุปกรณ์ เปิดทางได้ |
| 3: ต่อสู้ | เชื่อม W มอน log แพ้/เริ่มใหม่ | Enemy โจมตี เกราะ HP EXP/Level | ตีแล้วอยู่ที่เดิม มอนตายไม่สวน Level ไม่ Heal |
| 4: เกมครบ | สองชั้น บันได ทางออก อาร์ต และการจัดจอ | ปรับค่าสมดุลและแก้บั๊กข้อมูล | เล่นจบสองชั้นหรือแพ้ได้ |

Player มีชนิด Weapon/Armor/Potion/Enemy ในสัญญาเมธอด จึงต้องมีโครงหกคลาสของ Jezz ตั้งแต่รอบ 1 แม้พฤติกรรม Equip/โจมตีจะทำในรอบหลัง เปิดให้ Game เรียกเฉพาะส่วนที่เสร็จในรอบนั้น อย่าส่ง Player ที่อ้างถึงคลาสที่ยังไม่มีจน compile ไม่ผ่าน

Jezz ส่งฐานรอบแรกเข้า main ก่อน James นำ main เข้า branch ของตนแล้วต่อ Key/Game ระหว่างรอ James ทำผังด่านกับอาร์ตที่ไม่ติดข้อมูลได้

## ลองผลหลังรวมงาน

- constructor/getter ของทั้งสองส่วนใช้ชื่อและชนิดตรงกัน
- คำสั่งที่ชนกำแพงหรือเปิดประตูไม่ได้ไม่เปลี่ยนตำแหน่ง/ของ
- ตีทั้งครั้งที่มอนรอดและตาย ผู้เล่นอยู่ตำแหน่งเดิม
- มอนตายไม่สวนและได้ EXP ครั้งเดียว
- Equip ซ้ำไม่สะสมโบนัส ยาเต็มเลือดไม่ถูกใช้
- Level Up ไม่ฟื้น Current HP
- เริ่มใหม่รีเซ็ตโลกและผู้เล่น; ลงชั้นรักษาข้อมูลผู้เล่นครบ
- ข้อความและขนาดเฟรมตรงกับ [ART_GUIDE](ART_GUIDE.md)

ดูวิธีรวมงานใน [TEAM_WORKFLOW](TEAM_WORKFLOW.md) และงานรายคนใน [James](A_James.md) / [Jezz](B_Jezz.md)
