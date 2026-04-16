# /orchestrate — 기능 구현 전체 흐름

Orchestrator 에이전트로서 기능 구현 전체 흐름을 진행한다.

---

## 실행 순서

### 1. 컨텍스트 파악
```
- CONSTITUTION.md 확인
- docs/design-docs/core-beliefs.md 확인 (핵심 원칙 체크리스트)
- docs/PLANS.md에서 해당 기능 확인
- docs/product-specs/[기능].md 분석
- open-decisions.md에서 🔴 Blocker 없는지 확인
```

### 2. Pipeline 필요 에이전트 판단

| 작업 유형 | 필요 에이전트 |
|----------|------------|
| 코드 구현 | Implementer → QA → Documenter |
| 스테이지 생성만 | Stage Builder → Documenter |
| 코드 + 스테이지 | Implementer → QA → Stage Builder → Documenter |
| 버그 수정 | Implementer → QA → Documenter |

### 3. Implementer 서브에이전트 생성

```
다음 기능을 구현하세요: [기능명]

스펙 문서: docs/product-specs/[기능].md
핵심 원칙: docs/design-docs/core-beliefs.md
구현 규칙: CLAUDE.md, AGENTS.md

완료 후 아래 형식으로 보고:
## 완료: [기능명]
- 구현 내용: [요약]
- 변경 파일: [목록]
- 검증: ✅ [analyze 명령어]
```

### 4. Pipeline 자동 진행 모니터링

각 에이전트가 인수인계 조건 충족 시 자동으로 다음으로 넘어간다.
Orchestrator는 각 단계 완료/실패 보고를 수신하고 이상 시 개입.

### 5. 실패 처리

| 실패 단계 | 조치 |
|----------|------|
| Implementer 실패 (1~2회) | 실패 내용 전달 후 재작업 |
| QA 실패 (1~2회) | Implementer에게 버그 수정 지시 |
| 3회 연속 실패 | 에스컬레이션 |
| Stage Builder 실패 | Stage Builder에게 재생성 지시 |

### 6. 완료 보고

```
## ✅ [기능명] 완료

Pipeline 결과:
- Implementer: 구현 완료
- QA Agent: 테스트 통과
- Stage Builder: 스테이지 검증 통과 (해당 시)
- Documenter: 문서 동기화 + 커밋 완료

다음 작업: docs/PLANS.md 확인
```
