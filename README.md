<div align="center">
  <img width="100%" src="https://capsule-render.vercel.app/api?type=waving&color=0:172554,35:1d4ed8,70:7c3aed,100:f0abfc&height=190&section=header&text=WikiRun&fontSize=68&fontColor=ffffff&animation=twinkling&fontAlignY=36&desc=One%20article.%20One%20link.%20Keep%20moving.&descAlignY=58&descSize=17" alt="WikiRun animated header" />

  <a href="https://readme-typing-svg.demolab.com"><img src="https://readme-typing-svg.demolab.com?font=Fira+Code&size=18&pause=1100&color=F0ABFC&center=true&vCenter=true&width=720&lines=Pick+a+start+and+target;Choose+the+next+Wikipedia+link;Watch+the+route+unfold" alt="Typing tagline" /></a>

  <p>
    <img src="https://img.shields.io/badge/Python-3.10%2B-3776AB?style=for-the-badge&logo=python&logoColor=white" alt="Python" />
    <img src="https://img.shields.io/badge/FastAPI-WebSockets-009688?style=for-the-badge&logo=fastapi&logoColor=white" alt="FastAPI" />
    <img src="https://img.shields.io/badge/LLM-link%20selection-7C3AED?style=for-the-badge" alt="LLM link selection" />
    <a href="LICENSE"><img src="https://img.shields.io/badge/License-MIT-F0ABFC?style=for-the-badge" alt="MIT License" /></a>
  </p>
</div>

## 🗺️ What it does

WikiRun asks an LLM to navigate from a starting Wikipedia topic to a target by choosing among links on the current article. The backend fetches article summaries and links from Wikipedia, sends progress over a WebSocket, and caches page and run data when MongoDB is configured.

## 🔄 A run

```mermaid
flowchart LR
  A[Start and target] --> B[Fetch Wikipedia page]
  B --> C[Score available links]
  C --> D[LLM picks next link]
  D --> E[Stream hop to browser]
  E -->|target not reached| B
  E -->|target reached or limit| F[Finish route]
```

## 🧠 Selection and fallback

- The backend supplies the current article's candidate links to its configured chat-completion endpoint.
- FastEmbed embeddings and scikit-learn TF-IDF/cosine similarity are available in the project for semantic fallback and scoring.
- A small in-process LRU cache reduces repeated page fetches. MongoDB adds page/run caches with TTL indexes when `MONGODB_URI` is configured.
- The frontend renders the streamed route and article transitions.

## 🧰 Stack

| Part | Tools |
|---|---|
| API | Python, FastAPI, Uvicorn, HTTPX |
| Wikipedia | MediaWiki Action API |
| Link scoring | FastEmbed, NumPy, scikit-learn |
| Model endpoint | OpenAI-compatible endpoint; Groq/Moonshot settings in backend |
| Cache | In-memory LRU, optional MongoDB |
| Client | Static HTML/CSS/JavaScript, WebSocket |

## 🚀 Run locally

```bash
git clone https://github.com/ChristopherJoshy/WikiRun.git
cd wikirun/backend
python -m venv .venv
# Windows: .venv\Scripts\Activate.ps1
# macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
```

Set `GROQ_API_KEY` or `MOONSHOT_API_KEY` in a local environment. `MONGODB_URI` is optional; without it the app uses its in-process cache. Start the API:

```bash
uvicorn app:app --reload --port 8000
```

Then serve `frontend/` with a static HTTP server and point its API/WebSocket settings at the backend. The backend currently permits broad CORS origins for development; configure that before a public deployment.

## 🗂️ Layout

```text
backend/app.py   Wikipedia fetching, caching, scoring, LLM hop selection, WebSocket API
frontend/        browser interface and route visualization
```

## 📄 License

MIT. See [`LICENSE`](LICENSE).

## ⚠️ Notes

The Wikipedia API and model provider are external dependencies. Runs are bounded by a maximum-hop setting and provider/network availability; this is an exploratory route finder, not a guarantee of the shortest path.