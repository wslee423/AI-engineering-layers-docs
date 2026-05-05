# ARCHITECTURE_EXT.md — web-saas 아키텍처 가이드

> 참조 전용 문서. 자동 병합되지 않음.
> 프로젝트 `ARCHITECTURE.md` 작성 시 이 가이드를 기반으로 실제 스택을 기술한다.

---

## 1. 스택 선택 기준

### Web SaaS
서버 렌더링 / SEO 필요 / 백오피스·대시보드 중심인 경우.

| 역할 | 권장 | 대안 |
|------|------|------|
| Framework | Next.js (App Router) | Remix, Nuxt |
| Language | TypeScript (strict) | — |
| Styling | Tailwind CSS | CSS Modules |
| Backend/DB | Supabase (PostgreSQL + Auth + RLS) | PlanetScale, Firebase |
| AI | OpenAI / Anthropic API | Gemini |
| 배포 | Vercel | Netlify, Railway |

### Mobile SaaS
iOS/Android, 오프라인 지원 / 네이티브 UX가 중요한 경우.

| 역할 | 권장 | 대안 |
|------|------|------|
| Framework | Flutter | React Native |
| Language | Dart | TypeScript (RN) |
| Backend/DB | Supabase / Firebase | 자체 서버 |
| 상태 관리 | Riverpod / Provider | BLoC |
| 배포 | Google Play / App Store | — |

---

## 2. 핵심 설계 원칙

### 인증 흐름
```
클라이언트 → 인증 확인 → 보호된 리소스
                ↓ 미인증
              로그인 페이지 리다이렉트
```
- 모든 보호된 라우트는 서버에서 인증 확인
- 세션은 HttpOnly 쿠키 또는 안전한 저장소 사용

### 데이터 접근 계층
```
UI 컴포넌트
    ↓
API Layer (Route Handlers / REST)
    ↓
DB (Supabase / Firebase) + RLS
```
- UI는 직접 DB 접근 금지
- 모든 DB 접근은 API Layer를 통함

### 에러 전파
```
DB 오류 → API Layer catch → 표준 에러 응답 → UI 에러 상태
```
- DB 에러를 UI에 그대로 노출 금지
- 에러 코드 표준화 (400, 401, 403, 404, 500)

---

## 3. 폴더 구조 예시

### Web SaaS (Next.js App Router)
```
/app
  /api/[resource]/route.ts    ← API Routes
  /[page]/page.tsx            ← 페이지
  /layout.tsx
/components
  /ui/                        ← 재사용 원자 컴포넌트
  /features/[domain]/         ← 기능별 컴포넌트
/lib
  /[service]/                 ← 외부 서비스 클라이언트
  /utils/
/types
  index.ts                    ← 공용 타입 정의
```

### Mobile SaaS (Flutter)
```
/lib
  /features/[domain]/
    /screens/
    /widgets/
    /providers/
  /shared/widgets/, utils/
  /services/                  ← 외부 서비스 클라이언트
  /models/                    ← 데이터 모델
```

---

## 4. 구현 순서 원칙

```
1. DB 스키마 / 마이그레이션
2. RLS 정책
3. 타입 정의
4. API Layer
5. UI 컴포넌트
6. 연결 + 통합
7. 엣지 케이스
8. 검증
```

---

## 5. ARCHITECTURE.md 작성 시 결정 항목

- [ ] 선택한 스택과 이유
- [ ] 선택하지 않은 대안과 기각 이유
- [ ] 주요 제약 조건 (예산, 팀 역량)
- [ ] 나중에 바꿀 수 있는 것 vs 바꾸기 어려운 것

**"바꾸기 어려운 것"은 CONSTITUTION 불변 원칙 후보다.**
