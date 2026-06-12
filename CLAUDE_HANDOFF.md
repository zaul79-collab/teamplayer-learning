# 팀플레이어 학습 도구 — Claude Code 핸드오프 문서

> **이 문서는 Claude Code 세션 시작 시 첫 번째로 읽어야 할 컨텍스트 파일입니다.**
> 세션 시작 명령: `cat CLAUDE_HANDOFF.md`

---

## 1. 프로젝트 개요

Patrick Lencioni의 *The Ideal Team Player* 기반 **모바일 직원 학습 앱** 프로토타입.
케이옥션 IT팀 내부용. 3덕목(겸손·갈망·영리함)을 7개 모듈로 학습하고, 모델 소개·자기진단·착근기 재인증까지 연결되는 완성된 인터랙티브 프로토타입.
모든 모듈(M1~M7)의 카드뉴스·연습퀴즈·게이트 콘텐츠와 진행 상태(localStorage)가 채워진 상태입니다.

---

## 2. 파일 구조

```
팀플레이어학습도구/
├── prototype.html              ← ★ 메인 작업 파일 (단일 파일, 1421줄)
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

## 4. 화면 구조 (4개 탭 + 2개 슬라이드인 패널)

| 스크린 ID | 탭 | 설명 |
|-----------|-----|------|
| `s-home` | 홈 | 히어로 카드(블러 orb + "모델 알아보기 →" 칩), 진행 현황, 다음 모듈 |
| `s-modules` | 모듈 | 7개 모듈 목록, 덕목별 섹션, 순차 개방(잠금) |
| `s-diag` | 진단 | 18문항 자기진단, 결과 뷰 |
| `s-timeline` | 여정 | 학습 타임라인, Day+7/+21 재인증 |
| `s-module` | — | 모듈 상세 (우측 슬라이드인 패널, `.in`) |
| `s-about` | — | 모델 소개 / 이상적인 팀 플레이어 (우측 슬라이드인 패널, `.in`) |

### 라우터

```js
const SCREENS = { home:'s-home', modules:'s-modules', diag:'s-diag', timeline:'s-timeline' };
goTo('home')        // 탭 전환 (nav-item .on 토글)
openModule(0)       // 모듈 상세 슬라이드인 (잠긴 모듈은 토스트 후 차단)
goBackFromModule()  // 모듈 상세 → 슬라이드아웃 (.in 제거)
openAbout()         // 모델 소개 패널 슬라이드인 + IntersectionObserver 순차 노출
closeAbout()        // 모델 소개 패널 닫기
```

> 모듈 상세·모델 소개는 별도 탭이 아니라 `.screen` 위에 겹쳐지는 패널로, `visibility + translateX` 전환을 씁니다.

### 모델 소개 (`s-about`) 구성

홈 히어로 카드 우상단 **"모델 알아보기 →"** 칩으로 진입. 모듈 상세와 동일한 우측 슬라이드인, 뒤로가기 버튼. 출처: source/06_도해 (01_벤다이어그램, 02_7유형_매트릭스, 05_다섯함정_연결도).

1. **인트로** — 렌시오니 모델, '이상적' ≠ '완벽'
2. **벤다이어그램** — 겸손·갈망·영리함 3원 교집합 = 이상적인 팀 플레이어 (원 순차 등장 + 이상적 문구 금색 반짝임 `idealShimmer`)
3. **세 가지 덕목 카드**
4. **7가지 유형** — 겸손한 졸 / 불도저 / 사랑받는 연예인 / 돌발적인 사고뭉치 / 사랑스러운 게으름뱅이 / 노련한 정치가(가장 위험) / 이상적인 팀 플레이어(목표). 덕목 칩 + 위험도 배지. (유형명은 자기인식·코칭용, 동료 꼬리표 금지)
5. **세 덕목이 막는 다섯 함정** — ①신뢰의 결여 ~ ⑤결과에 대한 무관심 피라미드 + 겸손→신뢰 / 영리함→건전한 갈등 / 갈망→헌신·책임·결과
6. **CTA** — "학습 모듈 보러가기"

---

## 5. 핵심 JS 함수 목록 (48개)

### 상태 / 영속 (localStorage)
| 함수 | 설명 |
|------|------|
| `load()` / `save()` | `tp2-state` 키로 state 복원·저장 |
| `today()` | `YYYY.MM.DD` 문자열 |
| `doneCount()` | 완료 모듈 수 |
| `vDone(vk)` | 덕목별 완료 모듈 수 (`V[vk].mods` 기준) |
| `isUnlocked(i)` | 순차 개방 — i===0 또는 이전 모듈 완료 시 true |
| `nextModule()` | 다음에 풀 수 있는(잠금 해제+미완료) 모듈 인덱스 |
| `resetAll()` | 전체 초기화 후 재렌더 |

### 라우터 / 패널
| 함수 | 설명 |
|------|------|
| `goTo(name)` | 4개 탭 전환 |
| `openModule(i)` | 잠금 확인 → 렌더 → 모듈 상세 슬라이드인 |
| `goBackFromModule()` | 모듈 상세 닫기 |
| `openAbout()` / `closeAbout()` | 모델 소개 패널 열기·닫기 (`aboutObs` IntersectionObserver로 `.reveal` 순차 노출) |
| `switchTab(t)` | 모듈 상세 'read' / 'practice' / 'gate' 세그먼트 전환 |

### 헬퍼
| 함수 | 설명 |
|------|------|
| `toast(msg)` | 하단 토스트 (2.2초) |
| `syncDots(trackId, dotsId)` | 캐러셀 스크롤 → dot indicator 동기화 |
| `trackGoTo(trackId, i)` | 특정 슬라이드로 스무스 스크롤 |

### 홈
| 함수 | 설명 |
|------|------|
| `renderHome()` | 날짜·히어로 진행바·덕목 현황·다음 모듈 카드 렌더 |
| `homeNextGo()` | 다음 모듈 열기, 없으면 여정 탭으로 |

### 모듈 목록 / 상세
| 함수 | 설명 |
|------|------|
| `renderModules()` | 덕목별 섹션 + 7개 모듈 카드(잠금 표시) |
| `renderModule()` | 현재 모듈 헤더·세그먼트 + 3탭 콘텐츠 렌더 |
| `renderRead()` | 카드뉴스 캐러셀 + 실천 체크리스트 렌더 |
| `toggleCheck(i)` / `renderCkCta()` | 체크 토글 / 체크 진행 CTA 갱신 |

### 연습 퀴즈
| 함수 | 설명 |
|------|------|
| `renderPQ()` | 현재 모듈 `MODS[cur].pq` 캐러셀 렌더 |
| `pickPQ(qi, ai)` | 선택 → 즉시 정오답 + 해설 표시 |
| `retryPQ()` | 다시 풀기 |

### 게이트 퀴즈
| 함수 | 설명 |
|------|------|
| `renderGate()` | 인트로카드 + `MODS[cur].gq` 캐러셀, 채점 버튼(`gq-submit`) disabled로 시작 |
| `pickGate(qi, ai)` | 선택 저장 + 배지 → 해당 `gq-next-N` 활성, 전체 선택 시 `gq-submit` 활성 |
| `submitGate()` | 미선택 검증 → 로딩 1.5초 → 채점 → 통과 시 `completeModule`, 미통과 시 `gateFails` +1 → 결과시트 |
| `showGateSheet(score,total,pass,saved)` | 결과 바텀시트 — 통과 시 `launchConfetti()`, 미통과 시 실패 횟수별 멘토 안내 메시지 |
| `closeGateSheet(e)` / `retryGate()` | 결과시트 닫기 / 다시 도전 |
| `launchConfetti()` | 통과 축하 폭죽 캔버스 애니메이션 (내부 `draw()`) |
| `completeModule(i)` | 완료 처리 + 날짜 기록, 7개 완료 시 `certifiedAt` 설정 |

### 자기진단
| 함수 | 설명 |
|------|------|
| `renderSurvey()` | 공식 18문항(덕목별 6문항) 동적 렌더 |
| `pickSv(k, val, vk)` | 3단계 척도 선택 → sel 상태 토글 |
| `submitDiag()` | 18문항 검증 → 덕목 합산 → 최저 덕목 focus 저장 → 결과 |
| `renderDiagResult()` | 6~18점 막대 + 해석 밴드 + 개인 개발 초점 + 학습 추천 CTA |
| `diagToModule(i)` | 추천 모듈 열기(잠금 시 모듈 목록) |

### 모델 소개
| 함수 | 설명 |
|------|------|
| `renderAbout()` | `TYPES` 7유형 리스트 렌더 (덕목 칩 + 위험도 배지) |

### 착근기 재인증
| 함수 | 설명 |
|------|------|
| `openRecert(day)` | Day+7/+21 바텀시트, `RECERT[day]` 3문항 + 체크리스트 렌더 |
| `pickRecert(day, qi, ai)` | `ans===null`(자기보고)이면 selected, 아니면 correct/wrong |
| `toggleRecertCheck(i)` | 체크리스트 항목 토글 |
| `submitRecert(day)` | 완료 처리, 타임라인 갱신 |
| `closeRecertSheet(e)` | 재인증 바텀시트 닫기 |

### init (script 최하단 실행 순서)
```js
function renderAll(){ renderHome(); renderModules(); renderTimeline(); }
load();
renderAll();
renderAbout();
if(state.diag) renderDiagResult(); else renderSurvey();
```

---

## 6. 핵심 데이터 구조

### V (덕목 메타)
```js
const V = {
  h: { label:'겸손', en:'Humility', color, bg, dark, btn, badge, mods:[0,1,2] },
  g: { label:'갈망', en:'Hunger',   ..., mods:[3,4] },
  s: { label:'영리함', en:'Smarts', ..., mods:[5,6] }
};
```

### MODS (모듈 7개 — 모든 콘텐츠 인라인 보유)
```js
const MODS = [
  { n, title, v, emoji, desc,
    obj[],       // 학습 목표 ("~할 수 있다")
    cards[],     // 카드뉴스 {t, b}
    check[],     // 실천 체크리스트
    pq[],        // 연습 퀴즈 {q, opts[4], ans, exp}
    gq[] }       // 게이트 퀴즈 {q, opts[4], ans} (해설 미표시)
  // v: 'h' | 'g' | 's'
];
const GATE_PASS = 5; // 게이트 통과 기준 (5문항 정답)
```
> 퀴즈·카드뉴스가 모듈마다 `MODS[idx]`에 내장되어 있어 `openModule()` 시 자동 교체됩니다. (별도 PQ/GQ 전역 배열 없음)

### SURVEY / SCALE (자기진단 — 정본 18문항)
```js
const SURVEY = { h:[6문장], g:[6문장], s:[6문장] };  // 렌시오니 자기참조형 "나는 ~"
const SCALE  = [{v:3,t:'언제나'},{v:2,t:'때때로'},{v:1,t:'거의 아님'}];
// 덕목별 6문항 · 6~18점, 해석: 16↑ 강점 / 13~15 점검 / 6~12 개발
```

### TYPES (모델 소개 — 7유형)
```js
const TYPES = [
  { name, v:[h,g,s], feat, risk, lvl }
  // v: 덕목 보유 여부(0/1), lvl: 0 낮음 / 1 중간 / 3 가장위험(정치가) / 9 목표(이상적)
];
```

### RECERT (재인증)
```js
const RECERT = {
  7:  { title, sub, qs[3], practice[3] },
  21: { title, sub, qs[3], practice[3] }
  // qs[].ans === null 이면 자기보고 문항
};
```

### state (localStorage `tp2-state`)
```js
let state = { completed:{}, dates:{}, checks:{}, certifiedAt:null,
              recert:{}, recertDates:{}, diag:null, startDate:null,
              gateFails:{} };  // gateFails[모듈] = 미통과 누적 횟수
```

---

## 7. 주요 CSS 클래스 패턴

| 클래스 | 용도 |
|--------|------|
| `.screen.active` | 현재 표시 중인 탭 화면 |
| `#s-module.in` / `#s-about.in` | 모듈 상세·모델 소개 패널 슬라이드인 (visibility + translateX) |
| `.reveal` / `.reveal.show` | 모델 소개 스크롤 순차 노출 (IntersectionObserver) |
| `.sheet-overlay.show` (`gate-ovl`/`recert-ovl`) | 바텀시트 표시 |
| `.btn-ink` / `.btn-h` / `.btn-g` / `.btn-s` / `.btn-ghost` / `.btn-line` | 버튼 변형 |
| `.badge-h` / `.badge-g` / `.badge-s` / `.badge-ink` / `.badge-line` | 배지 |
| `.choice.selected` / `.choice.correct` / `.choice.wrong` | 퀴즈 선택지 상태 |
| `.cn-card` | 카드뉴스 캐러셀 카드 (덕목색 테두리 빛반사 `cardsheen`) |
| `.sv-opt.sel` | 자기진단 척도 선택 상태 |
| `.nav-item.on` | 활성 탭 |
| `.nudge` | "다음 문항" 화살표 넛지 / `.btn:disabled` 효과·그림자 제거 |

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
- 4개 탭 라우터 + 모듈 상세·모델 소개 슬라이드인 패널
- 홈: 히어로 카드(블러 orb 유기적 루프 + "모델 알아보기 →" 칩), 실시간 진행 현황, 다음 모듈 카드
- 모델 소개(`s-about`): 인트로 → 벤다이어그램(순차 등장+금색 반짝임) → 세 덕목 카드 → 7유형 → 다섯 함정 → CTA, 스크롤 순차 노출
- 모듈 목록: 7개 모듈, 덕목별 섹션, **순차 개방(잠금)** — 이전 모듈 완료 시 다음 모듈 해제
- 모듈 상세: 학습 목표, 3탭(학습/연습퀴즈/게이트), 아이콘 타일·덕목 칩 테두리 대비
- 카드뉴스 캐러셀 (모듈별 콘텐츠, 덕목색 테두리 빛반사 회전)
- 연습 퀴즈: 모듈별 캐러셀, 즉시 정오답+해설, 다시 풀기
- 게이트: 인트로카드+5문항 캐러셀, 단계별 버튼 활성(다음 문항/채점), 로딩 1.5초, **통과 시 폭죽**, 결과 바텀시트
- **게이트 미통과 → 멘토 알림**: `gateFails` 누적, 1·2·3회별 안내 메시지(3회 시 이메일 자동 발송)
- 자기진단: 정본 18문항(덕목별 6), 6~18점 막대 + 해석 밴드 + 개인 개발 초점(성찰 도구)
- 착근기 재인증: Day+7/+21 바텀시트, 3문항(자기보고 포함)+체크리스트
- localStorage 영속(`tp2-state`): 완료/날짜/체크/진단/재인증/실패횟수
- 디자인 모션: CTA 광택 스윕·그림자 호흡, 벤다이어그램·카드 빛반사, `prefers-reduced-motion` 대응

### 🟡 Medium Priority

| 항목 | 설명 |
|------|------|
| 멘토 알림 실제 발송 | 현재는 안내 메시지만. 3회 실패 시 이메일 단일 채널로 실제 발송 연동 (카카오워크 미사용) |
| 다크 모드 | `prefers-color-scheme: dark` 분기 |
| 모델 소개 출처 도해 | source/06_도해(벤다이어그램·7유형 매트릭스·다섯함정 연결도)와 시각 정합성 추가 점검 |

### 🟢 Low Priority (추가 기능)

| 항목 | 설명 |
|------|------|
| 온보딩 플로우 | 최초 진입 시 3단계 온보딩 (모델 소개 → 자기진단 유도 → 모듈 목록) |
| 성취 배지 시스템 | 덕목별/전체 완료 시 배지 발급 |
| 학습 스트릭 | 연속 학습일 표시 (홈 화면) |

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

- prototype.html 단일 파일 아키텍처 유지 (CSS, JS 분리 금지). `<!DOCTYPE html>` + 노치 safe-area-inset 적용
- `localStorage`(`tp2-state`)는 프로토타입 전용. 실제 서비스 전환 시 서버 API로 교체 예정
- max-width 모바일 기준 설계. 데스크탑 레이아웃 고려 불필요
- 멘토 알림은 이메일 단일 채널 가정(카카오워크 미사용), 현재는 안내 문구만 표시
- 유형명(7유형)은 자기인식·코칭용 — 동료를 꼬리표 붙이는 용도로 쓰지 않음

---

## 11. 세션 시작 명령 예시

### 멘토 알림 실제 발송 연동
```
팀플레이어학습도구/prototype.html 작업합니다.
CLAUDE_HANDOFF.md를 읽어 컨텍스트 파악 후 시작해주세요.

오늘 작업:
showGateSheet()의 3회 실패 분기에서 멘토 이메일 자동 발송이 실제로 트리거되도록 연동
(현재는 안내 문구만 표시. 채널은 이메일 단일)
```

### 다크 모드 추가
```
팀플레이어학습도구/prototype.html 작업합니다.
CLAUDE_HANDOFF.md 먼저 읽어주세요.

오늘 작업:
:root CSS 변수에 prefers-color-scheme: dark 분기를 추가해 다크 모드 지원
```

---

*최종 업데이트: 2026-06-12 · prototype.html 1421줄 / JS 48함수 기준 · 케이옥션 IT팀 서비스기획파트*
