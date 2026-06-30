# ⚡ Lead Intelligence Engine (LIE)

![React](https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB)
![FastAPI](https://img.shields.io/badge/FastAPI-005571?style=for-the-badge&logo=fastapi)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Tailwind](https://img.shields.io/badge/Tailwind_CSS-38B2AC?style=for-the-badge&logo=tailwind-css&logoColor=white)

An autonomous, AI-powered B2B sales enablement and Open Source Intelligence (OSINT) platform. 

The Lead Intelligence Engine accepts a target company's basic information, runs a multi-layered background intelligence sweep to bypass anti-bot protections, and pipes the raw web data through a Large Language Model (Llama 3 / Groq). The result is a highly structured, actionable JSON payload containing organizational metrics, technical quotes, and behavioral sales signals.

---

## 🏗️ System Architecture

This project utilizes a cleanly decoupled micro-frontend architecture for maximum performance and deployment flexibility.

* **Frontend (`/frontend`):** A blisteringly fast Single Page Application (SPA) built with React and Vite. It utilizes state-driven UI layouts and Tailwind CSS to display live AI parsing, token metrics, and lead history without heavy client-side routing.
* **Backend (`/backend`):** An asynchronous Python API built on FastAPI. It manages the OSINT scraping loop (BeautifulSoup4, Serper API, DuckDuckGo Search), handles the LLM inference bus (Groq), and ensures data persistence via a local SQLite ledger.

---

## ✨ Key Features

* **Multi-Stage OSINT Scraper:** Intelligently cascades from direct HTML extraction to premium B2B directory routing (ZoomInfo, Crunchbase) if standard HTTP requests are blocked by firewalls.
* **Dynamic JSON Sanitization:** Includes a custom regular expression engine that repairs corrupted LLM JSON outputs (missing brackets, trailing commas) before parsing, guaranteeing zero syntax crashes.
* **Automated Quoting Engine:** Calculates dynamic service pricing (e.g., VAPT, Network Audits) by evaluating base rates against user-defined scope, technical complexity, and AI-deduced enterprise scaling multipliers.
* **Persistent Historical Ledger:** Utilizes a lightweight SQLite database (`LIE.db`) to log all processed leads, enabling instant historical lookups and repeat-lead warnings.
* **Token Economics Dashboard:** Intercepts LLM usage metrics and renders them directly in the UI, allowing operators to monitor context-window efficiency in real-time.

---

## 🚀 Local Development Setup

### 1. Prerequisites
* Node.js & npm (v18+)
* Python 3.10+
* API Keys for **Groq** (LLM), **Serper** (Search Directory), and optionally **Firecrawl**.

### 2. Backend Initialization (FastAPI)
Navigate to the backend directory, set up your Python environment, and start the Uvicorn server.

```bash
cd backend
python -m venv venv

# Windows
.\venv\Scripts\activate
# Mac/Linux
source venv/bin/activate

pip install -r requirements.txt
