# Coranto | Portfolio Disruption Radar

<div align="center">

[![Next.js](https://img.shields.io/badge/Next.js-16-black?style=for-the-badge&logo=next.js)](https://nextjs.org/)
[![React](https://img.shields.io/badge/React-19-61DAFB?style=for-the-badge&logo=react)](https://react.dev/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.0-3178C6?style=for-the-badge&logo=typescript)](https://www.typescriptlang.org/)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-v4-38B2AC?style=for-the-badge&logo=tailwind-css)](https://tailwindcss.com/)
[![Prisma](https://img.shields.io/badge/Prisma-ORM-2D3748?style=for-the-badge&logo=prisma)](https://www.prisma.io/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-336791?style=for-the-badge&logo=postgresql)](https://www.postgresql.org/)
[![Deploy with Vercel](https://img.shields.io/badge/Vercel-Deployed-000000?style=for-the-badge&logo=vercel)](https://vercel.com/)

**An intelligent portfolio monitoring and thesis disruption radar powered by multi-provider LLMs, automated financial intelligence pipelines, and real-time alerts.**

[Features](#key-features) • [Architecture](#architecture) • [Tech Stack](#tech-stack) • [Quick Start](#getting-started) • [Vercel Deployment](#deploying-to-vercel)

</div>

---

## Overview

**Coranto** is a full-stack investment intelligence platform designed to actively stress-test investment theses against real-time market data, company filings, earnings releases, and curated industry news.

Rather than passive portfolio tracking, Coranto functions as an automated research analyst:
- Formulates structured **falsification criteria** for each asset holding.
- Continuously ingests intelligence via **Tavily AI Search** and **Finnhub**.
- Analyzes potential thesis disruption using **Google Gemini** and **Groq** LLMs.
- Computes real-time **Thesis Health Scores** and delivers high-signal **Email & Web Push** alerts.

---

## Key Features

- **Autonomous Thesis Falsification**: Generates key vulnerability hypotheses and validation questions for every portfolio holding.
- **Multi-Source News & Filings Ingestion**: Scheduled background cron pipelines query live news, SEC filings, and market transcripts.
- **Two-Tier LLM Evaluation Engine**: High-throughput deduplication, clustering, and deep-dive disruption assessment leveraging Google Gemini & Groq.
- **Interactive Portfolio Radar Dashboard**:
  - **Live Ticker Tape & Earnings Radar**: Next-event earnings radar and financial ticker tracking.
  - **Thesis Health Panel**: Dynamic health scores, disruption severities (`INFO`, `LOW`, `MEDIUM`, `HIGH`, `CRITICAL`), and historical verdicts.
  - **AI Copilot**: In-context assistant to query holding risks and recent findings.
- **Automated Delivery & Alerting**:
  - Scheduled daily/weekly digests via **Resend**.
  - Browser **Web Push Notifications** for critical thesis alerts.
  - Cron automation integrated with Vercel Crons.

---

## Architecture

```mermaid
flowchart TD
    subgraph Market ["Market & Intelligence Providers"]
        TH[Tavily Search API]
        FH[Finnhub Market Data]
    end

    subgraph Core ["Coranto Core Pipeline (Next.js & Worker)"]
        Cron["Vercel Cron / Worker Queue"] --> Ingest["Ingestion & Deduplication"]
        Ingest --> LLM["LLM Evaluation (Gemini / Groq)"]
        LLM --> Scorer["Thesis Health & Verdict Engine"]
    end

    subgraph Data ["Persistence Layer"]
        DB[(PostgreSQL via Prisma ORM)]
    end

    subgraph Presentation ["Client Interface & Delivery"]
        UI["Modern Next.js 16 Dashboard"]
        Email["Resend Email Digests"]
        Push["Web Push Notifications"]
    end

    Market --> Ingest
    Scorer --> DB
    DB --> UI
    Scorer --> Email
    Scorer --> Push
```

---

## Tech Stack

| Layer | Technologies |
| :--- | :--- |
| **Framework** | Next.js 16 (App Router), React 19, Server Actions |
| **Language & Styling** | TypeScript (Strict mode), Tailwind CSS v4, Lucide Icons |
| **Database & ORM** | PostgreSQL, Prisma ORM (`@prisma/adapter-pg`), `pg-boss` queue |
| **AI / Inference** | Google Gemini (`@google/genai`), Groq SDK (`groq-sdk`) |
| **Data Providers** | Finnhub API, Tavily Search API, MarketAux |
| **Auth & Security** | NextAuth.js v5 (Auth.js with Prisma Adapter), bcryptjs |
| **Alerts & Monitoring** | Resend, Web-Push (VAPID), Sentry |
| **Deployment** | Vercel (Frontend & Serverless Functions), Neon / Supabase (PostgreSQL) |

---

## Getting Started

### Prerequisites
- **Node.js** 20.x or higher
- **PostgreSQL** database (e.g., [Neon](https://neon.tech), [Supabase](https://supabase.com), or local Postgres)
- API Keys for **Gemini** (or Groq) and **Finnhub**

### 1. Clone & Install

```bash
git clone https://github.com/Adityasoumya/coranto.git
cd coranto
npm install
```

### 2. Configure Environment Variables

Copy the example environment file and update with your credentials:

```bash
cp .env.example .env
```

Key variables:
- `DATABASE_URL`: Connection string to your PostgreSQL instance.
- `AUTH_SECRET`: Random 32-character string (`openssl rand -base64 32`).
- `GEMINI_API_KEY`: API key from [Google AI Studio](https://aistudio.google.com/).
- `FINNHUB_API_KEY`: Free API key from [Finnhub](https://finnhub.io/).
- `TAVILY_API_KEY`: Free API key from [Tavily](https://tavily.com/).

### 3. Initialize the Database

Push the Prisma schema to your PostgreSQL database:

```bash
npx prisma db push
```

*(Optional) Seed market tickers:*
```bash
npm run build
npx tsx scripts/seed-tickers.ts
```

### 4. Run the Development Server

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) in your browser.

---

## Deploying to Vercel

1. **Push to GitHub**: Make sure this repository is pushed to your GitHub account.
2. **Import Project into Vercel**:
   - Go to [vercel.com/new](https://vercel.com/new).
   - Select your `coranto` GitHub repository.
3. **Set Environment Variables**:
   In the Vercel project configuration, add the required environment variables from your `.env.example`:
   - `DATABASE_URL` (Use a hosted Postgres like Neon or Supabase with connection pooling enabled)
   - `AUTH_SECRET`
   - `NEXT_PUBLIC_APP_URL` (e.g., `https://your-project.vercel.app`)
   - `GEMINI_API_KEY`
   - `FINNHUB_API_KEY`
   - `TAVILY_API_KEY`
   - `RESEND_API_KEY` (if using email digests)
4. **Deploy**:
   - Click **Deploy**. Vercel will automatically build the Next.js app and trigger the Prisma client generation step (`prisma generate && next build`).
   - The cron jobs configured in `vercel.json` (`/api/cron/ingest`, `/api/cron/digest`) will automatically activate under your Vercel project's Cron dashboard.

---

## License

This project is licensed under the MIT License.
