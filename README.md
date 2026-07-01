# folio — PDF Assistant

A single-page AI assistant for reading, chatting with, and understanding PDF documents. Upload one or more PDFs, ask questions, and get answers grounded in the actual document content — with source citations across multiple files.

**Repo:** github.com/UKG08/folio-vercel

---

## Features

- 📄 **Drag-and-drop PDF upload** — client-side parsing via PDF.js, no file ever leaves the browser except the extracted text sent for chat
- 🔍 **Smart chunking** — documents are split into overlapping ~1500-character chunks; the most relevant chunks are retrieved per question instead of stuffing the whole document into context
- 💬 **Multi-document chat** — ask questions scoped to one document or across your entire uploaded library, with the assistant citing which source each answer came from
- 📖 **Built-in PDF viewer** — page navigation, zoom, rendered directly from the uploaded file
- 🗂️ **Document library** — track uploaded docs, page count, and chunk count per file
- 🕘 **Chat history** per session
- 🎨 Fully custom dark UI (Playfair Display / Syne / DM Mono), no framework — single static HTML file
- 🔐 **Secure API key handling** — Groq calls are proxied through a serverless function; the key never touches the browser

---

## Tech stack

| Layer | Tech |
|---|---|
| Frontend | Vanilla JS, single-file HTML/CSS (no build step) |
| PDF parsing | PDF.js (via CDN) |
| LLM | Groq API — `llama-3.3-70b-versatile` |
| Backend | Vercel Serverless Function (`api/chat.js`) — proxies Groq requests, keeps API key server-side |
| Hosting | Vercel |

---

## Project structure

```
folio-vercel/
├── index.html      # entire frontend app — UI, PDF parsing, chunking, chat logic
├── api/
│   └── chat.js      # serverless proxy — attaches GROQ_API_KEY server-side, forwards to Groq
└── README.md
```

---

## Setup & deployment

### 1. Get a Groq API key
Create one at [console.groq.com/keys](https://console.groq.com/keys).

> ⚠️ Never hardcode the key into `index.html`. It's read from an environment variable by `api/chat.js` at request time.

### 2. Deploy to Vercel

**Via CLI:**
```bash
npm i -g vercel
vercel login
vercel
vercel env add GROQ_API_KEY   # paste your key when prompted
vercel --prod
```

**Via GitHub + dashboard:**
1. Push this repo to GitHub
2. Import at [vercel.com/new](https://vercel.com/new)
3. Framework preset: **Other** (no build step)
4. Add environment variable `GROQ_API_KEY` before deploying
5. Deploy

### 3. Local development
```bash
vercel dev
```
Serves the static frontend and `/api/chat` function together on one local port.

---

## How it works

1. User drops a PDF → PDF.js extracts text client-side
2. Text is split into overlapping chunks (`chunkText`, 1500 chars, 200 overlap)
3. On each question, relevant chunks are pulled from the active doc(s) and sent as context
4. The frontend calls `/api/chat` (not Groq directly) with the system prompt + context + question
5. `api/chat.js` attaches the real Groq key and forwards the request server-side
6. Response comes back and renders into the chat UI

---

## Roadmap / ideas
- [ ] Persist documents across sessions (currently in-memory per page load)
- [ ] Real embedding-based retrieval instead of naive chunk inclusion
- [ ] Streaming responses (`stream: true`) for faster perceived latency
- [ ] Export chat + citations as markdown/PDF
