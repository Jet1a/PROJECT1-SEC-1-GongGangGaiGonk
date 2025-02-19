# Project : TorKum
## รายละเอียด
เว็บไซต์เกมที่ต้องคิดคำที่ขึ้นต้นด้วยตัวอักษรสุดท้ายของคำก่อนหน้า เช่น ANT ต้องต่อด้วยคำที่ขึ้นต้นด้วยตัวอักษร T
  
## ขอบเขตของงาน (Week 2)
- ออกแบบหน้าเว็บไซต์ด้วย figma (รับผิดชอบโดยสมาชิกทุกคน) 22/1/2025
  
## ขอบเขตของงาน (Week 3)
เขียนโปรแกรมตาม components ที่ได้รับมอบหมาย
- 66130500016 นายเจตภูมิ ชาญทวีคุณ (Game Page)
- 66130500023 นายชาคริต ภูแล่นนา (Result Page)
- 66130500031 นายณัฐภัทร์  หลำนุ้ย (Home Page)
- 66130500053 นายเนติพงศ์  สุขเกษม (How to Play Page)

## ขอบเขตของงาน (Week 4)
- 66130500016 นายเจตภูมิ ชาญทวีคุณ (Responsive Style & Function ต่างๆสำหรับหน้า Game)
- 66130500023 นายชาคริต ภูแล่นนา (Result Page : result score page , back buntton , computed() to show the longest word ,  style and responsive)
- 66130500031 นายณัฐภัทร์  หลำนุ้ย (Adding Responsive,Update Start button and test prototype by user)
- 66130500053 นายเนติพงศ์  สุขเกษม (How to Play Page,Update fixed some component bugs and add reset stepcounter in reset function)

## ขอบเขตของงาน (Week 5)
- 66130500016 นายเจตภูมิ ชาญทวีคุณ
  - Achievement Page UI
  - เพิ่ม Array Object achievement เพื่อระบุชื่อและวิธีการทำ achievement นั้นให้สำเร็จโดยมีเงื่อนไขที่ต่างกัน
  - checkAchievement Function สำหรับตรวจสอบว่าทำ Achievement นั้นสำเร็จแล้วหรือไม่ 
  - watch(achievement) สำหรับแสดง UI Toast เมื่อสามารถทำ Achievement นั้นได้สำเร็จระหว่างการเล่นที่ Game Page
 - 66130500031 นายณัฐภัทร์  หลำนุ้ย
    - Hint Button Function
      - Hint เป็น feature ที่จะช่วยให้ผู้เล่นได้เล่นเกมง่ายขึ้นโดยผู้เล่นจะได้รับ hint 1 ครั้งเมื่อผู้เล่นกรอกคำทุกๆ 10 คำ
      - Function จะทำงานโดยคำที่สุ่มตัวอักษรตัวแรกต้องขึ้นต้นด้วยตัวอักษรตัวสุดท้ายของคำก่อนหน้าและจะดึงคำศัพท์มาจาก wordDictionary.json
      - และคำที่สุ่มมานั้นจะต้องไม่ซ้ำกับคำที่เคยพิมพ์ไปก่อนหน้า
    - Time Boost Button
      - Time Boost เป็น feature ที่่เพิ่มเวลาให้กับผู้เล่น 5 วินาที โดยจะได้ Time boost 1 ครั้งเมื่อผู้เล่นกรอกคำทุกๆ 15 คำ
      - Adding timer boost button to game page by watch at every 15 word on usedWord then add more 5 sec at counter on time.
- 66130500053 นายเนติพงศ์  สุขเกษม 
    - สร้างเกมโหมดใหม่อีก 2 โหมด รวมทั้งหมดเป็น 3 โหมด โดยโหมดที่เพิ่มมากโหมดแรกจะเป็น โหมดแบบจับเวลาไม่มีการ reset เวลาเมื่อตอบคำต่อไปได้ แต่จะเป็นการจับเวลาสามารถเลือกเวลาได้ 15,30,60 วินาที โหมดที่สอง เป็นโหมดที่จะรับ word ที่มาพิมพ์มาเพิ่มตามจำนวนคำที่เลือก เช่นโหมดปกติจะต้องเริ่มต้นด้วยคำศัพท์ที่มีตัวอักษร 3 ตัวขึ้นไป แต่โหมดนี้สามารถเลือกได้ว่าเอาขั้นตำ่เป็นตัวอักษร เพิ่มความยากให้กับเกม
    - แก้ไข UI สำหรับหน้า HOME ไว้แยกหน้าสำหรับ Achievement page และแยกหน้าของ HOWTOPLAY ไว้อีกทางไม่จำเป็นต้องอ่าน howtoplay ก่อนการเล่นทุกครั้งและเชื่อมหน้าต่างๆใหม่
    - Added history function, can be printed by pressing the up button, like Linux command
- 66130500023 นายชาคริต ภูแล่นนา
  - Update Result page : change computed() to watch() for finding the longest word
  - Update game page : add totalTime.value++ for counting total time each round
  - Add stats to result page : add stats element in result page that show when click at 'Show stats' button. It hides used word elemnet and show stats element instead. This element will show for each round (in session)
  - Update responsive of result page
    
## Demo and How to play TOR KUM
https://github.com/user-attachments/assets/75b38f77-6d61-4299-9184-18a0148ffe25


                                                                                                                                                                                                                                                                                                                                                                           


## สมาชิก
- 66130500016 นายเจตภูมิ ชาญทวีคุณ
- 66130500023 นายชาคริต ภูแล่นนา
- 66130500031 นายณัฐภัทร์ หลำนุ้ย
- 66130500053 นายเนติพงศ์ สุขเกษม
