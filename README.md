# Simulink Requirement Generator

ระบบสร้างเอกสารข้อกำหนด (Software Requirement Specification) จากไฟล์ MATLAB Simulink (.slx) โดยอัตโนมัติ ด้วยการประยุกต์ใช้ Large Language Model (LLM) ที่พัฒนาโดยองค์กรร่วมกับแนวทาง Retrieval-Augmented Generation (RAG) เพื่อลดเวลาการจัดทำเอกสาร Software Requirement ที่แต่เดิมต้องทำแบบแมนนวล

---

## 📌 ที่มาของโปรเจกต์

โปรเจกต์นี้เป็นผลงานจากการปฏิบัติงานสหกิจศึกษา ตำแหน่ง **Software Developer Intern**

- **ผู้จัดทำ:** นางสาวสิริภัสสร ศรีวัณโณ รหัส 65102010424
- **สาขาวิชา/คณะ:** สาขาวิชาวิทยาการคอมพิวเตอร์ คณะวิทยาศาสตร์ มหาวิทยาลัยศรีนครินทรวิโรฒ
- **สถานที่ปฏิบัติงาน:** บริษัทเคพีไอที เทค (ประเทศไทย) จำกัด
- **อาจารย์ที่ปรึกษาสหกิจศึกษา:** ผศ.ดร.นภา แซ่เบ๊, อ.ดร.บรรพตรี คมขำ
- **พนักงานที่ปรึกษา:** นายกวินภพ จิโน (Associate Technical Specialist)
- **ปีการศึกษา:** 1/2568 (ระยะเวลา 16 สัปดาห์)

**เอกสารอ้างอิง:** รายงานผลการปฏิบัติงานสหกิจศึกษา วิชาสหกิจศึกษา คณะวิทยาศาสตร์ มหาวิทยาลัยศรีนครินทรวิโรฒ, ปีการศึกษา 1/2568

---

## 🧠 ภาพรวมโปรเจกต์

บริษัทเคพีไอทีเป็นบริษัทระดับนานาชาติที่พัฒนาโซลูชันซอฟต์แวร์สำหรับอุตสาหกรรมยานยนต์ กระบวนการเดิมในการรับไฟล์ MATLAB Simulink จากลูกค้าและจัดทำเอกสาร Software Requirement เพื่อประกอบการทดสอบซอฟต์แวร์นั้นใช้เวลานานและทำแบบแมนนวล โปรเจกต์นี้จึงพัฒนาระบบที่:

1. รับไฟล์ `.slx` (Simulink Model) ซึ่งเป็นไฟล์ `.zip` ที่เก็บโครงสร้างข้อมูลแบบ `.xml`
2. แตกไฟล์และดึงโครงสร้างความสัมพันธ์ของระบบย่อยออกมาในรูปแบบ Tree Data Structure
3. ป้อนข้อมูลแต่ละกิ่งของโครงสร้างเข้าสู่ Prompt Template ที่ออกแบบตามหลัก **RACE Framework** (Role, Action, Context, Expectation)
4. ส่งข้อมูลผ่าน LangChain ไปยังโมเดล LLM ภายในองค์กร (KGPT API) เพื่อสร้างเนื้อหาแบบ Markdown
5. ประเมินคุณภาพผลลัพธ์อัตโนมัติ (Coverage, Structure, Readability, Redundancy, Faithfulness)
6. แปลงผลลัพธ์เป็นไฟล์ Word (.docx) / PDF พร้อมส่งออกให้ผู้ใช้งาน
7. เชื่อมต่อกับ Chatbot บนเว็บไซต์ HIL Commissioning พร้อมระบบ Tool Management สำหรับจัดการเครื่องมือ AI อื่น ๆ ในอนาคต

---

## 🛠️ เทคโนโลยีที่ใช้ (Tech Stack)

### ภาษาโปรแกรม
- **TypeScript / JavaScript** — พัฒนาส่วน Backend และ Business Logic โดย TypeScript ช่วยเพิ่ม Type Safety
- **Python** — ใช้สำหรับงานที่เกี่ยวข้องกับ AI/LLM, การประมวลผลไฟล์ Simulink และการประเมินผลเอกสาร
- **SQL** — จัดการฐานข้อมูล MySQL

### Backend
- **Node.js** — รัน JavaScript ฝั่งเซิร์ฟเวอร์
- **Express** — สร้าง RESTful API และจัดการ routing
- **FastAPI** *(ช่วงแรกของโปรเจกต์ ก่อนเปลี่ยนมาใช้ Express เพื่อความง่ายในการดูแลรักษาและความเข้ากันของระบบ)*

### Frontend
- **Svelte / SvelteKit** — เฟรมเวิร์กแบบ Component-Based คอมไพล์เป็น JavaScript ขนาดเล็กและเร็ว ใช้สร้าง UI และจัดการ Routing

### ฐานข้อมูล
- **MySQL** — จัดเก็บข้อมูล Session, ประวัติการแชต, การตั้งค่าเครื่องมือ (Tool), Keyword และ Metadata ของไฟล์ ออกแบบตามหลัก Normalization
- **MySQL Workbench** — ออกแบบและจัดการฐานข้อมูลผ่าน GUI

### AI / LLM Stack
- **Company AI API (KGPT)** — API ภายในองค์กร (คล้าย ChatGPT) รองรับโมเดลหลายประเภท เช่น `kgpt-multimodal` (32k tokens, รับภาพได้), `kgpt-mini-text`, `kgpt-reasoning-text` (สูงสุด 64k tokens), `kgpt-text-embedding`
- **LangChain** — เชื่อมต่อโมเดล LLM ผ่าน `ChatOpenAI`, จัดการ `ChatPromptTemplate`, `StrOutputParser`, และ Chain (`prompt | llm | parser`)
- **RACE Prompt Framework** — โครงสร้าง Prompt ประกอบด้วย Role, Action, Context, Expectation
- **Retrieval-Augmented Generation (RAG) Concept** — ใช้ในการออกแบบระบบระยะแรก ก่อนปรับมาใช้การแตกโครงสร้าง XML แบบ Tree โดยตรงเพื่อแก้ปัญหาข้อมูลตกหล่น
- **File Hub API** — API ภายในองค์กรสำหรับจัดการไฟล์ (คล้าย NotebookLM)

### Data Structures
- **Tree Data Structure** — จัดการโครงสร้างความสัมพันธ์ของไฟล์ Simulink (parent/leaf node traversal)

### สถาปัตยกรรมซอฟต์แวร์
- **Three-Tier Architecture** — แยก Frontend / Backend / Database
- **MVC (Model-View-Controller)** — แยกส่วนจัดการข้อมูล การแสดงผล และตรรกะการทำงาน
- **REST API** — สื่อสารระหว่าง Frontend และ Backend

### เอกสารและไฟล์
- **docx (Node.js library)** — สร้างไฟล์ `.docx` ตามมาตรฐาน Office Open XML รองรับข้อความ ตาราง Header/Footer Styles และรูปภาพ

### เครื่องมือพัฒนาและ DevOps
- **VS Code** — เครื่องมือหลักในการเขียนและแก้ไขโค้ด
- **Git / GitLab** — Version Control, Merge Request, CI/CD
- **Bun** — JavaScript Runtime ความเร็วสูง พร้อมตัวจัดการแพ็กเกจในตัว
- **SoapUI** — ทดสอบ API (SOAP และ REST)
- **draw.io** — ออกแบบ Diagram (Flowchart, ER Diagram)
- **Virtual Environments (Python)** — แยก dependency ของ Python

### การประเมินผลคุณภาพเอกสาร (Custom Evaluation Module)
เนื่องจากไม่มีชุดข้อมูลสำหรับวัด Accuracy/F1 Score โดยตรง จึงพัฒนาโมดูลประเมินผลเชิงคุณภาพและบริบทขึ้นเอง ประกอบด้วย:
- **Coverage Score** — ตรวจสอบความครอบคลุมของ checklist
- **Structure & Heading Score** — ตรวจสอบความครบถ้วนและลำดับของหัวข้อ (regex-based)
- **Readability (Flesch–Kincaid Grade Level)** — ประเมินความอ่านง่ายของเนื้อหา (รองรับภาษาไทย/อังกฤษ)
- **Repetition/Redundancy Score** — ใช้เทคนิค Shingles (n-grams) และ Jaccard Similarity ตรวจจับเนื้อหาซ้ำซ้อน
- **Unit Consistency Check** — ตรวจสอบความสอดคล้องของหน่วยวัด (เช่น km/m, THB/USD) ด้วย Regular Expressions
- **Faithfulness Score** — วัดความสอดคล้องของเนื้อหากับแหล่งข้อมูลต้นทาง ด้วย Jaccard Similarity ระหว่าง Shingles
- **Weighted Overall Score** — รวมคะแนนถ่วงน้ำหนัก: Coverage (25%), Structure (35%), Clarity (15%), Non-redundancy (15%), Faithfulness (10%)

---

## 📊 คุณสมบัติหลักของระบบ

- ระบบสร้าง Software Requirement จากไฟล์ `.slx` อัตโนมัติ พร้อมส่งออกเป็น Word/PDF
- Chatbot Interface สำหรับโต้ตอบด้วยภาษาธรรมชาติ พร้อมจัดการหลาย Session และอัปโหลดไฟล์แบบลากวาง
- ระบบจัดการไฟล์และโฟลเดอร์แบบ CRUD ครบวงจร
- ระบบ Tool Management สำหรับสร้าง/แก้ไข/เปิดปิดเครื่องมือ AI และกำหนดเงื่อนไขไฟล์ที่รองรับ
- ระบบยืนยันตัวตนด้วย API Token ที่ปลอดภัย

---

## 📄 บรรณานุกรม (อ้างอิงจากรายงานต้นฉบับ)

1. AiPromptsX. (n.d.). *Complete Guide to AI Prompt Frameworks: APE, RACE, ROSES & More.* https://aipromptsx.com
2. Bhargav, S. (2025). *GenAI 101: AI, ML, LLMs Explained.* https://medium.com/@sudhanshu.bhargav/genai-101-ai-ml-llms-explained-2e324aa2a0b0
3. docx.js. (n.d.). *Documentation.* https://docx.js.org
4. KGPT API Documentation. (n.d.). https://kgpt.ai/docs
5. LangChain. (n.d.). *Home - Docs by LangChain.* https://python.langchain.com/docs
6. Prompt Engineering Guide. (n.d.). *Basics of Prompting.* https://www.promptingguide.ai
7. Svelte. (n.d.). *Svelte Documentation.* https://svelte.dev

---

## 📝 หมายเหตุ

โปรเจกต์นี้เป็น Proof-of-Concept ที่พัฒนาขึ้นภายในบริษัท และมีข้อจำกัดด้าน Token ของ LLM รวมถึงไม่สามารถทำ Fine-Tuning โมเดลได้โดยตรง จึงแก้ปัญหาด้วยการทำ Chunking ตามโครงสร้าง Tree, Prompt Engineering และการจำกัดขอบเขตข้อมูลที่ส่งเข้าโมเดลในแต่ละรอบ
