# AI Harness Engineering 스캐폴딩 시스템

AI 에이전트와 사람이 함께 작업하는 프로젝트를 체계적으로 관리하고 지속적으로 고도화하기 위한 프레임워크.

---

## 4축 구조 (V3)

```
ai_engineering_docs/
├── core/           ← 원칙·표준·메타 가이드 (읽기 전용)
├── templates/      ← 복사 가능한 프로젝트 시작점
│   ├── base/       ← 모든 프로젝트 공통 기반
│   └── packs/      ← 프로젝트 타입별 확장
├── agent_runtime/  ← 실행 엔진 → project/.claude/ 로 복사
└── knowledge/      ← 프로젝트 경험에서 축적된 패턴
```

---

## 각 축 설명

### `core/` — 원칙과 메타 가이드

모든 프로젝트가 따르는 기준과 템플릿 작성 가이드.

| 파일 | 역할 |
|------|------|
| `AGENT_SCHEMA.md` | 에이전트 정의 4요소 형식 표준 |
| `WORKFLOW_TEMPLATE.md` | WORKFLOW.md 작성 가이드 |
| `LESSON_LIFECYCLE.md` | L1~L5 교훈 승격 doctrine |
| `QUALITY_SCORE_BASE.md` | 공통 품질 기준 (코드/테스트/에러처리/UI 상태) |
| `CONSTITUTION_TEMPLATE.md` | 프로젝트 헌법 작성 가이드 |
| `BACKPORT_PROPOSAL_TEMPLATE.md` | 교훈 역방향 반영 제안서 양식 |

---

### `templates/base/` — 프로젝트 기반 문서

새 프로젝트 루트에 복사하는 실제 문서들.

| 파일 | 역할 |
|------|------|
| `CONSTITUTION.md` | 프로젝트 헌법 (불변 원칙, 최대 5개) |
| `CLAUDE.md` | 에이전트 세션 자동 로드 컨텍스트 |
| `AGENTS.md` | Orchestrator 패턴 + 4 에이전트 정의 |
| `QUALITY_SCORE.md` | BASE 기준 인라인 + 팩 확장 섹션 |
| `WORKFLOW.md` | 사람-에이전트 반복 실행 루프 (9섹션) |
| `ARCHITECTURE.md` | 기술 스택/구조 템플릿 |
| `PLANS.md` | Phase별 로드맵 |
| `.harness/manifest.yml` | harness 버전·팩·커스터마이징 추적 |
| `docs/exec/NEXT_SESSION.md` | 세션 핸드오프 |
| `docs/exec/lessons.md` | 교훈 로그 (L1) |

---

### `templates/packs/` — 타입별 확장

Base 문서의 `## Pack Extensions` 섹션에 병합하는 확장 파일.

| 팩 | 상태 | 주요 확장 |
|----|------|---------|
| `web-saas/` | ✅ 완성 | 보안(RLS/인증/인가), API 성능, SaaS 구현 순서 |
| `mobile-game/` | ✅ 완성 | 성능(fps/메모리), 스테이지 검증, 게임 루프 원칙 |
| `ai-agent-product/` | 🔲 스켈레톤 | 응답 안전성, 토큰 비용, 프롬프트 관리 |
| `data-tool/` | 🔲 스켈레톤 | 데이터 정합성, 마이그레이션 안전성 |

각 팩: `README.md`, `QUALITY_EXT.md`, `AGENTS_EXT.md`, `ARCHITECTURE_EXT.md`, `CHECKLIST_EXT.md`

---

### `agent_runtime/` — 실행 커널

새 프로젝트 초기화 시 `.claude/` 로 전체 복사.

```
agent_runtime/
├── agents/         implementer, reviewer, documenter
├── commands/       orchestrate, review, sync-docs, learn
├── hooks/          validate-reviewer-call
├── shared/
│   └── ESCALATION_BLOCK.md   ← 에스컬레이션 단일 참조점
├── settings.json
├── VERSION.md      (현재: v0.1.0)
├── CHANGELOG.md
└── UPGRADE_GUIDE.md
```

**버전 정책**: Semantic Versioning. 기존 프로젝트 `.claude/`는 자동 업그레이드하지 않음.
`UPGRADE_GUIDE.md` 확인 후 Owner가 수동 적용.

---

### `knowledge/` — 축적된 경험

여러 프로젝트를 거쳐 검증된 패턴과 교훈.

| 파일 | 역할 |
|------|------|
| `LESSONS_LEARNED.md` | 실제 발생한 문제 사례 (L2 이상 승격 전 단계) |
| `ANTI_PATTERNS.md` | "X 하지 말고 Y 해라" 명문화된 패턴 |

---

## 새 프로젝트 초기화

### 초기화 흐름

```
1. agent_runtime/ → project/.claude/     (실행 커널 복사)
2. templates/base/ → project root        (기반 문서 복사)
3. 팩 선택 → EXT 내용 병합               (타입별 확장 적용)
4. .harness/manifest.yml 업데이트
5. 프로젝트 정보로 플레이스홀더 채우기
```

→ 자세한 절차: **[QUICKSTART.md](QUICKSTART.md)**

---

## 자기 개선 루프

프로젝트에서 발견한 교훈이 이 레포로 역방향 반영되는 흐름:

```
프로젝트 실수 발생
    → /sync-docs → /learn 후보 제시
    → /learn → L1~L5 판정 + Owner 승인
    → L4/L5: docs/exec/backports/BP-XXX.md 생성
    → Owner가 ai_engineering_docs 본체에 수동 반영
```

→ 자세한 절차: **[SELF_IMPROVING.md](SELF_IMPROVING.md)**

---

## 버전

| 항목 | 값 |
|------|-----|
| 문서 버전 | V3 |
| agent_runtime | v0.1.0 |
| 최종 업데이트 | 2026-05-05 |
