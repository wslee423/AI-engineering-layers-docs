# AI Harness Engineering 스캐폴딩 시스템

AI 에이전트와 인간이 함께 작업하는 프로젝트를 체계적으로 관리하고 지속적으로 고도화하기 위한 문서화 및 워크플로우 프레임워크입니다.

## 핵심 목표

1. **정해진 양식으로 학습** — 프로젝트마다 새로운 원칙을 배우지 않고, 일관된 문서 구조와 프로세스 표준을 따름
2. **실행 루프 자동화** — 사람과 AI 에이전트의 역할을 명확히 하고, 반복 가능한 작업 흐름을 구축
3. **AI 에이전트 고도화** — 각 프로젝트의 경험을 축적하여 에이전트의 의사결정과 실행 능력을 지속적으로 개선

---

## 4가지 레이어

### 1. Core Layer (`core_docs/`)

모든 프로젝트가 따르는 **기본 표준**과 **템플릿**을 정의합니다.

| 문서 | 설명 |
|------|------|
| **WORKFLOW.md** | 사람-에이전트 반복 실행 루프의 표준. 세션/기능/Phase 레벨의 3가지 루프 정의 |
| **AGENTS_PATTERN.md** | 모든 에이전트의 필수 정의 형식. 담당/자율범위/승인범위/인수인계조건 4요소 |
| **ESCALATION_PROTOCOL.md** | 에이전트가 사람에게 보고해야 하는 상황 정의 |
| **CONSTITUTION_TEMPLATE.md** | 프로젝트의 불변 원칙(헌법) 템플릿 |
| **GLOSSARY_TEMPLATE.md** | 프로젝트 용어 정의. 팀 간 소통의 혼선 방지 |
| **QUALITY_SCORE_BASE.md** | 결과물 품질 평가 기준 (코드, 문서, 테스트 등) |
| **LESSONS_LEARNED.md** | 프로젝트 진행 중 발견한 교훈과 개선점 |
| **ANTI_PATTERNS.md** | 피해야 할 패턴들. 프로젝트별로 구체화됨 |
| **LESSON_LIFECYCLE.md** | 교훈 승격 5단계 doctrine (L1~L5). `/learn` 명령의 기반 |
| **BACKPORT_PROPOSAL_TEMPLATE.md** | L4/L5 교훈을 이 레포로 역방향 반영할 때 쓰는 제안서 양식 |
| **README_TEMPLATE.md** | 프로젝트 README 작성 표준 |
| **open-decisions-template.md** | 미결정 사항 추적 템플릿 |
| **tech-debt-template.md** | 기술 부채 관리 |

---

### 2. Claude Code Layer (`claude_code/`)

**Claude Code 런타임에서 실행되는 보편 subagent와 slash command**를 정의합니다.
실제 프로젝트의 `.claude/` 디렉토리와 1:1 매핑됩니다.

```
claude_code/
├─ agents/
│  ├─ implementer.md    ← Large Feature 구현 (DB→Types→API→UI)
│  ├─ reviewer.md       ← READ-ONLY 검증 (QUALITY_SCORE.md 기준)
│  └─ documenter.md     ← 문서 동기화(Mode A) + 교훈 승격(Mode B)
└─ commands/
   ├─ orchestrate.md    ← Main Session = Orchestrator 행동 정의
   ├─ review.md         ← reviewer 단독 호출
   ├─ sync-docs.md      ← documenter Mode A 호출
   └─ learn.md          ← /learn "<문제>" 수동 트리거
```

**핵심 설계 원칙:**
- Orchestrator는 별도 subagent가 아니다 — Main Session이 직접 수행
- Reviewer는 READ-ONLY — Write/Edit 도구 없음, PASS/FAIL만 판정
- Reviewer에게 구현 reasoning 전달 금지 — 독립적 검증 보장
- Small Feature: Main이 직접 구현 → reviewer만 spawn
- Large Feature: implementer → reviewer → documenter 순서로 spawn

---

### 3. Template Layer (`templates_docs/`)

실제 프로젝트 타입별 **구체적인 예시와 설정**을 제공합니다.

#### Game 프로젝트 템플릿 (`templates_docs/game/`)

| 문서 | 설명 |
|------|------|
| **CLAUDE.md** | 게임 프로젝트의 AI 에이전트 지침 |
| **AGENTS.md** | 에이전트 역할 정의 (Stage Builder/QA → Implementer/Reviewer 흡수) |
| **ARCHITECTURE_GUIDE.md** | 게임 아키텍처 설계 가이드 |
| **QUALITY_SCORE.md** | 게임 특화 품질 기준 (스테이지 검증, fps, 핵심 파일 테스트) |
| **claude_code/commands/orchestrate.md** | Game 특화 Large 분류 + 구현 시퀀스 |
| **claude_code/commands/build-stage.md** | Stage 생성 명령 (현재 placeholder) |

#### SaaS 프로젝트 템플릿 (`templates_docs/saas/`)

| 문서 | 설명 |
|------|------|
| **CLAUDE.md** | SaaS 프로젝트의 AI 에이전트 지침 |
| **AGENTS.md** | 에이전트 역할 정의 (Orchestrator + 3 subagents) |
| **ARCHITECTURE_GUIDE.md** | SaaS 백엔드/프론트엔드 아키텍처 가이드 |
| **QUALITY_SCORE.md** | SaaS 특화 품질 기준 (RLS, API 인증, 보안) |
| **claude_code/commands/orchestrate.md** | SaaS 특화 Large 분류 + DB→Types→API→UI 시퀀스 |

---

### 4. Project Layer (실제 프로젝트)

Core + Claude Code + Template를 조합하여 구성합니다.

```
새 프로젝트/
├── CONSTITUTION.md          (Core: CONSTITUTION_TEMPLATE.md 기반)
├── CLAUDE.md                (Template: 선택한 타입의 CLAUDE.md)
├── AGENTS.md                (Template: 선택한 타입의 AGENTS.md)
├── QUALITY_SCORE.md         (Template: 선택한 타입의 QUALITY_SCORE.md)
├── PLANS.md                 (Template: PLANS_TEMPLATE.md)
├── GLOSSARY.md              (Core: GLOSSARY_TEMPLATE.md)
├── ARCHITECTURE.md          (Template: ARCHITECTURE_GUIDE.md 참조)
├── docs/exec-plans/
│   ├── open-decisions.md
│   ├── tech-debt-tracker.md
│   └── NEXT_SESSION.md
└── .claude/                 ← Claude Code Layer 복사본
    ├── agents/
    │   ├── implementer.md   (claude_code/agents/implementer.md)
    │   ├── reviewer.md      (claude_code/agents/reviewer.md)
    │   └── documenter.md    (claude_code/agents/documenter.md)
    └── commands/
        ├── orchestrate.md   (보편 + 타입별 override 병합)
        ├── review.md
        ├── sync-docs.md
        └── learn.md
```

---

## 시작하기

### 새로운 프로젝트 초기화 (SaaS 예시)

```bash
# 1. Core Layer 복사
cp core_docs/CONSTITUTION_TEMPLATE.md  my-project/CONSTITUTION.md
cp core_docs/GLOSSARY_TEMPLATE.md      my-project/GLOSSARY.md
cp core_docs/open-decisions-template.md my-project/docs/exec-plans/open-decisions.md
cp core_docs/tech-debt-template.md     my-project/docs/exec-plans/tech-debt-tracker.md

# 2. Template Layer 복사 (SaaS)
cp templates_docs/PLANS_TEMPLATE.md    my-project/docs/PLANS.md
cp templates_docs/saas/CLAUDE.md       my-project/CLAUDE.md
cp templates_docs/saas/AGENTS.md       my-project/AGENTS.md
cp templates_docs/saas/QUALITY_SCORE.md my-project/QUALITY_SCORE.md

# 3. Claude Code Layer 복사 (보편 base)
mkdir -p my-project/.claude/agents my-project/.claude/commands my-project/.claude/hooks
cp claude_code/agents/*.md             my-project/.claude/agents/
cp claude_code/commands/*.md           my-project/.claude/commands/
cp claude_code/settings.json           my-project/.claude/settings.json
cp claude_code/hooks/*.js              my-project/.claude/hooks/

# 4. Template 특화 override 적용 (있는 파일만 덮어쓰기)
cp templates_docs/saas/claude_code/commands/orchestrate.md \
   my-project/.claude/commands/orchestrate.md

# 5. Node.js 확인 (hook 실행 필수)
node --version   # v18 이상 권장

# 6. 프로젝트에 맞게 수정
# → CONSTITUTION.md, CLAUDE.md, AGENTS.md, PLANS.md 커스터마이징
```

---

## 교훈 승격 루프 (AI가 점점 똑똑해지는 방법)

```
1. 실제 문제 발생
2. /sync-docs 실행 → documenter가 /learn 후보 제시
3. /learn "<문제>" 실행 → documenter가 L1~L5 계획 제안
4. Owner 승인 → documenter가 적용
   L1: LESSONS_LEARNED.md
   L2: ANTI_PATTERNS.md
   L3: QUALITY_SCORE.md
   L4: .claude/agents/*.md 또는 .claude/commands/*.md
   L5: templates/<type>/*
5. L4/L5인 경우 → BACKPORT_PROPOSAL.md 생성
6. Owner가 ai_engineering_docs 본체에 수동 반영
```

자세한 기준: `core_docs/LESSON_LIFECYCLE.md`

---

## 문서 체계 요약

```
AI Harness Engineering
│
├─ Core Layer          모든 프로젝트의 공통 표준 + 교훈 축적
│
├─ Claude Code Layer   보편 subagent + slash command 실행 엔진
│
├─ Template Layer      프로젝트 타입별 구체 설정 (SaaS / Game)
│
└─ Project Layer       실제 프로젝트 (Core + Claude Code + Template 조합)
```

---

**Version**: 2.0
**Last Updated**: 2026-04-29
