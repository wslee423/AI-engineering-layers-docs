# LESSON_LIFECYCLE.md — 교훈 승격 체계

> Core Layer 문서. AI 에이전트가 점차 좋아지게 하기 위한 5단계 교훈 승격 doctrine.

---

## 왜 필요한가

세션마다 발견되는 문제들은 두 가지 운명을 맞이한다.

1. **사라진다** — 세션 종료와 함께 잊힘. 같은 실수가 반복됨.
2. **문서에만 박힌다** — `LESSONS_LEARNED.md`에 적혔지만 다음 세션 에이전트 행동은 변하지 않음.

이 문서는 두 운명을 모두 피하는 **5단계 승격 체계**를 정의한다.
교훈이 발견되면 그 무게에 맞는 레벨로 승격되고, 그 레벨에 도달한 순간 에이전트의 실제 행동이 변한다.

---

## 5단계

| 레벨 | 위치 | 효과 |
|------|------|------|
| **L1** | `LESSONS_LEARNED.md` | 사례 기록 — 사람이 회고하면 나옴 |
| **L2** | `ANTI_PATTERNS.md` | 패턴화 — "X 하지 말고 Y 해라" 규칙으로 명문화 |
| **L3** | `QUALITY_SCORE.md` | 기계적 검증 — Reviewer가 매번 체크 |
| **L4** | `.claude/agents/*.md` 또는 `.claude/commands/*.md` | 에이전트 행동 변경 — Implementer가 애초에 안 함 |
| **L5** | `templates/<type>/*` | 템플릿 기본값 — 다음 프로젝트는 처음부터 적용 |

레벨이 높아질수록 **변경 비용은 커지지만, 행동 변경 효과도 강해진다.**

---

## 승격 기준

### L1 → L2 (사례 → 패턴)

다음 중 **하나** 충족:
- 같은 문제가 두 번 이상 발생
- 회고 시 "이건 명백히 피할 수 있었다"는 합의

### L2 → L3 (패턴 → 기계적 검증)

다음 **모두** 충족:
- grep / lint / typecheck / test 같은 자동 도구로 탐지 가능
- 매번 체크해도 되는 비용 (false positive가 적음)

### L3 → L4 (검증 → 강제 행동)

다음 중 **하나** 충족:
- Reviewer가 잡았는데도 Implementer가 같은 실수를 3회 이상 반복
- 사후 검증보다 사전 차단이 더 효율적이라는 판단

### L4 → L5 (강제 행동 → 템플릿 기본값)

다음 **모두** 충족:
- 같은 프로젝트 타입(saas/game)의 대부분에 적용됨
- 프로젝트 특성과 무관한 보편적 규칙

> 대부분의 교훈은 **L2~L3에서 멈추는 것이 정상**이다. L4~L5는 정말 핵심적인 원칙만.

---

## 강등 / 제거 기준

다음 경우 해당 항목을 제거하거나 강등한다:

- 6개월 이상 위반 사례 0건 → L3에서 L2로, 또는 완전 제거
- 기술 스택 변경으로 무의미해짐 → 해당 항목 제거
- false positive가 빈번해 운영을 방해 → L3 → L2 강등 (사람이 회고할 때만 참조)

---

## /learn 흐름과의 통합

`.claude/commands/learn.md`의 `/learn "<문제>"` 슬래시 명령이 이 doctrine을 실행한다.

1. Owner가 `/learn` 호출 + 문제 설명 전달
2. Documenter subagent가 위 승격 기준에 따라 레벨 판정 + 변경 계획 작성
3. Owner 승인
4. Documenter가 변경 적용 + 단일 커밋 (`learn(L<n>): <요약>`)
5. L4 또는 L5인 경우 → `BACKPORT_PROPOSAL.md` 생성 (ai_engineering_docs로의 역방향 제안)

---

## 적용 예시

### 예시 1 — `select('*')` 남용

- L1: 한 번 발견 → LESSONS_LEARNED.md 기록
- L2: 두 번째 발견 → ANTI_PATTERNS.md "select('*') 금지, 명시 컬럼만"
- L3: grep 가능 → QUALITY_SCORE.md "select('*') 사용 0건 확인"
- L4: 굳이 갈 필요 없음 (Reviewer가 잡으면 충분)

→ **L3에서 정착**

### 예시 2 — Soft delete 누락

- L1: 발견 → LESSONS_LEARNED.md
- L2: ANTI_PATTERNS.md "Hard delete 금지"
- L3: QUALITY_SCORE.md "`.delete()` 사용 0건 확인"
- L4: Implementer 시스템 프롬프트에 "재무 데이터 hard delete 금지" 추가
- L5: templates/saas/QUALITY_SCORE.md 기본값에 포함

→ **L5까지 승격** (모든 saas 프로젝트에 적용 가능한 핵심 규칙)
