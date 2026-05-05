# agent_runtime CHANGELOG

---

## v0.1.0 — 2026-05-05

### 변경 요약
V3 리팩터링. `claude_code/`를 `agent_runtime/`으로 재구성하고, 4축 구조(core / templates / agent_runtime / knowledge)를 도입.

### 추가
- `shared/ESCALATION_BLOCK.md` — 에스컬레이션 공통 블록 (기존 core/ESCALATION_PROTOCOL.md 흡수)
- `VERSION.md` — agent_runtime Semantic Versioning 도입
- `CHANGELOG.md` — 이 파일
- `UPGRADE_GUIDE.md` — 기존 프로젝트 수동 업그레이드 지침

### 변경
- 모든 command/agent 파일의 경로 참조 갱신:
  - `core_docs/AGENTS_PATTERN.md` → `core/AGENT_SCHEMA.md`
  - `core_docs/WORKFLOW.md §3` → `WORKFLOW.md §3` (프로젝트 루트)
  - `core_docs/ESCALATION_PROTOCOL.md` → `.claude/shared/ESCALATION_BLOCK.md`
  - `core_docs/LESSON_LIFECYCLE.md` → `core/LESSON_LIFECYCLE.md`
  - `docs/exec-plans/` → `docs/exec/`
  - `docs/exec-plans/BACKPORT_PROPOSAL.md` → `docs/exec/backports/BP-XXX.md`
  - `templates_docs/<type>/claude_code/commands/orchestrate.md` → `AGENTS.md §Pack Extensions`
- `settings.json` 코멘트 갱신 (agent_runtime 복사 흐름 명시)

### 구조
- `claude_code/` → `agent_runtime/`
- `core_docs/` → `core/`
- `templates_docs/saas/`, `game/` → `templates/packs/web-saas/`, `mobile-game/` (Step 5)

---

_이전 버전 없음 (v0.1.0이 최초 버전)_
