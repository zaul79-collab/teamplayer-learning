# K-Auction 학습 플랫폼 디자인 시스템

> AI가 HTML 콘텐츠를 생성할 때 반드시 이 문서를 참조해 스타일 일관성을 유지한다.
> 출처: k-handbook.vercel.app 실제 CSS 추출 (2026-06-11)

---

## 1. CSS 변수 (Design Tokens)

아래 변수들은 사이트 전체에 `:root`에 정의되어 있다.  
AI가 HTML을 생성할 때 하드코딩된 색상값 대신 반드시 이 변수명을 사용한다.

```css
:root {
  /* 배경 */
  --bg:       #f5f6fa;   /* 페이지 전체 배경 */
  --sur:      #ffffff;   /* 카드·패널 표면 */
  --head-bg:  #dde1ea;   /* 헤더 영역 배경 */

  /* 텍스트 */
  --ink:      #1a1d27;   /* 본문 기본 텍스트 */
  --ink2:     #4b5160;   /* 보조 텍스트 */
  --ink3:     #9198a8;   /* 힌트·플레이스홀더 */

  /* 테두리 */
  --border:   #e2e5ec;   /* 기본 테두리 */

  /* 브랜드 컬러 */
  --gold:     #a4824f;   /* K-Auction 골드 (로고·강조) */

  /* 인터랙션 - 파랑 (Primary) */
  --pri:      #2563eb;
  --pri-bg:   #eff4ff;
  --pri-hv:   #1d4ed8;

  /* 상태 컬러 */
  --red:      #dc2626;   --red-bg:  #fef2f2;   /* 삭제·위험·안전규칙 강조 */
  --grn:      #16a34a;   --grn-bg:  #f0fdf4;   /* 성공·완료 */
  --amb:      #9a7320;   --amb-bg:  #fefce8;   /* 경고·주의 */
  --rule:     #a83f38;   --rule-bg: #faece9;   /* 안전규칙·필수 준수 */

  /* 모서리 */
  --r:        8px;    /* 기본 */
  --r-sm:     5px;    /* 소형 (버튼·태그) */
  --r-lg:     12px;   /* 카드·모달 */

  /* 그림자 */
  --sh-card:  0 1px 3px rgba(0,0,0,.06), 0 4px 16px -4px rgba(0,0,0,.10);
  --sh-panel: -4px 0 32px rgba(0,0,0,.10);
}
```

---

## 2. 타이포그래피

```css
/* 폰트 스택 */
font-family: Pretendard, "Apple SD Gothic Neo", "맑은 고딕", sans-serif;

/* 기본 */
body    { font-size: 14px; font-weight: 400; color: var(--ink); line-height: 1.6; }

/* 스케일 */
h1      { font-size: 22px; font-weight: 700; color: var(--ink); }
h2      { font-size: 18px; font-weight: 700; color: var(--ink); }
h3      { font-size: 15px; font-weight: 700; color: var(--ink); line-height: 24px; }
h4      { font-size: 13px; font-weight: 600; color: var(--ink2); }
small   { font-size: 12px; color: var(--ink3); }
```

---

## 3. 컴포넌트 클래스 패턴

사이트에서 실제 사용 중인 클래스명이다. HTML 생성 시 이 클래스들을 활용한다.

### 레이아웃
```
.shell          — 전체 레이아웃 래퍼 (사이드바 + 콘텐츠)
.lnb            — 좌측 내비게이션 바 (width: 220px, border-right: 1px solid var(--border))
.body           — 메인 콘텐츠 영역
.topbar         — 상단 바
.content        — 콘텐츠 패딩 영역
.panel          — 우측 슬라이드 패널
.panel-backdrop — 패널 오버레이
```

### 버튼
```
.btn            — 기본 버튼 베이스
.btn-ghost      — 테두리·배경 없는 버튼 (color: var(--ink2))
.btn-pri        — 주요 액션 (bg: var(--pri), color: white)
.btn-sm         — 소형 버튼 (font-size: 13px, padding: 7px 10px, radius: var(--r-sm))
.btn-red        — 삭제·위험 버튼 (color: var(--red))
.btn-icon       — 아이콘 전용 버튼
```

### 리스트/테이블 (어드민 board)
```
.board-toolbar  — 필터·검색 툴바 영역
.board-filters  — 필터 탭 그룹
.filter-pill    — 필터 탭 개별 아이템 (active 시 bg: var(--pri-bg), color: var(--pri))
.board-panel    — 테이블 감싸는 패널
.board-head     — 테이블 헤더 행
.board-row      — 테이블 데이터 행
.row-id         — 번호 셀 (color: var(--ink3))
.row-title      — 제목 셀 (font-weight: 600)
.row-cat        — 분류 태그 셀
.row-actions    — 수정·삭제 버튼 셀
.board-foot     — 테이블 하단 (총 n개 표시)
```

### 난이도 태그 (qd)
```
.qd             — 난이도 공통
.qd-상          — (color: var(--red))
.qd-중          — (color: var(--amb))
.qd-하          — (color: var(--grn))
```

### 핸드북 카드 (사용자 화면)
```
.card           — 학습 카드 (bg: var(--sur), border-radius: var(--r-lg), box-shadow: var(--sh-card))
.dot            — 안전규칙·확인필요 점 표시 (color: var(--rule))
```

### 사이드바 내비게이션
```
.lnb-brand      — 로고·브랜드명 영역
.lnb-group      — 네비 섹션 그룹
.lnb-label      — 섹션 레이블 (font-size: 11px, font-weight: 600, color: var(--ink3), uppercase)
.lnb-item       — 네비 아이템 (padding: 6px 12px, radius: var(--r-sm))
.lnb-item.on    — 활성 아이템 (bg: var(--pri-bg), color: var(--pri))
.lnb-sep        — 구분선
.lnb-foot       — 하단 고정 영역 (어드민 링크 등)
```

---

## 4. 카드 HTML 템플릿

AI가 모듈 콘텐츠·레퍼런스 핸드북 등을 HTML로 생성할 때 사용하는 기본 구조다.

```html
<!-- 학습 모듈 카드 -->
<article class="card" style="padding: 24px 28px; margin-bottom: 16px;">
  <header style="margin-bottom: 16px;">
    <span style="font-size: 11px; font-weight: 600; color: var(--ink3); text-transform: uppercase; letter-spacing: .06em;">M2 · 겸손</span>
    <h3 style="margin: 4px 0 0;">핵심 개념</h3>
  </header>
  <div style="color: var(--ink2); line-height: 1.75; font-size: 14px;">
    <!-- 본문 내용 -->
  </div>
</article>

<!-- 안전규칙·주의 블록 -->
<div style="background: var(--rule-bg); border-left: 3px solid var(--rule); border-radius: var(--r-sm); padding: 12px 16px; margin: 12px 0; font-size: 13px; color: var(--rule);">
  <!-- 규칙 내용 -->
</div>

<!-- 정보 블록 -->
<div style="background: var(--pri-bg); border-left: 3px solid var(--pri); border-radius: var(--r-sm); padding: 12px 16px; margin: 12px 0; font-size: 13px; color: var(--pri);">
  <!-- 정보 내용 -->
</div>

<!-- 태그/뱃지 -->
<span style="display: inline-block; font-size: 12px; font-weight: 600; padding: 3px 8px; border-radius: var(--r-sm); background: var(--pri-bg); color: var(--pri);">겸손</span>
<span style="display: inline-block; font-size: 12px; font-weight: 600; padding: 3px 8px; border-radius: var(--r-sm); background: var(--grn-bg); color: var(--grn);">완료</span>
<span style="display: inline-block; font-size: 12px; font-weight: 600; padding: 3px 8px; border-radius: var(--r-sm); background: var(--amb-bg); color: var(--amb);">진행 중</span>
```

---

## 5. AI HTML 생성 프롬프트 템플릿

AI에게 콘텐츠 HTML을 생성 요청할 때 아래 지시문을 반드시 포함한다.

```
[시스템 지시]
이 HTML은 k-auction 학습 플랫폼에 삽입됩니다.
아래 디자인 규칙을 반드시 따르세요.

- 폰트: Pretendard, "Apple SD Gothic Neo", "맑은 고딕", sans-serif
- 색상은 하드코딩 금지. 반드시 CSS 변수 사용:
    본문 텍스트: var(--ink) / 보조: var(--ink2) / 힌트: var(--ink3)
    배경: var(--bg) / 카드: var(--sur)
    강조(파랑): var(--pri), var(--pri-bg)
    경고: var(--rule), var(--rule-bg)
    성공: var(--grn), var(--grn-bg)
    주의: var(--amb), var(--amb-bg)
    브랜드: var(--gold)
- 모서리: var(--r) 기본 / var(--r-sm) 소형 / var(--r-lg) 카드
- 그림자: var(--sh-card) (카드에만)
- 기본 폰트 크기: 14px
- <style> 태그 사용 금지. 모든 스타일은 inline style 또는 위 CSS 변수로.
- DOCTYPE, <html>, <head>, <body> 태그 사용 금지 (삽입용 fragment만)
- 외부 라이브러리·CDN 사용 금지
```

---

## 6. 콘텐츠 유형별 색상 가이드

| 콘텐츠 유형 | 배경 | 텍스트 | 용도 |
|------------|------|--------|------|
| 핵심 개념·정보 | `--pri-bg` | `--pri` | 모듈 핵심 내용 강조 |
| 안전규칙·윤리 가드레일 | `--rule-bg` | `--rule` | 절대 준수 사항 |
| 주의·엣지케이스 | `--amb-bg` | `--amb` | 조심해야 할 내용 |
| 완료·성공 | `--grn-bg` | `--grn` | 통과·인증·긍정 피드백 |
| 일반 텍스트 | `--sur` | `--ink` | 본문 |
| 보조 설명 | `--bg` | `--ink2` | 부연·예시 |

---

## 7. 모션 & 인터랙션 이펙트

프로토타입(`prototype.html`)에 실제 구현된 애니메이션·인터랙션 효과다.
디자이너·개발자가 그대로 재현할 수 있도록 키프레임명과 핵심 값을 함께 정리한다.
**모든 모션은 `prefers-reduced-motion: reduce` 환경에서 정지**한다(접근성 항목 참조).

### 7.1 홈 히어로 블러 orb (유기적 루프 이동)
히어로 카드 안에 블러(`filter:blur(34px)`, `opacity:.5`) 처리된 원형 orb 3종이 서로 다른 주기로 떠다닌다.

```css
.hero .orb-1{animation:drift1 6s ease-in-out infinite}
.hero .orb-2{animation:drift2 8s ease-in-out infinite}
.hero .orb-3{animation:drift3 5s ease-in-out infinite}
/* 각 키프레임은 translate + scale 을 조합해 서로 다른 궤적으로 이동 */
@keyframes drift1{ 0%{transform:translate(0,0) scale(1)} 33%{transform:translate(-70px,55px) scale(1.25)}
  66%{transform:translate(50px,80px) scale(.82)} 100%{transform:translate(0,0) scale(1)} }
/* drift2(85px,-55px→-60px,-38px), drift3(-80px,-70px→65px,45px) 도 동일 패턴 */
```
- 주기를 6/8/5초로 어긋나게 두어 패턴이 반복되지 않는 유기적 움직임을 만든다.
- `will-change:transform` 으로 성능을 확보한다.

### 7.2 CTA 시선 유도
채워진 버튼(`.btn-ink` / `.btn-h` / `.btn-g` / `.btn-s`)에 광택 스윕과 그림자 호흡을 동시에 적용한다.

```css
/* 그림자 호흡 — 그림자가 커졌다 작아지며 살아있는 느낌 */
.btn-ink,.btn-h,.btn-g,.btn-s{animation:cta-breathe 3.6s ease-in-out infinite}
@keyframes cta-breathe{ 0%,100%{box-shadow:0 4px 14px -6px rgba(32,29,24,.28)}
  50%{box-shadow:0 6px 22px -6px rgba(32,29,24,.42)} }

/* 광택 스윕 — ::after 의 흰 빛 띠가 버튼 표면을 가로지름 */
.btn-ink::after{ content:'';position:absolute;top:0;left:-75%;width:45%;height:100%;
  background:linear-gradient(100deg,transparent,rgba(255,255,255,.38),transparent);
  transform:skewX(-18deg);animation:cta-shine 3.6s ease-in-out infinite; }
@keyframes cta-shine{ 0%{left:-75%} 22%{left:130%} 100%{left:130%} }
```

**"다음 문항" 화살표 넛지** — 클릭 대기 중인 화살표가 좌우로 살짝 움직여 시선을 끈다.
```css
.nudge{display:inline-block;animation:nudge 1.05s ease-in-out infinite}
@keyframes nudge{0%,100%{transform:translateX(0)}50%{transform:translateX(5px)}}
```

**비활성 버튼** — 흐리게 처리하고 모든 효과·그림자를 제거한다.
```css
.btn:disabled{opacity:.38;cursor:not-allowed;animation:none;box-shadow:none}
.btn:disabled::after{display:none}      /* 광택 스윕 제거 */
.btn:disabled .nudge{animation:none}    /* 넛지 제거 */
```

### 7.3 학습 캐러셀 카드 테두리 빛반사
`.cn-card::before` 의 `conic-gradient` 가 회전하면서 빛줄기가 카드 가장자리(테두리 링)만 돌며 스친다.
`@property --ba` 로 각도를 정식 애니메이션 가능한 커스텀 속성으로 등록하고, `mask-composite` 로 안쪽을 비워 테두리에만 그라데이션을 남긴다. 빛의 색은 덕목색 변수 `--vc` 로 카드마다 달라진다.

```css
@property --ba{syntax:'<angle>';initial-value:0deg;inherits:false}
.cn-card::before{
  content:'';position:absolute;inset:0;border-radius:inherit;padding:1.6px;pointer-events:none;
  background:conic-gradient(from var(--ba,0deg),
    transparent 0deg, transparent 72deg,
    rgba(255,255,255,.95) 92deg, var(--vc,#fff) 116deg,
    transparent 150deg, transparent 360deg);
  /* content-box 와 border-box 를 XOR/exclude → 테두리 링만 남김 */
  -webkit-mask:linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0);
  -webkit-mask-composite:xor; mask-composite:exclude;
  animation:cardsheen 4.5s linear infinite;
}
@keyframes cardsheen{to{--ba:360deg}}
```
- 카드 사용 예: `<div class="card cn-card" style="--vc:${덕목색}">`

### 7.4 게이트 통과 폭죽 (컨페티)
게이트 통과(합격) 시 `launchConfetti()` 가 캔버스를 동적으로 생성해 색종이를 떨어뜨린다.

- **앱 프레임 내로 제한**: `.app` 의 `getBoundingClientRect()` 로 캔버스 `left`·`width` 를 맞춰 모바일 프레임(최대 430px) 밖으로 넘치지 않게 한다.
- 조각 120개, 색상 7종(`#F59E0B`/`#2563EB`/`#16A34A`/`#EF4444`/`#EC4899`/`#8B5CF6`/`#fff`).
- `requestAnimationFrame` 으로 약 160프레임 동안 낙하·회전하며 90프레임 이후 페이드아웃.
- 호출: `if(pass) setTimeout(launchConfetti, 200);`

### 7.5 모델 소개 벤다이어그램
소개 화면(`#s-about`)이 슬라이드인되면 원이 순차로 등장하고, 중앙의 이상적 문구가 금색으로 반짝인다.

```css
/* 원 3개 순차 팝업 (겹치는 타임라인: 0 / .22 / .44s) */
#s-about.in .venn-c{animation:vennPop .62s cubic-bezier(.2,.85,.3,1) forwards}
#s-about.in .venn-c1{animation-delay:0s}
#s-about.in .venn-c2{animation-delay:.22s}
#s-about.in .venn-c3{animation-delay:.44s}
@keyframes vennPop{from{opacity:0;transform:scale(.55)}to{opacity:1;transform:scale(1)}}

/* 이상적 문구 — 등장(idealPop) 후 금색 반짝임 루프(idealShimmer) */
#s-about.in .venn-ideal{animation:idealPop .6s cubic-bezier(.2,.9,.3,1) .92s forwards,
  idealShimmer 2.4s ease-in-out 1.7s infinite}
@keyframes idealShimmer{0%,100%{filter:drop-shadow(0 0 0 rgba(245,158,11,0))}
  50%{filter:drop-shadow(0 0 7px rgba(245,158,11,.6))}}
```

**스크롤 순차 노출** — `.reveal` 요소가 `IntersectionObserver` 로 화면에 들어올 때 페이드·슬라이드업한다.
```css
#s-about .reveal{opacity:0;transform:translateY(18px);
  transition:opacity .5s ease, transform .55s cubic-bezier(.2,.8,.3,1)}
#s-about .reveal.show{opacity:1;transform:none}
```
```js
// root:#s-about, rootMargin:'0px 0px -12% 0px', threshold:.12
// 교차 시 .show 추가 후 unobserve (1회성). IntersectionObserver 미지원 시 즉시 .show.
```

### 7.6 모듈 상세 영역 대비
모션은 아니지만 시각적 깊이를 위해 테두리·그림자로 면을 분리한다.
- **아이콘 타일**: `border:1px solid rgba(32,29,24,.07)` + `box-shadow:0 6px 18px -8px rgba(32,29,24,.28)`.
- **덕목 칩(badge)**: 배경색에 맞춘 1px 테두리(예: `.badge-h{border:1px solid #F0DEB4}`)로 윤곽을 또렷하게.

### 7.7 접근성 & 문서 기반
- **모션 정지**: 위 모든 효과는 `@media (prefers-reduced-motion:reduce)` 에서 `animation:none`(필요 시 `opacity:1;transform:none`)으로 정지한다. 신규 모션을 추가할 때도 반드시 이 분기를 함께 작성한다.
- **문서 선언**: 프로토타입은 `<!DOCTYPE html>` 로 시작하는 완성 문서다(§5의 fragment 생성 규칙과 구분).
- **노치 대응**: 플로팅 바텀 내비는 노치/홈 인디케이터를 피하도록 `bottom:calc(14px + env(safe-area-inset-bottom))` 로 safe-area-inset 을 반영한다.

---

## 8. 변경 이력

| 버전 | 날짜 | 내용 |
|------|------|------|
| v1.0 | 2026-06-11 | 초기 작성 (k-handbook.vercel.app CSS 추출 기반) |
| v1.1 | 2026-06-12 | 모션·인터랙션 이펙트 섹션 추가 |
