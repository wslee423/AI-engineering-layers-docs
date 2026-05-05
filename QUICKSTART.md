# QUICKSTART.md — 새 프로젝트 시작 가이드

> PROJECT-SETUP-GUIDE.md + PROJECT-DECISION-AND-PROMPTS.md 통합 (V3).

---

## Step 1: 프로젝트 타입 결정

```
"어떤 프로젝트인가?"
    │
    ├─ 외부 사용자 앱 + 생산성/비즈니스 로직
    │   └─ web-saas 팩 (Next.js/Flutter + Supabase 등)
    │
    ├─ 외부 사용자 앱 + 재미/게임성
    │   └─ mobile-game 팩 (Flutter+Flame / Unity)
    │
    ├─ AI 에이전트 제품 (챗봇, 자동화, AI 어시스턴트)
    │   └─ ai-agent-product 팩 (스켈레톤 — 수치 직접 채워야 함)
    │
    ├─ 데이터 처리 도구 (ETL, 분석, ML 파이프라인)
    │   └─ data-tool 팩 (스켈레톤 — 수치 직접 채워야 함)
    │
    └─ 위에 없음
        └─ base만 사용 + 프로젝트 특화 기준 직접 추가
```

---

## Step 2: 수동 초기화

```bash
PROJECT=my-project
HARNESS=D:/wlabs/ai_engineering_docs   # 이 레포 경로

# 1. agent_runtime → .claude/ 복사 (실행 커널)
mkdir -p $PROJECT/.claude
cp -r $HARNESS/agent_runtime/agents   $PROJECT/.claude/
cp -r $HARNESS/agent_runtime/commands $PROJECT/.claude/
cp -r $HARNESS/agent_runtime/hooks    $PROJECT/.claude/
cp -r $HARNESS/agent_runtime/shared   $PROJECT/.claude/
cp    $HARNESS/agent_runtime/settings.json $PROJECT/.claude/

# 2. templates/base → 프로젝트 루트 복사
cp $HARNESS/templates/base/CONSTITUTION.md  $PROJECT/
cp $HARNESS/templates/base/CLAUDE.md        $PROJECT/
cp $HARNESS/templates/base/AGENTS.md        $PROJECT/
cp $HARNESS/templates/base/QUALITY_SCORE.md $PROJECT/
cp $HARNESS/templates/base/WORKFLOW.md      $PROJECT/
cp $HARNESS/templates/base/ARCHITECTURE.md  $PROJECT/
cp $HARNESS/templates/base/PLANS.md         $PROJECT/
cp $HARNESS/templates/base/README.md        $PROJECT/
mkdir -p $PROJECT/docs/exec
cp $HARNESS/templates/base/docs/exec/open-decisions.md  $PROJECT/docs/exec/
cp $HARNESS/templates/base/docs/exec/tech-debt.md       $PROJECT/docs/exec/
cp $HARNESS/templates/base/docs/exec/NEXT_SESSION.md    $PROJECT/docs/exec/
cp $HARNESS/templates/base/docs/exec/lessons.md         $PROJECT/docs/exec/
mkdir -p $PROJECT/.harness
cp $HARNESS/templates/base/.harness/manifest.yml $PROJECT/.harness/

# 3. 팩 적용 (예: web-saas)
PACK=web-saas
# → $HARNESS/templates/packs/$PACK/QUALITY_EXT.md 내용을
#    $PROJECT/QUALITY_SCORE.md의 "## Pack Extensions" 섹션에 병합
# → AGENTS_EXT.md → AGENTS.md, 나머지 EXT는 참조
# → manifest.yml applied_packs에 "$PACK" 추가
```

---

## Step 3: Claude로 자동 생성 (추천)

새 Claude 세션에 아래 프롬프트를 붙여넣는다.

---

### 프롬프트 템플릿 A — web-saas

```
당신은 AI Engineering Architect입니다.

다음 V3 하네스 시스템 기반으로 프로젝트를 초기화해주세요.
하네스 참조 경로: D:/wlabs/ai_engineering_docs

## 프로젝트 정보
- **이름**: [프로젝트명]
- **한 줄 설명**: [무엇을, 누구를 위해, 왜]
- **타입**: web-saas
- **스택**: [언어 / Frontend / Backend·DB / 배포]
- **기간**: [기간]

## 불변 원칙 후보 (CONSTITUTION §5에 들어갈 것)
1. [예: 조용한 실패 금지]
2. [예: RLS 모든 테이블 필수]
3. [예: 사용자 데이터 하드코딩 금지]

## 성공 지표
- [예: API 응답 500ms 이내, 월 활성 사용자 1000명]

## 생성 요청
1. CONSTITUTION.md (불변 원칙 3~5개 채워서)
2. CLAUDE.md (스택 + 명령어 채워서)
3. AGENTS.md (web-saas pack AGENTS_EXT 병합)
4. QUALITY_SCORE.md (web-saas pack QUALITY_EXT 병합)
5. ARCHITECTURE.md (선택 스택 + 설계 원칙)
6. PLANS.md (Phase 1~3 체크리스트)
7. README.md (프로젝트 소개)
8. docs/exec/open-decisions.md (초기 Blocker 항목)
9. .harness/manifest.yml (applied_packs: [web-saas])
```

---

### 프롬프트 템플릿 B — mobile-game

```
당신은 AI Engineering Architect입니다.

다음 V3 하네스 시스템 기반으로 게임 프로젝트를 초기화해주세요.
하네스 참조 경로: D:/wlabs/ai_engineering_docs

## 프로젝트 정보
- **이름**: [프로젝트명]
- **한 줄 설명**: [장르 + 핵심 재미 요소]
- **타입**: mobile-game
- **플랫폼**: [Mobile (Flutter+Flame) / PC (Unity) / 기타]
- **기간**: [기간]

## 핵심 게임 시스템
- 장르: [예: 캐주얼 퍼즐 / 아케이드 / RPG]
- 스테이지 기반: [Yes / No]
- 수익화: [광고 / 인앱결제 / 없음]
- 멀티플레이: [Yes / No]

## 불변 원칙 후보
1. [예: Pay-to-Win 금지]
2. [예: 클리어 불가능한 스테이지 금지]

## 생성 요청
1. CONSTITUTION.md
2. CLAUDE.md (핵심 파일 목록 포함)
3. AGENTS.md (mobile-game pack AGENTS_EXT 병합)
4. QUALITY_SCORE.md (mobile-game pack QUALITY_EXT 병합, 수치 채워서)
5. ARCHITECTURE.md (선택 스택 + 게임 루프 구조)
6. PLANS.md
7. docs/design-docs/core-beliefs.md (게임 설계 철학)
8. .harness/manifest.yml
```

---

### 프롬프트 템플릿 C — 기타 / 커스텀

```
당신은 AI Engineering Architect입니다.

다음 V3 하네스 시스템 기반으로 프로젝트를 초기화해주세요.
하네스 참조 경로: D:/wlabs/ai_engineering_docs

## 프로젝트 정보
- **이름**: [프로젝트명]
- **타입**: [ai-agent-product / data-tool / custom]
- **한 줄 설명**: [무엇을, 누구를 위해]
- **스택**: [주요 기술]

## 절대 금지 사항 (불변 원칙 후보)
- [항목]

## 성공 지표
- [정량 지표]

## 생성 요청
base 문서 전체 + 해당 팩 EXT 병합. 스켈레톤 팩의 경우 수치를 직접 채워서 생성.
1. CONSTITUTION.md, CLAUDE.md, AGENTS.md, QUALITY_SCORE.md
2. WORKFLOW.md, ARCHITECTURE.md, PLANS.md, README.md
3. docs/exec/ 4개 파일, .harness/manifest.yml
```

---

## Step 4: 프로젝트 정보로 플레이스홀더 채우기

생성된 파일에서 `[프로젝트명]`, `YYYY-MM-DD`, `[N]`, `(...)` 등 채우기.

체크리스트:
- [ ] CONSTITUTION.md §1 프로젝트 목적 기술
- [ ] CONSTITUTION.md §5 불변 원칙 3~5개 확정
- [ ] CLAUDE.md 스택/현재 Phase 업데이트
- [ ] QUALITY_SCORE.md 성능 수치 확정 (팩 EXT의 `[N]` 부분)
- [ ] ARCHITECTURE.md 실제 스택 + 선택 이유 기록
- [ ] .harness/manifest.yml applied_packs 확인

---

## Step 5: 첫 세션 시작

Claude Code를 열고 `CLAUDE.md` 가 있는 프로젝트 루트에서 시작.
에이전트가 `docs/exec/NEXT_SESSION.md` → `PLANS.md` → `docs/exec/open-decisions.md` 순으로 읽고 첫 작업을 제안한다.
