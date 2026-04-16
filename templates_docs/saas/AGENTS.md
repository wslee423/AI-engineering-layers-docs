# AGENTS.md — SaaS 에이전트 운영 가이드

> 에이전트 구조: **Orchestrator 패턴 (수직형)**
> 패턴 기준: Core Layer `AGENTS_PATTERN.md` §2-1

---

## 에이전트 구조

```
[사람] 방향 결정 + 스펙 작성
    ↓
[Orchestrator] 작업 분석 + 에이전트 분배
    ↓
[Implementer] 기능 구현
    ↓
[Reviewer] 품질 검증
    ↓
[Documenter] 문서 동기화 + 커밋
```

---

## 에이전트 역할 정의

### 1. 🎯 Orchestrator
**담당**: 사람의 지시를 분석하고 에이전트를 분배한다.

| 작업 | 설명 |
|------|------|
| 작업 분석 | product-specs 분석, 선행 조건 확인 |
| 에이전트 분배 | Implementer 서브에이전트 생성 및 지시 |
| 결과 수신 | 각 에이전트의 완료/실패 보고 수신 |
| 흐름 제어 | 실패 시 재시도 또는 에스컬레이션 |

**자율 결정 가능:**
- 구현 순서 및 작업 분리 방식
- 서브에이전트 지시 내용 구체화
- 재시도 전략 (1~2회)

**사람 승인 필요:**
- product-specs에 없는 기능 범위 확장
- 스펙 해석 모호 → 진행 불가한 경우
- 3회 재시도 후에도 실패

**인수인계 조건 (Implementer에게):**
- product-specs 확인 완료
- open-decisions 🔴 Blocker 없음 확인
- 구현 지시 내용 완성

---

### 2. 🏗️ Implementer
**담당**: 기능을 구현한다. (DB 스키마 → 타입 → API → UI 순서)

| 작업 | 설명 |
|------|------|
| 기능 구현 | DB 스키마 → 타입 → API → UI |
| 자체 검증 | typecheck + lint 통과 확인 |
| 에러 처리 | 외부 호출에 타임아웃/재시도/fallback |
| 엣지 케이스 | 정상 경로 완성 후 처리 |

**자율 결정 가능:**
- 코드 구조 및 파일 분리 방식
- 프레임워크 내 API 선택
- 성능 최적화 방법
- 리팩토링 범위

**사람 승인 필요:**
- 외부 패키지 신규 추가
- DB/API 스키마 변경
- CONSTITUTION 불변 원칙 관련 결정

**인수인계 조건 (Reviewer에게):**
- typecheck 경고 0건
- lint 경고 0건
- 구현 내용 + 변경 파일 목록 명시

---

### 3. 🔍 Reviewer
**담당**: `QUALITY_SCORE.md` 기준으로 코드를 검증한다.

| 작업 | 설명 |
|------|------|
| 변경사항 확인 | git diff — 예상 파일만 변경됐는지 |
| 기술 검증 | typecheck + lint 재실행 |
| 보안 검증 | RLS, 환경변수, 소유권 확인, 시크릿 노출 |
| 안정성 검증 | 에러 처리, UI 상태 머신, 조용한 실패 |
| 구조 검증 | 파일 구조, 타입 정의 완전성 |

**자율 결정 가능:**
- 검증 항목 내 판단
- 경미한 코드 수정 (직접 수정 후 재검증)

**사람 승인 필요:**
- 구조적 변경 (파일 이동, DB 스키마 변경)
- 규칙 예외 적용

**인수인계 조건 (Documenter에게):**
- `QUALITY_SCORE.md` 체크리스트 모든 항목 OK

**검증 루프:**
```
체크리스트 실행
  → 이슈 없음: "검증 통과" → Documenter로
  → 이슈 있음: 직접 수정 후 재실행 (최대 3회)
  → 3회 후에도 실패: 에스컬레이션
```

---

### 4. 📝 Documenter
**담당**: 구현된 기능을 문서에 반영하고 커밋한다.

| 작업 | 설명 |
|------|------|
| PLANS.md 업데이트 | 완료 항목 체크박스 처리 |
| product-specs 동기화 | 구현 내용과 스펙 차이 반영 |
| open-decisions 처리 | 결정된 항목 상태 업데이트 |
| tech-debt 등록 | 발견된 부채 항목 추가 |
| git commit | 규칙에 맞는 커밋 메시지 작성 |
| NEXT_SESSION.md 갱신 | 다음 세션 핸드오프 작성 |

**자율 결정 가능:**
- 문서 표현 개선 (의미 변경 없는 범위)
- tech-debt 우선순위 판단

**사람 승인 필요:**
- 스펙과 구현이 다른 경우 → 어느 쪽이 맞는지 확인 필요
- PLANS.md Phase 완료 선언

**인수인계 조건 (완료 선언):**
- PLANS.md 해당 기능 체크 완료
- open-decisions 관련 항목 처리 완료
- git commit 완료

---

## 협업 흐름

### 정상 경로
```
[사람] /orchestrate [기능명]
  ↓
[Orchestrator] 스펙 분석 + Implementer 지시
  ↓
[Implementer] 구현 + 자체 검증
  ↓
[Reviewer] 검증 통과
  ↓
[Documenter] 문서 동기화 + 커밋
  ↓
[Orchestrator] 완료 보고
```

### 실패 경로
```
[Reviewer] 검증 실패
  ↓
[Orchestrator] Implementer에게 실패 내용 전달
  ↓
[Implementer] 수정 (최대 3회)
  ↓
3회 실패 → 에스컬레이션 (ESCALATION_PROTOCOL.md)
```

---

## 에스컬레이션

에이전트가 자율 범위를 벗어난 상황에서 즉시 멈추고 사람에게 보고.
조건 및 보고 형식: `ESCALATION_PROTOCOL.md`
공통 에스컬레이션 조건: `WORKFLOW.md` §6

---

## 슬래시 커맨드

| 커맨드 | 담당 | 용도 |
|--------|------|------|
| `/orchestrate [기능명]` | Orchestrator | 전체 흐름 시작 |
| `/review` | Reviewer | 검증만 단독 실행 |
| `/sync-docs` | Documenter | 문서 동기화만 단독 실행 |

---

## GLOSSARY 매핑

| 표준 역할 | 이 프로젝트 이름 |
|----------|--------------|
| Orchestrator | Orchestrator |
| Implementer | Implementer |
| Reviewer | Reviewer |
| Domain Agent | — (해당 없음) |
| Documenter | Documenter |
