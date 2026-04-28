# /orchestrate — Game 특화 override

> 보편 명세는 `claude_code/commands/orchestrate.md` 참조.
> 이 파일은 Game 프로젝트 한정 추가 규칙만 정의한다.
> 새 프로젝트 초기화 시 `.claude/commands/orchestrate.md`로 복사.

---

## Game 특화: Large 분류 기준 추가

보편 기준(`claude_code/commands/orchestrate.md` §2) 외에 다음도 Large로 분류:

- 핵심 파일 수정 (`CLAUDE.md`의 "핵심 파일 목록" 참조)
- 게임 시스템 신규 추가 (충돌, 물리, 아이템 효과 등)
- 신규 스테이지/콘텐츠 배치 추가
- 수익화 구조 변경 (광고, IAP 등)

---

## Game 특화: 역할 흡수 정책

Stage Builder, QA Agent는 별도 subagent로 분리하지 않는다.

| 기존 역할 | 흡수처 | 비고 |
|----------|--------|------|
| Stage Builder | Implementer | 스테이지 데이터 생성도 implementer가 수행 |
| QA Agent | Reviewer | 테스트·스테이지 검증은 reviewer가 QUALITY_SCORE.md 기준으로 수행 |

향후 프로젝트 규모가 커져 분리 필요 시 `templates_docs/game/claude_code/commands/build-stage.md` 참조.

---

## Game 특화: Implementer 구현 시퀀스

**코드 작업:**
```
1. 컴포넌트 (Player, Enemy, Item 등)
2. 시스템 (충돌, 물리, 아이템 효과)
3. UI (HUD, 오버레이, 메뉴)
4. 핵심 파일 변경 시 테스트 1개 이상
5. 자체 검증 (analyze + test)
```

**스테이지 데이터 작업 포함 시:**
```
1. 스테이지 JSON/데이터 파일 생성
2. 스키마 유효성 자체 검증
3. 클리어 가능 여부 자체 검증
4. 자체 검증 (analyze + test)
```

---

## Game 특화: Reviewer에게 추가 전달

- 핵심 파일 변경 있는 경우 → "핵심 파일 테스트 존재 확인 필수"
- 스테이지 변경 있는 경우 → "스키마/클리어 가능 여부 검증 필수"
- 새 HUD/오버레이 있는 경우 → "상태 반응성 테스트 존재 확인 필수"
