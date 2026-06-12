# 팀플레이어 학습 도구 — Claude Code 핸드오프 문서

> **이 문서는 Claude Code 세션 시작 시 첫 번째로 읽어야 할 컨텍스트 파일입니다.**
> 세션 시작 명령: `cat CLAUDE_HANDOFF.md`

---

## 1. 프로젝트 개요

Patrick Lencioni의 *The Ideal Team Player* 기반 **모바일 직원 학습 앱** 프로토타입.
케이옥션 IT팀 내부용. 3덕목(겸손·갈망·영리함)을 7개 모듈로 학습하고, 자기진단과 착근기 재인증까지 연결되는 완성된 인터랙티브 프로토타입.

---

## 2. 파일 구조

```
팀플레이어학습도구/
├── prototype.html              ← ★ 메인 작업 파일 (단일 파일, 1537줄)
│                                   HTML + CSS + JS 전부 포함
├── CLAUDE_HANDOFF.md           ← 이 문서
├── 화면기획서.md                ← 5개 화면 UX 명세 (11섹션)
├── 시스템기획서.md              ← 4-레이어 시스템 아키텍처 (13섹션)
├── 정보설계기획서.md            ← IA, 앱맵, 플로우, 레이블링 (10섹션)
├── 작업지시서_디자인핸드오프.md ← 디자인 세션 핸드오프 문서
├── design-system.md            ← 디자인 시스템 레퍼런스
└── ux-principles.md            ← UX 원칙 레퍼런스
```

---

## 3. CSS 변수 시스템

```css
:root {
  /* 앱 배경/카드/테두리 */
  --bg: #f5f6fa
  --card: #fff
  --border: #e2e5ec

  /* 텍스트 */
  --text1: #1a1d27   /* 강조 본문 */
  --text2: #6b7280   /* 보조 본문 */
  --text3: #9198a8   /* 캡션/레이블 */

  /* 형태 */
  --radius: 12px
  --radius-sm: 8px

  /* 덕목 — 겸손(h) */
  --h-bg: #FFFBEB
  --h-color: #F59E0B
  --h-dark: #92400E
  --h-light: rgba(245,158,11,.12)

  /* 덕목 — 갈망(g) */
  --g-bg: #EFF6FF
  --g-color: #2563EB
  --g-dark: #1E3A8A
  --g-light: rgba(37,99,235,.12)

  /* 덕목 — 영리함(s) */
  --s-bg: #F0FDF4
  --s-color: #16A34A
  --s-dark: #14532D
  --s-light: rgba(22,163,74,.12)

  /* 프라이머리 */
  --primary: #6366f1
  --primary-light: #eef2ff
}
```

---

## 4. 화면 구조 (5개 스크린)

| 스크린 ID | 탭 | 설명 |
|-----------|-----|------|
| `s-home` | 홈 | 인사, 블롭 Venn, 진행 현황 카드 |
| `s-modules` | 모듈 | 7개 모듈 목록, 덕목별 섹션 |
| `s-module` | — | 모듈 상세 (풀스크린 슬라이드인) |
| `s-diag` | 진단 | 18문항 자기진단, 결과 뷰 |
| `s-timeline` | 여정 | 학습 타임라인, Day+7/+21 재인증 |

### 라우터

```js
const screens = {
  home:'s-home', modules:'s-modules', module:'s-module',
  diag:'s-diag', timeline:'s-timeline'
};
goTo('home')   // 탭 전환
openModule(0)  // 모듈 상세 슬라이드인
goBackFromModule()  // 모듈 → 모듈 목록 슬라이드아웃
```

---

## 5. 핵심 JS 함수 목록 (33개)

### 라우터
| 함수 | 설명 |
|------|------|
| `goTo(name)` | 탭 전환 |
| `goBackFromModule()` | 모듈 상세에서 뒤로 (슬라이드아웃) |

### 모듈
| 함수 | 설명 |
|------|------|
| `openModule(idx)` | 모듈 상세 열기, 데이터 렌더 + 슬라이드인 |
| `switchTab(t)` | 'read' / 'practice' / 'gate' 탭 전환 |

### 홈 Venn
| 함수 | 설명 |
|------|------|
| `initHomeVenn()` | 블롭 Venn 다이어그램 초기화 |

### 카드뉴스
| 함수 | 설명 |
|------|------|
| `initCN()` | 현재 모듈 카드뉴스 캐러셀 초기화 |
| `trackGoTo(trackId, idx)` | 특정 슬라이드로 스크롤 이동 |
| `syncTrackDots(trackId, dotsId)` | 스크롤에 따라 dot indicator 동기화 |

### 연습 퀴즈
| 함수 | 설명 |
|------|------|
| `renderPQCarousel()` | 연습 퀴즈 3문항 캐러셀 렌더 |
| `pickPQCard(qi, ai)` | 선택지 탭 → 즉시 정오답 표시 + 해설 |
| `retryPQ()` | 연습 퀴즈 초기화 후 재렌더 |

### 게이트 퀴즈
| 함수 | 설명 |
|------|------|
| `renderGate()` | 인트로카드 + 5문항 캐러셀 렌더 |
| `pickGate(qi, ai)` | 선택지 탭 → 선택 상태 저장 + 배지 업데이트 |
| `submitGate()` | 미선택 검증 → 로딩 1.6초 → 채점 → 결과 바텀시트 |
| `retryGate()` | 게이트 초기화 후 재도전 |
| `closeGateSheet(e)` | 게이트 결과 바텀시트 닫기 |

### 자기진단
| 함수 | 설명 |
|------|------|
| `renderSurvey()` | 18문항 동적 렌더, `window.svAnswers={}` 초기화 |
| `pickSurvey(key, val, vk)` | 척도 선택 → sel-3/2/1 클래스 토글 |
| `updateSvProgress()` | "N / 18 완료" 텍스트 업데이트 |
| `submitDiag()` | 18문항 완성 검증 → 점수 계산 → 결과 렌더 |
| `resetDiag()` | 진단 초기화 후 `renderSurvey()` 재호출 |

### 착근기 재인증
| 함수 | 설명 |
|------|------|
| `openRecert(day)` | Day+7/+21 바텀시트 열기, 3문항 + 체크리스트 렌더 |
| `pickRecert(qi, ai, isSelfReport)` | 자기보고면 'selected', 아니면 correct/wrong |
| `toggleRecertCheck(el, i)` | 체크리스트 항목 토글 |
| `submitRecert(day)` | 완료 처리, 타임라인 dot 활성화 |
| `closeRecertSheet(e)` | 재인증 바텀시트 닫기 |
| `initRecertButtons()` | 프로토타입용: Day+7 버튼 활성화 |

### init
```js
// script 최하단 실행 순서
initHomeVenn();
initCN();
renderGate();
renderSurvey();
initRecertButtons();
```

---

## 6. 핵심 데이터 구조

### MODS (모듈 7개)
```js
const MODS = [
  { id, n, title, virtue, vLabel, emoji, desc, objectives[] }
  // virtue: 'h' | 'g' | 's'
  // objectives: 학습 목표 2~3개 ("~할 수 있다" 형식)
];
```

### PQ (연습 퀴즈 — 현재 M1 기준 3문항 고정)
```js
const PQ = [
  { q, opts[4], ans, exp }  // ans: 정답 인덱스(0-based), exp: 해설
];
```

### GQ (게이트 퀴즈 — 현재 M1 기준 5문항 고정)
```js
const GQ = [
  { q, opts[4], ans }  // exp 없음 (게이트는 해설 미표시)
];
```

### SURVEY_DATA (자기진단)
```js
const SURVEY_DATA = {
  h: { label, color, dark, bg, modRange, items[6] },
  g: { ... },  // 갈망 6문항
  s: { ... }   // 영리함 6문항
};
// 척도: 언제나(3) / 때때로(2) / 거의 안 함(1)
// 최대 18점/덕목, 총 54점
```

### RECERT_DATA (재인증)
```js
const RECERT_DATA = {
  7: { title, subtitle, questions[3], practiceItems[3] },
  21: { title, subtitle, questions[3], practiceItems[3] }
  // questions[].ans === null이면 자기보고 문항
};
```

---

## 7. 주요 CSS 클래스 패턴

| 클래스 | 용도 |
|--------|------|
| `.screen.active` | 현재 표시 중인 화면 |
| `.slide-in` / `.slide-out` | 모듈 상세 슬라이드 애니메이션 |
| `.sheet-overlay.show` | 바텀시트 표시 (display:flex + pointer-events:auto) |
| `.btn-primary` / `.btn-h` / `.btn-g` / `.btn-s` / `.btn-outline` | 버튼 변형 |
| `.badge-h` / `.badge-g` / `.badge-s` / `.badge-common` | 덕목 배지 |
| `.choice.selected` / `.choice.correct` / `.choice.wrong` | 퀴즈 선택지 상태 |
| `.cn-slide` | 캐러셀 슬라이드 단위 |
| `.sv-opt.sel-3` / `.sel-2` / `.sel-1` | 자기진단 척도 선택 상태 |
| `.nav-item.on` | 활성 탭 |

### 바텀시트 열기/닫기 패턴
```js
// 열기
document.getElementById('some-sheet').classList.add('show');
// 닫기
document.getElementById('some-sheet').classList.remove('show');
// CSS: .sheet-overlay { display:flex; pointer-events:none; opacity:0; transition: ... }
//      .sheet-overlay.show { pointer-events:auto; opacity:1; }
```

---

## 8. 현재 구현 상태 & 다음 작업

### ✅ 완료된 기능
- 5개 화면 라우터 + 슬라이드인/아웃 애니메이션
- 홈: 블롭 Venn 다이어그램, 진행 현황 카드
- 모듈 목록: 7개 모듈, 덕목별 섹션
- 모듈 상세: 학습 목표 블록, 3탭(학습/연습퀴즈/게이트)
- 카드뉴스 캐러셀 (scroll-snap-type: x mandatory)
- 연습 퀴즈: 캐러셀 3문항, 즉시 정오답+해설, 다시 풀기
- 게이트: 인트로카드+5문항 캐러셀, 전체 채점, 로딩+결과 바텀시트
- 자기진단: 18문항 설문, 덕목별 그룹화, 점수 바차트+집중포인트
- 착근기 재인증: Day+7/+21 바텀시트, 3문항+체크리스트

### 🔴 High Priority (다음 세션 핵심 작업)

**1. 모듈별 PQ/GQ 교체 로직**
현재 PQ[]와 GQ[]가 M1 기준으로 고정되어 있음.
`openModule(idx)` 호출 시 해당 모듈의 퀴즈 데이터로 교체되어야 함.

방법 A: MODS 배열에 `pq[]`와 `gq[]` 필드 추가
```js
// MODS[idx].pq = [...3문항], MODS[idx].gq = [...5문항]
// openModule() 내에서:
// window._curPQ = MODS[idx].pq;
// window._curGQ = MODS[idx].gq;
// renderPQCarousel() / renderGate() 에서 PQ/GQ 대신 _curPQ/_curGQ 참조
```

방법 B: 별도 QUIZ_DATA 객체로 관리
```js
const QUIZ_DATA = {
  0: { pq: [...], gq: [...] },  // M1
  1: { pq: [...], gq: [...] },  // M2
  ...
};
```

**2. M2~M7 카드뉴스 콘텐츠 작성**
현재 `initCN()`이 M1 콘텐츠만 하드코딩됨.
MODS 배열 또는 별도 CARDS_DATA에 각 모듈 카드 데이터 추가 필요.

**3. 홈 진행 현황 연동**
현재 `겸손 1/3, 갈망 0/2, 영리함 0/2` 하드코딩.
모듈 완료 상태를 저장하고 실시간 반영해야 함.

```js
// 모듈 완료 상태 저장 예시 (localStorage 또는 JS 상태)
const moduleCompleted = { 0:false, 1:false, ... , 6:false };
function completeModule(idx) {
  moduleCompleted[idx] = true;
  updateHomeProgress();
}
function updateHomeProgress() {
  const hDone = [0,1,2].filter(i => moduleCompleted[i]).length;
  const gDone = [3,4].filter(i => moduleCompleted[i]).length;
  const sDone = [5,6].filter(i => moduleCompleted[i]).length;
  // DOM 업데이트
}
```

### 🟡 Medium Priority

| 항목 | 설명 |
|------|------|
| 게이트 통과 → 다음 모듈 연결 | 통과 시 실제 다음 모듈 CTA 동작 |
| 타임라인 완료 처리 시각화 | 완료 항목 녹색 체크 배지 + 날짜 표시 |
| 자기진단 결과 → 학습 추천 연동 | 집중 포인트 덕목의 첫 모듈 CTA |
| 다크 모드 | `prefers-color-scheme: dark` 분기 |
| 게이트 통과 축하 | 통과 시 pulse 또는 confetti 애니메이션 |

### 🟢 Low Priority (추가 기능)

| 항목 | 설명 |
|------|------|
| 온보딩 플로우 | 최초 진입 시 3단계 온보딩 (서비스 소개 → 자기진단 유도 → 모듈 목록) |
| 성취 배지 시스템 | 덕목별/전체 완료 시 배지 발급 |
| 학습 스트릭 | 연속 학습일 표시 (홈 화면) |
| 모듈 잠금 UI | 이전 미완료 시 locked 스타일 |

---

## 9. UX 카피 기준 (확정)

| 원칙 | 규칙 |
|------|------|
| CTA 동사 시작 | 풀기, 시작하기, 채점하기, 보기, 도전하기 |
| 목적어 명시 | ~~시작하기~~ → **게이트 채점하기** |
| 진행 표시어 | `완료` 계열 통일 (`N / N 완료`, `선택 전`, `✓ 선택됨`) |
| 긍정 프레이밍 | `~입니다` 시작 — `~아닙니다` 시작 금지 |
| 화살표(→) | 다음 화면 이동 시만 사용 |

---

## 10. 알려진 제약사항

- prototype.html 단일 파일 아키텍처 유지 (CSS, JS 분리 금지)
- `localStorage`는 프로토타입 전용. 실제 서비스 전환 시 서버 API로 교체 예정
- max-width: 430px 모바일 기준 설계. 데스크탑 레이아웃 고려 불필요
- 현재 PQ/GQ 데이터는 M1(겸손) 기준. M2~M7 콘텐츠 미작성

---

## 11. 세션 시작 명령 예시

### 모듈별 퀴즈 교체 로직 구현
```
팀플레이어학습도구/prototype.html 작업합니다.
CLAUDE_HANDOFF.md를 읽어 컨텍스트 파악 후 시작해주세요.

오늘 작업:
1. MODS 배열에 각 모듈별 pq(연습퀴즈 3문항)와 gq(게이트 5문항) 데이터 추가
2. openModule()에서 해당 모듈 퀴즈 데이터로 교체되도록 renderPQCarousel(), renderGate() 수정
3. M2~M3 연습퀴즈 문항 직접 작성 (겸손 덕목 관련)
```

### 홈 진행 현황 연동
```
팀플레이어학습도구/prototype.html 작업합니다.
CLAUDE_HANDOFF.md 먼저 읽어주세요.

오늘 작업:
모듈 완료 상태를 JS 상태로 관리하고, 게이트 통과 시 홈 화면 현황 카드(겸손 N/3, 갈망 N/2, 영리함 N/2)에 실시간 반영되도록 구현
```

---

*최종 업데이트: 2026-06-12 · 케이옥션 IT팀 서비스기획파트*
