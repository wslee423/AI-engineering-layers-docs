# QUALITY_SCORE_BASE.md — 공통 품질 기준

> Core Layer 문서. 모든 프로젝트에 공통 적용되는 품질 기준.
> 성능/밸런스/플랫폼 등 프로젝트 특화 기준은 각 프로젝트의 `QUALITY_SCORE.md`에서 추가한다.

---

## 1. 사용법

프로젝트의 `QUALITY_SCORE.md`는 **이 문서를 그대로 복사 → 프로젝트 특화 기준 추가**한다.

---

## 2. 코드 품질

### 2-1. 정적 분석 (경고 0건) `[자동검증 가능]`
| 항목 | 기준 |
|------|------|
| 컴파일/타입 오류 | 0건 |
| 린터 경고 | 0건 |
| 미사용 import/변수 | 0건 |
| 코드 포맷 | 100% 준수 |

**언어별 명령어 예시:**
- TypeScript: `npm run typecheck && npm run lint`
- Flutter: `flutter analyze`
- Python: `ruff check && mypy`
- Go: `go vet && golangci-lint`

### 2-2. 네이밍 규칙
| 대상 | 규칙 | 예시 |
|------|------|------|
| 클래스/타입 | PascalCase | `UserProfile` |
| 변수/함수 | camelCase | `fetchLinks()` |
| 상수 | UPPER_SNAKE_CASE | `MAX_RETRY_COUNT` |
| 파일명 | 언어 관례 따름 | 프로젝트에서 한 번 정하고 일관성 유지 |

### 2-3. 파일/함수 크기 `[자동검증 가능]`
| 항목 | 기준 | 초과 시 |
|------|------|--------|
| 단일 파일 | 300줄 이하 | 파일 분리 |
| 단일 함수 | 50줄 이하 | 함수 분리 |

**예외**: 자동 생성 파일, 데이터/상수 선언 전용 파일.

### 2-4. 타입 안정성
- 언어 엄격 타입 모드 사용 (TS `strict`, Python type hints, Dart 명시 타입)
- 동적 타입(`any`, `dynamic`) 사용 금지
- 공용 타입은 단일 파일에 집중

---

## 3. 테스트

### 3-1. 테스트 유형 분류

> ℹ️ **UI가 있는 프로젝트에만 해당**: Web/Mobile SaaS, 게임, UI 있는 데스크탑 툴.
> CLI/데이터 툴/백엔드 API만 있는 프로젝트는 "상태 반응성 테스트"를 생략하고
> 로직 테스트 + 통합 테스트만 요구한다.

UI 상태를 다루는 모든 기능에 아래 3가지 테스트가 모두 존재해야 한다.

| 유형 | 대상 | 검증 내용 |
|------|------|----------|
| **로직** | 순수 함수, 계산, 상태 변경 | 값 계산, 비즈니스 로직 |
| **상태 반응성** | 상태 → UI 연결 | 상태 변경 시 UI가 리빌드되는가 |
| **통합** | 전체 흐름 | 이벤트 → 상태 → UI end-to-end |

> ⚠️ **상태 반응성 테스트 누락은 버그를 은닉한다.**
> 로직 테스트만으로는 "값은 바뀌었는데 UI가 안 바뀌는" 버그를 못 잡는다.
> (knowledge/LESSONS_LEARNED.md: HudOverlay StatelessWidget 사례)

### 3-2. 테스트 커버리지 `[자동검증 가능]`
| 대상 | 기준 |
|------|------|
| 핵심 비즈니스 로직 | 70% 이상 |
| UI 컴포넌트 | 상태 반응성 테스트 1개 이상 (UI 있는 프로젝트) |
| 엣지 케이스 | 실패 케이스 테스트 포함 |

---

## 4. 에러 처리

### 4-1. 핵심 원칙: "조용한 실패 금지"
모든 실패는 아래 중 하나로 처리:
1. 사용자에게 명확한 에러 메시지 표시
2. DB/로그에 에러 기록 + 재시도 수단 제공
3. 의도적 무시인 경우 주석으로 명시

**❌ 금지:**
```js
try { ... } catch(e) {}              // 빈 catch
fetch(url).catch(() => {})            // fire-and-forget without 기록
```

**✅ 허용:**
```js
fetch(url).catch(err => saveErrorToDB(err))   // 에러 기록
try { ... } catch(e) {
  // 의도적 무시: [이유]
}
```

### 4-2. 에러 등급
| 등급 | 설명 | 대응 |
|------|------|------|
| Critical | 크래시, 데이터 손실 | 즉시 수정 |
| High | 주요 기능 작동 불가 | 같은 세션 내 수정 |
| Medium | UX 저하, 간헐적 오류 | 현재 Phase 내 수정 |
| Low | 미세한 UI 이슈 | tech-debt 등록 |

### 4-3. 외부 의존성 호출
외부 API/라이브러리 호출 시 반드시 3가지를 명시:
- 타임아웃
- 재시도 정책 (횟수, 간격)
- 실패 시 fallback 동작

**구체 수치는 프로젝트 QUALITY_SCORE.md에서 정한다.**

---

## 5. UI 상태 머신 (UI 있는 프로젝트)

비동기 작업을 표시하는 모든 UI는 **3가지 상태가 모두 도달 가능해야 한다.**

| 상태 | UI | 조건 |
|------|-----|------|
| Loading | 스켈레톤/스피너 | 작업 진행 중 |
| Success | 결과 표시 | 작업 완료 |
| Error | 에러 메시지 + 재시도 버튼 | 작업 실패 |

### 올바른 구현

**❌ error 분기가 success 안에 숨어있음 — 영구 로딩 버그 발생:**
```tsx
if (data) return <Content data={data} />
else if (error) return <RetryButton />  // data=null이어야 도달. 둘 다 null이면 영원히 skeleton
return <Skeleton />
```

**✅ error를 최우선으로 분기:**
```tsx
if (error) return <RetryButton reason={error} />
if (!data && timedOut) return <RetryButton reason="시간 초과" />
if (!data) return <Skeleton />
return <Content data={data} />
```

> (knowledge/LESSONS_LEARNED.md: Fire-and-forget + UI 상태 머신 사례)

---

## 6. 문서 품질

### 6-1. 모든 프로젝트에 필수 문서
- [ ] `README.md`
- [ ] `CONSTITUTION.md`
- [ ] `CLAUDE.md`
- [ ] `AGENTS.md`
- [ ] `QUALITY_SCORE.md`
- [ ] `PLANS.md`
- [ ] `docs/exec/open-decisions.md`
- [ ] `docs/exec/tech-debt.md`

### 6-2. 문서 작성 규칙
- 한국어 우선, 기술 용어는 영문 허용
- 코드 블록에 언어 태그 명시
- 예시는 "좋은 예 / 나쁜 예" 쌍으로 제시

---

## 7. 검증 체크리스트 (기능 완료 시)

```
Step 1: 정적 분석
  □ 경고 0건

Step 2: 테스트
  □ 로직 테스트 통과
  □ 상태 반응성 테스트 (UI 있는 프로젝트) 존재 및 통과
  □ 통합 테스트 통과

Step 3: 에러 처리
  □ 외부 호출에 타임아웃/재시도/fallback
  □ 조용한 실패 없음

Step 4: UI 상태 (UI 있는 프로젝트)
  □ Loading/Success/Error 3가지 모두 도달 가능
  □ Error가 Success 분기 안에 숨어있지 않음

Step 5: 문서 동기화
  □ PLANS.md 체크리스트 반영
  □ open-decisions 관련 항목 처리
  □ 필요 시 tech-debt 등록
```

---

## 8. 프로젝트 특화 기준 추가

이 문서 아래에 프로젝트별 기준을 추가한다. 예시 카테고리:
- **SaaS**: API 응답 시간, SEO, 보안 (RLS 등)
- **게임**: fps, 메모리, 디바이스 호환, 밸런스 (클리어율/플레이타임)
- **데이터 툴**: 처리 시간, 데이터 정합성, 스키마 마이그레이션
- **AI 에이전트**: 응답 지연, 토큰 비용, 프롬프트 안정성
