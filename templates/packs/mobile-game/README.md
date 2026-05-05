# mobile-game Pack

> Mobile/PC 게임 프로젝트용 확장 팩.
> base 템플릿에 게임 특화 성능, 스테이지 검증, 게임 루프 기준을 추가한다.

---

## 포함 파일

| 파일 | 병합 대상 | 내용 |
|------|----------|------|
| `QUALITY_EXT.md` | `QUALITY_SCORE.md §Pack Extensions` | 성능, 스테이지 검증, 게임 플레이 품질, 출시 체크리스트 |
| `AGENTS_EXT.md` | `AGENTS.md §Pack Extensions` | Implementer 게임 구현 순서, Stage Builder 흡수, Reviewer 게임 검증 |
| `ARCHITECTURE_EXT.md` | 참조 전용 | Mobile/PC 게임 스택 + 게임 루프 설계 원칙 |
| `CHECKLIST_EXT.md` | Reviewer 체크리스트 보충 | 성능/스테이지/안정성 추가 검증 항목 |

> 이 Pack은 command를 직접 포함하지 않습니다. 스테이지 생성 전용 실행 흐름이 필요한 경우 `agent_runtime/commands/build-stage.md`를 참조하세요. command/agent/hook의 원본은 항상 `agent_runtime/`에만 둡니다.

---

## 적용 대상

- Flutter + Flame 기반 모바일 게임
- Unity / Godot 기반 PC/모바일 게임
- 스테이지 기반 / 캐주얼 / 미드코어 게임

---

## 적용 방법

1. EXT 파일 내용을 프로젝트 각 base 문서의 `## Pack Extensions` 섹션에 병합
2. `ARCHITECTURE_EXT.md`를 프로젝트 `ARCHITECTURE.md` 작성 참고로 사용
3. `.harness/manifest.yml`의 `applied_packs`에 `mobile-game` 추가

---

## 게임 특화 추가 문서 (프로젝트에서 생성)

- `docs/design-docs/core-beliefs.md` — 게임 설계 철학 (게임 복잡도에 따라)
- `docs/design-docs/stage-system.md` — 스테이지 시스템 규칙
