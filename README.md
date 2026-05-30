# BehaviourIQ

![CloudCamp Hackathon](https://img.shields.io/badge/event-CloudCamp%20Hackathon-2563EB?style=flat-square)
![Tests](https://img.shields.io/badge/ML%20tests-85%2F87%20passing-22C55E?style=flat-square)
![API E2E](https://img.shields.io/badge/API%20smoke-passing-22C55E?style=flat-square)
![Python](https://img.shields.io/badge/python-3.10+-3776AB?style=flat-square&logo=python&logoColor=white)
![Node](https://img.shields.io/badge/node-22+-339933?style=flat-square&logo=nodedotjs&logoColor=white)
![Next.js](https://img.shields.io/badge/next.js-15-000000?style=flat-square&logo=nextdotjs&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=flat-square&logo=fastapi&logoColor=white)
![Pinecone](https://img.shields.io/badge/Pinecone-hybrid%20search-000000?style=flat-square)
![Docker](https://img.shields.io/badge/docker-compose-2496ED?style=flat-square&logo=docker&logoColor=white)
![AWS](https://img.shields.io/badge/deployed-AWS%20EC2-FF9900?style=flat-square&logo=amazonaws&logoColor=white)
![Grafana](https://img.shields.io/badge/monitoring-Grafana-F46800?style=flat-square&logo=grafana&logoColor=white)
![Live](https://img.shields.io/badge/live-biq--web.nxs.bd-22C55E?style=flat-square)
![Kaggle](https://img.shields.io/badge/data-Kaggle-20BEFF?style=flat-square&logo=kaggle&logoColor=white)
![License](https://img.shields.io/badge/license-unlicensed-lightgrey?style=flat-square)

Behavior-aware commerce for e-commerce. BehaviourIQ reads shopper signals and turns them into personalized prices, intent-based search and churn alerts. Built as a CloudCamp hackathon demo.

**This repo** holds the platform overview (`README.md`) and the shared product catalog in `data/`. The JSON files under `data/` are sourced from **Kaggle** e-commerce datasets, cleaned and normalized for the demo storefront and Pinecone index. Application code lives in three separate GitHub repos. We did not use a monorepo during the hackathon; each teammate forked and worked in parallel, then synced into the repos below.

[![behavioriq-api](https://img.shields.io/badge/behavioriq--api-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/saminyasar004/behavioriq-api)
[![behavioriq-ml-service](https://img.shields.io/badge/behavioriq--ml--service-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/smabdullah2002/behavioriq-ml-service)
[![biq-web](https://img.shields.io/badge/biq--web-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/maamspy/biq-web)

### Live deployment (AWS EC2)

| Service | URL |
|---------|-----|
| Storefront + dashboard | [biq-web.nxs.bd](http://biq-web.nxs.bd/) |
| API | [biq-api.nxs.bd](http://biq-api.nxs.bd/) |
| ML | [biq-ml.nxs.bd](http://biq-ml.nxs.bd/) |
| Grafana (logs + metrics) | [biq-grafana.nxs.bd/dashboards](https://biq-grafana.nxs.bd/dashboards) |

| Repo | GitHub | Stack | Role |
|------|--------|-------|------|
| [behavioriq-api](https://github.com/saminyasar004/behavioriq-api) | saminyasar004 | Hono, Prisma, PostgreSQL, Redis, Socket.IO | Backend, events, pricing, dashboard API |
| [behavioriq-ml-service](https://github.com/smabdullah2002/behavioriq-ml-service) | smabdullah2002 | FastAPI, scikit-learn, Pinecone, Hugging Face | Intent, churn, semantic search, reranking |
| [biq-web](https://github.com/maamspy/biq-web) | maamspy | Next.js 15, React, Tailwind | Storefront and admin dashboard |

All three services run on a single **AWS EC2** instance (Docker Compose on the host). Pinecone search runs as a managed cloud index outside the box.

---

## What it does

BehaviourIQ sits between a merchant storefront and their analytics stack. It ingests live shopper events, builds behavioral profiles and drives three outcomes:

1. **Dynamic pricing** based on purchase intent and churn risk
2. **Intent-based search** with semantic retrieval reranked by shopper behavior
3. **Churn prevention** using RFM predictions, real-time alerts and win-back offers

```text
Shopper (web) -> API (5000) -> ML (8001)
                     |              |
                     +-- Postgres    +-- Pinecone hybrid index
                     +-- Redis
```

Product catalog: `data/products.json` in **this repo** (Kaggle-sourced). API and ML mount or read the same file at deploy time.

---

## Data (`data/`)

This repo ships the shared catalog only. All JSON under `data/` comes from **Kaggle**:

| File | Source | Used for |
|------|--------|----------|
| `data/products.json` | Kaggle e-commerce / fashion product dataset | Storefront catalog, Pinecone hybrid index, dynamic pricing base prices |

The raw Kaggle exports were cleaned into a single JSON schema (`id`, `name`, `desc`, `category`, `brand`, `price`, `rating`, `image`, etc.). API and ML both read this file so search, pricing and browse stay in sync.

Churn model training uses a separate Kaggle dataset inside the ML repo.

## Status

| Area | Status |
|------|--------|
| Storefront with personalized prices | Working |
| Intent-aware search (Pinecone hybrid) | Working (TF-IDF fallback without keys) |
| Churn model (Kaggle Online Retail RFM) | Trained and deployed |
| Dynamic pricing engine | Working |
| Admin dashboard | Working |
| Real-time Socket.IO alerts | Working |
| Pixel event ingestion | Working |
| GTM / Meta Pixel integration | Documented pixel contract |
| Observability (Loki, Grafana, Prometheus) | ML stack via Docker |
| AWS EC2 deployment | All services on one EC2 host |
| System CPU and bottleneck monitoring | Prometheus + Grafana on EC2 |
| Production hardening | Hackathon scope |

Working end-to-end demo. Real churn training, Pinecone semantic search and behavior-aware reranking. Ready to pitch.

---

## Tests

| Suite | Repo | Result |
|-------|------|--------|
| ML endpoint tests (intent, churn, search rerank) | behavioriq-ml-service | **85/87 passed** (97.7%) |
| API E2E smoke test | behavioriq-api | Passing (`yarn test:e2e`) |

ML test breakdown:

| Endpoint | Tests | Status |
|----------|-------|--------|
| `/ml/churn-predict` | 15 | PASS |
| `/ml/churn-predict` (comprehensive) | 17 | PASS |
| `/ml/intent-score` | 5 | PASS |
| `/health` | 1 | PASS |
| `/ml/search-rerank` | 49 | PASS (1 warning in original suite) |

Run ML tests from `behavioriq-ml-service/test_results/tests/`. See [TEST_RESULTS_SUMMARY.md](https://github.com/smabdullah2002/behavioriq-ml-service/blob/main/test_results/TEST_RESULTS_SUMMARY.md).

---

## Business pitch

### Problem

Merchants lose revenue when:

- Search returns keyword matches instead of what the shopper wants
- Pricing is static so hot buyers and at-risk churners see the same price
- Retention is reactive and churn shows up too late

### Solution

BehaviourIQ is a drop-in intelligence layer:

| Capability | Value |
|------------|-------|
| Semantic search + intent reranking | Better conversion on queries like "cheap running shoes under $80" |
| Dynamic pricing | Protect margin on hot buyers, nudge hesitant browsers, win back churners |
| Churn prediction + alerts | Retention before the customer leaves |
| Behavioral pixel | Works with **Google Tag Manager** and **Meta Pixel**. No rip-and-replace |
| Admin dashboard | Live KPIs, pricing audit log and what-if simulator |

### Who it's for

- D2C brands on Shopify or custom stacks
- Marketplaces that care about search quality and margin
- Growth teams already running GTM and Meta Pixel

### Revenue ideas

- SaaS tier by monthly events or MAU
- Performance fee on incremental conversion from dynamic pricing
- Enterprise: custom churn models and per-catalog Pinecone indexes

---

## Features

### Storefront

- Product catalog with pagination
- Intent-aware search ("premium nike shoes", "cheap gifts under $50")
- Personalized prices per user
- Demo persona switcher (hot buyer, deal hunter, churning customer)

### Business developers (GTM + Meta Pixel)

Merchants track events via **GTM dataLayer** and **Facebook Meta Pixel**. BehaviourIQ accepts the same event shapes through its pixel API.

| GTM / Meta event | BehaviourIQ `event_type` |
|------------------|--------------------------|
| `view_item` / `ViewContent` | `product_view` |
| `add_to_cart` / `AddToCart` | `cart_add` |
| `search` | `search` |
| `begin_checkout` / `InitiateCheckout` | `checkout` |
| page exit / time on page | `page_exit` |

Setup:

1. Keep GTM and Meta Pixel on the storefront
2. Add a GTM tag or server forwarder that POSTs to `/api/events/batch`
3. BehaviourIQ rebuilds profiles and drives pricing, search and churn alerts

Full contract: [PIXEL_EVENT_FORMAT.md](https://github.com/saminyasar004/behavioriq-api/blob/main/PIXEL_EVENT_FORMAT.md)

### Dynamic pricing

- **Intent score** (0-100) from visits, cart adds, time on page, search-to-view ratio, price affinity and recency
- **Churn probability** (0-1) from RFM model. Above 65% triggers win-back discounts

Action types: `premium`, `nudge_discount`, `moderate_discount`, `win_back`

### Churn prediction

- Logistic regression trained on **Kaggle Online Retail**
- RFM features with calibrated probabilities
- Real-time `churn:alert` on Socket.IO namespace `/dashboard`

### ML / search pipeline

```text
query
  -> dense embeddings (Hugging Face) + sparse BM25 vectors
  -> Pinecone hybrid retrieval (top 100)
  -> intent analysis (price_sensitive, premium, comparison, gift, urgent_buy, exploratory)
  -> behavior-aware reranking (vector + intent + pricing + churn)
  -> ranked results with explanations
```

- Index: `behavioriq-products` (dotproduct, dense + sparse)
- Fallback: offline TF-IDF when `PINECONE_API` or `HF_API_KEY` are missing
- Check `GET /health` for `search_backend: pinecone_hybrid`

### Performance monitoring and bottlenecks

We track whole-system and per-service metrics to spot slow paths and resource pressure on the EC2 host.

**What we scrape (ML service via `/metrics`):**

| Metric | What it tells us |
|--------|------------------|
| `behavioriq_system_cpu_percent` | Host CPU load on EC2 |
| `behavioriq_process_cpu_percent` | ML process CPU usage |
| `behavioriq_process_threads` | Thread count under load |
| `behavioriq_process_open_fds` | File descriptor pressure |
| `behavioriq_gpu_utilization_percent` | GPU use when available |
| `behavioriq_http_request_duration_seconds` | Per-endpoint latency |
| `behavioriq_step_duration_seconds` | Pipeline step timing (embed, retrieve, rerank) |

Prometheus scrapes `/metrics` every 15s. **[Grafana](https://biq-grafana.nxs.bd/dashboards)** shows ML logs (Loki) and metrics: CPU, request latency and pipeline step breakdowns so we can see whether search, churn or pricing is the bottleneck.

Logs go to **Loki** on the EC2 host for request traces and error correlation.

---

## Architecture

Production runs on **AWS EC2**. Web, API, ML, Postgres, Redis, Prometheus, Grafana and Loki all live on the same instance behind Docker Compose.

```text
AWS EC2
  biq-web.nxs.bd        -> storefront + dashboard
  biq-api.nxs.bd        -> Postgres, Redis
  biq-ml.nxs.bd         -> Pinecone (managed, external)
  biq-grafana.nxs.bd    -> logs (Loki) + metrics (Prometheus)
```

```mermaid
flowchart LR
  subgraph Web["biq-web.nxs.bd"]
    SF[Storefront]
    AD[Admin Dashboard]
  end

  subgraph API["biq-api.nxs.bd"]
    EV[Event Ingestion]
    PR[Pricing Engine]
    SR[Search Proxy]
    DB[(PostgreSQL)]
    RD[(Redis)]
    SIO[Socket.IO]
  end

  subgraph ML["biq-ml.nxs.bd"]
    IS[Intent Score]
    CP[Churn Predict]
    PC[(Pinecone)]
    RR[Reranker]
  end

  GTM[GTM / Meta Pixel] -.->|forward events| EV
  SF --> PR
  SF --> SR
  AD --> API
  EV --> DB
  PR --> IS
  PR --> CP
  SR --> RR
  RR --> PC
  CP --> SIO
  PR --> SIO
```

### Key endpoints

| Method | Path | Description |
|--------|------|-------------|
| POST | `/api/events/batch` | Pixel / GTM events |
| GET | `/api/products` | Catalog |
| GET | `/api/pricing/:productId?userId=` | Personalized price |
| GET | `/api/search?q=&userId=` | Intent-aware search |
| GET | `/api/dashboard/*` | KPIs, churn, pricing log |
| POST | `/ml/intent-score` | Session intent |
| POST | `/ml/churn-predict` | Churn probability |
| POST | `/ml/search` | Full search pipeline |

- API docs: [biq-api.nxs.bd/docs](http://biq-api.nxs.bd/docs) (local: `http://localhost:5000/docs`)
- ML docs: [biq-ml.nxs.bd/docs](http://biq-ml.nxs.bd/docs) (local: `http://localhost:8001/docs`)

### Service URLs

| Service | Live | Root | Health | Docs |
|---------|------|------|--------|------|
| Web | [biq-web.nxs.bd](http://biq-web.nxs.bd/) | / | n/a | n/a |
| API | [biq-api.nxs.bd](http://biq-api.nxs.bd/) | [/](http://biq-api.nxs.bd/) | [/health](http://biq-api.nxs.bd/health) | [/docs](http://biq-api.nxs.bd/docs) |
| ML | [biq-ml.nxs.bd](http://biq-ml.nxs.bd/) | [/](http://biq-ml.nxs.bd/) | [/health](http://biq-ml.nxs.bd/health) | [/docs](http://biq-ml.nxs.bd/docs) |
| Grafana | [biq-grafana.nxs.bd](https://biq-grafana.nxs.bd/dashboards) | dashboards | n/a | n/a |

Local dev uses the same paths on ports `3001` (web), `5000` (api) and `8001` (ml).

---

## Setup

### Prerequisites

- Node.js 22+ and Yarn 1.x (API)
- Python 3.10+ (ML, optional if using Docker)
- Docker and Docker Compose
- npm (Web)

### Repository layout

This overview repo plus three service repos. No monorepo; teams forked during the hackathon and merged into the canonical repos above.

```text
BehaviourIQ/                    <- this repo (README + data only)
  README.md
  data/
    products.json               <- shared catalog (single source of truth)

behavioriq-api/                 <- git clone (saminyasar004)
behavioriq-ml-service/          <- git clone (smabdullah2002)
biq-web/                        <- git clone (maamspy)
```

```bash
mkdir BehaviourIQ && cd BehaviourIQ
# clone this overview repo first, then the three services alongside it

git clone https://github.com/saminyasar004/behavioriq-api.git
git clone https://github.com/smabdullah2002/behavioriq-ml-service.git
git clone https://github.com/maamspy/biq-web.git
```

Point `PRODUCT_CATALOG_PATH` (API/ML) or Docker volume mounts at `../data/products.json` from this repo.

---

## Environment variables

Copy each `.env.example` to `.env` before first run.

### API (`behavioriq-api/.env`)

```bash
cd behavioriq-api && cp .env.example .env
```

```env
DATABASE_URL=postgresql://postgres:postgres@localhost:5432/behavioriq
DOCKER_DATABASE_URL=postgresql://postgres:postgres@postgres:5432/behavioriq
PORT=5000
NODE_ENV=development
REDIS_URL=redis://localhost:6380
DOCKER_REDIS_URL=redis://redis:6379
ML_SERVICE_URL=http://localhost:8001
GEMINI_AI_API_KEY=your_gemini_api_key_here
```

| Mode | Settings |
|------|----------|
| Local API | `DATABASE_URL`, `REDIS_URL`, `ML_SERVICE_URL=http://localhost:8001` |
| Docker API | Compose uses `DOCKER_*` URLs. Set `ML_SERVICE_URL=http://host.containers.internal:8001` |

### ML (`behavioriq-ml-service/.env`)

```bash
cd behavioriq-ml-service && cp .env.example .env
```

```env
LOKI_URL=http://localhost:3100/loki/api/v1/push
APP_ENV=hackathon
PINECONE_API=your_pinecone_api_key
HF_API_KEY=your_huggingface_api_key
```

Without Pinecone and Hugging Face keys, search falls back to offline TF-IDF.

### Web (`biq-web/.env`)

```bash
cd biq-web && cp .env.example .env
```

```env
NEXT_PUBLIC_API_BASE_URL=http://127.0.0.1:5000
```

Production (EC2):

```env
NEXT_PUBLIC_API_BASE_URL=http://biq-api.nxs.bd
```

---

## Docker

No root compose file. Start ML and API separately. Run Web locally or on the same EC2 host.

On **AWS EC2**, clone all three service repos next to this overview repo, copy `.env` files, then run `docker compose up` in `behavioriq-ml-service` and `behavioriq-api`. Start `biq-web` with `npm run dev` or a process manager.

### Ports

| Port | Service |
|------|---------|
| `3001` | Web |
| `5000` | API |
| `5432` | PostgreSQL |
| `6380` | Redis |
| `8001` | ML |
| `3000` | Grafana |
| `3100` | Loki |
| `9090` | Prometheus |

Web runs on **3001** so it does not clash with Grafana on **3000**.

### ML stack

```bash
cd behavioriq-ml-service
docker compose up --build
```

Starts ML (8001), Loki (3100), Grafana (3000), Prometheus (9090).

View logs and metrics: [biq-grafana.nxs.bd/dashboards](https://biq-grafana.nxs.bd/dashboards) (production) or http://localhost:3000 locally.

### API stack

Set in `behavioriq-api/.env`:

```env
ML_SERVICE_URL=http://host.containers.internal:8001
```

```bash
cd behavioriq-api
docker compose up --build
```

Runs Postgres, Redis, migrations, seed and API on port 5000.

### Web

```bash
cd biq-web
npm install && npm run dev
```

Open [biq-web.nxs.bd](http://biq-web.nxs.bd/) (production) or http://localhost:3001 (local)

### Quick start (3 terminals)

**Terminal 1 (ML):**

```bash
cd behavioriq-ml-service && cp .env.example .env && docker compose up --build
```

**Terminal 2 (API):**

```bash
cd behavioriq-api && cp .env.example .env
# set ML_SERVICE_URL=http://host.containers.internal:8001
docker compose up --build
```

**Terminal 3 (Web):**

```bash
cd biq-web
echo "NEXT_PUBLIC_API_BASE_URL=http://127.0.0.1:5000" > .env.local
npm install && npm run dev
```

### All-local (infra in Docker only)

```bash
cd behavioriq-api && docker compose up -d postgres redis

cd behavioriq-ml-service
python -m pip install -r requirements.txt
uvicorn main:app --host 0.0.0.0 --port 8001 --reload

cd behavioriq-api
yarn install && yarn db:generate && yarn db:push && yarn db:seed && yarn dev

cd biq-web && npm install && npm run dev
```

### Health checks

```bash
curl http://localhost:5000/
curl http://localhost:5000/health
curl http://localhost:5000/docs

curl http://localhost:8001/
curl http://localhost:8001/health
curl http://localhost:8001/docs
```

---

## Demo personas

After `yarn db:seed` in behavioriq-api:

| Persona | Intent | Churn | Pricing |
|---------|--------|-------|---------|
| Sara (hot buyer) | ~87 | Low | No discount |
| Lee (deal hunter) | ~44 | Medium | ~12% nudge |
| Anna (churning) | ~21 | 0.79 | ~24% win-back |

Toggle personas on the storefront at [biq-web.nxs.bd](http://biq-web.nxs.bd/). Admin dashboard: [biq-web.nxs.bd/admin/dashboard](http://biq-web.nxs.bd/admin/dashboard)

---

## Socket.IO events

Namespace `/dashboard` on the API origin:

| Event | Trigger |
|-------|---------|
| `churn:alert` | Churn threshold crossed |
| `intent:high` | High purchase intent |
| `pricing:decision` | New personalized price |

---

## Troubleshooting

| Symptom | Fix |
|---------|-----|
| Web cannot reach API | Check `NEXT_PUBLIC_API_BASE_URL` |
| API cannot reach ML | Local: `http://localhost:8001`. Docker: `http://host.containers.internal:8001` |
| Poor search results | Set `PINECONE_API` and `HF_API_KEY`. Check `/health` for `pinecone_hybrid` |
| DB errors | `docker compose ps` in behavioriq-api. Re-run `yarn db:push && yarn db:seed` |
| Port 3000 conflict | Grafana uses 3000. Web uses 3001 |

---

## Docs

- [behavioriq-api README](https://github.com/saminyasar004/behavioriq-api)
- [behavioriq-ml-service README](https://github.com/smabdullah2002/behavioriq-ml-service)
- [biq-web README](https://github.com/maamspy/biq-web)
- [ML integration contract](https://github.com/smabdullah2002/behavioriq-ml-service/blob/main/project_markdowns/behavioriq-ml-integration.md)
- [Pixel event format](https://github.com/saminyasar004/behavioriq-api/blob/main/PIXEL_EVENT_FORMAT.md)

---

## License

No license file yet. Add one to each service repo before public use.

Built for **CloudCamp hackathon**.

| Repo | Maintainer |
|------|------------|
| [behavioriq-api](https://github.com/saminyasar004/behavioriq-api) | [@saminyasar004](https://github.com/saminyasar004) |
| [behavioriq-ml-service](https://github.com/smabdullah2002/behavioriq-ml-service) | [@smabdullah2002](https://github.com/smabdullah2002) |
| [biq-web](https://github.com/maamspy/biq-web) | [@maamspy](https://github.com/maamspy) |
