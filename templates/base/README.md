# [프로젝트명]

> 한 줄 설명: (무엇을, 누구를 위해, 왜 만들었는지)

---

## 📌 프로젝트 정보

| 항목 | 내용 |
|------|------|
| 프로젝트 타입 | (data-tool / ai-agent / web-saas / mobile-saas / mobile-game / pc-game / other) |
| 현재 상태 | (Phase N — 개발중 / 출시됨 / 유지보수) |
| 마지막 업데이트 | YYYY-MM-DD |

---

## 🧱 핵심 스택

| 역할 | 기술 |
|------|------|
| 언어 | (예: TypeScript / Dart / Python) |
| Frontend | (예: Next.js 16 + Tailwind / 없음) |
| Backend | (예: Supabase / Firebase / 로컬 전용) |
| AI/외부 API | (예: OpenAI gpt-4o-mini / 없음) |
| 배포 | (예: Vercel / Google Play / 로컬) |

---

## 🚀 빠른 시작

```bash
# 설치
[설치 명령]

# 실행
[개발 서버 실행 명령]

# 검증 (typecheck / lint / test)
[검증 명령]
```

### 환경 변수
`.env` 파일 필요. 자세한 값은 보안 문서 참조.
```
API_KEY=
DATABASE_URL=
```

---

## 🤖 에이전트 명령어

| 명령어 | 용도 |
|--------|------|
| `/[cmd]` | (용도) |
| `/[cmd]` | (용도) |

상세: `AGENTS.md` 및 `.claude/commands/*.md`

---

## 📚 문서 구조

### 최상위 문서
| 파일 | 역할 |
|------|------|
| `CONSTITUTION.md` | 불변 원칙 (최상위 권위) |
| `CLAUDE.md` | 에이전트 세션 자동 로드 컨텍스트 |
| `AGENTS.md` | 에이전트 역할 및 자율 범위 |
| `QUALITY_SCORE.md` | 품질 기준 |

### 내부 문서 (`docs/`)
| 경로 | 내용 |
|------|------|
| `PLANS.md` | 개발 로드맵, 체크리스트 |
| `ARCHITECTURE.md` | 기술 스택 및 구조 |
| `product-specs/` | 기능별 상세 스펙 |
| `design-docs/` | 디자인 가이드 *(선택)* |
| `exec-plans/open-decisions.md` | 미결 의사결정 |
| `exec-plans/tech-debt-tracker.md` | 기술 부채 |
| `exec-plans/NEXT_SESSION.md` | 다음 세션 핸드오프 |

---

## 🔄 현재 상태

- **현재 Phase**: (Phase N: [이름])
- **최근 완료**: (기능명) — YYYY-MM-DD
- **다음 할 것**: (작업명)

상세: `docs/PLANS.md`, `docs/exec-plans/NEXT_SESSION.md`

---

## 🚫 이 프로젝트의 불변 원칙

CONSTITUTION.md 섹션 5 요약. **수정은 CONSTITUTION.md에서만.**

1. **[원칙 1]**
2. **[원칙 2]**
3. **[원칙 3]**

---

## 👤 협업 방식

사람(방향 결정) + AI 에이전트(구현)의 협업.

- **사람**: 방향 결정, 큰 기능 승인, CONSTITUTION 개정
- **에이전트**: 구현, 검증, 문서 동기화, 작은 기능 자체 완료

상세: Core의 `WORKFLOW.md`

---

## 📄 라이선스

(라이선스 또는 "Private")
