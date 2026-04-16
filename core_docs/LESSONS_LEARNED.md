# LESSONS_LEARNED.md — 프로젝트 교훈 축적

> **Core Layer의 복리 문서.** 프로젝트를 거듭할수록 쌓이는 실제 경험.
> 한 번 배운 교훈은 여기 남기고, 다음 프로젝트에서 **규칙으로 변환**한다.

---

## 원칙

1. **실제 발생 사례만** 등록 (가상 시나리오 금지)
2. **규칙 변환 필수** — 규칙으로 변환되지 않은 교훈은 무효
3. **사실과 조치만** — 감성 문구 배제

---

## 카테고리

| 범위 | 설명 |
|------|------|
| LL-001~ | 코드/구현 |
| LL-100~ | 문서/프로세스 |
| LL-200~ | 에이전트 운영 |

---

## 등록 형식

```markdown
### LL-XXX: [한 줄 제목]
- **발생**: [프로젝트명], YYYY-MM-DD, Phase N
- **증상**: (무슨 일이 일어났는지)
- **원인**: (근본 원인)
- **대응**: (어떻게 해결했는지)
- **규칙 변환**: [문서명] [섹션/AP-ID]
```

---

## 1. 코드/구현

### LL-001: StatelessWidget의 상태 반응성 누락
- **발생**: fantasy_bricks, 2026-04-12, Phase 1~2 전환기
- **증상**: `lives` 값이 감소해도 HUD의 하트 아이콘이 갱신되지 않음. 로직 테스트는 전부 통과.
- **원인**: `HudOverlay`가 `StatelessWidget`이라 `ValueNotifier` 변경 시 리빌드되지 않음. 로직 테스트는 값만 검증하고 UI 리빌드를 검증하지 않음.
- **대응**:
  - 상태 의존 영역을 `ValueListenableBuilder`로 감쌈
  - 상태 반응성 테스트 3개 추가 (초기 하트 3개 / lives=2 / lives=1)
- **규칙 변환**: `QUALITY_SCORE_BASE.md` §3-1 (상태 반응성 테스트 필수), `ANTI_PATTERNS.md` AP-010

---

### LL-002: Fire-and-forget API 실패 → UI 영구 로딩
- **발생**: link_memory, 2026-04-15, AI 요약 구현 중
- **증상**: `/api/links/[id]/summarize` 실패 시 UI는 계속 스켈레톤 표시. 사용자는 실패를 인지 못 함.
- **원인**: 백엔드가 `fetch(...).catch(() => {})`로 에러를 삼킴. UI는 `ai_summary=null`을 "로딩 중"으로 해석.
- **대응**:
  - DB에 `ai_summary_error` 컬럼 추가 (실패 사유 저장)
  - UI에 `createdAt` 기반 타임아웃 fallback (N초 후 "요약 생성 실패" + 재시도 버튼)
- **규칙 변환**: `QUALITY_SCORE_BASE.md` §4-1 (조용한 실패 금지), `ANTI_PATTERNS.md` AP-001, AP-002

---

### LL-003: UI 상태 분기에서 error가 success 뒤에 숨음
- **발생**: link_memory, 2026-04-15, AI 요약 UI 리뷰 중
- **증상**: `summary`와 `error` 둘 다 null인 초기 실패 상태에서 UI가 영원히 스켈레톤. 재시도 버튼 도달 불가.
- **원인**: 분기 순서가 잘못됨.
  ```tsx
  if (summary) return <Content />         // summary 먼저
  else if (error) return <RetryButton />  // summary=null이어야만 도달
  return <Skeleton />                     // error도 null이면 여기서 멈춤
  ```
- **대응**: error를 최우선 분기.
  ```tsx
  if (summaryError) return <RetryButton reason={summaryError} />
  if (!summary && timedOut) return <RetryButton reason="시간 초과" />
  if (!summary) return <Skeleton />
  return <Content summary={summary} />
  ```
- **규칙 변환**: `QUALITY_SCORE_BASE.md` §5 (UI 상태 머신), `ANTI_PATTERNS.md` AP-011

---

### LL-004: PATCH/DELETE에서 소유권 확인 누락
- **발생**: link_memory, 2026-04-15, 태그 수정 기능 구현 중
- **증상**: 다른 사용자의 `link_tags`를 이론적으로 조작 가능. 보안 검증 중 발견.
- **원인**: `tagIds`만 업데이트하는 경로에서 `user_id` 소유권 확인을 건너뜀. RLS에 의존했지만 모든 경로를 커버하지 못함.
- **대응**: 모든 변경/삭제 라우트에서 소유권 먼저 조회.
  ```ts
  const { data: owned } = await supabase
    .from('links').select('id')
    .eq('id', id).eq('user_id', user.id).maybeSingle()
  if (!owned) return 404
  ```
- **규칙 변환**: `ANTI_PATTERNS.md` AP-020, 프로젝트 QUALITY_SCORE.md SECURITY 규칙에 "인가 완전성" 항목 추가

---

## 2. 문서/프로세스

### LL-100: CONSTITUTION 개정 절차 자체를 개정
- **발생**: fantasy_bricks, 2026-04-12, v1.0 → v1.1
- **증상**: 초기 헌법은 "변하지 않는다"고만 규정. 에이전트가 현실과의 모순을 발견해도 보고할 공식 경로 없음.
- **원인**: 개정 절차에서 에이전트의 역할을 설계하지 않음.
- **대응**: v1.1로 개정 — 6조에 "에이전트는 개정 **제안** 가능, **실행**은 사람만" 명시.
- **규칙 변환**: `CONSTITUTION_TEMPLATE.md` §6 표준 반영 (제안 가능, 실행은 사람)

---

## 3. 에이전트 운영

*(아직 등록된 항목 없음. LL-200부터 등록.)*

---

## 새 교훈 등록 기준 및 검토

### 등록 조건 (모두 Yes여야 등록)
- [ ] 실제 발생한 사례인가? (프로젝트명 + 날짜 명시 가능)
- [ ] 근본 원인이 파악되었는가?
- [ ] 대응이 실행되었는가?
- [ ] 규칙 문서로 변환되었거나 변환 검토 중인가?

### 규칙 변환 검토 (하나 이상 반영)
- [ ] `CONSTITUTION_TEMPLATE.md` — 불변 원칙 승격 수준인가?
- [ ] `QUALITY_SCORE_BASE.md` — 검증 항목 추가 가능한가?
- [ ] `ANTI_PATTERNS.md` — 신규 AP 등록 가능한가?
- [ ] `AGENTS_PATTERN.md` — 공통 금지 사항 추가 필요한가?
- [ ] `WORKFLOW.md` — 에스컬레이션 조건 추가 필요한가?

**한 교훈이 여러 문서에 영향을 줄 수 있다.** 모두 해당하면 모두 반영.

### 주기적 검토 (Phase 완료/회고 시)
- 이번 Phase에서 발생한 교훈이 모두 등록되었는가?
- 규칙으로 변환했는데도 재발한 교훈이 있는가? → 규칙 강화 필요
- 중복된 교훈이 있는가? → 통합
