# AGENTS.md — Game 에이전트 운영 가이드

> 에이전트 구조: **Hybrid 패턴 (Orchestrator + Pipeline)**
> 패턴 기준: Core Layer `AGENTS_PATTERN.md` §2-3

---

## 에이전트 구조

```
[사람] 방향 결정 + 스펙 작성
    ↓
[Orchestrator] 작업 분석 + Pipeline 시작
    ↓
[Implementer] 코드 구현
    ↓ 인수인계 조건 충족 시 자동
[QA Agent] 테스트 + 검증
    ↓ 인수인계 조건 충족 시 자동
[Stage Builder] 스테이지 생성 + 검증  ← 해당 작업 시만
    ↓ 인수인계 조건 충족 시 자동
[Documenter] 문서 동기화 + 커밋
```

Orchestrator가 Pipeline을 시작하고, 이후 각 에이전트가 조건 충족 시 자율 인수인계.

> ℹ️ **Stage Builder는 도메인 특화 에이전트다.** 스테이지 시스템이 없는 게임(오픈월드,
> 로그라이크 등)은 이를 **Content Builder**, **Level Designer** 등으로 대체하거나 생략한다.

---

## 에이전트 역할 정의

### 1. 🎯 Orchestrator
**담당**: 사람의 지시를 분석하고 Pipeline을 시작한다.

| 작업 | 설명 |
|------|------|
| 작업 분석 | product-specs + core-beliefs 확인, 선행 조건 체크 |
| Pipeline 결정 | 필요한 에이전트 조합 판단 (Stage Builder 필요 여부 등) |
| Pipeline 시작 | Implementer에게 구현 지시 |
| 결과 수신 | 각 단계 완료/실패 보고 수신 및 흐름 제어 |

**자율 결정 가능:**
- 구현 순서 및 작업 분리
- Pipeline에 포함할 에이전트 조합
- 재시도 전략 (1~2회)

**사람 승인 필요:**
- product-specs에 없는 기능 확장
- core-beliefs.md 체크리스트 위반 가능성
- 3회 재시도 후에도 실패

**인수인계 조건 (Implementer에게):**
- product-specs + core-beliefs 확인 완료
- open-decisions 🔴 Blocker 없음 확인
- 구현 지시 내용 완성

---

### 2. 🏗️ Implementer
**담당**: 게임 코드(컴포넌트, 시스템, UI)를 구현한다.

| 작업 | 설명 |
|------|------|
| 컴포넌트 구현 | 게임 오브젝트 (Player, Enemy, Item 등) |
| 시스템 구현 | 충돌, 물리, 아이템 효과 등 게임 로직 |
| UI 구현 | HUD, 오버레이, 메뉴 화면 |
| 버그 수정 | QA Agent에서 발견된 버그 자율 수정 |

**자율 결정 가능:**
- 코드 구조 및 파일 분리
- 엔진/프레임워크 API 선택
- 성능 최적화 방법 (오브젝트 풀링 등)
- 리팩토링 범위

**사람 승인 필요:**
- 외부 패키지 신규 추가
- core-beliefs.md 원칙 위반 가능성
- 수익화 구조 변경

**인수인계 조건 (QA Agent에게):**
- 프로젝트 정적 분석 명령어 경고 0건 (예: `flutter analyze`)
- 구현 기능 목록 + 변경 파일 목록 명시

---

### 3. 🧪 QA Agent
**담당**: 테스트를 작성하고 실행하여 코드 품질을 검증한다.

| 작업 | 설명 |
|------|------|
| 단위 테스트 | 로직, 충돌, 아이템 효과 |
| 상태 반응성 테스트 | HUD/오버레이 UI 리빌드 검증 |
| 스테이지 검증 | JSON 스키마, 클리어 가능 여부 (Stage Builder 작업 시) |
| 회귀 테스트 | 버그 수정 후 기존 기능 재확인 |
| 성능 테스트 | 목표 fps 유지 여부 |

**자율 결정 가능:**
- 테스트 케이스 설계
- 성능 측정 방법

**사람 승인 필요:**
- 테스트 커버리지 기준 조정
- 성능 목표 수치 변경

**인수인계 조건:**

*Stage Builder로 넘길 때:*
- 전체 테스트 통과
- 핵심 파일 각각 테스트 1개 이상 존재
- 새 HUD/오버레이 추가 시 상태 반응성 테스트 1개 이상 존재

*Documenter로 직접 넘길 때 (Stage Builder 불필요):*
- 위 조건 동일 + Stage Builder 불필요 이유 명시

---

### 4. 🗺️ Stage Builder
**담당**: 스테이지/레벨 데이터를 생성하고 검증한다.

| 작업 | 설명 |
|------|------|
| 스테이지 생성 | 스테이지 JSON/데이터 파일 생성 |
| 난이도 커브 적용 | `docs/stage-system.md` 구간별 규칙 준수 |
| 콘텐츠 배치 | 아이템 드롭, 적 배치, 장애물 배치 |
| 자동 검증 | 클리어 가능 여부 + 스키마 유효성 확인 |

**자율 결정 가능:**
- 배치 패턴 및 모양
- 수치 분포 (구간 기준 내)
- 스테이지 내 빈 공간 구성

**사람 승인 필요:**
- 스테이지 시스템 규칙 변경 (`stage-system.md` 수정)
- 밸런스 목표 수치 변경 (클리어율 등)

**인수인계 조건 (Documenter에게):**
- `/run-qa` 스테이지 검증 전체 통과
- 생성된 스테이지 목록 명시

---

### 5. 📝 Documenter
**담당**: 완료된 기능을 문서에 반영하고 커밋한다.

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
- `core-beliefs.md` / `ARCHITECTURE.md` 수정 (사람만 가능)

**인수인계 조건 (완료 선언):**
- PLANS.md 해당 기능 체크 완료
- open-decisions 관련 항목 처리 완료
- git commit 완료

---

## 협업 흐름

### 정상 경로 (코드 + 스테이지)
```
[사람] /orchestrate [기능명]
  ↓
[Orchestrator] 스펙 분석 + Implementer 지시
  ↓
[Implementer] 구현 완료 → QA Agent로 자동 인수인계
  ↓
[QA Agent] 테스트 통과 → Stage Builder로 자동 인수인계
  ↓
[Stage Builder] 스테이지 생성 + 검증 → Documenter로 자동 인수인계
  ↓
[Documenter] 문서 동기화 + 커밋
  ↓
[Orchestrator] 완료 보고
```

### 정상 경로 (코드만)
```
[Implementer] 구현 → [QA Agent] 테스트 → [Documenter] 문서 + 커밋
```

### 실패 경로
```
[QA Agent] 테스트 실패
  ↓
[Implementer] 버그 수정 (자율, 최대 3회)
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
| `/run-qa` | QA Agent | 테스트 + 스테이지 검증 단독 실행 |
| `/build-stage [N]` | Stage Builder | 스테이지 생성 단독 실행 |
| `/sync-docs` | Documenter | 문서 동기화 단독 실행 |

---

## GLOSSARY 매핑

| 표준 역할 | 이 프로젝트 이름 |
|----------|--------------|
| Orchestrator | Orchestrator |
| Implementer | Implementer |
| Reviewer | QA Agent |
| Domain Agent | Stage Builder |
| Documenter | Documenter |
