# docs/product-specs/ — 기능별 상세 스펙

기능별 상세 스펙을 저장하는 디렉토리입니다.
기능 구현 전 요구사항, 입력/출력, 엣지 케이스, 완료 기준을 기록합니다.

---

## 파일 명명 규칙

```
docs/product-specs/[기능명].md
예: docs/product-specs/user-auth.md
    docs/product-specs/payment-flow.md
    docs/product-specs/stage-generator.md
```

## 스펙 파일 기본 형식

```markdown
# [기능명] 스펙

## 목적
(한 줄 — 무엇을 위한 기능인가)

## 입력
(파라미터, 데이터 구조)

## 출력
(반환값, 상태 변화, 부수 효과)

## 엣지 케이스
- (예외 상황과 처리 방법)

## 완료 기준
- [ ] (검증 가능한 조건 1)
- [ ] (검증 가능한 조건 2)
```
