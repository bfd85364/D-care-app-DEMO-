# D-care-app-DEMO-

# download-link
https://drive.google.com/file/d/11BLR-3ndl_VDxocrzM5VM9lh4ScBfyzp/view?usp=sharing

# description:

https://github.com/user-attachments/assets/41f8884e-9016-403c-8f27-b9b38915a064

[All accounts is dummy-account for test]

# D-Care

**D-Care** is a mobile chronic-disease (diabetes) health management application developed as a cross-departmental capstone design project. It combines an LLM-RAG chatbot for consultation/lifestyle guidance with a machine-learning risk classification and explainability (XAI) module, delivered as a native mobile app.

## Overview

Managing a chronic condition like diabetes requires continuous, personalized guidance — not a one-time diagnosis. D-Care addresses this by combining:

1. **LLM-RAG Chatbot** — A retrieval-augmented chatbot that answers health/lifestyle questions grounded in curated medical reference documents.
2. **Risk Insight Tab** — A LightGBM-based diabetes risk classifier with SHAP-driven variable prioritization, translated into rule-based, human-readable feedback (not LLM-generated explanations).
3. **Persistent Health Profile & Chat History** — A 5-page health profile intake form and full chat history, stored per authenticated user.

This is a joint project between a **Computer Engineering** track (mobile app, backend, LLM-RAG integration) and an **AI/ML** track (model training, SHAP/XAI pipeline design, feature engineering from KNHANES survey data).

## Architecture

```
┌─────────────────────┐        HTTPS / JWT        ┌──────────────────────────┐
│   Flutter App        │ ─────────────────────────▶│   FastAPI Backend         │
│  (Android / iOS)      │◀───────────────────────── │   (AWS EC2) │
│  - Riverpod            │                          │  - JWT Auth                │
│  - flutter_secure_     │                          │  - SQLite (diabetes.db)    │
│    storage (JWT)       │                          │  - LangChain + FAISS RAG   │
└─────────────────────┘                            │  - OpenRouter API (Claude)  │
                                                     │  - LightGBM + SHAP         │
                                                     └──────────────────────────┘
```

- **Frontend:** Flutter (Android Studio), Riverpod for state management, `flutter_secure_storage` for JWT persistence, Dio as HTTP client.
- **Backend:** FastAPI on AWS EC2, SQLAlchemy ORM over SQLite.
- **LLM-RAG:** LangChain + FAISS vector store + HuggingFace embeddings, calling an LLM via the **OpenRouter API** (currently Claude Haiku, chosen for stronger Korean-language quality).
- **Risk Model:** LightGBM (`lgbm_base.txt`, `lgbm_full.txt`, `model_meta.json`), trained on KNHANES (Korean National Health and Nutrition Examination Survey) data by the AI/ML team, with a rule-based correction layer for known fasting-glucose-related feature leakage.
- **Explainability:** SHAP TreeExplainer for variable prioritization, mapped to rule-based feedback messages (authored by the AI/ML team) in the Insight Tab.
- **Dev tunneling:** ngrok is used to expose the local/EC2 backend across AP-isolated networks (classroom / dormitory) during development and testing on physical devices.

> **Note:** The LLM-RAG prompt schema (`app_schemas.py`) and the ML model's input schema (`health_profile_model.dart`) are intentionally kept separate. The chatbot and the risk-prediction pipeline share no state or logic.

## Features

- 🔐 JWT-based authentication on all critical endpoints
- 💬 LLM-RAG powered chatbot with persistent chat history
- 📊 Risk Insight Tab: SHAP-based variable prioritization + rule-based feedback
- 📝 5-page health profile intake form
- 🌐 Cross-network development support via ngrok

## Getting Started

### Prerequisites

- Flutter SDK (Android Studio recommended)
- Python 3.10+
- An OpenRouter API key
- (Optional) AWS EC2 instance for backend hosting

### Backend Setup

```bash
# Clone the repository
git clone https://github.com/<your-org>/D-care_01.git
cd D-care_01

# Create and activate a virtual environment
python3 -m venv venv
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt

# Configure environment variables
cp .env.example .env
# Edit .env and set:
#   SECRET_KEY=<your-jwt-secret>
#   OPENROUTER_API_KEY=<your-openrouter-key>

# Generate the database (or regenerate after any schema change)
python create_db.py

# Run the server
uvicorn main:app --host 0.0.0.0 --port <BACKEND_PORT>
```

### Frontend Setup

```bash
cd D-care_app  # Flutter project directory

# Install packages
flutter pub get

# Configure API base URL
# In .env (Flutter), set API_BASE_URL to your machine's wireless LAN IPv4 address
# (NOT 10.0.2.2 — that only works for the Android emulator, not physical devices)

# Run on a connected device / emulator
flutter run
```

### Cross-Network Access (ngrok)

When developing across restricted or AP-isolated networks (e.g., switching between classroom and dormitory Wi-Fi), expose the backend with ngrok:

```bash
ngrok http <BACKEND_PORT>
```

Update the Flutter app's `API_BASE_URL` to the ngrok forwarding URL. Physical device testing uses the backend's configured port (see internal config — not published here).

> Windows users: remember to add/adjust an inbound firewall rule for the dev port whenever the network changes.

## Environment Variables

| Variable | Location | Description |
|---|---|---|
| `SECRET_KEY` | Backend `.env` | JWT signing secret |
| `OPENROUTER_API_KEY` | Backend `.env` | API key for OpenRouter (LLM access) |
| `API_BASE_URL` | Flutter `.env` | Backend base URL (LAN IP or ngrok URL) |

## Known Limitations / Roadmap

These are tracked as post-presentation cleanup items:

- Removal of dead `rag_used` field (DB column, schema, router, Flutter model) — RAG is currently always active
- Prompt-injection hardening in `llm_service.py` (strict system/user role separation, input delimiting)
- Investigation of feature leakage in the V8 risk model (`HE_HbA1c` in `layer_b`)
- Activation of trend tracking (`trend_message`, `last_factors_json` column)
- Granularizing family-history input from a single boolean into three model features (`HE_DMfh1/2/3`)
- Refresh/retry token logic, CORS restriction (currently `allow_origins=["*"]`), login rate limiting
- Potential OAuth login (e.g., Kakao)

## Security Notes

- All critical endpoints require JWT authentication (`Depends(get_current_user)`).
- Secrets are loaded from `.env`, not hardcoded.
- SQLite is not network-exposed; access is only via authenticated API endpoints.
- Backend ports are open broadly to support multi-network demos; SSH access should be restricted to known IPs. (Specific port numbers and IP ranges are intentionally omitted here — see internal deployment docs.)

## Team

- **Computer Engineering:** Mobile app (Flutter), backend (FastAPI), LLM-RAG integration
- **AI/ML:** Data preprocessing, model training (LightGBM), SHAP/XAI pipeline, rule-based feedback design

## License

Specify your license here (e.g., MIT).




