# AGENTS_PATTERN.md — 에이전트 정의 패턴

> Core Layer 문서. 프로젝트의 `AGENTS.md` 작성 표준.
> 에이전트 구조(Orchestrator/Pipeline/Hybrid)는 Template Layer에서 선택한다.

---

## 1. 에이전트 표준 템플릿

모든 에이전트는 아래 형식으로 정의한다. 4가지 필수 요소를 모두 포함.

```markdown
### [N]. [이모지] [에이전트명] (표준 역할: [Orchestrator/Implementer/...])
**담당**: (한 줄 설명)

| 작업 | 설명 |
|------|------|
| (작업1) | (설명) |

**자율 결정 가능:**
- (항목1)
- (항목2)

**사람 승인 필요:**
- (항목1)
- (항목2)

**인수인계 조건 (다음 단계로 넘어가려면):**
- (검증 가능한 조건, 수치 기준 권장: 예 "typecheck 통과", "fps 60 이상")
```

**4가지 필수 요소:**
1. **담당** — 무엇을 하는가 (한 줄)
2. **자율 범위** — 사람 승인 없이 가능한 것
3. **승인 범위** — 사람에게 반드시 물어볼 것
4. **인수인계 조건** — 검증 가능한 완료 조건

---

## 2. 에이전트 구조

현재 표준 구조는 **Orchestrator 패턴**. SaaS/Game 템플릿 모두 이 구조를 사용한다.

### 2-1. Orchestrator 패턴 (표준)

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

> 표준 역할 정의는 `GLOSSARY_TEMPLATE.md` 참조.

---

## 3. 에이전트 간 통신 프로토콜

### 3-1. 완료 보고
```
## 완료: [작업명]
- 구현/산출: [간략 설명]
- 변경 파일: [목록]
- 검증: ✅ [항목1] / ✅ [항목2]
- 다음: [다음 에이전트 또는 완료]
```

### 3-2. 실패 보고
```
## 실패: [작업명]
- 원인: [구체적 이유]
- 시도: [무엇을 했는지]
- 필요: [다음 단계 제안 또는 질문]
```

### 3-3. 에스컬레이션
`ESCALATION_PROTOCOL.md` 참조.

---

## 4. 에이전트 공통 금지 사항

프로젝트별 추가 금지 사항은 각 AGENTS.md에 작성. 아래는 **모든 프로젝트 공통**.

- CONSTITUTION 불변 원칙 위반
- 하드코딩된 시크릿/API 키 커밋
- 조용한 실패 (빈 catch, 에러 무시)
- **사람 승인 없는 변경** (아래 모두 해당):
  - 외부 의존성 추가 (package.json, pubspec.yaml 등)
  - 데이터 스키마 변경 (DB, 저장 포맷, API 스펙, JSON 스키마)
  - CONSTITUTION / CLAUDE.md / AGENTS.md 수정

> 프로젝트별 구체 금지 패턴은 `ANTI_PATTERNS.md` 참조 (3차 생성 예정).

---

## 5. 새 에이전트 추가 기준

추가하려면 **아래 4가지 모두 Yes** 여야 한다.

- [ ] 기존 에이전트로 커버 안 되는 고유 역할이 있는가?
- [ ] 자율 범위와 승인 범위가 명확한가?
- [ ] 인수인계 조건이 검증 가능한가?
- [ ] 다른 에이전트와 충돌 시 우선순위가 정해지는가?

하나라도 No면 **기존 에이전트를 확장**하는 것을 우선 고려.
