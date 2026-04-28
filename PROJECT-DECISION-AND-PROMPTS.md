# 새 프로젝트 기획 실행 플로우

## Step 0: 당신이 만들려는 것이 뭔지 판단

"우리가 만들려는 게 뭐지?" → 다음 체크리스트로 판단

---

## 📋 프로젝트 유형 판단 플로우

### 질문 1: 주요 고객이 누구?

```
A. 외부 사용자 (앱 스토어에 올릴 수 있는 수준)
   └─ SaaS 또는 Game으로 가기

B. 팀 내부 (우리 팀만 쓰는 도구)
   └─ Internal Tool로 가기

C. 잘 모르겠음
   └─ 아래 추가 질문 보기
```

### 질문 2: 핵심 성공 지표가?

```
A. 사용자 수, 활성도, 매출
   └─ SaaS 또는 Game

B. 기능 완성도, 안정성, 운영 효율
   └─ Internal Tool

C. 창의성, 재미, 중독성
   └─ Game 가능성 높음
```

### 질문 3: 팀 구성과 개발 기간?

```
A. 2-3명, 2-4주 스프린트 (빨리 결과 봐야 함)
   └─ 기존 SaaS/Game 템플릿 사용

B. 1명 또는 5명+, 장기 프로젝트
   └─ 기존 Internal Tool 템플릿 사용

C. 새로운 형태 (예: B2B SaaS + Game화)
   └─ 신규 템플릿 고려
```

---

## 🛣️ 의사결정 트리

```
"우리 프로젝트는?"
    │
    ├─ "외부 사용자 앱 + 재미 중심" → GAME
    │   └─ templates_docs/game/ 템플릿 있음 ✓
    │
    ├─ "외부 사용자 앱 + 생산성 중심" → SaaS
    │   └─ templates_docs/saas/ 템플릿 있음 ✓
    │
    ├─ "팀 내부 도구" → INTERNAL TOOL
    │   └─ 템플릿 미구현 — Scenario 3 방식으로 직접 생성
    │
    └─ "위 3개에 안 맞음" → 신규 템플릿 필요
        └─ "어떤 타입의 신규?"
            ├─ "B2B SaaS (복잡도 높음)" → 신규 b2b-saas 만들기
            ├─ "Open Source Tool" → 신규 oss 만들기
            └─ 등등...
```

---

## 💬 프롬프트 3가지 시나리오

### Scenario 1: 기존 템플릿에 맞는 경우 (SaaS)

**상황**: "우리는 PDF를 AI로 요약해주는 웹앱을 만들 거야"

**의사결정**:
- 외부 사용자? ✓ (웹앱)
- 핵심 성공 지표? 사용자 수, 정확도
- 팀? 2명, 2주 스프린트
- 결론: **SaaS 템플릿 기존 것 사용**

**당신이 할 프롬프트** (한 번에):

```
당신은 AI Engineering 아키텍트입니다.

아래 소스 파일들을 참고해서 이 프로젝트 전용 문서를 새 프로젝트 폴더에 생성해주세요.
- 참고: core_docs/CONSTITUTION_TEMPLATE.md, templates_docs/saas/ 전체

## 프로젝트 정보
- 이름: PDFask
- 카테고리: SaaS
- 팀: 2명 (엔지니어 1, PM 1)
- 기간: 2주 스프린트

## 핵심
- Mission: 사용자가 긴 PDF를 AI로 빠르게 이해하고 상호작용할 수 있게 한다
- 절대 금지: 할루시네이션 (거짓 답변), 사용자 파일 노출
- 성공 신호: 정확도 90% 이상, 응답시간 3초 이내

## 생성할 문서들 (내 프로젝트 루트에)
- CONSTITUTION.md  (core_docs/CONSTITUTION_TEMPLATE.md 기반, 프로젝트 정보로 채워줘)
- CLAUDE.md        (templates_docs/saas/CLAUDE.md 기반, 프로젝트 정보로 채워줘)
- AGENTS.md        (templates_docs/saas/AGENTS.md 기반, 프로젝트 정보로 채워줘)
- QUALITY_SCORE.md (templates_docs/saas/QUALITY_SCORE.md 기반, 프로젝트 정보로 채워줘)
- docs/PLANS.md    (templates_docs/PLANS_TEMPLATE.md 기반, 2주 스프린트 구조로 채워줘)
- GLOSSARY.md      (core_docs/GLOSSARY_TEMPLATE.md 기반)
```

---

### Scenario 2: 기존 템플릿에 거의 맞지만 약간 다른 경우 (Game + 멀티플레이)

**상황**: "우리는 게임을 만들 건데, 싱글플레이 턴 기반 전략 게임이야. 하지만 나중에 멀티플레이를 추가할 거야"

**의사결정**:
- 기존 game/ 템플릿 기초 사용하되
- "멀티플레이 아키텍처" 부분만 추가로 고려해야 함
- 결론: **Game 템플릿 사용 + 약간 확장**

**당신이 할 프롬프트** (한 번에):

```
당신은 Game Architecture 전문가입니다.

아래 소스 파일들을 참고해서 이 프로젝트 전용 문서를 새 프로젝트 폴더에 생성해주세요.
- 참고: core_docs/CONSTITUTION_TEMPLATE.md, templates_docs/game/ 전체

## 프로젝트 정보
- 이름: ChessCraft
- 카테고리: Game (턴 기반 전략)
- 팀: 3명 (엔지니어, 디자이너, QA)
- 기간: 1개월 (4주 스프린트)

## 특이사항
Phase 1은 싱글플레이 (AI 상대), Phase 2는 멀티플레이 추가 예정
아키텍처는 멀티플레이를 염두에 두고 설계하기

## 생성할 문서들 (내 프로젝트 루트에)
- CONSTITUTION.md   (core_docs/CONSTITUTION_TEMPLATE.md 기반, 프로젝트 정보로 채워줘)
- CLAUDE.md         (templates_docs/game/CLAUDE.md 기반, 프로젝트 정보로 채워줘)
- AGENTS.md         (templates_docs/game/AGENTS.md 기반, 프로젝트 정보로 채워줘)
- QUALITY_SCORE.md  (templates_docs/game/QUALITY_SCORE.md 기반, 프로젝트 정보로 채워줘)
- docs/PLANS.md     (templates_docs/PLANS_TEMPLATE.md 기반, 4주 Phase 1 구조로 채워줘)
- docs/ARCHITECTURE.md (templates_docs/game/ARCHITECTURE_GUIDE.md 기반, 멀티플레이 확장 고려)
- GLOSSARY.md       (core_docs/GLOSSARY_TEMPLATE.md 기반)
```

---

### Scenario 3: 완전 새로운 템플릿이 필요한 경우 (B2B SaaS)

**상황**: "우리는 B2B SaaS를 만드는데, 엔터프라이즈급이야. SSO, 감시, 규정 준수가 필요해"

**의사결정**:
- SaaS 템플릿과 비슷하지만
- 보안, 규정, 엔터프라이즈 기능이 훨씬 복잡
- 결론: **신규 b2b-saas 템플릿 만들어야 함**

**당신이 할 프롬프트** (한 번에):

```
당신은 B2B SaaS 아키텍처 전문가입니다.

아래 소스 파일들을 참고해서 B2B SaaS 신규 템플릿과 프로젝트 문서를 생성해주세요.
- 참고: core_docs/CONSTITUTION_TEMPLATE.md, templates_docs/saas/ 전체

## 프로젝트 정보
- 이름: CustomerFlow (예시)
- 카테고리: B2B SaaS
- 팀: 5명 (엔지니어 2, 디자인 1, 보안 1, PM 1)
- 기간: 6주 MVP

## B2B SaaS 특수성
- 다중 팀 지원 (여러 회사가 한 플랫폼)
- SSO/OAuth 필수
- 감시/감사 로깅 필수
- GDPR, SOC2 등 규정 준수
- SLA 관리 (99.9% 가동률)

## 생성할 문서들

### 신규 템플릿 (templates_docs/b2b-saas/ 에)
- CLAUDE.md         (templates_docs/saas/CLAUDE.md 기반 + B2B 특화)
- AGENTS.md         (templates_docs/saas/AGENTS.md 기반 + B2B 특화)
- QUALITY_SCORE.md  (templates_docs/saas/QUALITY_SCORE.md 기반 + 보안/규정 강화)
- ARCHITECTURE_GUIDE.md (Multi-tenancy 강조)
- SECURITY_CHECKLIST.md (SSO, 감사 로깅, GDPR 항목)

### 프로젝트 문서 (내 프로젝트 루트에)
- CONSTITUTION.md   (core_docs/CONSTITUTION_TEMPLATE.md 기반, B2B 특화)
- CLAUDE.md         (위 b2b-saas 템플릿 기반)
- AGENTS.md         (위 b2b-saas 템플릿 기반)
- QUALITY_SCORE.md  (위 b2b-saas 템플릿 기반)
- docs/PLANS.md     (templates_docs/PLANS_TEMPLATE.md 기반, 6주 MVP 구조)
```

---

## ✅ 시작 전 체크리스트

- [ ] 외부/내부 사용자 구분했나?
- [ ] 핵심 성공 지표 명확한가?
- [ ] 기존 템플릿(saas/game)에 맞는가?
  - [ ] Yes → Scenario 1 프롬프트
  - [ ] 약간 다름 → Scenario 2 프롬프트
  - [ ] No → Scenario 3 프롬프트
- [ ] 프롬프트 실행 후 `README.md` 초기화 명령으로 `.claude/` 디렉토리 구성했나?
