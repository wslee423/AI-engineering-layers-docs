# data-tool Pack

> 데이터 처리 도구 (ETL, 분석 파이프라인, ML 전처리, CLI 도구 등) 프로젝트용 확장 팩.
> base 템플릿에 데이터 정합성, 처리 성능, 스키마 마이그레이션 안전성 기준을 추가한다.

---

## 포함 파일

| 파일 | 병합 대상 | 내용 |
|------|----------|------|
| `QUALITY_EXT.md` | `QUALITY_SCORE.md §Pack Extensions` | 처리 성능, 데이터 정합성, 스키마 마이그레이션 |
| `AGENTS_EXT.md` | `AGENTS.md §Pack Extensions` | 데이터 변경 검증 제약, 롤백 안전성 |
| `ARCHITECTURE_EXT.md` | 참조 전용 | 데이터 파이프라인 아키텍처 패턴 |
| `CHECKLIST_EXT.md` | Reviewer 체크리스트 보충 | 데이터 정합성/마이그레이션 검증 항목 |

---

## 적용 대상

- ETL 파이프라인 (데이터 수집/변환/적재)
- 데이터 분석/리포팅 도구
- ML 데이터 전처리 스크립트
- 관리자용 CLI/배치 도구

---

## 적용 방법

1. EXT 파일 내용을 프로젝트 각 base 문서의 `## Pack Extensions` 섹션에 병합
2. `.harness/manifest.yml`의 `applied_packs`에 `data-tool` 추가

---

## 스켈레톤 상태

> ⚠️ 이 팩은 스켈레톤(MVP 구조만 정의)이다.
> 프로젝트 적용 시 실제 수치와 기준으로 채워 넣어야 한다.
