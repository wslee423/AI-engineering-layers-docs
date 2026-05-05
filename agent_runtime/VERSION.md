# agent_runtime VERSION

**현재 버전**: v0.1.0

---

## 버전 정책 (Semantic Versioning)

| 변경 종류 | 버전 올리기 | 예시 |
|----------|-----------|------|
| **patch** | 오타 수정, 문구 개선, non-breaking 체크 추가 | v0.1.0 → v0.1.1 |
| **minor** | 새로운 command, hook, agent 규칙 추가 | v0.1.0 → v0.2.0 |
| **major** | 기존 command contract 변경, 필수 문서 구조 변경, 호환성 깨짐 | v0.1.0 → v1.0.0 |

---

## 업그레이드 원칙

- 기존 프로젝트의 `.claude/`는 **자동으로 업그레이드하지 않는다.**
- Owner가 `UPGRADE_GUIDE.md`를 확인하고 수동으로 진행한다.
- minor/major 변경 시 `UPGRADE_GUIDE.md`에 마이그레이션 지침을 추가한다.

---

## 버전 히스토리

| 버전 | 날짜 | 요약 |
|------|------|------|
| v0.1.0 | 2026-05-05 | V3 리팩터링 — 4축 구조(core/templates/agent_runtime/knowledge) 도입, ESCALATION_BLOCK 통합 |
