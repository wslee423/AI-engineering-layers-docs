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
| `commands/build-stage.md` | `.claude/commands/` | 스테이지 생성 슬래시 커맨드 (현재 placeholder) |

---

## 적용 대상

- Flutter + Flame 기반 모바일 게임
- Unity / Godot 기반 PC/모바일 게임
- 스테이지 기반 / 캐주얼 / 미드코어 게임

---

## 적용 방법

1. EXT 파일 내용을 프로젝트 각 base 문서의 `## Pack Extensions` 섹션에 병합
2. `commands/build-stage.md`를 프로젝트 `.claude/commands/`에 복사
3. `ARCHITECTURE_EXT.md`를 프로젝트 `ARCHITECTURE.md` 작성 참고로 사용
4. `.harness/manifest.yml`의 `applied_packs`에 `mobile-game` 추가

---

## 게임 특화 추가 문서 (프로젝트에서 생성)

- `docs/design-docs/core-beliefs.md` — 게임 설계 철학 (게임 복잡도에 따라)
- `docs/design-docs/stage-system.md` — 스테이지 시스템 규칙
