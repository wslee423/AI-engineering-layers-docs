# CLAUDE.md — [프로젝트명] 에이전트 컨텍스트

> Claude Code 세션 시작 시 자동 로드되는 컨텍스트.
> 이 파일에 없는 것은 CONSTITUTION.md → AGENTS.md → QUALITY_SCORE.md 순으로 참조.

---

## 이 프로젝트

(프로젝트 한 줄 설명)

- **타입**: (web-saas / mobile-game / ai-agent / data-tool / other)
- **스택**: (주요 기술 2~3가지)
- **현재 Phase**: (Phase N — 이름)

---

## 세션 시작 체크리스트

1. `CONSTITUTION.md` 확인 — 불변 원칙
2. `WORKFLOW.md` 확인 — 사람-에이전트 운영 계약
3. `docs/exec/NEXT_SESSION.md` 확인 — 이전 세션 핸드오프
4. `PLANS.md` 현재 위치 확인
5. `docs/exec/open-decisions.md` — 🔴 Blocker 항목 확인
6. `.harness/manifest.yml` — 적용 팩 및 runtime 버전 확인

---

## 에이전트 명령어

| 명령어 | 용도 |
|--------|------|
| `/orchestrate "<작업>"` | 기능 구현 전체 흐름 (Implementer → Reviewer → Documenter) |
| `/review` | 코드 검증만 단독 수행 |
| `/sync-docs` | 세션 종료 전 문서 동기화 + 커밋 |
| `/learn "<문제>"` | 교훈 승격 (LESSON_LIFECYCLE 5단계) |

---

## 핵심 제약

- 사람 승인 없는 외부 의존성 추가 금지
- 사람 승인 없는 DB/API 스키마 변경 금지
- CONSTITUTION 개정은 사람만 가능
- 큰 기능 완료 후 반드시 `/sync-docs` 실행

---

## 문서 위계

```
CONSTITUTION.md > WORKFLOW.md > CLAUDE.md > AGENTS.md > QUALITY_SCORE.md > ARCHITECTURE.md
```

충돌 시 상위 문서 우선. 해결 안 되면 에스컬레이션 (`.claude/shared/ESCALATION_BLOCK.md`).
