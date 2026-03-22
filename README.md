# PlanForge
### AI-powered engineering planning assistant for developers and teams

![Python](https://img.shields.io/badge/Python-3.12-blue)
![FastAPI](https://img.shields.io/badge/FastAPI-0.135-green)
![Claude](https://img.shields.io/badge/Claude-API-orange)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Database-blue)
![Docker](https://img.shields.io/badge/Docker-27.3-blue)
![Railway](https://img.shields.io/badge/Deployed-Railway-blueviolet)

---

## Problem Statement

Developers often start with a product idea but no structured plan to build it.
The gap between **"I have an idea"** and **"I know what to build first"**
slows teams down and leads to poor architectural decisions made under pressure.
PlanForge fills that gap — acting as a senior engineering collaborator
that thinks through scope, architecture, and risk before a single line of code is written.

---

## Solution

PlanForge uses **Anthropic's Claude API** to automatically:

- Analyse requirements and extract goals, users, features, and constraints
- Separate MVP scope from future enhancements
- Propose a stack-aware high-level architecture
- Draft API contracts with endpoints, payloads, and auth rules
- Break work into milestones, tasks, and subtasks
- Surface risks, ambiguities, and integration concerns
- Generate structured development tickets with acceptance criteria and story points
- Save, load, and delete plans with full ticket history

---

## Architecture

```
┌─────────────────────────────────┐
│   Frontend (HTML / CSS / JS)    │
└──────────────┬──────────────────┘
               │ HTTP requests
               ▼
┌─────────────────────────────────┐
│      main.py (FastAPI)          │
└──────────────┬──────────────────┘
               │ MCP context + requirement
               ▼
┌─────────────────────────────────┐
│   planner_service.py (Agent)    │
└──────────────┬──────────────────┘
               │ Anthropic API calls
               ▼
┌─────────────────────────────────┐
│      Claude API (Anthropic)     │
└──────────────┬──────────────────┘
               │ structured plan → saved to
               ▼
┌─────────────────────────────────┐
│       PostgreSQL Database       │
└─────────────────────────────────┘
```

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| AI Model | Claude (Anthropic) — claude-sonnet-4-6 |
| Backend | FastAPI (Python) |
| Database | PostgreSQL |
| Container | Docker |
| Deployment | Railway |
| Frontend | HTML / CSS / JS |
| Env management | python-dotenv |
| Version Control | GitHub |

---

## Project Structure

```
DevProject_AI/
│
├── .env.example                           ← Environment variable template
├── .gitignore                             ← Ignores .env and venv
├── Dockerfile                             ← Docker build config
├── docker-compose.yml                     ← Docker run config
├── README.md                              ← You are here!
│
├── backend/
│   ├── main.py                            ← Entry point + CORS + lifespan
│   ├── requirements.txt                   ← Python dependencies
│   ├── .env                               ← API keys (never commit)
│   └── app/
│       ├── agent_prompt.py                ← Agent system prompt loader
│       ├── core/
│       │   └── config.py                  ← Env loading
│       ├── api/
│       │   ├── routes_plan.py             ← /plan/* endpoints
│       │   └── routes_history.py          ← /history/* endpoints
│       ├── schemas/
│       │   ├── plan.py                    ← Plan request/response models
│       │   ├── task.py                    ← Ticket models
│       │   └── history.py                 ← History models
│       ├── db/
│       │   ├── database.py                ← SQLAlchemy engine and session
│       │   └── models.py                  ← PlanRecord, TicketRecord ORM models
│       ├── services/
│       │   ├── planner_service.py         ← Plan generation logic
│       │   ├── task_service.py            ← Ticket generation logic
│       │   └── claude_service.py          ← Claude API interactions
│       └── mcp_tools/
│           ├── repo_tool.py               ← Repo / GitHub scanner
│           ├── docs_tool.py               ← Docs / URL fetcher
│           └── shell_tool.py              ← Shell command runner
│
├── frontend/
│   ├── index.html                         ← UI structure
│   ├── style.css                          ← Dark theme styles
│   └── script.js                          ← API calls and plan/ticket rendering
│
└── docs/
    ├── agent.md                           ← Agent documentation
    ├── skill.md                           ← Skill workflow documentation
    ├── mcp_tools.md                       ← MCP tools documentation
    └── backend_flow.md                    ← End-to-end request flow
```

---

## MCP Tools (3 Context Tools)

| Tool | Input | Output |
|------|-------|--------|
| `repo_tool` | Local directory path or public GitHub URL | File contents (.py, .md, .txt, .json, .yaml, .yml, .toml) |
| `docs_tool` | URL or local file path | Fetched documentation text |
| `shell_tool` | Shell command (e.g. `pip list`) | Command stdout as context |

> **Note:** Local directory and file paths only work when running the backend locally.
> When deployed on Railway, use a public GitHub URL for repo context instead.

---

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/` | Health check (includes DB status) |
| POST | `/plan/generate-with-context` | Generate plan with optional repo, docs, and shell context |
| POST | `/plan/generate-tasks` | Generate development tickets from a plan |
| GET | `/history` | List all saved plans |
| GET | `/history/{id}` | Load a saved plan with its tickets |
| POST | `/history/save` | Save a plan and its tickets |
| DELETE | `/history/{id}` | Delete a saved plan and its tickets |

---

## Getting Started

### Prerequisites

- Python 3.12+
- PostgreSQL (local install or Railway plugin)
- Docker Desktop (optional)
- Anthropic API key → https://console.anthropic.com
- Git

---

### Step 1 — Clone the Repository

```bash
git clone https://github.com/Shine-23/DevProject-AI.git
cd DevProject-AI
```

---

### Step 2 — Create a Virtual Environment

```bash
python -m venv venv
venv\Scripts\activate        # Windows
source venv/bin/activate     # Mac/Linux
```

---

### Step 3 — Install Dependencies

```bash
cd backend
pip install -r requirements.txt
```

---

### Step 4 — Set Up Environment Variables

Copy the template and fill in your values:

```bash
cp .env.example backend/.env
```

Edit `backend/.env`:

```
ANTHROPIC_API_KEY=your_api_key_here
DATABASE_URL=postgresql://postgres:password@localhost:5432/devproject_ai
```

Get your Anthropic API key at → https://console.anthropic.com

---

### Step 5 — Create the PostgreSQL Database

```bash
psql -U postgres -c "CREATE DATABASE devproject_ai;"
```

---

### Step 6 — Run Locally (Without Docker)

```bash
cd backend
uvicorn main:app --reload
```

Open `frontend/index.html` directly in your browser (no build step needed).

---

### Step 7 — Run with Docker

```bash
cp .env.example backend/.env
# Edit backend/.env — set ANTHROPIC_API_KEY (DATABASE_URL is handled by docker-compose)

docker compose up --build
```

Open → http://localhost:8000

---

### Step 8 — Deploy to Railway

1. Go to → https://railway.app
2. Sign in with GitHub
3. Click "New Project" → "Deploy from GitHub repo"
4. Select `DevProject-AI`
5. Add a **PostgreSQL** plugin — Railway sets `DATABASE_URL` automatically
6. Add environment variable:
   ```
   ANTHROPIC_API_KEY=your_key_here
   ```
7. Click "Deploy" — Railway detects `railway.toml` and uses the Dockerfile automatically
8. Go to Settings → Networking → Generate Domain

---

## Testing the API

### Generate an Engineering Plan

```bash
curl -X POST http://localhost:8000/plan/generate-with-context \
  -H "Content-Type: application/json" \
  -d '{"requirement": "Build a task management app with user auth and team collaboration"}'
```

### Generate Tasks from a Plan

```bash
curl -X POST http://localhost:8000/plan/generate-tasks \
  -H "Content-Type: application/json" \
  -d '{"requirement_summary": "...", "implementation_plan": "..."}'
```

### List Saved Plans

```bash
curl http://localhost:8000/history
```

---

## Live Demo

| | URL |
|---|---|
| 🔗 Live App | https://devproject-ai-production.up.railway.app |
| 📦 GitHub | https://github.com/Shine-23/DevProject-AI |

---

## Future Improvements

- [ ] Voice input — describe your idea verbally
- [ ] Export plans as PDF or Notion pages
- [ ] GitHub Issues integration — push tickets directly to a repo
- [ ] Team workspaces with shared plan history
- [ ] Support for more file types in repo context (.docx, .pdf)
- [ ] Fine-tuned model on engineering planning examples
- [ ] Jira / Linear integration for ticket export

---

## License

MIT License — feel free to use and modify for your own projects.
