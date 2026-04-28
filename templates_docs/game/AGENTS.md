# AGENTS.md — Game 에이전트 운영 가이드

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
[Implementer subagent] 코드 구현 + 스테이지 데이터 생성
    ↓  Agent(subagent_type="reviewer")
[Reviewer subagent] 품질 검증 + 테스트 확인 (READ-ONLY)
    ↓  Agent(subagent_type="documenter")
[Documenter subagent] 문서 동기화 + 커밋
```

> ⚠️ **Orchestrator는 별도 subagent 파일이 없다.**
> Main Claude Session이 직접 Orchestrator 역할을 수행한다.

> ℹ️ **Stage Builder, QA Agent는 현재 별도 subagent로 분리되지 않는다.**
> Stage Builder → Implementer 흡수 / QA Agent → Reviewer 흡수.
> 향후 프로젝트 규모에 따라 분리 검토. (`build-stage.md` 참조)

---

## 1. 🎯 Orchestrator (Main Session)

**파일**: 없음 — Main Session 자체
**행동 정의**: `CLAUDE.md` + `.claude/commands/orchestrate.md`

| 작업 | 설명 |
|------|------|
| 작업 분석 | product-specs + core-beliefs 확인, Blocker 체크 |
| Pipeline 결정 | 코드만 / 스테이지만 / 코드+스테이지 분류 |
| subagent 분배 | implementer / reviewer / documenter 순서로 spawn |
| 흐름 제어 | 실패 시 재시도 또는 에스컬레이션 |

**자율 결정 가능:**
- 구현 순서 및 작업 분리
- subagent 호출 내용 구체화
- 재시도 전략 (1~2회)

**사람 승인 필요:**
- product-specs에 없는 기능 확장
- core-beliefs.md 체크리스트 위반 가능성
- 3회 재시도 후에도 실패

---

## 2. 🏗️ Implementer (subagent)

**파일**: `.claude/agents/implementer.md`
**도구**: Read, Write, Edit, Bash, Grep, Glob
**담당 범위**: 코드 구현 + 스테이지 데이터 생성 (Stage Builder 흡수)

| 작업 | 설명 |
|------|------|
| 컴포넌트 구현 | 게임 오브젝트 (Player, Enemy, Item 등) |
| 시스템 구현 | 충돌, 물리, 아이템 효과 등 |
| UI 구현 | HUD, 오버레이, 메뉴 화면 |
| 스테이지 생성 | 스테이지 JSON/데이터 파일 생성 + 자체 검증 |

**자율 결정 가능:**
- 코드 구조 및 파일 분리
- 엔진/프레임워크 API 선택
- 성능 최적화 방법 (오브젝트 풀링 등)
- 스테이지 배치 패턴 및 구성 (구간 기준 내)

**사람 승인 필요:**
- 외부 패키지 신규 추가
- core-beliefs.md 원칙 위반 가능성
- 수익화 구조 변경
- 스테이지 시스템 규칙 변경 (`stage-system.md` 수정)

**인수인계 조건 (Reviewer에게):**
- 정적 분석 명령어 경고 0건
- 변경 파일 목록 + 구현 내용 명시
- 스테이지 작업 시 자체 스키마 검증 통과

---

## 3. 🔍 Reviewer (subagent)

**파일**: `.claude/agents/reviewer.md`
**도구**: Read, Grep, Glob, Bash (**★ Write/Edit 없음 — READ-ONLY**)
**담당 범위**: 코드 품질 검증 + 테스트 실행 + 스테이지 검증 (QA Agent 흡수)

| 작업 | 설명 |
|------|------|
| 정적 분석 실행 | analyze 명령어 실행, 경고 0건 확인 |
| 테스트 실행 | 전체 테스트 + 핵심 파일 테스트 존재 확인 |
| 상태 반응성 | HUD/오버레이 리빌드 테스트 존재 확인 |
| 스테이지 검증 | JSON 스키마, 클리어 가능 여부 (QUALITY_SCORE.md §6) |

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

**사람 승인 필요:**
- 스펙과 구현이 다른 경우
- PLANS.md Phase 완료 선언
- /learn 변경 계획 승인
- `core-beliefs.md` / `ARCHITECTURE.md` 수정 (사람만 가능)

---

## 협업 흐름

### 정상 경로 (코드 작업)
```
[사람] /orchestrate [기능명]
  ↓
[Orchestrator] 스펙 분석 + implementer 호출
  ↓
[Implementer] 구현 완료
  ↓
[Reviewer] 테스트 + 검증 통과
  ↓
[Documenter] 문서 동기화 + 커밋
```

### 정상 경로 (Small)
```
[Main Session] 직접 구현 → [Reviewer] 검증 → [Documenter] 문서 + 커밋
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

조건 및 보고 형식: `core_docs/ESCALATION_PROTOCOL.md`
공통 조건: `core_docs/WORKFLOW.md` §6
