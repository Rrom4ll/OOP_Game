# OOP_Game

เกม Java บน Terminal สำรวจดันเจี้ยนแบบ Grid มุมมองบุคคลที่หนึ่ง ภาพเป็น ASCII และข้อความในเกมเป็นภาษาอังกฤษ

ตอนนี้มี `src/Main.java` เป็นจุดเริ่มโปรแกรม ระบบเกมจะเพิ่มตามงานของ James และ Jezz

## เริ่มใช้งาน

ต้องมี JDK และใช้คำสั่ง `java` กับ `javac` ได้

```bash
git clone https://github.com/Rrom4ll/OOP_Game.git
cd OOP_Game
javac -encoding UTF-8 -d build src/*.java
java -cp build Main
```

โครงเริ่มต้นแสดง:

```text
OOP_Game
Java Terminal Dungeon Crawler
The dungeon is under construction.
```

ไฟล์ที่ compile แล้วอยู่ใน `build/` และไม่เก็บใน Git

## เริ่มงานของตัวเอง

| สมาชิก | Branch | งาน |
|---|---|---|
| A (James) | `feat/world-view` | โลกเกม ภาพ หน้าจอ รับคำสั่ง และรวมระบบ |
| B (Jezz) | `feat/player-combat` | ผู้เล่น ไอเทม มอน การต่อสู้ และ EXP/Level |
| ใช้ร่วมกัน | `main` | เวอร์ชันรวม โดย James ประสานการ merge |

หลัง clone ใช้ branch ของตัวเอง:

```bash
git fetch origin
git switch feat/world-view
git merge origin/main
```

Jezz เปลี่ยนคำสั่ง switch เป็น `git switch feat/player-combat` แล้ว merge origin/main เช่นกัน

ก่อนเริ่มเขียน อ่านตามลำดับ:

1. [กติกาเกม](docs/GAME_DESIGN.md) — การเดิน การตี ของ ประตู และค่าตั้งต้น
2. งานของตัวเอง: [A (James)](docs/A_James.md) หรือ [B (Jezz)](docs/B_Jezz.md)
3. [แผนพัฒนาและสัญญาเมธอด](docs/PROJECT_PLAN.md) — constructor, ชนิดข้อมูล และลำดับส่งงาน
4. [Git flow](docs/TEAM_WORKFLOW.md) — ส่งงานและรวมกลับเข้า main

งานแรกของ Jezz คือทำโครงหกคลาสให้ compile ร่วมกันได้ พร้อม Player/Item พื้นฐาน งานแรกของ James คือแผนที่ทดลองกับภาพทางเดิน แล้วเชื่อม Player หลังฐานของ Jezz เข้า main

## เอกสารประกอบ

| เอกสาร | เนื้อหา |
|---|---|
| [ภาพรวมเกม](docs/PROJECT_BRIEF.md) | ประสบการณ์เล่นและตัวอย่างหน้าจอ |
| [ความรู้ที่ใช้](docs/COURSE_SCOPE.md) | แนวคิด Java/OOP และการใช้กับงานจริง |
| [แนวทางอาร์ต](docs/ART_GUIDE.md) | ขนาดภาพ การซ้อน และการจัดหน้าจอ |
| [อาร์ตตัวอย่าง](docs/examples/README.md) | ไฟล์ข้อความ 120×40, 160×50 และ sprite |

กติกาอ้างอิงจาก GAME_DESIGN ส่วนชื่อและชนิดเมธอดอ้างอิงจาก PROJECT_PLAN ถ้าเปลี่ยนส่วนที่อีกคนใช้อยู่ให้ตกลงและปรับเอกสารจุดนั้นพร้อมกัน
