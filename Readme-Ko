
# D-care-app-DEMO-

# download-link
https://drive.google.com/file/d/11BLR-3ndl_VDxocrzM5VM9lh4ScBfyzp/view?usp=sharing

# description:

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

## 시작하기

### 사전 요구사항

- Flutter SDK (Android Studio 권장)
- Python 3.10 이상
- OpenRouter API 키
- (선택) 백엔드 호스팅용 AWS EC2 인스턴스

### 백엔드 설정

```bash
# 저장소 클론
git clone https://github.com/<your-org>/D-care_01.git
cd D-care_01

# 가상환경 생성 및 활성화
python3 -m venv venv
source venv/bin/activate

# 의존성 설치
pip install -r requirements.txt

# 환경변수 설정
cp .env.example .env
# .env 파일에 아래 값 설정:
#   SECRET_KEY=<JWT 서명용 비밀키>
#   OPENROUTER_API_KEY=<OpenRouter API 키>

# DB 생성 (스키마 변경 시마다 재생성 필요)
python create_db.py

# 서버 실행
uvicorn main:app --host 0.0.0.0 --port <BACKEND_PORT>
```

### 프론트엔드 설정

```bash
cd D-care_app  # Flutter 프로젝트 디렉토리

# 패키지 설치
flutter pub get

# API Base URL 설정
# Flutter .env 파일에서 API_BASE_URL을 현재 사용 중인 무선 LAN IPv4 주소로 설정
# (10.0.2.2는 안드로이드 에뮬레이터 전용이므로 실기기에서는 동작하지 않음)

# 연결된 기기/에뮬레이터에서 실행
flutter run
```

### 다중 네트워크 환경 접속 (ngrok)

강의실 ↔ 기숙사처럼 네트워크를 자주 이동하며 개발할 경우, ngrok으로 백엔드를 외부에 노출합니다.

```bash
ngrok http <BACKEND_PORT>
```

Flutter 앱의 `API_BASE_URL`을 ngrok이 발급한 포워딩 URL로 업데이트하세요. 실기기 테스트는 백엔드에 설정된 포트를 사용합니다 (구체적인 포트 번호는 내부 설정 문서 참고).

> Windows 사용자는 네트워크가 바뀔 때마다 개발용 포트에 대한 인바운드 방화벽 규칙을 다시 확인/설정해야 합니다.

## 환경변수

| 변수명 | 위치 | 설명 |
|---|---|---|
| `SECRET_KEY` | 백엔드 `.env` | JWT 서명용 비밀키 |
| `OPENROUTER_API_KEY` | 백엔드 `.env` | OpenRouter API 키(LLM 호출용) |
| `API_BASE_URL` | Flutter `.env` | 백엔드 기본 주소 (LAN IP 또는 ngrok URL) |

## 알려진 한계점 / 향후 계획

발표 이후 정리 예정인 항목들입니다.

- 사용되지 않는 `rag_used` 필드 제거 (DB 컬럼, 스키마, 라우터, Flutter 모델) — 현재 RAG는 항상 활성화된 상태
- `llm_service.py`의 프롬프트 인젝션 방어 강화 (system/user 프롬프트 역할 분리, 사용자 입력 구분자 처리)
- V8 위험도 모델의 피처 누수(`layer_b`의 `HE_HbA1c`) 원인 조사 (인공지능 팀과 협의)
- 트렌드 추적 기능 활성화 (`trend_message`, `last_factors_json` 컬럼 추가 필요)
- 가족력 입력을 단일 boolean에서 모델 피처 3개(`HE_DMfh1/2/3`)로 세분화
- 리프레시/재시도 토큰 로직, CORS 제한(현재 `allow_origins=["*"]`), 로그인 요청 제한(rate limiting) 추가
- 카카오 로그인 등 OAuth 도입 검토

## 보안 관련 참고사항

- 모든 핵심 엔드포인트는 JWT 인증(`Depends(get_current_user)`)을 요구합니다.
- 민감정보는 하드코딩하지 않고 `.env`에서 로드합니다.
- SQLite는 네트워크에 직접 노출되지 않으며, 인증된 API 엔드포인트를 통해서만 접근 가능합니다.
- 다중 네트워크 환경 시연을 위해 백엔드 포트는 광범위하게 열려 있으나, SSH 접근은 알려진 IP로 제한해야 합니다. (구체적인 포트 번호와 IP 대역은 보안상 여기서는 생략합니다.)

## 팀 구성

- **컴퓨터공학과:** 모바일 앱(Flutter), 백엔드(FastAPI), LLM-RAG 이식
- **인공지능학과:** 데이터 전처리, 모델 학습(LightGBM), SHAP/XAI 파이프라인, 규칙 기반 피드백 설계

## 라이선스

사용할 라이선스를 여기에 명시하세요 (예: MIT).

