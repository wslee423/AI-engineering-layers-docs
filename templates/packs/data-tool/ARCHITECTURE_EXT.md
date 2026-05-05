# ARCHITECTURE_EXT.md — data-tool 아키텍처 가이드

> ⚠️ 스켈레톤. 참조 전용 — 자동 병합되지 않음.

---

## 1. 데이터 파이프라인 레이어

```
데이터 소스 (DB / 파일 / API)
    ↓
Extract (추출 + 검증)
    ↓
Transform (변환 + 정제)
    ↓
Load (적재 + 검증)
    ↓
리포팅 / 알림
```

---

## 2. 핵심 설계 원칙

- **멱등성(Idempotency)**: 같은 파이프라인을 여러 번 실행해도 결과가 동일해야 함
- **체크포인트**: 장시간 처리 중단 시 이어서 재실행 가능
- **관찰 가능성**: 처리 건수, 오류 건수, 소요 시간 로깅
- **스키마 우선**: 데이터 구조 변경은 코드보다 스키마 변경이 선행

---

## 3. 스택 예시

| 역할 | 선택지 |
|------|--------|
| 언어 | Python / TypeScript / Go |
| 데이터 처리 | Pandas / Polars / DuckDB |
| 파이프라인 오케스트레이션 | Prefect / Airflow / 직접 구현 |
| 저장소 | PostgreSQL / BigQuery / S3 |
| 스케줄링 | Cron / GitHub Actions |

---

## 4. 스키마 마이그레이션 전략

```
스테이징 검증 → 프로덕션 적용
  ↓                ↓
성공: 완료       실패: rollback 스크립트 실행
```

- 모든 마이그레이션에 rollback 스크립트 포함
- 대용량 테이블은 단계적 배포 (Add column nullable → Backfill → Add NOT NULL)
