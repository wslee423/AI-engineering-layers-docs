# SELF_IMPROVING.md — 자기 개선 루프

> 프로젝트에서 발견한 교훈이 어떻게 이 레포(ai_engineering_docs)로 역방향 반영되는지 정의한다.
> 이 루프가 없으면 같은 실수가 다른 프로젝트에서 반복된다.

---

## 두 레포의 역할 구분

| 레포 | 역할 | 원칙 |
|------|------|------|
| **프로젝트 repo** | 실제 제품 개발. `project/.claude/`는 복사된 local runtime. | 원본 수정 금지. Backport Proposal까지만 생성. |
| **ai_engineering_docs repo** | 하네스 원본. 여러 프로젝트의 교훈을 흡수해 개선. | 이 레포에서 직접 Claude Code를 열고 반영. |

**핵심 원칙:** 프로젝트 repo에서 `ai_engineering_docs`를 직접 수정하지 않는다.
교훈은 `docs/exec/backports/BP-XXX.md`로 제안하고, `ai_engineering_docs` 레포에서 Owner가 직접 반영한다.

---

## 5단계 승격 체계

| 레벨 | 위치 | 효과 |
|------|------|------|
| **L1** | 프로젝트 `docs/exec/lessons.md` | 사례 기록 — 사람이 회고하면 나옴 |
| **L2** | `knowledge/ANTI_PATTERNS.md` | "X 하지 말고 Y 해라" 패턴으로 명문화 |
| **L3** | 프로젝트 `QUALITY_SCORE.md` | Reviewer가 매번 기계적으로 체크 |
| **L4** | `.claude/agents/*.md` 또는 `.claude/commands/*.md` | Implementer가 애초에 안 하게 됨 |
| **L5** | `templates/base/*` 또는 `templates/packs/*` | 다음 프로젝트는 처음부터 적용 |

레벨이 높아질수록 변경 비용은 크지만 행동 변경 효과도 강해진다.
**대부분의 교훈은 L2~L3에서 멈추는 것이 정상.** L4~L5는 정말 핵심 원칙만.

승격 상세 기준: `core/LESSON_LIFECYCLE.md`

---

## 전체 self-improving 흐름

### [프로젝트 repo에서]

```
1. project/.claude/ 수정 (local runtime 개선)
        ↓
2. 프로젝트에서 검증 (실제 기능 구현에 적용)
        ↓
3. docs/exec/lessons.md 에 교훈 기록 (L1)
        ↓
4. /sync-docs 실행 → /learn 후보 1~3개 제시
        ↓
5. Owner가 후보 중 하나 선택 → /learn "문제 설명" 실행
        ↓
6. Documenter Mode B: 레벨 판정 + 변경 계획 작성 + Owner 승인 대기
        ↓
7. Owner 승인 → Documenter가 변경 적용 + 커밋
        ↓
8. L4/L5인 경우 → docs/exec/backports/BP-XXX.md 자동 생성
        ↓
9. Owner에게 알림: "ai_engineering_docs 반영 검토 필요"
```

### [ai_engineering_docs repo에서]

```
10. ai_engineering_docs 레포에서 Claude Code 실행
        ↓
11. BP-XXX.md 내용 + 프로젝트 repo 맥락 확인
        ↓
12. 반영 위치 결정:
    - agent_runtime/commands/ or agents/ → runtime 동작 개선
    - templates/base/ → 다음 프로젝트 기본 문서 개선
    - templates/packs/[pack]/ → 특정 타입 확장 개선
    - core/ → 원칙/가이드 개선
    - knowledge/ → 패턴/교훈 축적
        ↓
13. 해당 파일 수정
        ↓
14. VERSION.md 업데이트 (버전 정책 기준)
        ↓
15. CHANGELOG.md 업데이트 (backport(L<n>): <요약> from <프로젝트명>)
        ↓
16. 필요 시 UPGRADE_GUIDE.md 업데이트
        ↓
17. 커밋: backport(L<n>): <요약> from <프로젝트명>
```

### [다음 프로젝트]

```
18. 최신 agent_runtime/ → 새 프로젝트 .claude/로 복사
        ↓
19. 개선된 하네스로 시작 (이전 교훈이 기본값으로 내장)
```

### [기존 프로젝트]

```
자동 업그레이드 없음
        ↓
필요 시 UPGRADE_GUIDE.md 확인 → Owner가 수동 업그레이드 결정
```

---

## 세션 종료 시 실행 흐름 (`/sync-docs`)

```
[Documenter Mode A]
  ↓
PLANS.md + NEXT_SESSION.md + tech-debt + open-decisions 갱신
  ↓
/learn 후보 1~3개 제시 (실행 안 함)
  ↓
Owner가 후보 중 하나를 선택하면 → /learn 실행
```

---

## 교훈 승격 흐름 (`/learn "<문제>"`)

```
[Owner] /learn "Reviewer가 select('*')를 두 번째 잡았는데 Implementer가 또 씀"
  ↓
[Documenter Mode B]
  1. L1~L5 레벨 판정
  2. 변경 계획 작성 (어떤 파일을 어떻게 바꿀지)
  3. Owner 승인 대기
  ↓
[Owner] 승인 / 수정 / 거부
  ↓
[Documenter] 승인된 변경 적용 + 커밋
  ↓
L4 또는 L5인 경우 → docs/exec/backports/BP-XXX.md 자동 생성
```

---

## Backport 파일 형식

`docs/exec/backports/BP-XXX.md` (프로젝트에서 자동 생성 — proposal만):

```markdown
# BP-001: [요약]

## 메타데이터
| Source 프로젝트 | [프로젝트명] |
| 승격 레벨 | L4 / L5 |
| 작성일 | YYYY-MM-DD |

## 원래 문제
[/learn에 입력된 문제 설명]

## 왜 backport해야 하는가
- [ ] 보편적 원칙 (프로젝트 타입과 무관)
- [ ] 같은 타입 다수 프로젝트에 적용됨
- [ ] 기존 템플릿이 잘못된 기본값을 권하고 있음

## 제안 변경사항
| 파일 | 변경 내용 |
|------|----------|
| [예: templates/packs/web-saas/QUALITY_EXT.md] | [예: select('*') 금지 추가] |

## Owner 승인
- [ ] 검토 완료
- [ ] 적용 진행
- [ ] 거부 — 사유:
```

양식: `core/BACKPORT_PROPOSAL_TEMPLATE.md`

---

## Owner의 역할 요약

| 단계 | Owner 행동 |
|------|-----------|
| `/learn` 후 계획 검토 | 승인 / 수정 요청 / 거부 |
| L4/L5 BP-XXX.md 생성 후 | ai_engineering_docs에서 Claude Code 열고 반영 여부 결정 |
| 반영 시 | ai_engineering_docs 레포에서 직접 수정 + 버전 업데이트 + 커밋 |

**L4/L5 변경은 ai_engineering_docs에 자동 반영되지 않는다.**
Owner가 BP-XXX.md를 보고 직접 ai_engineering_docs 레포에서 판단·적용한다.

---

## 좋은 교훈 vs 나쁜 교훈

### 좋은 교훈 (/learn에 적합)
- "Reviewer가 select('*')를 두 번째 잡았는데 Implementer가 같은 실수를 반복함"
- "soft delete 누락이 production에서 발견됨"
- "AI 에이전트가 투자 조언을 면책 문구 없이 했음"

### /learn에 부적합한 것
- 특정 프로젝트에만 해당하는 one-off 버그 → L1로 기록
- 아직 한 번만 발생한 문제 → L1로 기록, 두 번째 발생 시 L2 고려
- 이미 L3 이상으로 적용된 기준 중복 등록
