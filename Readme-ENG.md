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

## Environment Variables

| Variable | Location | Description |
|---|---|---|
| `SECRET_KEY` | Backend `.env` | JWT signing secret |
| `OPENROUTER_API_KEY` | Backend `.env` | API key for OpenRouter (LLM access) |
| `API_BASE_URL` | Flutter `.env` | Backend base URL (LAN IP or ngrok URL) |


## Team

- **Computer Engineering:** Mobile app (Flutter), backend (FastAPI), LLM-RAG integration
- **AI/ML:** Data preprocessing, model training (LightGBM), SHAP/XAI pipeline, rule-based feedback design

Specify your license here (e.g., MIT).




