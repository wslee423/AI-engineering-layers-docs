# ARCHITECTURE_EXT.md — ai-agent-product 아키텍처 가이드

> ⚠️ 스켈레톤. 참조 전용 — 자동 병합되지 않음.

---

## 1. AI 에이전트 레이어 구조

```
사용자 입력
    ↓
Input Validation (프롬프트 인젝션 방어)
    ↓
Context Builder (히스토리 + 시스템 프롬프트 조합)
    ↓
LLM API (Claude / OpenAI 등) + 타임아웃/재시도
    ↓
Output Parser + Safety Filter
    ↓
응답 출력 + 비용 로깅
```

---

## 2. 핵심 설계 원칙

- **시스템 프롬프트 분리**: 코드가 아닌 설정/파일로 관리, git 버전 관리
- **컨텍스트 윈도우 관리**: 오래된 히스토리 자동 트리밍 전략 명시
- **비용 추적**: 모든 API 호출에 입력/출력 토큰 수 기록
- **Graceful Degradation**: API 장애 시 fallback 메시지 제공

---

## 3. 스택 예시

| 역할 | 선택지 |
|------|--------|
| LLM | Claude (Anthropic API) / OpenAI / Gemini |
| 프레임워크 | LangChain / LlamaIndex / 직접 구현 |
| 벡터 DB | Supabase pgvector / Pinecone / Weaviate |
| 캐시 | Redis / 인메모리 |
| 배포 | Vercel / Railway / AWS Lambda |
