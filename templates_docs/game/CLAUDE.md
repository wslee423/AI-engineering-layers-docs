# CLAUDE.md — [프로젝트명] 에이전트 컨텍스트

> Claude Code가 매 세션 자동으로 읽는 핵심 컨텍스트.
> 간결하게 유지. 상세 규칙은 AGENTS.md 및 docs/ 참조.

---

## 먼저 읽을 문서

1. `CONSTITUTION.md` — 최상위 원칙 (항상)
2. `docs/design-docs/core-beliefs.md` — 게임 설계 철학 (항상)
3. `AGENTS.md` — 에이전트 역할 및 자율 범위
4. `docs/PLANS.md` — 현재 Phase 및 다음 작업
5. `docs/exec-plans/NEXT_SESSION.md` — 이전 세션 핸드오프 (있으면)

---

## 프로젝트 개요

**[프로젝트명]**: (한 줄 설명)
**장르**: (예: 캐주얼 / 퍼즐 / RPG / 아케이드)
**플랫폼**: (아래에서 선택)

### 플랫폼/스택 선택 가이드
프로젝트 생성 시 실제 스택으로 교체할 것.

| 역할 | Mobile Game | PC Game |
|------|------------|---------|
| Framework | Flutter + Flame | Unity / Godot |
| Language | Dart | C# / GDScript |
| 저장소 | SharedPreferences / Hive | PlayerPrefs / 파일 |
| 광고 | AdMob | — |
| 배포 | Google Play / App Store | Steam / itch.io |

**현재 프로젝트 스택:**
| 역할 | 기술 |
|------|------|
| Framework | |
| Language | |
| 저장소 | |
| 배포 | |

---

## 핵심 명령어

```bash
# 개발 실행 (실제 명령어로 교체)
[run 명령어]

# 검증 (매 작업 후 반드시 실행)
[analyze 명령어]
[test 명령어]
```

---

## Critical Rules

### Must Follow
- `core-beliefs.md` 체크리스트 항상 확인
- 타입 안정성 필수
- 핵심 파일 수정 시 테스트 필수 (아래 목록 참조)
- 스테이지/콘텐츠 생성 시 자동 검증 필수
- 조용한 실패 금지

### 핵심 파일 목록 (테스트 없이 수정 금지)
```
# 프로젝트 생성 시 실제 파일로 교체
[핵심 파일 1]  ← 예: ball.dart, Player.cs
[핵심 파일 2]  ← 예: collision_system.dart
[핵심 파일 3]
[핵심 파일 4]
```

### Must NOT Do
- Pay-to-Win 요소 추가 (CONSTITUTION 위반)
- 외부 패키지 무단 추가
- 데이터 스키마 무단 변경
- 클리어 불가능한 스테이지 생성

---

## 파일 구조

```
/                              ← 레포 루트
  CONSTITUTION.md
  CLAUDE.md                   ← 이 파일
  AGENTS.md
  QUALITY_SCORE.md
  README.md
  lib/                        ← 게임 코드 (Mobile) 또는 Assets/ (Unity)
    game/
      components/             ← 게임 오브젝트
      systems/                ← 게임 로직
    ui/                       ← UI 화면
    data/
      stages/                 ← 스테이지 데이터 (JSON 등)
  test/                       ← 테스트
  docs/
    PLANS.md
    ARCHITECTURE.md
    QUALITY_SCORE.md
    design-docs/
      core-beliefs.md         ← 게임 설계 철학
      visual-design.md        ← 비주얼 가이드
    product-specs/
    exec-plans/
      open-decisions.md
      tech-debt-tracker.md
      NEXT_SESSION.md
  .claude/
    commands/
      orchestrate.md
      run-qa.md
      build-stage.md
      sync-docs.md
```

*(실제 프로젝트 구조에 맞게 수정)*
