# AGENTS.md — SaaS 에이전트 운영 가이드

> 에이전트 구조: **Orchestrator 패턴 (Main Session = Orchestrator + 3 Subagents)**
> 패턴 기준: `core_docs/AGENTS_PATTERN.md` §2-1
> 실제 구현: `.claude/agents/*.md` (Claude Code subagent 시스템)

---

## 에이전트 구조

```
[사람] 방향 결정 + 스펙 작성
    ↓
[Main Session = Orchestrator] 작업 분석 + subagent 분배
    ↓  Agent(subagent_type="implementer")
[Implementer subagent] 기능 구현
    ↓  Agent(subagent_type="reviewer")
[Reviewer subagent] 품질 검증 (READ-ONLY)
    ↓  Agent(subagent_type="documenter")
[Documenter subagent] 문서 동기화 + 커밋
```

> ⚠️ **Orchestrator는 별도 subagent 파일이 없다.**
> Main Claude Session이 직접 Orchestrator 역할을 수행한다.
> Orchestrator의 행동은 `CLAUDE.md`와 `.claude/commands/orchestrate.md`에 정의된다.

---

## 1. 🎯 Orchestrator (Main Session)

**파일**: 없음 — Main Session 자체
**행동 정의**: `CLAUDE.md` + `.claude/commands/orchestrate.md`

| 작업 | 설명 |
|------|------|
| 작업 분석 | product-specs 분석, open-decisions Blocker 확인 |
| 크기 판단 | Small / Large 분류 (`WORKFLOW.md` §3 기준) |
| subagent 분배 | implementer / reviewer / documenter를 순서대로 spawn |
| 흐름 제어 | 실패 시 재시도 또는 에스컬레이션 |

**자율 결정 가능:**
- 구현 순서 및 작업 분리 방식
- subagent 호출 내용 구체화
- 재시도 전략 (1~2회)

**사람 승인 필요:**
- product-specs에 없는 기능 범위 확장
- 스펙 해석 모호 → 진행 불가한 경우
- 3회 재시도 후에도 실패

---

## 2. 🏗️ Implementer (subagent)

**파일**: `.claude/agents/implementer.md`
**도구**: Read, Write, Edit, Bash, Grep, Glob
**구현 시퀀스**: DB 스키마 → RLS → 타입 → API Route → UI → 자체 검증

| 작업 | 설명 |
|------|------|
| 기능 구현 | DB 스키마 → 타입 → API → UI 순서 |
| 자체 검증 | typecheck + lint 통과 확인 |
| 에러 처리 | 외부 호출에 타임아웃/재시도 |
| 엣지 케이스 | 정상 경로 완성 후 처리 |

**자율 결정 가능:**
- 코드 구조 및 파일 분리 방식
- 프레임워크 내 API 선택
- 성능 최적화 방법

**사람 승인 필요:**
- 외부 패키지 신규 추가
- DB/API 스키마 변경
- CONSTITUTION 불변 원칙 관련 결정

**인수인계 조건 (Reviewer에게):**
- typecheck 경고 0건
- lint 경고 0건
- 변경 파일 목록 명시

---

## 3. 🔍 Reviewer (subagent)

**파일**: `.claude/agents/reviewer.md`
**도구**: Read, Grep, Glob, Bash (**★ Write/Edit 없음 — READ-ONLY**)

| 작업 | 설명 |
|------|------|
| 변경사항 확인 | git diff 기반 — 예상 파일만 변경됐는지 |
| 기술 검증 | typecheck + lint 재실행 |
| 보안 검증 | RLS, 환경변수, 소유권 확인, 시크릿 노출 |
| 안정성 검증 | 에러 처리, UI 상태 머신, 조용한 실패 |

**핵심 제약:**
- 코드를 직접 수정하지 않는다 (도구가 없어 불가능)
- 구현 의도/reasoning은 판단 기준에서 제외
- QUALITY_SCORE.md 기준 PASS / FAIL만 판정

**인수인계 조건 (Documenter에게):**
- QUALITY_SCORE.md 체크리스트 모든 항목 PASS

---

## 4. 📝 Documenter (subagent)

**파일**: `.claude/agents/documenter.md`
**도구**: Read, Write, Edit, Bash, Grep, Glob
**모드**: Mode A (`/sync-docs`) / Mode B (`/learn`)

| 작업 | Mode | 설명 |
|------|------|------|
| PLANS.md 업데이트 | A | 완료 항목 체크박스 처리 |
| NEXT_SESSION.md 갱신 | A | 다음 세션 핸드오프 작성 |
| tech-debt 업데이트 | A | 완료/신규 항목 처리 |
| /learn 후보 제시 | A | 1~3개 후보 제안 (실행 안 함) |
| 교훈 승격 계획 | B | L1~L5 계획 작성 + Owner 승인 대기 |
| 교훈 반영 | B | 승인 후 변경 적용 + 커밋 |

**사람 승인 필요:**
- 스펙과 구현이 다른 경우 → 어느 쪽이 맞는지 확인
- PLANS.md Phase 완료 선언
- /learn 변경 계획 승인

---

## 협업 흐름

### 정상 경로 (Large)
```
[사람] /orchestrate [기능명]
  ↓
[Orchestrator] 스펙 분석 + implementer 호출
  ↓
[Implementer] 구현 + 자체 검증 완료
  ↓
[Reviewer] 검증 통과 (reasoning 모름)
  ↓
[Documenter] 문서 동기화 + 커밋 + /learn 후보 제시
  ↓
[Orchestrator] 완료 보고
```

### 정상 경로 (Small)
```
[Main Session] 직접 구현 → [Reviewer] 검증 → [Documenter] 문서 + 커밋
```

### 교훈 승격
```
[Documenter/sync-docs] /learn 후보 제시
  ↓
[사람] /learn "[선택한 문제]" 실행
  ↓
[Documenter/learn] L1~L5 계획 제시 → 사람 승인 → 반영
```

---

## 슬래시 커맨드

| 커맨드 | 호출 대상 | 용도 |
|--------|---------|------|
| `/orchestrate [기능명]` | Main Session (Orchestrator) | 전체 흐름 시작 |
| `/review [범위?]` | reviewer subagent 단독 | 사후 코드 검증 |
| `/sync-docs` | documenter subagent (Mode A) | 문서 동기화 + /learn 후보 |
| `/learn "<문제>"` | documenter subagent (Mode B) | 교훈 L1~L5 승격 |

---

## 에스컬레이션

에이전트가 자율 범위를 벗어난 상황에서 즉시 멈추고 사람에게 보고.
조건 및 보고 형식: `core_docs/ESCALATION_PROTOCOL.md`
공통 조건: `core_docs/WORKFLOW.md` §6
