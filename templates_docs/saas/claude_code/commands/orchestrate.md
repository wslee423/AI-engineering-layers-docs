# /orchestrate — SaaS 특화 override

> 보편 명세는 `claude_code/commands/orchestrate.md` 참조.
> 이 파일은 SaaS 프로젝트 한정 추가 규칙만 정의한다.
> 새 프로젝트 초기화 시 `.claude/commands/orchestrate.md`로 복사.

---

## SaaS 특화: Large 분류 기준 추가

보편 기준(`claude_code/commands/orchestrate.md` §2) 외에 다음도 Large로 분류:

- DB 스키마 변경 (테이블 추가 / 컬럼 변경 / RLS 정책 변경)
- API 라우트 신규 추가 또는 인증/소유권 로직 변경
- 외부 API 연동 추가 (인증 제공자, 결제, 이메일 등)
- 환경변수 신규 추가

---

## SaaS 특화: Implementer 구현 시퀀스

implementer subagent 호출 시 다음 시퀀스를 prompt에 명시한다:

```
1. DB 스키마 (마이그레이션 파일 또는 DDL)
2. RLS 정책 + service role 분리
3. 타입 정의 (DB 자동 생성 타입 사용 권장)
4. API Route (인증 확인 → 소유권 확인 → 비즈니스 로직)
5. UI 컴포넌트 (Loading / Success / Error / Empty 상태 모두)
6. 자체 검증 (typecheck + lint)
```

---

## SaaS 특화: Reviewer에게 추가 전달

보편 전달 항목 외에, 다음 조건에 따라 추가 검토 요청을 reviewer 호출 prompt에 포함:

- 신규 DB 테이블 있는 경우 → "RLS 활성화 확인 필수"
- 신규 API Route 있는 경우 → "인증/소유권 코드 존재 확인 필수"
- 환경변수 추가된 경우 → "클라이언트 노출 여부 확인 필수"
