# Mindfolio – Investment Behavioral Analytics System

An AI/ML-powered web application that analyses a person's investment history and finds emotional and psychological patterns in their decisions, such as selling winners too early or holding losers too long.

Think of it as a **health check for your investing habits**.

> **Status:** Design and setup stage. Every phase below is still **Planned**, so this README describes a design, not a built system. The project will be called *production-ready* only after its security, reliability, performance and deployment have been tested in a real target environment.

---

## What it does

- Imports a user's trades (manually or by CSV upload) and validates them.
- Scores four behavioral biases with evidence: **disposition effect**, **loss aversion**, **overconfidence** and **herding**.
- Flags unusual trading behaviour with machine learning (Isolation Forest) and groups behaviour patterns (clustering).
- Explains results in plain language using a free local LLM, written only from the real calculated numbers.
- Shows portfolio analytics, historical bias trends and alerts on a dashboard.

## What it is NOT

- Not a stock-tip or buy/sell recommendation tool.
- Does not predict the market. It analyses the investor's behaviour, not the price.
- Does not give financial advice. Output is an *indicator with evidence*, not a diagnosis.

---

## Tech stack

Versions are baselines to be confirmed and locked when Phase 1 starts.

| Area | Technology |
|---|---|

| Backend | Python 3.12, Django 5.2 LTS, Django REST Framework, Simple JWT |

| Database | PostgreSQL 17 |

| Background jobs and cache | Celery, Redis |

| Data and ML | Pandas, NumPy, scikit-learn (Isolation Forest, KMeans/DBSCAN) |

| AI explanations | Ollama with a small open LLM (Qwen or Llama family) |

| Frontend | React, TypeScript, Vite |

| DevOps | Docker, Docker Compose, GitHub Actions, Nginx |

| Quality | pytest, pytest-django, Ruff, mypy, bandit, pip-audit |

---

## Project structure

```
Mindfolio/
├── folio-backend/
│   ├── backend/
│   │   ├── config/            settings, urls, celery
│   │   ├── common/            shared helpers
│   │   ├── accounts/          user, login, JWT
│   │   ├── portfolio/         portfolios, assets
│   │   ├── transactions/      buy/sell, CSV import
│   │   ├── analysis/          features, bias detectors
│   │   ├── ml_engine/         Isolation Forest, models
│   │   ├── tests/
│   │   ├── manage.py
│   │   ├── requirements.txt
│   │   ├── Dockerfile
│   │   ├── .env               (secret, never committed)
│   │   └── .env.example       (safe template)
│   └── docker-compose.yml
├── folio-frontend/            React + TypeScript
├── .gitignore
└── README.md
```

Folders are added phase by phase, not all on day one.

---

## Architecture (summary)

```
Browser -> CDN (static files) / Load balancer (API)
        -> stateless Django API containers
        -> Redis cache -> PostgreSQL
Long jobs: API -> Redis queue -> Celery workers -> PostgreSQL
Explanations: Celery worker -> Ollama (local LLM, with template fallback)
```

Key design rules:

- Views handle HTTP only. Business rules live in a **service layer**.
- Analysis runs in the **background**, so the API replies instantly with a job status.
- Core analytics never depend on the LLM. If it is down, a template explanation is used.
- Every component is labelled honestly as **rule-based**, **statistical** or **ML**.

---

## Getting started

Setup instructions are written step by step as we build. Planned order:

1. Install Python 3.12, Git, VS Code and PostgreSQL.
2. Create the Git repository and `.gitignore`.
3. Create the folder structure.
4. Create a virtual environment.
5. Install packages and create `requirements.txt`.
6. Create the Django project (`config`) and first app (`accounts`).
7. Create `.env` and `.env.example`.
8. Create the PostgreSQL database and connect Django.
9. Run the first migration and start the dev server.
10. Make the first commit.

Commands will be added here as each step is completed and verified.

---

## Build roadmap

| Phase                          | Work                                                                   | Status    |
|--------------------------------|------------------------------------------------------------------------|-----------|
| 1. Foundation + accounts       | Structure, environments, PostgreSQL, custom user, JWT, permissions, CI | 🔴 Planned |

| 2. Investment domain           | Portfolio, assets, transactions, CSV import, holdings                  | 🔴 Planned |

| 3. Analytics + bias rules      | Feature engineering, performance metrics, four detectors               | 🔴 Planned |

| 4. ML engine                   | Isolation Forest, clustering, evaluation, model versioning             | 🔴 Planned |

| 5. AI explanations             | Ollama integration, grounded prompts, fallback                         | 🔴 Planned |

| 6. Async workflows             | Celery queues, job status, retries, alerts, schedules                  | 🔴 Planned |

| 7. Hardening and performance   | Caching, query tuning, security headers, monitoring, load tests        | 🔴 Planned |

| 8. Deployment, HA and recovery | Docker, load balancer, CDN, HTTPS, backups, restore tests              | 🔴 Planned |

## Testing and evaluation

There is no public dataset that labels investors as "biased" or "not biased", so the project does **not** claim real-world accuracy. Instead it uses:

- Synthetic investors with injected behaviour, where the expected result is known.
- Unit tests on small hand-computed examples.
- API, permission and database constraint tests.
- Integration tests of the full flow: import CSV, run analysis, read results.
- Load tests and recovery drills in the later phases.

Anomaly detection results are reported as *"on synthetic data"*, never as real-world accuracy.

---

## Known limitations

- Transaction history alone cannot prove a psychological bias.
- Herding needs market or peer data, so it depends on the data source connected.
- Small histories give low-confidence results, and the system says so.
- LLM text only explains calculated numbers. It is not a source of facts.
- Scaling, availability and recovery designs are plans until tested in the target environment.

---

## Security notes

- Secrets live in `.env` and are never committed. Use `.env.example` as the template.
- JWT with refresh rotation, object-level permissions and rate limiting protect user data.
- Only model files created by this project's own pipeline are loaded.

---

## Documentation

- `Mindfolio_Project_Guide.pdf` – full project guide: concepts, architecture, versions, roadmap and glossary.

## License

To be decided.