# Raindeer Social

AI-native social media management: brand intelligence, an agent pipeline that
turns a calendar slot into a reviewed draft, and human-in-the-loop scheduling
and publishing — built as a modular monolith, not a pile of microservices.

For the full system design, data model, agent pipeline, and issue roadmap,
see [`raindeer-social-blueprint.md`](./raindeer-social-blueprint.md). This
README covers what's here and how to get running; the blueprint covers why.

## System overview

Four backend domains, one repo:

| Path                | Responsibility |
|----------------------|----------------|
| `apps/api`           | FastAPI monolith — routers per domain (`/brands`, `/calendar`, `/posts`, `/agents`, `/publishing`, `/analytics`) |
| `packages/agents`     | LangGraph agent graphs (research, creative, generation, reviewer, onboarding) |
| `apps/web`            | Next.js frontend |
| `packages/schemas`    | Pydantic + Zod schemas, generated from one OpenAPI source of truth |
| `migrations`          | Alembic migrations for the Postgres schema |

**Stack:** Python 3.11 / FastAPI / SQLAlchemy + Alembic · LangGraph ·
PostgreSQL (Supabase) + pgvector · Redis + Celery · Next.js + TypeScript ·
Docker Compose for local dev.

## Getting started

This covers the backend (`apps/api`). Frontend (`apps/web`) setup will land
with its own issue.

### Prerequisites

- Python 3.11
- PostgreSQL 16+ with the [pgvector](https://github.com/pgvector/pgvector)
  extension available

### 1. Clone and create a virtualenv

```bash
git clone <repo-url>
cd raindeer-social
python3.11 -m venv .venv
source .venv/bin/activate   # Windows: .venv\Scripts\activate
```

### 2. Install dependencies

```bash
pip install -r apps/api/requirements.txt
```

### 3. Set up Postgres

Create the local database and enable pgvector:

```bash
createdb raindeer
psql raindeer -c "CREATE EXTENSION IF NOT EXISTS vector;"
```

### 4. Configure environment variables

```bash
cp .env.example .env
```

Edit `.env` and fill in `DATABASE_URL` (defaults to the `createdb raindeer`
database above) and at least one LLM provider key (`OPENROUTER_API_KEY` or
`OPENAI_API_KEY`).

### 5. Run the API

```bash
uvicorn apps.api.main:app --reload
```

It should boot with no import errors. Check `python --version` (3.11.x),
`which python` (should point into `.venv`), and
`psql raindeer -c "SELECT 1;"` if anything above fails.

## Contributing

See [`CONTRIBUTING.md`](./CONTRIBUTING.md) for branch naming, PR process,
commit style, and the review rules before opening a PR. See
[`CODE_OF_CONDUCT.md`](./CODE_OF_CONDUCT.md) for how we expect people to
treat each other in issues and reviews.

## License

Proprietary — see [`LICENSE.md`](./LICENSE.md).
