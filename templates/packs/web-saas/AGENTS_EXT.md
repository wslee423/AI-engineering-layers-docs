# AGENTS_EXT.md — web-saas 에이전트 확장

> 이 파일의 내용은 프로젝트 `AGENTS.md`의 `## Pack Extensions` 섹션에 병합한다.
> BASE 에이전트 정의에 추가되는 SaaS 특화 행동만 포함한다.

---

## [EXT] Implementer — SaaS 구현 순서

SaaS 기능 구현 시 아래 순서를 따른다:

```
1. DB 스키마 / 마이그레이션
2. RLS 정책 설정
3. 타입 정의
4. API Route (서버 로직)
5. UI 컴포넌트
6. 연결 + 통합 확인
7. 엣지 케이스 처리
8. 검증 명령어 실행
```

### Implementer 추가 제약 (SaaS)

- 외부 패키지 무단 추가 금지
- DB/API 스키마 무단 변경 금지
- 신규 DB 테이블 생성 시 RLS 정책 동시 작성 필수
- 클라이언트 코드에 서버 전용 시크릿 노출 절대 금지

---

## [EXT] Reviewer — SaaS 보안 검증

BASE 검증 외 추가 체크:

| 항목 | 확인 방법 |
|------|----------|
| 모든 API 라우트 인증 확인 코드 존재 | grep으로 auth 체크 패턴 확인 |
| PATCH/DELETE 소유권 확인 (user_id 검증) | 각 라우트 수동 확인 |
| 시크릿 하드코딩 없음 | grep으로 API 키 패턴 확인 |
| 클라이언트에 서버 전용 변수 노출 없음 | 클라이언트 코드 내 env 사용 확인 |
| RLS 활성화 (신규 테이블 있는 경우) | DB migration 파일 확인 |
