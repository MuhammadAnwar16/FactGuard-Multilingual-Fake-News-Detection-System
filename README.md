
# FactGuard — Multilingual Fake News Detection

FactGuard helps detect misinformation across languages by combining NLP modules, an optional custom fact-checking model, and a simple UI for non-technical users. It is built as a Django backend (API + analysis modules) and a React + Vite frontend.

**What problem does this solve?**
- Identifies likely misinformation in short claims or headlines.
- Extracts named entities and semantic cues to help analysts trace claims.
- Provides a reproducible query history for auditing and exporting results.

## How it works (high level)
1. User submits text from the frontend or sends a POST to `/analyze/`.
2. Backend runs NER (`ner_module.py`) and semantic analysis (`semantic_module.py`).
3. Optionally calls an external custom fact-check model (configured via `CUSTOM_MODEL_URL`) to get a detailed verdict and sources.
4. Similarity checks (`similarity_module.py`) run if a second text is provided.
5. Results are returned to the frontend and saved to `QueryHistory` for later review or export.

## Architecture
- Frontend: React + Vite (`factguard/`) — UI, PDF export, account flows.
- Backend: Django REST + DRF (`ner_project/`, `ner_app/`) — analysis endpoints and history API.
- Optional external model: LLM / RAG host (private), accessed via `CUSTOM_MODEL_URL` & `CUSTOM_MODEL_API_KEY`.

## Tech stack
| Layer | Technology |
|---|---|
| Backend | Python, Django, Django REST Framework |
| NLP | Custom modules in `ner_app` (`ner_module.py`, `semantic_module.py`, `similarity_module.py`) |
| Frontend | React, Vite, Tailwind CSS |
| Storage | SQLite (default) / Django ORM |
| Auth / Extras | Firebase, Supabase (client libs included in frontend) |

## Quick start — Backend
1. Create a Python virtual environment and activate it:

```bash
python -m venv .venv
source .venv/bin/activate
```
2. Install dependencies:

```bash
pip install -r requirements.txt
```
3. Set required environment variables (example):

```bash
export CUSTOM_MODEL_URL="http://private-model-host/query"
export CUSTOM_MODEL_API_KEY="your-api-key"
```
4. Run migrations and start the server:

```bash
python manage.py migrate
python manage.py runserver 0.0.0.0:8000
```

The API root (home) responds at `/` and the analyze endpoint is at `/analyze/`.

## Quick start — Frontend
1. Install and run the frontend dev server:

```bash
cd factguard
npm install
npm run dev
```
2. Open the address shown by Vite (usually `http://localhost:5173`).

## API Examples
- Analyze endpoint (POST /analyze/)

```bash
curl -X POST http://localhost:8000/analyze/ \
  -H "Content-Type: application/json" \
  -d '{"text":"The Eiffel Tower is in Berlin."}'
```

Sample response (abridged):

```json
{
  "entities": [...],
  "sentiment": {...},
  "similarity": null,
  "customModel": {
    "success": true,
    "verdict": "VERIFIED",
    "credibility": "High (4/5)",
    "summary": "...",
    "sources": ["https://example.com/source"]
  }
}
```

- History API (DRF router): `GET /history/` returns stored `QueryHistory` items.

## Screenshots
Logo and build assets (local):

![FactGuard Logo](factguard/public/FactGuardLogo.png)
![Vite Logo](factguard/public/vite.svg)

Previous UI screenshots (hosted):

![Screenshot 1](https://github.com/user-attachments/assets/14a5f2a3-3115-4c66-8272-dee7cd4ff73e)
![Screenshot 2](https://github.com/user-attachments/assets/8ce9d8f7-f7d9-4674-88a5-a34b88da5444)
![Screenshot 3](https://github.com/user-attachments/assets/d526e921-4d63-46e2-adde-9a38beda21b5)
![Screenshot 4](https://github.com/user-attachments/assets/c8a1946c-d647-4be0-b3bb-41e98a72e158)
![Screenshot 5](https://github.com/user-attachments/assets/777ffb6e-6f70-43e8-a17c-0c11dd227a6e)
![Screenshot 6](https://github.com/user-attachments/assets/4b681681-bb05-4672-b0f1-48742d7d1442)
![Screenshot 7](https://github.com/user-attachments/assets/a38c67cf-8bde-4856-87fe-796100d4c323)
![Screenshot 8](https://github.com/user-attachments/assets/d40f0a55-2811-48ad-aa53-47a6fb4b7851)

## Useful commands
- Export query history (management command):

```bash
python manage.py export_history
```

- Build frontend for production:

```bash
cd factguard
npm run build
```

## Configuration notes
- `CUSTOM_MODEL_URL` and `CUSTOM_MODEL_API_KEY` configure the external fact-checking model. If not set, the backend will attempt to hit the fallback URL and may return error responses.
- The repository includes integration code for Firebase and Supabase in the frontend; add your credentials in the frontend environment as needed.

## Contributing
- Please open issues for bugs or feature requests.
- For code contributions, create a branch and submit a pull request describing the change.

## Maintainers / Contact
- Asfandyar.Shabbir77@gmail.com
- anwarimdad@gmail.com

---
