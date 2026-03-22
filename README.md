# 🎮 FogOfWar — Gaming Market Intelligence Dashboard

> **Real-time market signals. AI-powered insights. No guesswork.**

[![React](https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB)](https://react.dev)
[![GraphQL](https://img.shields.io/badge/GraphQL-E10098?style=for-the-badge&logo=graphql&logoColor=white)](https://graphql.org)
[![Supabase](https://img.shields.io/badge/Supabase-3ECF8E?style=for-the-badge&logo=supabase&logoColor=white)](https://supabase.com)
[![Groq](https://img.shields.io/badge/Groq_AI-F55036?style=for-the-badge)](https://groq.com)
[![AWS EC2](https://img.shields.io/badge/AWS_EC2-232F3E?style=for-the-badge&logo=amazonaws&logoColor=white)](https://aws.amazon.com/ec2)

---

## 📌 About

**FogOfWar** is a gaming market intelligence dashboard designed for indie game founders and startup scouts. It aggregates real-time signals from **Steam, SteamSpy, Twitch, and Reddit**, processes them through a **Groq-powered LLM pipeline**, and delivers actionable insights via a dark-mode gaming dashboard.

The project was built as a portfolio piece to demonstrate a production-quality full-stack system combining modern AI engineering — RAG pipelines, local embeddings, vector search — with real-time data aggregation and a polished user interface.

> ⚠️ FogOfWar is rate-limited to **30 searches per IP per day** to stay within free-tier API quotas. The dashboard data refreshes automatically every 2 hours via a background scheduler.

---

## ✨ Features

- 🔍 Game search with **semantic similarity** — finds games even with approximate or partial titles using vector embeddings
- 📊 Per-game dashboard with **market data** (owners, price, reviews) sourced from Steam and SteamSpy
- 📺 **Twitch viewership analytics** — live viewer counts, top streamers, and tag trends
- 💬 **Reddit sentiment analysis** — community activity, post volume, and sentiment scoring per game
- 🤖 **AI-generated insights** — Groq LLM synthesizes market and community signals into a concise founder-facing brief
- 💡 **RAG chat interface** — ask natural language questions about any game; answers are grounded in aggregated data stored in pgvector
- 🎯 **Genre-level dashboard** — trending genres with global sentiment and market overview, refreshed every 6 hours
- 🚦 IP-based rate limiting with a live counter showing remaining daily searches

---

## 🏗️ Architecture

FogOfWar is built as a **monorepo** with a React frontend and a Node.js backend, connected via a GraphQL API.

The backend exposes three GraphQL schemas: one for game search and on-demand pipeline execution, one for the dashboard feed driven by the scheduled pipeline, and one for the RAG chat interface.

The **RAG pipeline** generates embeddings locally using `@xenova/transformers` with the `all-MiniLM-L6-v2` model (384 dimensions). Embeddings are stored in **pgvector** inside a custom Supabase schema. When a user searches for a game, the query is embedded and matched against the vector store via cosine similarity before triggering live API calls — this keeps response times fast and avoids redundant LLM calls for recently searched games.

The **LLM pipeline** uses two Groq models: `llama-3.1-8b-instant` for fast classification tasks and `llama-3.3-70b-versatile` for generating the full market insight brief. Steam, Twitch, and Reddit data is fetched in parallel, normalized, and passed as structured context to the LLM.

The backend runs on **AWS EC2** (not serverless) by design — the local embedding model must stay warm in memory between requests, and the `node-cron` scheduler requires a persistent process. PM2 manages the Node.js process with automatic restart on failure. nginx sits in front as a reverse proxy with HTTPS via Let's Encrypt.

---

## 🛠️ Stack

| Layer | Technology |
|---|---|
| Frontend | React 19, TypeScript, Vite, Apollo Client v3, Zustand, Recharts, React Router |
| Backend | Node.js, Express, Apollo Server 5, `@as-integrations/express5` |
| API Layer | GraphQL (game, dashboard, RAG schemas) |
| LLM | Groq (`llama-3.1-8b-instant`, `llama-3.3-70b-versatile`) |
| Embeddings | `@xenova/transformers` — `all-MiniLM-L6-v2` (384 dims, runs locally) |
| Vector DB | pgvector on Supabase (custom `fogofwar` schema) |
| Database | Supabase (PostgreSQL) — rate limits, snapshots, embeddings |
| External APIs | Steam Web API, SteamSpy, Twitch Helix API, Reddit API |
| Scheduler | `node-cron` — dashboard pipeline every 6 hours |
| Deploy (Frontend) | Vercel |
| Deploy (Backend) | AWS EC2 (Ubuntu), PM2, nginx, GitHub Actions CI/CD |

---

## 🌐 Live Demo

🌐 Live demo available — search any game title to trigger the full pipeline.

[![Live Demo](https://img.shields.io/badge/Live_Demo-7F77DD?style=for-the-badge&logo=vercel&logoColor=white)](https://fogofwar.peter-campos.site)

> The first search for a new game may take 10–15 seconds while the pipeline fetches live data from Steam, Twitch, Reddit, and Groq.

---

## 🧠 RAG Pipeline

```
User query
    ↓
Local embedding (all-MiniLM-L6-v2)
    ↓
pgvector similarity search (cosine, top-k)
    ↓
Retrieved game context + live API data
    ↓
Groq LLM (llama-3.3-70b-versatile)
    ↓
Grounded answer
```

The vector store is pre-seeded with the **top 100 Steam games** and enriched progressively as users search — every new game search adds its processed data to the vector store for future RAG queries.

---

## 🚀 Roadmap

- [ ] 🔔 Game watchlist with email alerts on significant market changes
- [ ] 📈 Historical trend comparison across time windows
- [ ] 🏷️ Genre-level deep dive pages
- [ ] 🌐 Multi-language support
- [ ] 📱 Responsive mobile layout

---

## 📸 Screenshots

*Screenshots coming soon*

---

## 📄 License

Portfolio project. Source code not publicly available.  
Built to demonstrate full-stack and AI engineering skills in a production-quality context.

---

## 📬 Contact

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Peter%20Campos-0077B5?style=for-the-badge&logo=linkedin)](https://www.linkedin.com/in/peter-campos)
[![Email](https://img.shields.io/badge/Email-peter.campos.leon%40gmail.com-D14836?style=for-the-badge&logo=gmail&logoColor=white)](mailto:peter.campos.leon@gmail.com)
