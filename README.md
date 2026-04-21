# สรุป Workflow: Line RAG Chatbot for Data Science

## ภาพรวม Workflow

Workflow นี้เป็นระบบ **RAG Chatbot สำหรับ LINE** ที่ใช้ Google Gemini + Supabase Vector Store โดยมีการจำแนกประเภทคำถามก่อนตอบ

---

## การทำงานแต่ละขั้นตอน

### 1. รับข้อความ (Input Layer)
- **Webhook** → รับ POST request จาก LINE API
- **Code: Pull message text** → ดึงข้อความจาก payload ของ LINE

### 2. จำแนกประเภทคำถาม (Classification Layer)
- **AI Agent (Classifier)** → ใช้ Google Gemini Chat Model 1 วิเคราะห์ว่าเป็นข้อความ "greeting" หรือ "faq"
- **Switch (mode: Rules)** → แยก routing ตามผลลัพธ์

### 3a. กรณี Greeting
- **AI Agent (Greeting)** → สร้างคำทักทายด้วย Google Gemini Chat (For Greeting)

### 3b. กรณี FAQ / ถามข้อมูล
- **RAG TO Message** → แปลงข้อความเพื่อค้นหา
- **AI Agent (RAG)** ใช้:
  - Google Gemini Chat Model
  - Simple Memory
  - **Supabase Vector Store** → ค้นหาข้อมูลที่เกี่ยวข้อง
  - **Embeddings Google Gemini** → แปลงข้อความเป็น vector

### 4. ส่งผลลัพธ์ (Output Layer)
- **Code in JavaScript** → จัดรูปแบบข้อความ
- **HTTP Line (POST)** → ส่งคำตอบกลับไปยัง LINE API

---

## README.md สำหรับ GitHub

```markdown
# 🤖 LINE RAG Chatbot for Data Science

A smart LINE chatbot powered by **Google Gemini** and **Supabase Vector Store**
built with n8n workflow automation, featuring intelligent message classification
and Retrieval-Augmented Generation (RAG).

## 📌 Features

- 🔀 **Auto Classification** — แยกแยะระหว่างการทักทายทั่วไปและคำถาม FAQ อัตโนมัติ
- 🔍 **RAG Pipeline** — ค้นหาข้อมูลจาก Vector Database ก่อนตอบ
- 💬 **LINE Integration** — เชื่อมต่อ LINE Messaging API โดยตรง
- 🧠 **Conversation Memory** — จำบริบทการสนทนาด้วย Simple Memory
- ⚡ **Google Gemini** — ขับเคลื่อนด้วย Gemini สำหรับทั้ง Chat และ Embeddings

## 🏗️ Architecture

```
LINE User
   │
   ▼
Webhook (POST)
   │
   ▼
Pull Message Text
   │
   ▼
AI Agent (Classifier) ── Google Gemini
   │
   ├── greeting ──► AI Agent (Greeting) ──────────────────┐
   │                    └── Google Gemini                  │
   │                                                       │
   └── faq ──► RAG To Message                             │
                   │                                       │
                   ▼                                       │
           AI Agent (RAG)                                  │
           ├── Google Gemini Chat                          │
           ├── Simple Memory                               │
           └── Supabase Vector Store                       │
               └── Embeddings (Gemini)                    │
                   │                                       │
                   └───────────────────────────────────────┘
                                   │
                                   ▼
                          Code in JavaScript
                                   │
                                   ▼
                        HTTP POST → LINE API
```

## 🛠️ Tech Stack

| Component | Technology |
|---|---|
| Workflow Engine | n8n |
| LLM | Google Gemini |
| Vector Database | Supabase (pgvector) |
| Embeddings | Google Gemini Embeddings |
| Messaging Platform | LINE Messaging API |
| Memory | n8n Simple Memory |

## ⚙️ Setup

### Prerequisites
- n8n instance (self-hosted or cloud)
- Google Gemini API Key
- Supabase project with pgvector extension
- LINE Messaging API Channel

### Installation

1. **Clone repository**
   ```bash
   git clone https://github.com/yourusername/line-rag-chatbot.git
   ```

2. **Import workflow to n8n**
   - เปิด n8n → Import from file → เลือกไฟล์ `workflow.json`

3. **ตั้งค่า Credentials**
   - Google Gemini API Key
   - Supabase URL + Service Key
   - LINE Channel Access Token + Secret

4. **เตรียม Vector Store**
   - สร้าง table ใน Supabase สำหรับเก็บ embeddings
   - อัปโหลดข้อมูล Data Science ที่ต้องการให้ bot รู้จัก

5. **ตั้งค่า LINE Webhook**
   - นำ URL ของ n8n Webhook ไปใส่ใน LINE Developers Console

## 📂 Project Structure

```
line-rag-chatbot/
├── workflow.json        # n8n workflow export
├── README.md
└── docs/
    └── architecture.png # Workflow diagram
```

## 🔄 How It Works

1. ผู้ใช้ส่งข้อความมาทาง LINE
2. ระบบดึงข้อความและส่งให้ **AI Classifier** วิเคราะห์
3. หากเป็น **การทักทาย** → ตอบกลับด้วย Greeting Agent
4. หากเป็น **คำถาม** → ค้นหาข้อมูลจาก Supabase Vector Store
   และสร้างคำตอบจากข้อมูลที่ค้นพบด้วย RAG
5. ส่งคำตอบกลับผ่าน LINE API

## 📝 License

MIT License
```

---

นำไปใช้ได้เลยครับ! สามารถแก้ไข username, รายละเอียด Supabase table setup หรือเพิ่ม screenshot ของ workflow เข้าไปใน `docs/` ได้ตามต้องการ
