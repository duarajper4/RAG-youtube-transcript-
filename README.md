# RAG-youtube-transcript-
# 🎥 YouTube RAG Q&A System

> **Ask any question about any YouTube video — powered by Retrieval-Augmented Generation**

[![Python](https://img.shields.io/badge/Python-3.10+-blue.svg)](https://python.org)
[![Groq](https://img.shields.io/badge/LLM-Groq%20LLaMA%203.3-orange.svg)](https://groq.com)
[![FAISS](https://img.shields.io/badge/Vector%20DB-FAISS-green.svg)](https://github.com/facebookresearch/faiss)
[![Gradio](https://img.shields.io/badge/UI-Gradio%204.x-purple.svg)](https://gradio.app)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

---

## What it does

Paste a YouTube URL → the system fetches the transcript, chunks it, embeds it into a local FAISS vector database, and lets you have a grounded conversation with the video content via Groq's blazing-fast LLaMA 3.3-70B model.

**All processing is free and open-source.** No OpenAI key needed.

---

## Architecture

```
YouTube URL
    │
    ▼
YouTubeTranscriptApi          ← free, no API key
    │  raw transcript text
    ▼
RecursiveCharacterTextSplitter  ← LangChain text splitter
    │  ~500-char chunks with 50-char overlap
    ▼
SentenceTransformer            ← all-MiniLM-L6-v2, runs on CPU
    │  384-dim normalised embeddings
    ▼
FAISS IndexFlatIP              ← cosine similarity, in-memory
    │
    │◄── user query (embedded on-the-fly)
    │    top-4 most similar chunks retrieved
    ▼
Groq LLaMA-3.3-70B-Versatile  ← 128K context, ~800 tok/s
    │  grounded, factual answer
    ▼
Gradio UI                      ← two-tab chat interface
```

---

## Quickstart (Google Colab)

1. Open `youtube_rag_qa.py` in Colab (or copy-paste the cells).
2. Add your Groq API key to **Colab Secrets** (🔑 icon in left sidebar) as `GROQ_API_KEY`.  
   Get a free key at [console.groq.com](https://console.groq.com).
3. Run the install cell, then the main cell.
4. Click the Gradio **public link** that appears.

---

## Local setup

```bash
git clone https://github.com/YOUR_USERNAME/youtube-rag-qa.git
cd youtube-rag-qa

python -m venv .venv
source .venv/bin/activate      # Windows: .venv\Scripts\activate

pip install -r requirements.txt

export GROQ_API_KEY="gsk_..."  # or create a .env file

python youtube_rag_qa.py
```

Open `http://localhost:7860` in your browser.

---

## Tech stack

| Component | Library | Why |
|-----------|---------|-----|
| Transcript | `youtube-transcript-api` | Free, no YouTube API key |
| Embeddings | `sentence-transformers` | Local, CPU-friendly, 384-dim |
| Vector DB | `faiss-cpu` | Fast similarity search, in-memory |
| Text split | `langchain-text-splitters` | Robust recursive splitting |
| LLM | `groq` + LLaMA-3.3-70B | ~800 tok/s, free tier, 128K context |
| UI | `gradio` 4.x | Fast prototyping, public sharing link |

---

## Deployment options

### Option A — Hugging Face Spaces (recommended for portfolios)
1. Create a new Space → Gradio SDK.
2. Upload `youtube_rag_qa.py` as `app.py` and `requirements.txt`.
3. Add `GROQ_API_KEY` in Space Settings → Repository secrets.
4. Free, permanent URL, no server management.

### Option B — Vercel + FastAPI backend
1. Wrap the RAG functions in a FastAPI app (`/api/process`, `/api/chat`).
2. Build a Next.js frontend calling those endpoints.
3. Deploy API on Railway/Render (free tier), frontend on Vercel.

### Option C — Docker + any cloud
```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY youtube_rag_qa.py .
CMD ["python", "youtube_rag_qa.py"]
```

---

## Student resources you can leverage

| Resource | How to use |
|----------|-----------|
| **GitHub Student Developer Pack** | Free Namecheap domain + hosting credits |
| **Azure for Students** | $100 credit — deploy on Azure Container Apps |
| **Hugging Face** | Free Spaces hosting (always free) |
| **Groq** | Free API tier (generous rate limits) |
| **MongoDB Atlas** | Free 512 MB cluster — persist chat history |

---

## Roadmap (upgrade ideas)

- [ ] Persistent FAISS index saved to disk between sessions
- [ ] Multi-video support — query across several videos at once
- [ ] Timestamp citations — show where in the video the answer comes from
- [ ] Streaming LLM responses (Groq supports SSE streaming)
- [ ] Chat history saved per session to MongoDB Atlas
- [ ] Docker + GitHub Actions CI/CD

---

## License

MIT — free to use, modify, and deploy.
