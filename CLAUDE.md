### 🔄 Project Awareness & Context
- **Always read `PLANNING.md`** at the start of a new conversation to understand the project's architecture, goals, style, and constraints.
- **Check `TASK.md`** before starting a new task. If the task isn’t listed, add it with a brief description and today's date.
- **Use consistent naming conventions, file structure, and architecture patterns** as described in `PLANNING.md`.
- **Use venv_linux** (the virtual environment) whenever executing Python commands, including for unit tests.
- **Use Python 3.11+** for the backend and **Node.js 20+** for the frontend. Use npm or pnpm for frontend tasks (build, lint, test, dev server).
- **Pin dependencies** (backend `requirements.txt` or `pyproject.toml`; frontend `package.json` + lockfile). Avoid unpinned wildcard versions in production.

### 🧱 Code Structure & Modularity
- **Never create a file longer than 500 lines of code.** If a file approaches this limit, refactor by splitting it into modules or helper files.
- **Organize code into clearly separated modules**, grouped by feature or responsibility.
- **Top-level layout**:
  - `backend/` (Python FastAPI)
    - `app/main.py` (app factory/startup)
    - `app/api/` (routers/endpoints)
    - `app/models/` (ORM models)
    - `app/schemas/` (pydantic models)
    - `app/services/` (business logic)
    - `app/core/` (settings, security, logging, CORS)
    - `tests/` (pytest suite)
  - `frontend/` (React + TypeScript + Vite)
    - `src/components/`
    - `src/pages/` or `src/routes/`
    - `src/hooks/`
    - `src/lib/` (api client, utils)
    - `src/types/`
    - `src/__tests__/` (unit tests) or colocated `*.test.tsx`
- **Imports**: keep imports clear and consistent (prefer relative within packages; use tsconfig paths for FE where helpful).
- **Environment variables**: backend via `python-dotenv` and a settings module; frontend via `.env` files (Vite uses `VITE_` prefixed vars).

### 🧪 Testing & Reliability
- **Backend (pytest)**
  - Use `pytest`, `pytest-asyncio` for async routes, and `httpx` `AsyncClient` to test FastAPI.
  - Place tests under `backend/tests/` mirroring app structure.
  - Run with coverage and fail below threshold (e.g., 80%).
- **Frontend (Vitest + React Testing Library)**
  - Use `vitest` with `jsdom` + `@testing-library/react` and `@testing-library/user-event`.
  - Place tests under `frontend/src/__tests__/` or next to components as `*.test.tsx`.
- **E2E (optional)**: Use `playwright` for UI flows.
- **Each feature** should include at least:
  - 1 test for expected use
  - 1 edge case
  - 1 failure case

### ✅ Task Completion
- **Mark completed tasks in `TASK.md`** immediately after finishing them.
- Add new sub-tasks or TODOs discovered during development to `TASK.md` under a “Discovered During Work” section.

### 📎 Style & Conventions
- **Backend: Python (FastAPI). Frontend: TypeScript + React (Vite).**
- **Backend standards**
  - Follow **PEP8**; format with **black**; lint with **ruff** (including import sorting); type-check with **mypy** (strict where feasible).
  - Use **FastAPI** + **uvicorn**; **pydantic v2** for request/response validation; **SQLAlchemy 2.x** or **SQLModel** for ORM; **Alembic** for migrations if a DB is used.
  - Add **CORS** via `fastapi.middleware.cors.CORSMiddleware` and configure allowed origins for the FE dev server.
  - Prefer dependency injection with FastAPI `Depends`, split routers per domain, and keep business logic out of route handlers.
  - Use structured logging (standard logging or `structlog`) and meaningful error messages.
- **Frontend standards**
  - Enable **TypeScript strict mode**.
  - Enforce **ESLint (typescript-eslint)** and **Prettier**.
  - Prefer **Vite** tooling; organize **npm scripts**: `dev`, `build`, `test`, `lint`, `typecheck`.
  - For server state, prefer **TanStack Query (React Query)**; for schema validation, prefer **Zod**.
  - Use a thin HTTP wrapper over `fetch` or **axios**; centralize API calls in `src/lib/api/`.
  - Consider a UI library (e.g., MUI or Tailwind CSS) as needed; keep component APIs simple and typed.
- **Pre-commit hooks**: Configure pre-commit to run `ruff`, `black`, `mypy` (backend) and `eslint`, `prettier`, `tsc --noEmit` (frontend) on staged files.

### 🔗 API Contract & Types
- Rely on FastAPI's OpenAPI. Generate frontend types from the backend schema to keep FE/BE in sync.
  - Recommended: **openapi-typescript** to generate `frontend/src/types/api.d.ts` from `backend`'s `/openapi.json`.
  - Use the generated types in `src/lib/api/` to ensure typed requests/responses.

### 🔒 Security & Config
- **Never commit secrets**. Use `.env` files locally; configure environment variables in deployment.
- Validate all external input with pydantic (backend) and Zod (frontend) when handling user-entered data.
- Configure sensible CORS, timeouts, and request size limits.

### 🧾 Example Test Expectations (non-binding)
- Backend route tests use `httpx.AsyncClient` against the FastAPI app with dependency overrides for DB/services.
- Frontend component tests render with Testing Library, assert accessible roles/text, and mock network with MSW or fetch mocks.

### 📚 Documentation & Explainability
- **Update `README.md`** when new features are added, dependencies change, or setup steps are modified.
- **Comment non-obvious code** and ensure everything is understandable to a mid-level developer.
- When writing complex logic, **add an inline `# Reason:` comment** explaining the why, not just the what.

### 🧠 AI Behavior Rules
- **Never assume missing context. Ask questions if uncertain.**
- **Never hallucinate libraries or functions** – only use known, verified Python and JavaScript packages.
- **Always confirm file paths and module names** exist before referencing them in code or tests.
- **Never delete or overwrite existing code** unless explicitly instructed to or if part of a task from `TASK.md`.