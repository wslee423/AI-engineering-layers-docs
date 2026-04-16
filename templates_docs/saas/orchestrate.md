# /orchestrate — 기능 구현 전체 흐름

Orchestrator 에이전트로서 기능 구현 전체 흐름을 진행한다.

---

## 실행 순서

### 1. 컨텍스트 파악
```
- CONSTITUTION.md 확인
- docs/PLANS.md에서 해당 기능 확인
- docs/product-specs/[기능].md 분석
- open-decisions.md에서 🔴 Blocker 없는지 확인
```

### 2. 기능 크기 판단
`WORKFLOW.md` 섹션 3 기준으로 Small/Large 판단.
- **Small** → Implementer 자체 완료 허용
- **Large** → 구현 후 사람 최종 승인 필요

### 3. Implementer 서브에이전트 생성

아래 형식으로 지시:
```
다음 기능을 구현하세요: [기능명]

스펙 문서: docs/product-specs/[기능].md
구현 규칙: CLAUDE.md, AGENTS.md
구현 순서: DB 스키마 → 타입 → API → UI → 검증

완료 후 아래 형식으로 보고:
## 완료: [기능명]
- 구현 내용: [요약]
- 변경 파일: [목록]
- 검증: ✅ typecheck / ✅ lint
```

### 4. Reviewer 호출

Implementer 완료 보고 수신 후:
```
/review 실행
```

### 5. 결과 처리

| 결과 | 행동 |
|------|------|
| 검증 통과 | Documenter 호출 (`/sync-docs`) |
| 검증 실패 (1~2회) | Implementer에게 실패 내용 전달 후 재작업 |
| 검증 실패 (3회) | 에스컬레이션 → 사람에게 보고 |

### 6. 완료 보고

```
## ✅ [기능명] 완료

- Implementer: 구현 완료
- Reviewer: 검증 통과
- Documenter: 문서 동기화 + 커밋 완료

다음 작업: docs/PLANS.md 확인
```
