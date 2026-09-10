
# D-care-app-DEMO-

# download-link
https://drive.google.com/file/d/11BLR-3ndl_VDxocrzM5VM9lh4ScBfyzp/view?usp=sharing

# description:


https://github.com/user-attachments/assets/7657c8c9-8075-4f5b-bcde-f1ffd82d5c8f

(해당 배포본에 등록된 모든 계정은 실존하지 않는 더미 계정입니다.)

# D-Care

**D-Care**는 학과 간 협업으로 진행되는 캡스톤 디자인 프로젝트로, 만성질환(당뇨) 헬스케어 모바일 애플리케이션입니다. LLM-RAG 기반 챗봇을 통한 상담/생활습관 가이드와, 머신러닝 기반 위험도 분류 및 설명가능성(XAI) 모듈을 하나의 네이티브 모바일 앱으로 제공합니다.

## 개요

당뇨와 같은 만성질환 관리는 일회성 진단이 아니라 지속적이고 개인화된 가이드가 필요합니다. D-Care는 다음 세 가지를 결합하여 이를 지원합니다.

1. **LLM-RAG 챗봇** — 신뢰할 수 있는 의료 참고 문서를 근거로, 건강/생활습관 관련 질의에 답변하는 검색증강생성(RAG) 챗봇
2. **위험군 인사이트 탭** — SHAP 기반 변수 우선순위화를 규칙 기반 피드백으로 변환한 LightGBM 기반 당뇨 위험도 분류 모델 (LLM이 생성한 설명이 아님)
3. **건강 프로필 & 채팅 기록 저장** — 5페이지 구성의 건강 프로필 입력 폼과, 인증된 사용자별 전체 채팅 기록 저장

본 프로젝트는 **컴퓨터공학과**(모바일 앱, 백엔드, LLM-RAG 이식)와 **인공지능학과**(모델 학습, SHAP/XAI 파이프라인 설계, KNHANES 데이터 기반 피처 엔지니어링) 간의 협업 프로젝트입니다.

## 아키텍처

```
┌─────────────────────┐        HTTPS / JWT        ┌──────────────────────────┐
│   Flutter 앱           │ ─────────────────────────▶│   FastAPI 백엔드           │
│  (Android / iOS)      │◀───────────────────────── │   (AWS EC2) │
│  - Riverpod             │                          │  - JWT 인증                │
│  - flutter_secure_      │                          │  - SQLite (diabetes.db)    │
│    storage (JWT 저장)    │                          │  - LangChain + FAISS RAG   │
└─────────────────────┘                            │  - OpenRouter API (Claude)  │
                                                     │  - LightGBM + SHAP         │
                                                     └──────────────────────────┘
```

- **프론트엔드:** Flutter(Android Studio 기반), 상태관리는 Riverpod, JWT 저장은 `flutter_secure_storage`, HTTP 클라이언트는 Dio 사용
- **백엔드:** AWS EC2 위 FastAPI, SQLite에 SQLAlchemy ORM 사용
- **LLM-RAG:** LangChain + FAISS 벡터스토어 + HuggingFace 임베딩, **OpenRouter API**를 통해 LLM 호출(현재 한국어 품질이 우수한 Claude Haiku 사용)
- **위험도 예측 모델:** 인공지능학과 팀이 KNHANES(국민건강영양조사) 데이터로 학습한 LightGBM 모델(`lgbm_base.txt`, `lgbm_full.txt`, `model_meta.json`), 공복혈당 관련 피처 누수(leakage) 이슈를 보완하기 위한 규칙 기반 보정 레이어 포함
- **설명가능성(XAI):** SHAP TreeExplainer로 변수 우선순위를 산출하고, 인공지능학과 팀이 작성한 규칙 기반 피드백 메시지로 매핑하여 인사이트 탭에 표시
- **개발용 터널링:** 강의실/기숙사 등 AP 격리(AP-isolation) 네트워크 환경에서 실기기 테스트 시 로컬/EC2 백엔드를 외부에 노출하기 위해 ngrok 사용

> **참고:** LLM-RAG 프롬프트 스키마(`app_schemas.py`)와 ML 모델 입력 스키마(`health_profile_model.dart`)는 의도적으로 분리되어 있습니다. 챗봇 파이프라인과 위험도 예측 파이프라인은 상태나 로직을 공유하지 않습니다.

## 주요 기능

- 🔐 모든 핵심 엔드포인트에 JWT 기반 인증 적용
- 💬 채팅 기록이 저장되는 LLM-RAG 챗봇
- 📊 위험군 인사이트 탭: SHAP 기반 변수 우선순위화 + 규칙 기반 피드백
- 📝 5페이지 구성의 건강 프로필 입력 폼
- 🌐 ngrok을 통한 다중 네트워크 환경 개발 지원


## 팀 구성

- **컴퓨터공학과:** 모바일 앱(Flutter), 백엔드(FastAPI), LLM-RAG 이식
- **인공지능학과:** 데이터 전처리, 모델 학습(LightGBM), SHAP/XAI 파이프라인, 규칙 기반 피드백 설계


