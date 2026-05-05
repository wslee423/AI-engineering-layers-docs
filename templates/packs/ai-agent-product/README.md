# ai-agent-product Pack

> AI 에이전트 제품(챗봇, AI 어시스턴트, 자동화 도구 등) 프로젝트용 확장 팩.
> base 템플릿에 AI 특화 안전성, 비용, 응답 품질 기준을 추가한다.

---

## 포함 파일

| 파일 | 병합 대상 | 내용 |
|------|----------|------|
| `QUALITY_EXT.md` | `QUALITY_SCORE.md §Pack Extensions` | 응답 지연, 토큰 비용, 안전성, 프롬프트 안정성 |
| `AGENTS_EXT.md` | `AGENTS.md §Pack Extensions` | AI 호출 패턴, 비용 추적 제약 |
| `ARCHITECTURE_EXT.md` | 참조 전용 | AI 에이전트 아키텍처 패턴 가이드 |
| `CHECKLIST_EXT.md` | Reviewer 체크리스트 보충 | 안전성/비용/응답 품질 검증 항목 |

---

## 적용 대상

- Claude / OpenAI / Gemini API 기반 챗봇
- 자동화 에이전트 (코드 생성, 데이터 처리 등)
- AI 어시스턴트 제품

---

## 적용 방법

1. EXT 파일 내용을 프로젝트 각 base 문서의 `## Pack Extensions` 섹션에 병합
2. `.harness/manifest.yml`의 `applied_packs`에 `ai-agent-product` 추가

---

## 스켈레톤 상태

> ⚠️ 이 팩은 스켈레톤(MVP 구조만 정의)이다.
> 프로젝트 적용 시 실제 수치와 기준으로 채워 넣어야 한다.
