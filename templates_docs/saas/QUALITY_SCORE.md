# QUALITY_SCORE.md — SaaS 품질 기준

> Core Layer의 `QUALITY_SCORE_BASE.md`를 기반으로 SaaS 특화 기준을 추가.
> **이 문서는 reviewer subagent가 매번 검토 시 source of truth로 읽는다.**
> `/review` 또는 `/orchestrate` 흐름에서 자동 호출된다.

---

## 1. 코드 품질 (Core 기준 그대로 적용)

### 1-1. 정적 분석 (경고 0건)

```bash
# CLAUDE.md의 검증 명령어 실행
[typecheck 명령어]
[lint 명령어]
```

### 1-2. 네이밍, 파일/함수 크기, 타입 안정성
→ `QUALITY_SCORE_BASE.md` §2-2, 2-3, 2-4 동일 적용

---

## 2. 테스트 (Core 기준 + SaaS 추가)

### 2-1. 테스트 유형 분류
→ `QUALITY_SCORE_BASE.md` §3-1 동일 적용 (로직 / 상태 반응성 / 통합)

### 2-2. 테스트 커버리지
| 대상 | 기준 |
|------|------|
| 핵심 비즈니스 로직 | 70% 이상 |
| API Route (서버) | 주요 경로 + 에러 케이스 포함 |
| UI 컴포넌트 | 상태 반응성 테스트 1개 이상 |
| 인증/인가 로직 | 100% (예외 없음) |

---

## 3. 에러 처리 (Core 기준 그대로 적용)
→ `QUALITY_SCORE_BASE.md` §4 동일 적용

---

## 4. UI 상태 머신 (Core 기준 그대로 적용)
→ `QUALITY_SCORE_BASE.md` §5 동일 적용

---

## 5. SaaS 특화: 보안 기준

### 5-1. 인증/인가
| 항목 | 기준 |
|------|------|
| 모든 API 라우트 | 인증 확인 (미인증 시 401) |
| PATCH/DELETE 라우트 | 소유권 확인 (타인 리소스 접근 시 404) |
| 관리자 기능 | 역할 기반 접근 제어 |

소유권 확인 패턴:
```ts
// ✅ 반드시 user_id와 함께 조회
const { data: owned } = await db
  .from('items').select('id')
  .eq('id', id).eq('user_id', user.id).maybeSingle()
if (!owned) return 404
```

### 5-2. 환경변수 및 시크릿
| 항목 | 기준 |
|------|------|
| 서버 전용 시크릿 | 절대 클라이언트 노출 금지 |
| 공개 가능 변수 | 명시적 접두사 구분 (예: `NEXT_PUBLIC_*`) |
| .env 파일 | .gitignore 등록 필수 |
| 시크릿 하드코딩 | 절대 금지 |

### 5-3. DB 보안 (Supabase 사용 시)
| 항목 | 기준 |
|------|------|
| RLS | 모든 테이블 활성화 필수 |
| CRUD 정책 | 각 테이블마다 명시적 정의 |
| Service Role | 서버 전용, 클라이언트 절대 사용 금지 |

---

## 6. SaaS 특화: 성능 기준

| 항목 | 목표 | 측정 방법 |
|------|------|----------|
| API 응답 시간 | < 500ms (p95) | 로컬 측정 또는 모니터링 |
| 페이지 초기 로딩 | < 2초 | Lighthouse / 브라우저 DevTools |
| DB 쿼리 | N+1 없음 | 쿼리 로그 확인 |

*(프로젝트 특성에 맞게 수치 조정)*

---

## 7. SaaS 특화: 필수 문서 체크리스트

프로젝트 생성 시 아래 문서가 모두 존재해야 한다:
- [ ] `README.md`
- [ ] `CONSTITUTION.md`
- [ ] `CLAUDE.md`
- [ ] `AGENTS.md`
- [ ] `QUALITY_SCORE.md`
- [ ] `docs/PLANS.md`
- [ ] `docs/ARCHITECTURE.md`
- [ ] `docs/exec-plans/open-decisions.md`
- [ ] `docs/exec-plans/tech-debt-tracker.md`

---

## 8. 검증 체크리스트 (기능 완료 시)

**Reviewer subagent가 순서대로 실행한다. 각 항목을 grep/lint/실행으로 확인.**

```
Step 1: 정적 분석
  □ typecheck 경고 0건
  □ lint 경고 0건
  □ 예상 파일만 변경됨 (git diff 확인)

Step 2: 보안
  □ 모든 API 라우트 인증 확인 코드 존재
  □ PATCH/DELETE 소유권 확인 (user_id 검증)
  □ 시크릿 하드코딩 없음
  □ 클라이언트에 서버 전용 변수 노출 없음
  □ RLS 활성화 (신규 테이블 있는 경우)

Step 3: 안정성
  □ 외부 호출에 타임아웃/재시도/fallback
  □ 조용한 실패 없음 (빈 catch 없음)
  □ 모든 API 실패 → 사용자에게 에러 전달

Step 4: UI 상태 (UI 변경 있는 경우)
  □ Loading / Success / Error 3가지 모두 도달 가능
  □ Error가 Success 분기 안에 숨어있지 않음
  □ 상태 반응성 테스트 존재

Step 5: 스펙 일치
  □ 변경사항이 product-spec과 일치
  □ 스펙에 없는 기능 추가 없음
```
