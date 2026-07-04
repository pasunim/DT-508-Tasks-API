# Workshop 1 — MediaPipe Hand & Pose Landmark Demos

โปรแกรมสาธิตการตรวจจับจุดสำคัญ (landmark) ของมือและท่าทางร่างกายแบบเรียลไทม์ โดยใช้ [MediaPipe](https://ai.google.dev/edge/mediapipe/solutions/guide) Tasks Vision API ร่วมกับ OpenCV อ่านวิดีโอสดจากกล้องเว็บแคม

## คุณสมบัติ

- **`hand_demo.py`** — ตรวจจับมือได้สูงสุด 2 ข้าง และวาดจุด landmark ทั้ง 21 จุดต่อมือ โดยจุดปลายนิ้ว (หัวแม่มือ, นิ้วชี้, นิ้วกลาง, นิ้วนาง, นิ้วก้อย) จะถูกไฮไลต์เป็นสีเขียวและวาดขนาดใหญ่กว่า ส่วนข้อต่ออื่น ๆ วาดเป็นสีขาว
- **`pose_demo.py`** — ตรวจจับท่าทางร่างกายแบบเต็มตัวได้ 1 คน และวาดจุด landmark ทั้ง 33 จุดเป็นสีเหลือง

ทั้งสองสคริปต์ประมวลผลแบบอะซิงโครนัส (โหมด `LIVE_STREAM`) ทำให้วิดีโอยังคงลื่นไหลในขณะที่ MediaPipe ประมวลผลเฟรมอยู่เบื้องหลัง

## ความต้องการของระบบ

- Python 3.14 ขึ้นไป
- กล้องเว็บแคม
- [uv](https://docs.astral.sh/uv/) (แนะนำ) หรือ pip

ไลบรารีที่ใช้ (ดูใน [pyproject.toml](pyproject.toml)):

- [`mediapipe`](https://pypi.org/project/mediapipe/) >= 0.10.35
- [`opencv-python`](https://pypi.org/project/opencv-python/) >= 5.0.0.93

## โครงสร้างโปรเจกต์

```
.
├── hand_demo.py                       # สาธิตการตรวจจับ landmark ของมือ
├── pose_demo.py                       # สาธิตการตรวจจับ landmark ของท่าทางร่างกาย
├── main.py                            # จุดเริ่มต้นตัวอย่าง (placeholder)
├── models/
│   ├── hand_landmarker.task           # โมเดล MediaPipe สำหรับตรวจจับมือ
│   └── pose_landmarker_lite.task      # โมเดล MediaPipe สำหรับตรวจจับท่าทาง (lite)
├── pyproject.toml
├── uv.lock
└── requirements.txt
```

## การติดตั้ง

โคลนโปรเจกต์และติดตั้งไลบรารีที่จำเป็น:

```bash
git clone https://github.com/pasunim/DT-508-Tasks-API.git
cd DT-508-Tasks-API
uv sync
```

คำสั่ง `uv sync` จะสร้าง virtual environment (`.venv`) และติดตั้งไลบรารีตามเวอร์ชันที่ล็อกไว้ใน `uv.lock` ให้อัตโนมัติ หากต้องการใช้ `pip` แบบปกติแทน:

```bash
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

ไฟล์โมเดลในโฟลเดอร์ `models/` ถูกรวมมาในโปรเจกต์นี้แล้ว จึงไม่จำเป็นต้องดาวน์โหลดเพิ่มเติม แต่หากต้องการดาวน์โหลดโมเดลใหม่ด้วยตนเอง (เช่น ต้องการอัปเดตเป็นเวอร์ชันล่าสุด) สามารถรันคำสั่งต่อไปนี้จาก root ของโปรเจกต์:

**macOS / Linux (bash):**

```bash
mkdir -p models
wget -q -O models/hand_landmarker.task https://storage.googleapis.com/mediapipe-models/hand_landmarker/hand_landmarker/float16/latest/hand_landmarker.task
wget -q -O models/pose_landmarker_lite.task https://storage.googleapis.com/mediapipe-models/pose_landmarker/pose_landmarker_lite/float16/latest/pose_landmarker_lite.task
```

**Windows (PowerShell):**

```powershell
New-Item -ItemType Directory -Force -Path models
Invoke-WebRequest -Uri "https://storage.googleapis.com/mediapipe-models/hand_landmarker/hand_landmarker/float16/latest/hand_landmarker.task" -OutFile "models\hand_landmarker.task"
Invoke-WebRequest -Uri "https://storage.googleapis.com/mediapipe-models/pose_landmarker/pose_landmarker_lite/float16/latest/pose_landmarker_lite.task" -OutFile "models\pose_landmarker_lite.task"
```

**Windows (PowerShell / cmd, ใช้ `curl.exe` ที่มีมาให้ในตัว):**

```powershell
mkdir models
curl.exe -o models\hand_landmarker.task https://storage.googleapis.com/mediapipe-models/hand_landmarker/hand_landmarker/float16/latest/hand_landmarker.task
curl.exe -o models\pose_landmarker_lite.task https://storage.googleapis.com/mediapipe-models/pose_landmarker/pose_landmarker_lite/float16/latest/pose_landmarker_lite.task
```

> ใน PowerShell คำสั่ง `curl` เป็น alias ของ `Invoke-WebRequest` ไม่ใช่ curl จริง จึงต้องระบุ `curl.exe` ให้ชัดเจนเพื่อเรียกใช้ curl ตัวจริงที่ติดตั้งมากับ Windows 10/11

## วิธีใช้งาน

รันสาธิตการตรวจจับมือ:

```bash
uv run python hand_demo.py
```

รันสาธิตการตรวจจับท่าทางร่างกาย:

```bash
uv run python pose_demo.py
```

จะมีหน้าต่างเปิดขึ้นแสดงภาพจากกล้องเว็บแคมพร้อมจุด landmark ที่ซ้อนทับแบบเรียลไทม์ กด **`ESC`** เพื่อปิดหน้าต่างและหยุดโปรแกรม

> **หมายเหตุ:** หากระบบปฏิบัติการขอสิทธิ์เข้าถึงกล้อง ให้อนุญาตสิทธิ์ให้กับ terminal หรือ IDE ที่ใช้รัน มิฉะนั้น `cv2.VideoCapture(0)` จะไม่สามารถอ่านภาพจากกล้องได้

## หลักการทำงาน

ทั้งสองสคริปต์ทำงานตามรูปแบบเดียวกัน:

1. ตั้งค่าออบเจ็กต์ `HandLandmarkerOptions` / `PoseLandmarkerOptions` โดยชี้ไปยังไฟล์โมเดล `.task` ที่แนบมาในโปรเจกต์ ทำงานในโหมด `LIVE_STREAM` พร้อม callback แบบอะซิงโครนัส (`result_callback`)
2. เปิดกล้องเว็บแคมเริ่มต้นด้วย `cv2.VideoCapture(0)`
3. ในแต่ละรอบของลูป จะแปลงเฟรมจากรูปแบบสี BGR (OpenCV) เป็น RGB แล้วห่อเป็น `mp.Image` ก่อนส่งเข้า `detect_async` พร้อม timestamp
4. ฟังก์ชัน callback (`process_result` / `process_pose`) จะเก็บผลลัพธ์การตรวจจับล่าสุดไว้ในตัวแปรระดับโมดูล
5. ลูปหลักจะอ่านผลลัพธ์ล่าสุดที่เก็บไว้ (ถ้ามี) แล้ววาดวงกลมของแต่ละ landmark ลงบนเฟรมปัจจุบันด้วย `cv2.circle`
6. เฟรมจะถูกพลิกกลับด้านซ้าย-ขวา (mirror) แล้วแสดงผลด้วย `cv2.imshow`

## แก้ปัญหาเบื้องต้น

- **หน้าต่างค้าง/มืด หรือไม่พบ landmark ใด ๆ**: ตรวจสอบว่าแสงสว่างเพียงพอ และมือ/ร่างกายอยู่ในกรอบภาพครบถ้วน
- **เจอ `FileNotFoundError` ของไฟล์ `.task`**: ต้องรันสคริปต์จาก root ของโปรเจกต์ เพื่อให้ path แบบ relative (`models/...`) ถูกต้อง
- **ปัญหาสิทธิ์การเข้าถึงกล้องบน macOS**: ไปที่ *การตั้งค่าระบบ → ความเป็นส่วนตัวและความปลอดภัย → กล้อง* แล้วเปิดสิทธิ์ให้กับ terminal หรือ IDE ที่ใช้งาน
