# AGENTS_EXT.md — mobile-game 에이전트 확장

> 이 파일의 내용은 프로젝트 `AGENTS.md`의 `## Pack Extensions` 섹션에 병합한다.
> BASE 에이전트 정의에 추가되는 게임 특화 행동만 포함한다.

---

## [EXT] Orchestrator — 게임 Pipeline 분류

작업 시작 전 아래 파이프라인을 결정한다:

| 작업 유형 | 파이프라인 |
|----------|-----------|
| 코드만 변경 | Implementer → Reviewer |
| 스테이지 데이터만 생성 | Implementer (스테이지 생성 모드) → Reviewer |
| 코드 + 스테이지 | Implementer (코드) → Implementer (스테이지) → Reviewer |

---

## [EXT] Implementer — 게임 구현 범위 및 순서

**담당 범위**: 코드 구현 + 스테이지 데이터 생성 (Stage Builder 흡수)

게임 기능 구현 순서:
```
1. 게임 오브젝트 (Player, Enemy, Item 등)
2. 시스템 (충돌, 물리, 아이템 효과)
3. UI (HUD, 오버레이, 메뉴)
4. 스테이지 데이터 생성 + 자체 스키마 검증
5. 연결 + 통합 확인
6. 검증 명령어 실행
```

### Implementer 추가 제약 (Game)

- 외부 패키지 무단 추가 금지
- 수익화 구조 변경은 사람 승인 필수
- 스테이지 시스템 규칙 변경 (`stage-system.md` 수정) 시 사람 승인 필수
- 스테이지 생성 시 자동 검증 (클리어 가능 여부, 스키마) 필수

---

## [EXT] Reviewer — 게임 검증 항목

BASE 검증 외 추가 체크:

| 항목 | 확인 방법 |
|------|----------|
| 핵심 파일 각각 테스트 존재 | CLAUDE.md 핵심 파일 목록 대조 |
| 새 HUD/오버레이 상태 반응성 테스트 존재 | 테스트 파일 확인 |
| fps 목표 달성 | 수동 또는 자동 측정 |
| 메모리 누수 없음 | 씬 전환 후 확인 |
| 스테이지 JSON 스키마 유효성 | 검증 스크립트 실행 |
| 클리어 가능 여부 | 자동 또는 수동 확인 |
| 저장 실패 시 복구 동작 | 엣지 케이스 수동 확인 |

> Stage Builder 역할은 현재 Implementer가 흡수.
> 스테이지 작업 규모가 전체의 30% 이상이 되면 별도 분리 검토 (`commands/build-stage.md` 참조).
