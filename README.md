# 🤖 DocTalk — AI Document Reader Bot for Telegram

An intelligent Telegram bot that can **read, summarize, and answer questions** about documents you send — powered by multiple AI models and built with n8n automation.

---

## ✨ Features

- 📄 **PDF** — Full text extraction and summarization
- 📊 **Excel (XLSX/XLS)** — Reads spreadsheet data, explains columns and rows
- 📋 **CSV** — Parses and summarizes tabular data
- 📝 **Word (DOCX)** — Extracts and reads document content
- 💬 **Q&A** — Ask anything about your uploaded document
- 🌐 **Bilingual** — Responds in English by default, switches to Indonesian if you write in Indonesian
- 🧠 **Multi-model AI** — Uses Google Gemini, OpenAI, and Anthropic Claude with automatic fallback

---

## 🛠️ Tech Stack

| Tool | Purpose |
|------|---------|
| [n8n](https://n8n.io) | Workflow automation engine |
| [Telegram Bot API](https://core.telegram.org/bots/api) | Chat interface |
| Google Gemini | Primary AI model |
| OpenAI GPT | Fallback AI model |
| Anthropic Claude | Fallback AI model |
| SQLite | n8n local database |

---

## 🔄 How It Works

```
Telegram User
     │
     ▼
Schedule Trigger (polls Telegram every N seconds)
     │
     ▼
HTTP Request → Telegram getUpdates API
     │
     ▼
Code in JavaScript (parse message, detect file type, deduplication)
     │
     ├── No document → AI Agent (chat response)
     │
     └── Has document
           │
           ├── PDF  → Extract from File (PDF)  ──────────┐
           ├── XLSX → Extract from File (XLSX)            │
           │          → Aggregate → Code Node             ├──→ AI Agent → Send Message
           ├── CSV  → Extract from File (CSV)             │
           └── DOCX → Move to Base64 → Code Node ────────┘
```

### Key Design Decisions

- **Aggregate Node** before AI Agent for XLSX/CSV — prevents the bot from sending one message per row (fixes multi-message spam bug)
- **ZIP parsing with zlib** for DOCX — extracts `word/document.xml` directly without external libraries
- **Message deduplication** via n8n static data — prevents processing the same message twice
- **Fallback AI models** — if primary model hits quota/rate limit, automatically switches to next model

---

## 📸 Screenshots

### Reading an Excel File
> Bot summarizes a 31-row employee dataset across multiple branches

![Excel Summary](screenshots/excel-summary.png)

### Reading a Word Document
> Bot reads and summarizes a DOCX file

![DOCX Summary](screenshots/docx-summary.png)

### n8n Workflow
> Full automation workflow in n8n editor

![n8n Workflow](screenshots/workflow.png)

---

## 🚀 Setup

### Prerequisites
- [Node.js](https://nodejs.org) v18+
- n8n installed globally: `npm install -g n8n`
- Telegram Bot Token (from [@BotFather](https://t.me/BotFather))
- API keys: Google Gemini / OpenAI / Anthropic

### Environment Variables

Create or edit `%USERPROFILE%\.n8n\.env`:

```env
EXECUTIONS_TIMEOUT=300
N8N_EXECUTION_TIMEOUT=300
NODE_FUNCTION_ALLOW_BUILTIN=zlib,buffer,stream
NODE_FUNCTION_ALLOW_EXTERNAL=*
```

### Running

```bash
n8n start
```

Or with env vars inline:

```bash
set NODE_FUNCTION_ALLOW_BUILTIN=zlib,buffer,stream && set NODE_FUNCTION_ALLOW_EXTERNAL=* && n8n start
```

Then open `http://localhost:5678` and import the workflow JSON.

---

## 📁 Project Structure

```
.
├── workflow.json        # n8n workflow export
├── README.md
└── screenshots/
    ├── excel-summary.png
    ├── docx-summary.png
    └── workflow.png
```

---

## 🤝 Contributing

Feel free to fork this project and adapt it for your own use case. Pull requests are welcome!

---

## 📄 License

MIT License — free to use and modify.

---

> DocTalk — Built with ❤️ using n8n + Telegram + AI
