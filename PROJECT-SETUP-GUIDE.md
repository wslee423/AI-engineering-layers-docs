# 🚀 새 프로젝트 초기화 가이드

AI Engineering Harness 시스템을 기반으로 새 프로젝트를 완전 자동 초기화하는 방법입니다.

---

## ⚡ 5분 시작 가이드

### 1️⃣ 프롬프트 선택
아래 3가지 중 프로젝트 타입에 맞는 프롬프트 복사:
- **SaaS**: [예시 1: SaaS 프로젝트](#-실제-예시-1-saas-프로젝트)
- **Game**: [예시 2: Game 프로젝트](#-실제-예시-2-game-프로젝트)
- **기타**: [일반 템플릿](#-프롬프트-템플릿-일반형)

### 2️⃣ 정보 채우기
프롬프트의 `[  ]` 부분을 프로젝트 정보로 수정

### 3️⃣ Claude 입력
새 Claude 세션 열기 → 프롬프트 전체 붙여넣기

### 4️⃣ 결과 받기
✅ 9~11개 완성 문서 + 초기화 스크립트

### 5️⃣ 로컬 초기화
```bash
bash .claude-init.sh
# ai-engineering-docs 경로 지정 → 자동 복사
```

**소요 시간**: 프롬프트 입력 1분 + Claude 생성 15분 = 약 16분

---

## 📋 프롬프트 템플릿 (일반형)

다른 프로젝트 타입이거나 커스터마이징이 필요하면 이 템플릿 사용.

```markdown
당신은 AI Engineering Architect입니다.

내 새 프로젝트를 다음 스캐폴딩 시스템 기반으로 완전하게 기획하고 초기화해주세요:
📍 ai-engineering-docs (GitHub: https://github.com/wslee423/AI-engineering-layers-docs)

---

## 1️⃣ 프로젝트 정보

### 기본 정보
- **프로젝트명**: [프로젝트 이름]
- **한 줄 설명**: [무엇을, 누구를 위해, 왜 하는가]
- **프로젝트 타입**: SaaS / Game / [기타]

### 팀 & 일정
- **팀 구성**: [역할별 인원. 예: 엔지니어 2명, PM 1명, 디자이너 1명]
- **개발 기간**: [기간. 예: 4주 스프린트 / 6주 MVP / 3개월]
- **현재 단계**: [미시작 / 기획 완료 / 기술 검증 완료]

### 핵심 요구사항
- **주요 기능**: 
  - [기능 1]
  - [기능 2]
  - [기능 3]
- **기술 스택** (확정된 부분만):
  - Language: [예: TypeScript]
  - Frontend: [예: Next.js 14 + Tailwind / Flutter]
  - Backend/DB: [예: Supabase / Firebase / 자체 서버]
  - Deployment: [예: Vercel / Google Play / Steam]
- **절대 금지 사항** (불변 원칙 후보):
  - [예: 조용한 실패 금지, RLS 필수, 결제 강제 금지]
- **성공 지표** (정량화):
  - [예: 응답시간 2초 이내, 정확도 95% 이상, 월 활성 사용자 1000명]

### 특수 요구사항 (해당시만)
- **아키텍처 특수성**: [예: 멀티테넌시, 실시간 동기화, 멀티플레이]
- **규정/보안**: [예: GDPR, SOC2, PCI-DSS, 암호화 저장]
- **제약조건**: [예: 기술 스택 고정, 레거시 시스템 연동, 예산 제한]

---

## 2️⃣ 생성 요청사항

### 생성 대상 (모두 제공)

**프로젝트 루트에 생성할 파일:**
```
프로젝트루트/
├── CONSTITUTION.md           ← 불변 원칙
├── CLAUDE.md                 ← 세션 가이드 + Critical Rules
├── AGENTS.md                 ← 에이전트 역할 정의
├── QUALITY_SCORE.md          ← 품질 기준 체크리스트
├── README.md                 ← 프로젝트 소개
├── docs/
│   ├── PLANS.md              ← Phase별 로드맵
│   ├── ARCHITECTURE.md       ← 기술 의사결정 + 구현 순서
│   ├── GLOSSARY.md           ← 팀 용어 정의
│   ├── product-specs/        ← 기능별 상세 스펙 (선택)
│   └── exec-plans/
│       ├── open-decisions.md       ← 미결 의사결정
│       ├── tech-debt-tracker.md    ← 기술 부채
│       └── NEXT_SESSION.md         ← 첫 세션 핸드오프
└── .claude-init.sh           ← 초기화 스크립트 (별도 생성)
```

**초기화 스크립트 요청:**
- `.claude-init.sh`: ai-engineering-docs에서 복사할 파일들과 명령어
- 실행 가이드: 스크립트 사용 방법 + 손으로 해야 할 작업 명시

---

## 3️⃣ 작성 기준

### CONSTITUTION.md
- 5개 이하의 구체적이고 검증 가능한 불변 원칙
- 각 원칙마다: [원칙 제목] — [정의 + 검증 방법]

### CLAUDE.md
- 세션 시작 시 반드시 읽을 문서 순서 (1~5번)
- Critical Rules: Must Follow / Must NOT Do (각 3~5개)
- 파일 구조와 환경변수 명시

### AGENTS.md
- Orchestrator (Main Claude Session — 파일 없음)
- Implementer / Reviewer / Documenter (3 subagents)
- 각 역할의: 담당 / 자율 결정 가능 / 사람 승인 필요 / 인수인계 조건

### QUALITY_SCORE.md
- 프로젝트 기술 스택 반영 (정적 분석, 테스트, 보안 등)
- Small Feature는 reviewer subagent만 / Large는 implementer→reviewer→사람 승인→documenter
- 각 항목: PASS/FAIL 판정 기준 명확

### PLANS.md
- Phase 1~N 정의 (최소 MVP까지)
- 각 Phase: 목표 / 완료 조건 / 작업 체크리스트

### ARCHITECTURE.md
- 선택한 스택 (언어, Framework, DB, API, 배포)
- 선택하지 않은 대안과 기각 이유
- 구현 순서 (예: DB → Types → API → UI)

### GLOSSARY.md
- 팀이 사용할 용어 정의 (예: "구현 완료", "검증 통과", "완료")
- 프로젝트 특화 용어 (예: "Stage", "RLS", "Trigger")

### exec-plans/
- **open-decisions.md**: 🔴 Blocker / 🟠 High / 🟡 Medium / 🟢 Low 우선순위별 미결 항목
- **tech-debt-tracker.md**: Critical / High / Medium / Low 기술 부채 추적
- **NEXT_SESSION.md**: 첫 세션 끝난 후 작성할 내용 미리 템플릿 제공

---

## 4️⃣ 완료 후 제공할 것

1. ✅ 모든 문서 (위 파일 목록)
2. ✅ `.claude-init.sh` 스크립트 (ai-engineering-docs 복사 명령어)
3. ✅ 초기화 후 폴더 구조 트리
4. ✅ "첫 번째 세션 시작 가이드" (Orchestrator가 할 첫 작업)
5. ✅ 주의사항 (예: node_modules, .env, .claude/ 복사 여부)

---

## 📖 참고
- ai-engineering-docs: Core Layer(표준) + Claude Code Layer(subagents) + Template Layer
- 이 프롬프트는 Template Layer의 해당 프로젝트 타입 템플릿을 기반으로 커스터마이징
```

---

## 💡 실제 예시 1: SaaS 프로젝트

**프로젝트**: LinkHub (팀 협업 북마크 플랫폼)

```markdown
당신은 AI Engineering Architect입니다.

내 새 프로젝트를 다음 스캐폴딩 시스템 기반으로 완전하게 기획하고 초기화해주세요:
📍 ai-engineering-docs (GitHub: https://github.com/wslee423/AI-engineering-layers-docs)

---

## 1️⃣ 프로젝트 정보

### 기본 정보
- **프로젝트명**: LinkHub
- **한 줄 설명**: 팀이 공유 링크를 AI로 요약·분류·협업하는 프라이빗 북마크 플랫폼
- **프로젝트 타입**: SaaS (Web)

### 팀 & 일정
- **팀 구성**: 풀스택 엔지니어 1명, PM 1명
- **개발 기간**: 6주 MVP
- **현재 단계**: 기획 완료

### 핵심 요구사항
- **주요 기능**:
  - 구글/깃헙 OAuth 인증 + 팀 초대 시스템
  - 링크 저장 + OpenAI API로 자동 요약
  - 카테고리 자동 분류 (태그 기반)
  - 팀 내 실시간 댓글/협업
  - 전체 텍스트 검색
  
- **기술 스택**:
  - Language: TypeScript (strict mode)
  - Frontend: Next.js 14 (App Router) + TailwindCSS
  - Backend: Supabase (PostgreSQL + Auth + RLS)
  - AI: OpenAI API (gpt-4-mini)
  - Deployment: Vercel

- **절대 금지 사항**:
  - 조용한 실패 금지 (모든 API 에러를 UI에 표시)
  - 모든 DB 테이블 RLS 활성화 필수
  - 클라이언트에서 API 키 노출 금지
  - 사용자 개인정보 평문 저장 금지

- **성공 지표**:
  - 요약 품질: 사용자 평가 4.5/5 이상
  - 응답시간: API 응답 3초 이내
  - 가용성: 월 99.5% uptime

### 특수 요구사항
- **아키텍처 특수성**: 팀 기반 멀티 테넌트 (Row Level Security)
- **규정/보안**: 
  - HTTPS 필수
  - 민감정보(API 키 등) 암호화 저장
  - GDPR 개인정보 삭제 정책
- **제약조건**: Vercel + Supabase 스택 고정

---

## 2️⃣ 생성 요청사항

### 생성 대상 (모두 제공)

**프로젝트 루트에 생성할 파일:**
```
프로젝트루트/
├── CONSTITUTION.md           ← 불변 원칙
├── CLAUDE.md                 ← 세션 가이드 + Critical Rules
├── AGENTS.md                 ← 에이전트 역할 정의
├── QUALITY_SCORE.md          ← 품질 기준 체크리스트
├── README.md                 ← 프로젝트 소개
├── docs/
│   ├── PLANS.md              ← Phase별 로드맵
│   ├── ARCHITECTURE.md       ← 기술 의사결정 + 구현 순서
│   ├── GLOSSARY.md           ← 팀 용어 정의
│   ├── product-specs/        ← 기능별 상세 스펙 (선택)
│   └── exec-plans/
│       ├── open-decisions.md       ← 미결 의사결정
│       ├── tech-debt-tracker.md    ← 기술 부채
│       └── NEXT_SESSION.md         ← 첫 세션 핸드오프
└── .claude-init.sh           ← 초기화 스크립트 (별도 생성)
```

**초기화 스크립트 요청:**
- `.claude-init.sh`: ai-engineering-docs에서 복사할 파일들과 명령어
- 실행 가이드: 스크립트 사용 방법 + 손으로 해야 할 작업 명시

---

## 3️⃣ 작성 기준
[위 템플릿의 작성 기준 동일]

---

## 4️⃣ 완료 후 제공할 것
[위 템플릿의 완료 후 제공사항 동일]

---

## 📖 참고
- ai-engineering-docs: Core Layer(표준) + Claude Code Layer(subagents) + Template Layer
- 이 프롬프트는 SaaS 템플릿을 기반으로 커스터마이징
```

---

## 💡 실제 예시 2: Game 프로젝트

**프로젝트**: BrickBreaker Plus (캐주얼 모바일 게임)

```markdown
당신은 AI Engineering Architect입니다.

내 새 프로젝트를 다음 스캐폴딩 시스템 기반으로 완전하게 기획하고 초기화해주세요:
📍 ai-engineering-docs (GitHub: https://github.com/wslee423/AI-engineering-layers-docs)

---

## 1️⃣ 프로젝트 정보

### 기본 정보
- **프로젝트명**: BrickBreaker Plus
- **한 줄 설명**: 클래식 벽돌 깨기 게임을 모던 UI와 스테이지 시스템으로 재해석한 캐주얼 게임
- **프로젝트 타입**: Game (Mobile)

### 팀 & 일정
- **팀 구성**: 게임 엔지니어 1명, 디자이너 1명
- **개발 기간**: 4주 (Phase 1: 싱글플레이, Phase 2: 순위표)
- **현재 단계**: 기술 검증 완료

### 핵심 요구사항
- **주요 기능**:
  - Phase 1: 10개 스테이지 + 점진적 난이도 상승
  - 라이프 시스템 (3목숨) + 게임오버/클리어 판정
  - 점수 계산 + 로컬 저장
  - 사운드 ON/OFF + 진동 효과
  - Phase 2: 온라인 순위표 (Firebase)

- **기술 스택**:
  - Language: Dart
  - Framework: Flutter + Flame
  - 저장소: SharedPreferences (Phase 1) / Firebase (Phase 2)
  - 배포: Google Play

- **절대 금지 사항**:
  - Pay-to-Win 요소 금지 (게임성 무결성)
  - 클리어 불가능한 스테이지 생성 금지
  - 오브젝트 끼임(게임 루프 멈춤) 금지

- **성공 지표**:
  - FPS: 60 FPS 유지 (모든 디바이스)
  - 크래시율: 0.1% 이하
  - 평균 플레이 시간: 세션당 5분 이상

### 특수 요구사항
- **아키텍처 특수성**: 
  - Phase 2 멀티플레이 대비 아키텍처 설계
  - 스테이지 데이터 JSON 분리 (로직과 콘텐츠 분리)
- **규정/보안**: 해당 없음
- **제약조건**: 
  - Flutter + Flame 고정
  - 외부 물리 엔진 사용 금지 (Flame 내장만)

---

## 2️⃣ 생성 요청사항

### 생성 대상 (모두 제공)

**프로젝트 루트에 생성할 파일:**
```
프로젝트루트/
├── CONSTITUTION.md           ← 불변 원칙
├── CLAUDE.md                 ← 세션 가이드 + Critical Rules
├── AGENTS.md                 ← 에이전트 역할 정의
├── QUALITY_SCORE.md          ← 품질 기준 체크리스트
├── README.md                 ← 프로젝트 소개
├── docs/
│   ├── PLANS.md              ← Phase별 로드맵
│   ├── ARCHITECTURE.md       ← 기술 의사결정 + 구현 순서
│   ├── GLOSSARY.md           ← 팀 용어 정의
│   ├── design-docs/
│   │   └── core-beliefs.md   ← 게임 설계 철학
│   ├── product-specs/        ← 기능별 상세 스펙 (선택)
│   └── exec-plans/
│       ├── open-decisions.md       ← 미결 의사결정
│       ├── tech-debt-tracker.md    ← 기술 부채
│       └── NEXT_SESSION.md         ← 첫 세션 핸드오프
└── .claude-init.sh           ← 초기화 스크립트 (별도 생성)
```

**초기화 스크립트 요청:**
- `.claude-init.sh`: ai-engineering-docs에서 복사할 파일들과 명령어
- 실행 가이드: 스크립트 사용 방법 + 손으로 해야 할 작업 명시

---

## 3️⃣ 작성 기준
[위 템플릿의 작성 기준 동일, game 특화 항목 추가]

### Game 특화 추가
- **CLAUDE.md**: "핵심 파일 목록" (game-specific, 테스트 필수)
- **QUALITY_SCORE.md**: FPS, 클리어 가능성, 상태 반응성 항목 강화
- **docs/design-docs/core-beliefs.md**: 게임 설계 철학 (난이도 곡선, 게임성 원칙)

---

## 4️⃣ 완료 후 제공할 것
[위 템플릿의 완료 후 제공사항 동일]

---

## 📖 참고
- ai-engineering-docs: Core Layer(표준) + Claude Code Layer(subagents) + Template Layer
- 이 프롬프트는 Game 템플릿을 기반으로 커스터마이징
```

---

## 🎯 다음 단계

1. **프롬프트 복사**: 위 3가지 중 선택
2. **정보 수정**: 프로젝트에 맞게 커스터마이징
3. **Claude 입력**: 새 세션에 붙여넣기
4. **스크립트 실행**: `.claude-init.sh` 로컬 실행
5. **프로젝트 시작**: 첫 세션 가이드 따라 시작

**총 소요 시간**: ~20분

