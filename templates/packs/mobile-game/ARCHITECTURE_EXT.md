# ARCHITECTURE_EXT.md — mobile-game 아키텍처 가이드

> 참조 전용 문서. 자동 병합되지 않음.
> 프로젝트 `ARCHITECTURE.md` 작성 시 이 가이드를 기반으로 실제 스택을 기술한다.

---

## 1. 스택 선택 기준

### Mobile Game
모바일 우선, 터치 입력, 캐주얼/미드코어 게임.

| 역할 | 권장 | 대안 |
|------|------|------|
| Framework | Flutter + Flame | Unity (IL2CPP) |
| Language | Dart | C# |
| 물리/충돌 | Flame 내장 | Box2D |
| 저장소 | SharedPreferences / Hive | SQLite |
| 광고 | AdMob | Unity Ads |
| 배포 | Google Play / App Store | — |

### PC Game
마우스/키보드, 높은 그래픽 수준, 코어 게임.

| 역할 | 권장 | 대안 |
|------|------|------|
| Engine | Unity | Godot, Unreal |
| Language | C# | GDScript |
| 저장소 | PlayerPrefs / JSON | SQLite |
| 배포 | Steam | itch.io, Epic |

---

## 2. 핵심 설계 원칙

### 게임 루프 구조
```
업데이트 루프
  ↓
입력 처리 → 상태 업데이트 → 충돌 감지 → 렌더링
```
- 게임 로직과 렌더링 분리
- 상태(State)와 뷰(View) 분리

### 데이터 구조
```
스테이지 데이터 (JSON/파일)
    ↓ 로드
StageLoader
    ↓
게임 컴포넌트들 (런타임 상태)
    ↓ 저장/로드
LocalStorage (진행 상황, 설정, 보상)
```
- 런타임 상태와 영속 데이터 분리
- 저장 실패 시 이전 상태 유지 (덮어쓰기 방지)

### 상태 관리 원칙
- 게임 상태는 반드시 **관찰 가능한 형태** 유지 (ValueNotifier, Signal 등)
- HUD가 게임 상태를 직접 폴링하지 않음 → 상태 변화 시 자동 리빌드
- **이 원칙을 어기면 UI 반응성 버그 발생** (knowledge/LESSONS_LEARNED.md LL-001)

---

## 3. 폴더 구조 예시

### Mobile Game (Flutter + Flame)
```
/lib
  /game
    /components           ← 게임 오브젝트 (Ball, Paddle, Enemy 등)
    /systems              ← 게임 로직 (collision, item, score 등)
    /data/stages          ← 스테이지 JSON
  /ui
    /hud                  ← 인게임 HUD
    /overlays             ← 클리어/실패/일시정지
    /lobby                ← 로비/메뉴
  /services               ← 저장소, 광고, 사운드
  /models                 ← 데이터 모델
/test
  /game                   ← 게임 로직 테스트
  /ui                     ← UI 상태 반응성 테스트
```

### PC Game (Unity)
```
/Assets
  /Scripts
    /Game                 ← 게임 로직
    /UI                   ← UI 스크립트
    /Data                 ← 데이터 모델
    /Services             ← 저장소, 오디오
  /Scenes
  /Resources              ← 스테이지 데이터 등
```

---

## 4. 스테이지 시스템 설계 원칙

```
스테이지 데이터 파일 (JSON/ScriptableObject)
  ↓ StageLoader (파일 읽기 + 검증)
  ↓ 게임 초기화 (오브젝트 배치)
  ↓ 게임 플레이 → 클리어/실패 판정
  ↓ 다음 스테이지 또는 로비
```

- 데이터와 로직 분리 (데이터 파일만 바꾸면 새 스테이지)
- 생성 시 자동 검증 (클리어 가능 여부, 스키마 유효성)
- 난이도 커브는 `docs/design-docs/stage-system.md`에 명시

---

## 5. 게임 루프 안정성 원칙

- 수치 무한 증가 방지: 속도/데미지/스코어에 **최대값 클램프**
- 오브젝트 끼임 방지: N프레임 이상 같은 위치면 리셋 로직
- 메모리 관리: 자주 생성/소멸 오브젝트에 **오브젝트 풀링** 적용
- 저장 안정성: 저장 전 유효성 검증, 실패 시 이전 데이터 유지

---

## 6. ARCHITECTURE.md 작성 시 결정 항목

- [ ] 선택한 엔진/프레임워크와 이유
- [ ] 물리 시스템 선택 (엔진 내장 vs 외부 라이브러리)
- [ ] 저장소 방식 (로컬 전용 vs 서버 연동)
- [ ] 수익화 방식 (광고 / 인앱결제 / 없음)
- [ ] 멀티플레이어 여부

**"서버 연동", "수익화 구조"는 CONSTITUTION 불변 원칙 후보다.**
