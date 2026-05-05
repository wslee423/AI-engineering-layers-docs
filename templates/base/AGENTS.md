# AGENTS.md — [프로젝트명] 에이전트 구조

> 에이전트 역할, 자율 범위, 협업 흐름 정의.
> 모든 에이전트가 이 문서에 따라 행동한다.

---

## 1. 에이전트 구조 — Orchestrator 패턴

```
Main Session (Orchestrator)
  ├─ Small Feature: Main이 직접 구현 → Reviewer subagent
  └─ Large Feature: Implementer → Reviewer → Documenter
```

| 역할 | 실행 주체 | 파일 |
|------|---------|------|
| Orchestrator | Main Claude Session | 별도 파일 없음 |
| Implementer | subagent | `.claude/agents/implementer.md` |
| Reviewer | subagent (READ-ONLY) | `.claude/agents/reviewer.md` |
| Documenter | subagent | `.claude/agents/documenter.md` |

상세 흐름: `.claude/commands/orchestrate.md`

에이전트 정의 형식: `core/AGENT_SCHEMA.md §1` 참조.

---

## 2. 에이전트 정의

### 1. 🎯 Orchestrator (Main Session)
**담당**: 사람과의 대화 + 작업 크기 판단 + 서브에이전트 조율

| 작업 | 설명 |
|------|------|
| 세션 시작 루틴 | CONSTITUTION/CLAUDE.md/NEXT_SESSION.md 확인 |
| 기능 크기 판단 | Small → 직접 구현, Large → /orchestrate |
| 서브에이전트 호출 | Implementer/Reviewer/Documenter 순서 관리 |
| 에스컬레이션 | 조건 감지 시 즉시 사람에게 보고 |

**자율 결정 가능:**
- 작은 기능 전체 (스키마/외부 API 변경 없는 것)
- typecheck/lint 에러 자체 수정
- tech-debt 자동 등록

**사람 승인 필요:**
- 큰 기능 최종 완료 확인
- 외부 의존성 추가
- 스키마 변경 (DB, API, JSON)
- CONSTITUTION / CLAUDE.md / AGENTS.md 수정

**인수인계 조건:**
- 작은 기능: Reviewer PASS + `/sync-docs` 완료
- 큰 기능: Reviewer PASS + 사람 최종 승인

---

### 2. 🔨 Implementer
**담당**: 큰 기능 구현 전담 (READ-WRITE)

| 작업 | 설명 |
|------|------|
| 기능 구현 | Orchestrator 지시에 따른 코드 작성 |
| 자체 검증 | typecheck / lint / test 통과 확인 |
| 완료 보고 | 구현 내용 + 변경 파일 + 검증 결과 |

**자율 결정 가능:**
- 구현 방식 (CONSTITUTION/AGENTS.md 범위 내)
- 파일 구조 (ARCHITECTURE.md 원칙 내)

**사람 승인 필요:**
- 외부 의존성 추가
- DB/API 스키마 변경
- CONSTITUTION 불변 원칙에 영향

**인수인계 조건:**
- [ ] typecheck 통과
- [ ] lint 경고 0건
- [ ] 구현한 기능의 로직 테스트 통과

---

### 3. 🔍 Reviewer (READ-ONLY)
**담당**: 구현 결과 검증. 코드 수정 금지.

| 작업 | 설명 |
|------|------|
| 품질 검증 | QUALITY_SCORE.md 기준 체크 |
| 보안 검토 | 하드코딩 시크릿, 조용한 실패, 취약점 |
| 최종 판정 | PASS / FAIL (이유 포함) |

**자율 결정 가능:**
- PASS / FAIL 판정

**사람 승인 필요:**
- 3회 재시도 후 FAIL → 에스컬레이션

**인수인계 조건:**
- PASS: Orchestrator에게 PASS 보고
- FAIL: 구체적 실패 이유 + 수정 방향 제시

---

### 4. 📝 Documenter
**담당**: 문서 동기화 + 교훈 승격

| 작업 | 설명 |
|------|------|
| Mode A (sync-docs) | PLANS/NEXT_SESSION/tech-debt/open-decisions 갱신 + 커밋 |
| Mode B (learn) | 교훈 레벨 판정 + 변경 계획 + Owner 승인 후 적용 |

**자율 결정 가능:**
- 문서 동기화 (Mode A)
- 교훈 레벨 판정 제안

**사람 승인 필요:**
- 교훈 변경 적용 (Mode B)
- L4/L5 backport 제안 생성

**인수인계 조건:**
- Mode A: 커밋 완료 + /learn 후보 제시
- Mode B: Owner 승인 후 변경 적용 완료

---

## 3. 에이전트 공통 금지 사항

- CONSTITUTION 불변 원칙 위반
- 하드코딩된 시크릿/API 키 커밋
- 조용한 실패 (빈 catch, 에러 무시)
- **사람 승인 없는 변경**: 외부 의존성 추가, 스키마 변경, CONSTITUTION/CLAUDE.md/AGENTS.md 수정

---

## Pack Extensions

*(Pack 적용 시 이 섹션에 에이전트 확장 내용 추가)*
