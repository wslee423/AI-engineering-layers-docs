# ANTI_PATTERNS.md — 금지 패턴 모음

> Core Layer 문서. **코드/구조 차원**의 반복 안티패턴.
> 행동 차원 금지(무단 커밋, 스키마 변경 등)는 `AGENTS_PATTERN.md` 섹션 4.
> 실제 발생 사례는 `LESSONS_LEARNED.md`.

---

## 번호 체계

| 범위 | 카테고리 |
|------|---------|
| AP-001~009 | 공통 (모든 프로젝트) |
| AP-010~019 | UI/상태 관리 |
| AP-020~029 | 백엔드/API |
| AP-100~ | Web SaaS 특화 |
| AP-200~ | 게임 특화 |
| AP-300~ | 데이터 툴 특화 |
| AP-400~ | AI 에이전트 제품 특화 |

---

## 1. 공통 안티패턴

### AP-001: 조용한 실패
**패턴:**
```js
try { ... } catch(e) {}
fetch(url).catch(() => {})
```
**왜 나쁜가:** 에러가 발생해도 아무도 모른다. 디버깅 불가.
**대응:** `QUALITY_SCORE_BASE.md` 섹션 4-1.

---

### AP-002: Fire-and-forget 없이 에러 기록
**패턴:** 비동기 작업을 띄우고 결과를 추적하지 않음.
```js
fetch('/api/task', { method: 'POST' }).catch(() => {})
// 실패 시 UI는 영구 로딩
```
**왜 나쁜가:** 실패 감지 불가 → 사용자 UI가 멈춰있음. (link_memory 사례)
**대응:** DB에 에러 상태 기록 + UI에 시간 기반 fallback.

---

### AP-003: 하드코딩된 시크릿
**패턴:**
```js
const API_KEY = "sk-proj-abc123..."
```
**왜 나쁜가:** git 히스토리에 영구 노출. 회수 불가.
**대응:** 환경변수. `.env`는 `.gitignore`.

---

### AP-004: 무단 의존성 추가
**패턴:** 사람 승인 없이 `package.json` / `pubspec.yaml` 등에 패키지 추가.
**왜 나쁜가:** 라이선스, 번들 크기, 보안, 유지보수 부담.
**대응:** 에스컬레이션. 표준 라이브러리로 먼저 검토.

---

## 2. UI/상태 관리 안티패턴

### AP-010: UI 리빌드 미보장
**패턴:** 상태가 변해도 UI가 반영되지 않음. 두 가지 원인:
1. 로직 테스트만 있고 UI 반응 테스트 없음 — 버그 은닉 (fantasy_bricks 사례)
2. 상태 의존 UI를 StatelessWidget/메모이즈 컴포넌트에 넣음 — 리빌드 트리거 안 됨

**왜 나쁜가:** 값은 바뀌는데 화면이 안 바뀜.
**대응:**
- 상태 반응성 테스트 필수 (`QUALITY_SCORE_BASE.md` 섹션 3-1)
- 상태 의존 UI는 적절한 반응성 컨테이너 사용 (`ValueNotifier` / `StatefulWidget` / hook 등)

---

### AP-011: UI 상태 분기에서 error가 success 뒤에 숨음
**패턴:**
```tsx
if (data) return <Content />
else if (error) return <RetryButton />  // data=null일 때만 도달
return <Skeleton />
```
**왜 나쁜가:** data와 error 둘 다 null이면 영원히 Skeleton. (link_memory 사례)
**대응:** `QUALITY_SCORE_BASE.md` 섹션 5 — error를 최우선 분기.

---

## 3. 백엔드/API 안티패턴

### AP-020: 소유권 확인 없는 업데이트/삭제
**패턴:**
```js
await db.items.update({ id }, data)  // id 소유자 확인 누락
```
**왜 나쁜가:** 다른 사용자 리소스 변경 가능. (link_memory 사례)
**대응:**
```js
const owned = await db.items.findOne({ id, userId })
if (!owned) return 404
await db.items.update({ id, userId }, data)
```

---

### AP-021: 타임아웃 없는 외부 호출
**패턴:**
```js
const res = await fetch(externalUrl)  // 무한 대기 가능
```
**왜 나쁜가:** 외부 서비스 장애 시 전체 응답 지연.
**대응:** `AbortController` 또는 라이브러리 timeout 옵션 필수.

---

### AP-022: 민감 데이터 응답 노출
**패턴:** API 응답에 내부 전용 필드 포함 (예: `transcript`, `internal_notes`).
**왜 나쁜가:** 클라이언트에 불필요 데이터. 프라이버시/보안 이슈.
**대응:** 응답 타입 명시적 정의 (DTO 또는 `select` 절).

---

## 4. 프로젝트 타입별 안티패턴

### Web SaaS

| ID | 패턴 | 요약 |
|----|------|------|
| AP-100 | `any` 타입 사용 (TypeScript) | 타입 안정성 포기 → 런타임 에러 |
| AP-101 | 클라이언트에서 서버 전용 환경변수 접근 | `NEXT_PUBLIC_*` 아닌 변수를 클라이언트에서 참조 |
| AP-102 | RLS 없는 테이블 (Supabase) | 모든 테이블에 RLS 필수 |

### 게임

| ID | 패턴 | 요약 |
|----|------|------|
| AP-200 | Pay-to-Win 요소 | 결제가 승패에 영향 (CONSTITUTION 위반 가능성) |
| AP-201 | 테스트 없이 핵심 로직 수정 | 프로젝트별 "핵심 파일" 지정 후 테스트 필수 |
| AP-202 | 클리어 불가능한 스테이지 생성 | 생성 시 자동 검증 필수 |
| AP-203 | 수치 무한 증가 (예: 볼 속도) | 게임 루프 내 클램프 필수 |

### 데이터 툴

| ID | 패턴 | 요약 |
|----|------|------|
| AP-300 | 멱등성 없는 파이프라인 | 재실행 시 누적/중복 금지 |
| AP-301 | 스키마 검증 없는 데이터 적재 | 들어오는 데이터 검증 필수 |

### AI 에이전트 제품

| ID | 패턴 | 요약 |
|----|------|------|
| AP-400 | 프롬프트에 시크릿/PII 포함 | 로그, 히스토리에 민감 정보 노출 |
| AP-401 | 토큰 비용 상한 없는 루프 | 무한 재시도로 비용 폭발 |
| AP-402 | 구조화 출력 검증 없음 | LLM 응답 파싱 시 스키마 검증 필수 |

---

## 5. 안티패턴 추가 규칙

새 안티패턴 등록 조건 (모두 Yes):

- [ ] 실제 발생 사례가 있는가? (추측 금지)
- [ ] 재현 가능한가?
- [ ] 구체적 대응 방법이 있는가?
- [ ] 기존과 중복 아닌가?

등록 시:
- 적절한 번호 범위에서 순차 ID 부여
- `LESSONS_LEARNED.md`에 발견 사례 기록
- 필요 시 `QUALITY_SCORE.md` 검증 항목으로 승격
