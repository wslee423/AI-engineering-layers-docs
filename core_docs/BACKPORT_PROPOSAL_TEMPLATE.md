# BACKPORT_PROPOSAL — [요약]

> 프로젝트에서 L4 또는 L5로 승격된 교훈을 `ai_engineering_docs` 본체로 역방향 반영하기 위한 제안서.
> 자동 적용되지 않는다. Owner가 직접 검토 후 수동 적용.

---

## 메타데이터

| 항목 | 값 |
|------|-----|
| Source 프로젝트 | [프로젝트명] |
| 작성일 | YYYY-MM-DD |
| 승격 레벨 | L4 / L5 |
| 작성자 | documenter subagent |

---

## 원래 문제

[/learn에 입력된 문제 설명. 발생 정황과 재현 조건 포함]

---

## 왜 backport해야 하는가

이 교훈이 `ai_engineering_docs` 본체에 반영되어야 하는 이유:

- [ ] 보편적 원칙 (프로젝트 타입과 무관)
- [ ] 같은 타입(saas/game) 다수 프로젝트에 적용됨
- [ ] 기존 템플릿이 잘못된 기본값을 권하고 있음

---

## 제안 변경사항

| 파일 | 변경 내용 |
|------|----------|
| [예: core_docs/QUALITY_SCORE_BASE.md] | [예: §3에 "select('*') 금지" 추가] |
| [예: claude_code/agents/reviewer.md] | [예: 체크리스트에 grep 명령 추가] |

### 영향 분석

- **하위 호환성**: [기존 프로젝트에 영향 있는지]
- **다른 템플릿**: [game 등 다른 타입 템플릿에도 영향 있는지]

---

## Owner 승인

- [ ] 검토 완료
- [ ] 적용 진행
- [ ] 거부 — 사유: [기록]

승인 시 적용 절차:
1. `ai_engineering_docs` 레포에서 위 변경 직접 적용
2. 커밋 메시지: `backport(L<n>): <요약> from <프로젝트명>`
3. 이 BACKPORT_PROPOSAL.md를 프로젝트의 `docs/exec-plans/archive/`로 이동
