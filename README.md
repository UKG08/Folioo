# Folio — Deploy to Vercel

## Structure
```
folio-vercel/
├── index.html      ← static frontend (no API key inside anymore)
├── api/
│   └── chat.js      ← serverless function, holds the real Groq key server-side
└── README.md
```

## Deploy steps

1. Push this folder to a GitHub repo (or `vercel` CLI can deploy without git too).
2. Go to https://vercel.com/new and import the repo.
3. Framework preset: **Other** (it's a static file + serverless function, no build step needed).
4. Before deploying, add an Environment Variable:
   - Key: `GROQ_API_KEY`
   - Value: your Groq key
   - Scope: Production (and Preview if you want branch deploys to work)
5. Deploy.

## IMPORTANT — rotate your key

The key that was in the original file (`gsk_0OeK76...`) was exposed in this chat
and in the uploaded file. **Revoke it and generate a new one** at
https://console.groq.com/keys, then use the new one in step 4 above.

## Local testing

```
npm i -g vercel
vercel dev
```
This runs both the static file and the `/api/chat` function locally on the same port,
so `fetch('/api/chat')` in the frontend works exactly like it will in production.
