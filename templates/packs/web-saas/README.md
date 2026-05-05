# web-saas Pack

> Web/Mobile SaaS 프로젝트용 확장 팩.
> base 템플릿에 SaaS 특화 보안, 성능, 아키텍처 기준을 추가한다.

---

## 포함 파일

| 파일 | 병합 대상 | 내용 |
|------|----------|------|
| `QUALITY_EXT.md` | `QUALITY_SCORE.md §Pack Extensions` | 보안(RLS/인증), API 성능, 테스트 추가 기준 |
| `AGENTS_EXT.md` | `AGENTS.md §Pack Extensions` | Implementer SaaS 구현 순서, Reviewer 보안 체크 |
| `ARCHITECTURE_EXT.md` | 참조 전용 | Web/Mobile SaaS 스택 선택 + 설계 원칙 가이드 |
| `CHECKLIST_EXT.md` | Reviewer 체크리스트 보충 | 기능 완료 시 SaaS 특화 검증 항목 |

---

## 적용 대상

- Next.js / Remix 기반 Web SaaS
- Flutter 기반 Mobile SaaS
- Supabase / Firebase / PlanetScale 백엔드

---

## 적용 방법

1. EXT 파일 내용을 프로젝트 각 base 문서의 `## Pack Extensions` 섹션에 병합
2. `ARCHITECTURE_EXT.md`를 프로젝트 `ARCHITECTURE.md` 작성 참고로 사용
3. `.harness/manifest.yml`의 `applied_packs`에 `web-saas` 추가

---

## EXT 병합 규칙

- 더 엄격한 기준이 이긴다 (보안/비용/성능 항목)
- 충돌 시 `docs/exec/open-decisions.md`에 등록 후 Owner 결정
