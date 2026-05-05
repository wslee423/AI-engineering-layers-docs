# CHECKLIST_EXT.md — web-saas Reviewer 체크리스트 확장

> 기능 완료 시 Reviewer subagent가 BASE 체크리스트 이후 추가로 실행한다.

---

## Step 6: 보안 (SaaS 추가)

```
□ 모든 API 라우트 인증 확인 코드 존재
□ PATCH/DELETE 소유권 확인 (user_id 검증)
□ 시크릿 하드코딩 없음 (grep으로 확인)
□ 클라이언트에 서버 전용 변수 노출 없음
□ RLS 활성화 (신규 테이블 있는 경우)
```

## Step 7: 스펙 일치 (SaaS 추가)

```
□ 변경사항이 product-specs와 일치
□ 스펙에 없는 기능 추가 없음
□ API 응답 시간 목표 달성 (QUALITY_EXT.md §성능 기준)
```
