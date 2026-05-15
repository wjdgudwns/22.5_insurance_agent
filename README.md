# 🛡️ 삼성화재 AI 보험 어시스턴트

삼성화재 보험 공모전을 위해 개발된 AI 기반 보험 상담 시스템입니다.
멀티 에이전트 RAG 구조로 개인화된 보험 Q&A, 보험금 청구 안내, 민원 관리 기능을 제공합니다.

---

## 🚀 라이브 데모

🔗 [앱 실행하기](https://225insuranceagent-kxcwtatcl2g5nulmynzcnu.streamlit.app/)

### 테스트 계정
| 구분 | ID | 비밀번호 |
|------|----|----------|
| 고객 | CUST-0001 ~ CUST-0050 | 1234 |
| 관리자 | admin | 1234 |

---

## ✨ 주요 기능

### 👤 고객 모드
- **약관 Q&A** — 실제 보험 약관 기반으로 보장 내용, 특약, 상세 정보 질의응답
- **개인화 답변** — 고객별 가입 상품 및 특약에 맞춘 맞춤형 답변 제공
- **복수 상품 지원** — 자동차/암/치아 보험 동시 처리 가능
- **보험금 청구** — 단계별 청구 안내 및 서류 체크리스트 제공
- **서류 업로드** — Gemini Vision 기반 서류 자동 분류
- **대화 맥락 유지** — 최근 6턴 히스토리로 자연스러운 연속 질문 처리

### 🔐 관리자 모드
- **전체 현황** — 상담 건수, 민원, 이관, 청구 실시간 통계
- **대화 로그** — Intent/도메인 필터링 및 CSV 다운로드
- **민원 관리** — 감정 점수 기반 민원 추적
- **이관 현황** — 상담원 이관 케이스 모니터링
- **고객 현황** — 상품별 가입 통계

---

## 🤖 AI 에이전트 구조

```
사용자 질문
    │
    ▼
Intent Router (Gemini 2.5 Flash)
    │
    ├── 가입조회     → 고객 DB 조회
    ├── 보장조회     → RAG 에이전트 (벡터 DB 검색)
    ├── 미가입문의   → RAG 에이전트 + 가입 안내
    ├── 사고청구     → RAG 에이전트 + 청구 에이전트
    ├── 일반문의     → RAG 에이전트
    └── out_of_scope → 안내 메시지
    │
    ▼
RAG 에이전트
    ├── 특약명별 개별 검색
    ├── 판례/분쟁 사례 검색
    └── LLM 답변 생성
    │
    ▼
민원 에이전트 (모든 답변에 자동 실행)
    ├── 감정 점수 측정
    ├── 민원 감지 및 로깅
    └── Human Handoff (감정점수 ≤ 3 또는 법적 키워드 감지 시)
```

---

## 🗂️ 보험 상품

| 상품 | 도메인 | 설명 |
|------|--------|------|
| 자동차보험 | `auto` | 사고 보상, 렌터카, 법률비용 등 특약 |
| 암보험 | `cancer` | 암 진단비, 치료비, 입원 급여 |
| 치아보험 | `teeth` | 임플란트, 크라운, 신경치료, 보철 |

---

## 🛠️ 기술 스택

| 구성 요소 | 기술 |
|-----------|------|
| LLM | Gemini 2.5 Flash |
| 임베딩 | BAAI/bge-m3 |
| 벡터 DB | ChromaDB (MMR 검색) |
| UI | Streamlit |
| 이미지 분석 | Gemini Vision |
| 언어 | Python 3.12 |

---

## 📁 프로젝트 구조

```
insurance_agent/
├── app.py                    # 메인 Streamlit 대시보드
├── customers.csv             # 고객 DB (50명)
├── requirements.txt
├── agents/
│   ├── customer_agent.py     # 고객 DB 조회 및 로그인
│   ├── rag_agent.py          # 벡터 검색 + LLM 답변 생성
│   ├── claim_agent.py        # 서류 검증 및 청구 처리
│   └── complaint_agent.py    # 민원 감지 및 Human Handoff
├── utils/
│   └── llm_setup.py          # LLM, 임베딩, 벡터 DB 초기화
├── insurance_chroma_db_last/ # 자동차보험 벡터 DB
├── cancer_chroma_db_last/    # 암보험 벡터 DB
├── teeth_chroma_db_last/     # 치아보험 벡터 DB
└── precedent_chroma_db/      # 판례 벡터 DB
```

---

## ⚙️ 로컬 실행 방법

### 1. 패키지 설치
```bash
pip install -r requirements.txt
```

### 2. 환경 변수 설정
프로젝트 루트에 `.env` 파일 생성:
```
GOOGLE_API_KEY=발급받은_구글_API_키
```

### 3. 앱 실행
```bash
streamlit run app.py
```

---

## 🔐 환경 변수

| 변수 | 설명 |
|------|------|
| `GOOGLE_API_KEY` | Google AI Studio API 키 (Gemini) |

Streamlit Cloud 배포 시 **Settings → Secrets**에 입력해주세요.

---

## 📊 동작 흐름

1. 고객이 ID와 비밀번호로 로그인
2. Intent Router가 질문을 6가지 카테고리로 분류
3. RAG 에이전트가 관련 보험 약관 벡터 DB 검색
4. Gemini가 검색된 약관 청크를 바탕으로 개인화 답변 생성
5. 모든 답변에 대해 민원 감지 및 감정 분석 자동 실행
6. 강한 불만 또는 법적 키워드 감지 시 상담원 이관 처리

---

## 📝 라이선스

본 프로젝트는 **삼성화재 보험 AI 공모전** 출품작입니다.
