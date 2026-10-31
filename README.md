# SoulTalk – Mental Health Chatbot

[![License](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![Python](https://img.shields.io/badge/Python-3.10%20%7C%203.11-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![Node](https://img.shields.io/badge/Node-18%20%7C%2020-339933?logo=node.js&logoColor=white)](https://nodejs.org/)
[![React](https://img.shields.io/badge/React-19.2-61DAFB?logo=react&logoColor=black)](https://reactjs.org/)

---

## Table of Contents
- [Project Overview](#project-overview)
- [Key Features](#key-features)
- [Tech Stack](#tech-stack)
- [Architecture](#architecture)
- [Installation & Setup](#installation--setup)
  - [1️⃣ AI Chatbot (Flask + LangChain)](#1-ai‑chatbot-flask‑langchain)
  - [2️⃣ Backend API (Node.js + Express)](#2-backend-api-nodejs‑express)
  - [3️⃣ Frontend (React + Vite)](#3-frontend-react‑vite)
- [Environment Variables](#environment-variables)
- [Running the Project](#running-the-project)
- [API Reference](#api-reference)
- [Folder Structure](#folder-structure)
- [Future Improvements](#future-improvements)
- [Contributing](#contributing)
- [Author & Contact](#author--contact)

---

## Project Overview
**SoulTalk** is a full‑stack mental‑health chatbot that combines Retrieval‑Augmented Generation (RAG) with mood detection. Users can chat with the AI, receive supportive responses extracted from a curated PDF knowledge base, and get a quick mood label (happy, sad, anxiety, anger, neutral) for each interaction.

The solution is split into three independent parts that communicate over HTTP:
1. **AI Chatbot** – a Flask server that hosts the LLM (Groq) and performs RAG via Pinecone vector store.
2. **Backend API** – a Node.js/Express service that handles user management, JWT authentication, and CRUD for chats, journals, and mood analytics.
3. **Frontend** – a modern React app built with Vite, providing a polished UI for authentication, chat, mood dashboards, and journal entries.

---

## Key Features
- 💬 **RAG‑powered conversations** using LangChain, Pinecone and Groq LLM.
- 😊 **Mood detection** on every user message (single‑word classification).
- 🔐 **JWT‑based authentication** with access‑/refresh‑token rotation.
- 📊 **Mood analytics dashboard** visualising emotional trends.
- 📓 **Personal journal** CRUD with server‑side storage.
- 🛡️ **CORS & secure cookies** configured for cross‑origin usage.
- 📦 **Modular architecture** – each layer can be deployed independently.

---

## Tech Stack
| Layer | Technology | Why? |
|------|------------|------|
| AI Chatbot | **Python 3.10**, **Flask**, **LangChain**, **Groq**, **Pinecone** | Fast prototyping, rich LLM tooling, vector‑search‑as‑a‑service |
| Backend | **Node.js 18+**, **Express**, **MongoDB**, **Mongoose**, **JWT**, **bcrypt** | Scalable REST API, mature ecosystem for auth & data models |
| Frontend | **React 19**, **Vite**, **TailwindCSS**, **React‑Router**, **Recharts**, **React‑Hot‑Toast** | Modern dev experience, utility‑first styling, interactive charts |
| Dev Ops | **npm**, **pip**, **dotenv**, **nodemon**, **vite** | Simple scripts, hot‑reloading for rapid iteration |

---

## Architecture
```mermaid
flowchart LR
    subgraph Frontend[Frontend (React)]
        UI[UI Components] --> API[Calls Backend API]
    end
    subgraph Backend[Backend (Express)]
        Auth[Auth / Users] --> DB[(MongoDB)]
        Chat[Chat Routes] --> AI[AI Chatbot (Flask)]
        Journal[Journal Routes] --> DB
        Mood[Mood Routes] --> DB
    end
    subgraph AI[AI Chatbot (Flask)]
        LLM[Groq LLM] --> Pine[Pinecone Vector Store]
        MoodDetect[Prompt‑based Mood Detection]
    end
    UI -->|HTTPS| Auth & Chat & Journal & Mood
    Chat -->|HTTP| AI
    AI -->|HTTPS| LLM & Pine
```

---

## Installation & Setup
> **Prerequisites**: Node ≥ 18, Python ≥ 3.10, npm, pip, Git.

### 1️⃣ AI Chatbot (Flask + LangChain)
```bash
# Clone the repository (if not already done)
git clone https://github.com/Piyushkohlii/Mental-health-chatbot-MERN-with-RAG.git
cd "E:\Mental Health Chatbot"

# Create a dedicated virtual environment for the Python side
python -m venv SoulTalk
.\\SoulTalk\Scripts\activate   # PowerShell

# Install Python dependencies
pip install -r AI_chatbot/requirements.txt
```
#### Required environment variables (see **Environment Variables** section below)
```bash
# Example .env placed at AI_chatbot/.env
PINECONE_API_KEY=your-pinecone-key
GROQ_API_KEY=your-groq-key
CORS_ORIGIN=http://localhost:5173   # matches React dev server
```
Run the Flask service:
```bash
cd AI_chatbot
python app.py   # server starts on http://0.0.0.0:8080
```

### 2️⃣ Backend API (Node.js + Express)
```bash
cd backend
npm install            # installs Express, Mongoose, etc.
```
Create a `.env` file (see below) with your MongoDB connection string and JWT secrets.
Start the API in development mode:
```bash
npm run dev   # uses nodemon – server runs on http://localhost:5000
```

### 3️⃣ Frontend (React + Vite)
```bash
cd frontend
npm install
npm run dev          # Vite dev server → http://localhost:5173
```
The UI automatically talks to the backend (`http://localhost:5000`) and the AI service (`http://localhost:8080`).

---

## Environment Variables
<details>
<summary>Click to expand</summary>

### Backend (`backend/.env`)
```
MONGODB_URI=mongodb+srv://<user>:<password>@soultalk.yi06dl7.mongodb.net
PORT=5000
CORS_ORIGIN=http://localhost:5173
ACCESS_TOKEN_SECRET=your-access-secret
ACCESS_TOKEN_EXPIRY=1d
REFRESH_TOKEN_SECRET=your-refresh-secret
REFRESH_TOKEN_EXPIRY=5d
```
### AI Chatbot (`AI_chatbot/.env`)
```
PINECONE_API_KEY=your-pinecone-key
GROQ_API_KEY=your-groq-key
CORS_ORIGIN=http://localhost:5173
```
</details>

---

## Running the Project
1. **Start the AI service** (`python app.py`).
2. **Start the backend API** (`npm run dev`).
3. **Start the frontend** (`npm run dev`).
4. Open <http://localhost:5173> in your browser. The app will handle authentication, then you can start chatting.

---

## API Reference
| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/v1/user/register` | Register a new user.
| `POST` | `/api/v1/user/login` | Return access & refresh tokens.
| `POST` | `/api/v1/user/logout` *(protected)* | Invalidate refresh token.
| `GET`  | `/api/v1/user/me` *(protected)* | Get profile of the authenticated user.
| `POST` | `/api/v1/chat` | Forward user message to the Flask AI service; returns `{ response, mood }`.
| `GET`  | `/api/v1/journal` *(protected)* | List user's journal entries.
| `POST` | `/api/v1/journal` *(protected)* | Create a new journal entry.
| `GET`  | `/api/v1/mood` *(protected)* | Retrieve mood analytics for the user.

*All protected routes require the `Authorization: Bearer <access‑token>` header.*

---

## Folder Structure
```
Mental Health Chatbot/
├─ .idea/                     # IDE meta‑data (ignore)
├─ .venv/                     # Python virtual environment (ignore)
├─ AI_chatbot/                # Flask + LangChain service
│   ├─ app.py                 # Entry point
│   ├─ src/                   # helper & prompt modules
│   ├─ data/                  # PDF knowledge base
│   └─ .env                   # API keys & CORS config
├─ backend/                   # Express API
│   ├─ src/
│   │   ├─ app.js             # Express app configuration
│   │   ├─ routes/            # Route definitions
│   │   ├─ controllers/       # Business logic
│   │   ├─ models/            # Mongoose schemas
│   │   └─ middlewares/       # Auth middleware
│   ├─ .env                   # DB & JWT secrets
│   └─ package.json
├─ frontend/                  # React UI (Vite)
│   ├─ src/
│   │   ├─ components/        # UI building blocks
│   │   ├─ context/           # React context for auth & chat state
│   │   ├─ pages/             # Route‑level pages
│   │   └─ App.jsx
│   └─ package.json
└─ README.md                  # ← this file
```

---

## Future Improvements
- 📈 **Real‑time mood graphs** using WebSockets.
- 🗂️ **Admin dashboard** for managing PDFs and monitoring usage.
- 🔐 **OAuth2 login** (Google, Apple) alongside email/password.
- 🧪 **Unit & integration tests** for each layer (Jest, PyTest).
- 📦 **Docker compose** to containerise the three services.

---

## Contributing
Contributions are welcome! Please follow these steps:
1. Fork the repository.
2. Create a feature branch (`git checkout -b feat/awesome-feature`).
3. Ensure code follows existing style (Prettier for JS/TS, Black for Python).
4. Add or update tests where applicable.
5. Open a Pull Request with a clear description of the change.

---

## Author & Contact
**Piyush Kohli** – Full‑stack developer
- GitHub: [PiyushKumaar](https://github.com/PiyushKumaar)
- Email: <piiussh@gmail.com>

Feel free to open an issue for bugs, feature requests, or general questions.

---

*Happy coding and take care of your mental health!*