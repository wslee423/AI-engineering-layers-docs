# CLAUDE.md — [프로젝트명] 에이전트 컨텍스트

> Claude Code가 매 세션 자동으로 읽는 핵심 컨텍스트.
> 간결하게 유지. 상세 규칙은 AGENTS.md 및 docs/ 참조.

---

## 세션 시작 시 읽을 문서 (순서대로)

1. `CONSTITUTION.md` — 최상위 원칙
2. `AGENTS.md` — 에이전트 역할 및 자율 범위
3. `docs/PLANS.md` — 현재 Phase 및 다음 작업
4. `docs/exec-plans/NEXT_SESSION.md` — 이전 세션 핸드오프 (있으면)

---

## 프로젝트 개요

**[프로젝트명]**: (한 줄 설명)

| 역할 | 기술 |
|------|------|
| 언어 | |
| Frontend | |
| Backend/DB | |
| AI/외부 API | |
| 배포 | |

*(스택 선택 가이드: `ARCHITECTURE_GUIDE.md` 참조)*

---

## 핵심 명령어

```bash
# 개발 서버
[dev 명령어]

# 검증 — 매 작업 후 반드시 실행
[typecheck 명령어]
[lint 명령어]
[test 명령어]
```

---

## Critical Rules

### Must Follow
- 타입 안정성 필수 (`any` / `dynamic` 금지)
- 서버 전용 시크릿 클라이언트 노출 금지
- 모든 DB 테이블 RLS 활성화 (Supabase 사용 시)
- 검증 명령어 통과 후 커밋
- 조용한 실패 금지 — 모든 에러는 사용자에게 전달

### Must NOT Do
- 외부 패키지 무단 추가
- 데이터 스키마 무단 변경
- CONSTITUTION / CLAUDE.md / AGENTS.md 무단 수정
- 하드코딩된 시크릿/API 키 커밋

---

## 파일 구조

```
/
  CONSTITUTION.md
  CLAUDE.md               ← 이 파일
  AGENTS.md
  QUALITY_SCORE.md
  README.md
  docs/
    PLANS.md
    ARCHITECTURE.md
    product-specs/        ← 기능별 스펙
    design-docs/          ← 디자인 가이드 (선택)
    exec-plans/
      open-decisions.md
      tech-debt-tracker.md
      NEXT_SESSION.md
  .claude/
    agents/               ← subagent 정의 (claude_code/agents/*.md 복사)
      implementer.md
      reviewer.md
      documenter.md
    commands/             ← 슬래시 커맨드 (claude_code/commands/*.md 복사)
      orchestrate.md      ← 보편 + saas 특화 override 병합
      review.md
      sync-docs.md
      learn.md
```

*(실제 프로젝트 구조에 맞게 수정)*

---

## 환경 변수

```
# 필수 환경 변수 목록 (값은 .env, 커밋 금지)
[ENV_VAR_1]=
[ENV_VAR_2]=
```
