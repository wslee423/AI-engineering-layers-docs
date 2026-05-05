# agent_runtime UPGRADE_GUIDE

> 기존 프로젝트의 `.claude/`를 최신 agent_runtime으로 업그레이드하는 지침.
> **업그레이드는 자동으로 일어나지 않는다. Owner가 직접 판단하고 수행한다.**

---

## 업그레이드 원칙

1. `CHANGELOG.md`에서 변경 내역 확인
2. 해당 버전 섹션의 "Breaking Changes" 또는 "마이그레이션 필요" 항목 확인
3. 프로젝트 `.claude/`에 수동 반영
4. 프로젝트의 `.harness/manifest.yml`에 `agent_runtime_version` 갱신
5. 적용 후 `/review` 또는 `/orchestrate`로 동작 검증

---

## 버전별 업그레이드 지침

### → v0.1.0 (최초 버전)

신규 프로젝트: `agent_runtime/`을 그대로 `.claude/`로 복사.

기존 프로젝트 (claude_code 기반):
```
변경 사항:
1. .claude/hooks/validate-reviewer-call.js 경로 참조 — 변경 없음
2. .claude/commands/ 내 경로 참조 갱신 필요:
   - core_docs/ → core/
   - docs/exec-plans/ → docs/exec/
   - BACKPORT_PROPOSAL.md → docs/exec/backports/BP-XXX.md
3. .claude/shared/ 디렉토리 생성
4. ESCALATION_BLOCK.md를 .claude/shared/에 복사
5. VERSION.md, CHANGELOG.md, UPGRADE_GUIDE.md를 .claude/에 복사
6. .harness/manifest.yml 생성 (templates/base/.harness/manifest.yml 참조)
```

---

## 로컬 커스터마이징이 있는 경우

프로젝트 `.claude/`에 upstream과 다른 수정이 있는 경우:

1. 수정된 파일을 확인 (git diff 또는 manifest.yml의 `runtime_customizations`)
2. upstream 변경이 커스터마이징에 영향을 주는지 확인
3. 영향 없으면 — upstream 변경만 반영
4. 영향 있으면 — 수동 병합 + `/learn`으로 backport 재검토

---

## manifest.yml 업데이트

업그레이드 완료 후:

```yaml
agent_runtime_version: vX.Y.Z   # 새 버전으로 갱신
```
