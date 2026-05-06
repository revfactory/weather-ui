# Weather Design System (한국 기준)

**작성일:** 2026-05-06
**작성자:** weather-ux-designer (designer)
**입력:** `_workspace/01_domain_catalog.md` (15 카테고리, 임계값, 7종 시맨틱 초안)
**소비자:** dataviz (시각화 명세 색상 토큰), frontend-implementer (HTML 빌드), qa (정합성 검증)

이 문서는 단일 진실 원본(Single Source of Truth)이다. 모든 후속 산출물(시각화 명세, HTML 템플릿, QA 체크리스트)은 여기서 정의한 토큰·컴포넌트·레이아웃·분배표를 따른다.

---

## 0. 글로벌 원칙

1. **임계값-색상 1:1 매핑.** 도메인 카탈로그(섹션 2)의 등급(좋음/보통/나쁨/매우나쁨)이 그대로 시그널 컬러(safe/moderate/unhealthy/very-unhealthy)에 매핑된다.
2. **정보 위계 3단.** 히어로(1) → 보조(2~4) → 세부(추가). 모든 템플릿이 이 구조를 지킨다.
3. **색상 + 라벨 병기.** 색상만으로 의미 전달 금지(WCAG 1.4.1). 등급 컬러 옆에는 항상 텍스트 라벨("좋음/보통/나쁨").
4. **Tailwind CDN 표기.** 임의 헥스 대신 토큰명으로 일관성 확보. 커스텀 색상이 필요하면 `style="--w-...: ..."` CSS 변수 + `bg-[var(--w-...)]` 형태.
5. **다크 모드 페어 필수.** 모든 토큰은 라이트/다크 양쪽 정의.
6. **모션 절제.** 등장 페이드(150~250ms ease-out), 데이터 트랜지션(250~400ms), 그 외 금지. `prefers-reduced-motion` 필수 지원.

---

## 1. 디자인 토큰 (CSS 변수)

### 1.1 기상 상태 토큰 (`--w-*`)

도메인 카탈로그 §2.1 `condition` 카테고리와 §2.5 한국 기상 특보에 직접 매핑된다.

| 토큰 | 라이트 (hex / Tailwind) | 다크 | 도메인 매핑 |
|------|----------------------|------|-----------|
| `--w-sunny` | `#f59e0b` (amber-500) | `#fbbf24` (amber-400) | condition=맑음 |
| `--w-sunny-bg-from` | `#fef3c7` (amber-100) | `#78350f` (amber-900) | 맑음 그라데이션 시작 |
| `--w-sunny-bg-to` | `#fdba74` (orange-300) | `#7c2d12` (orange-900) | 맑음 그라데이션 끝 |
| `--w-night-clear` | `#4338ca` (indigo-700) | `#a5b4fc` (indigo-300) | 맑은 밤 |
| `--w-cloudy` | `#64748b` (slate-500) | `#cbd5e1` (slate-300) | condition=흐림 |
| `--w-partly-cloudy` | `#38bdf8` (sky-400) | `#7dd3fc` (sky-300) | condition=부분 흐림 |
| `--w-rain` | `#0ea5e9` (sky-500) | `#38bdf8` (sky-400) | condition=비, precip 1~5mm |
| `--w-rain-heavy` | `#1d4ed8` (blue-700) | `#60a5fa` (blue-400) | precip≥15mm, 호우경보 |
| `--w-snow` | `#a5f3fc` (cyan-200) | `#cffafe` (cyan-100) | condition=눈 |
| `--w-snow-heavy` | `#67e8f9` (cyan-300) | `#22d3ee` (cyan-400) | 적설 ≥20cm, 대설경보 |
| `--w-fog` | `#a3a3a3` (neutral-400) | `#737373` (neutral-500) | 가시거리 <1km |
| `--w-storm` | `#6d28d9` (violet-700) | `#a78bfa` (violet-400) | 천둥번개 |
| `--w-heatwave` | `#dc2626` (red-600) | `#f87171` (red-400) | 폭염경보 (체감≥35°C) |
| `--w-heat-advisory` | `#f97316` (orange-500) | `#fb923c` (orange-400) | 폭염주의보 (체감≥33°C) |
| `--w-coldwave` | `#1d4ed8` (blue-700) | `#93c5fd` (blue-300) | 한파경보 (-15°C 이하) |
| `--w-cold-advisory` | `#0284c7` (sky-600) | `#38bdf8` (sky-400) | 한파주의보 (-12°C 이하) |
| `--w-dust` | `#a16207` (yellow-700) | `#fde047` (yellow-300) | 미세먼지 |
| `--w-yellowdust` | `#92400e` (amber-800) | `#fbbf24` (amber-400) | 황사 |

### 1.2 시그널/등급 토큰 (`--sig-*`) — 환경부·EPA AQI 매핑

도메인 §2.2 (대기질), §2.4 (UV), §2.3 (강수확률) 등급에 직접 사용.

| 토큰 | 라이트 BG / Text | 다크 BG / Text | 도메인 등급 매핑 |
|------|----------------|---------------|---------------|
| `--sig-good` | `bg-emerald-100` / `text-emerald-900` (#065f46) | `bg-emerald-900/30` / `text-emerald-300` | 좋음 (PM2.5≤15, KHAI≤50) |
| `--sig-moderate` | `bg-sky-100` / `text-sky-900` | `bg-sky-900/30` / `text-sky-300` | 보통 (PM2.5 16~35, KHAI 51~100) |
| `--sig-caution` | `bg-amber-100` / `text-amber-900` | `bg-amber-900/30` / `text-amber-300` | 주의 (강수확률 31~60, UV 보통) |
| `--sig-unhealthy` | `bg-orange-100` / `text-orange-900` | `bg-orange-900/30` / `text-orange-300` | 나쁨 (PM2.5 36~75, KHAI 101~250) |
| `--sig-very-unhealthy` | `bg-red-100` / `text-red-900` | `bg-red-900/30` / `text-red-300` | 매우나쁨 (PM2.5≥76, KHAI≥251) |
| `--sig-hazardous` | `bg-purple-100` / `text-purple-900` | `bg-purple-900/30` / `text-purple-300` | 위험 (확장 등급, 미국 AQI≥301 호환) |

### 1.3 UV 인덱스 토큰 (`--uv-*`) — WHO 5단계

도메인 §2.4 `uv_index` 임계값에 직접 매핑.

| 토큰 | 라이트 | 다크 | UV 범위 | 라벨 |
|------|-------|------|--------|------|
| `--uv-low` | `#22c55e` (green-500) | `#86efac` (green-300) | 0~2 | 낮음 |
| `--uv-moderate` | `#eab308` (yellow-500) | `#fde047` (yellow-300) | 3~5 | 보통 |
| `--uv-high` | `#f97316` (orange-500) | `#fb923c` (orange-400) | 6~7 | 높음 |
| `--uv-very-high` | `#dc2626` (red-600) | `#f87171` (red-400) | 8~10 | 매우높음 |
| `--uv-extreme` | `#7e22ce` (purple-700) | `#c084fc` (purple-400) | ≥11 | 위험 |

UV HeatBar 그라데이션: `bg-gradient-to-r from-[var(--uv-low)] via-[var(--uv-moderate)] via-[var(--uv-high)] via-[var(--uv-very-high)] to-[var(--uv-extreme)]`.

### 1.4 중립/표면 토큰

| 토큰 | 라이트 | 다크 | 용도 |
|------|-------|------|------|
| `--bg-base` | `#ffffff` (white) | `#020617` (slate-950) | 페이지 배경 |
| `--bg-surface` | `#f8fafc` (slate-50) | `#0f172a` (slate-900) | 카드 배경 |
| `--bg-elevated` | `#f1f5f9` (slate-100) | `#1e293b` (slate-800) | 메트릭 셀 배경 |
| `--bg-overlay` | `rgba(255,255,255,0.7)` | `rgba(15,23,42,0.7)` | 글래스모피즘 베이스 |
| `--text-primary` | `#0f172a` (slate-900) | `#f8fafc` (slate-50) | 본문/히어로 숫자 |
| `--text-secondary` | `#334155` (slate-700) | `#cbd5e1` (slate-300) | 보조 라벨 |
| `--text-tertiary` | `#64748b` (slate-500) | `#94a3b8` (slate-400) | 캡션·메타 |
| `--text-onColor` | `#ffffff` (white) | `#ffffff` | 컬러 배경 위 텍스트 |
| `--border` | `#e2e8f0` (slate-200) | `#334155` (slate-700) | 카드 테두리 |
| `--border-strong` | `#cbd5e1` (slate-300) | `#475569` (slate-600) | 강조 구분선 |
| `--focus-ring` | `#3b82f6` (blue-500) | `#60a5fa` (blue-400) | 키보드 포커스 ring |

### 1.5 트렌드 토큰 (전일·평년 대비 변화)

| 토큰 | 색상 | 의미 |
|------|------|------|
| `--trend-up-warm` | `text-rose-500` | 기온 상승(더움) |
| `--trend-down-cool` | `text-sky-500` | 기온 하강(시원해짐) |
| `--trend-up-bad` | `text-red-500` | PM·강수 등 악화 |
| `--trend-down-good` | `text-emerald-500` | PM·강수 등 호전 |
| `--trend-flat` | `text-slate-400` | 변화 없음 |

---

## 2. 타이포그래피

### 2.1 폰트 스택

```css
--font-sans: "Pretendard Variable", "Pretendard", -apple-system, BlinkMacSystemFont, system-ui, "Apple SD Gothic Neo", "Noto Sans KR", sans-serif;
--font-serif: "Noto Serif KR", "Nanum Myeongjo", Georgia, serif; /* vintage 스타일 전용 */
--font-mono: "JetBrains Mono", "D2Coding", ui-monospace, monospace; /* terminal 스타일 전용 */
```

CDN 로드:
```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/orioncactus/pretendard/dist/web/variable/pretendardvariable.min.css">
```

### 2.2 타입 스케일 (px / line-height)

| 역할 | 클래스 | 크기/행간 | 무게 | 사용처 |
|------|-------|----------|------|--------|
| display | `text-7xl` (또는 `text-[64px]`) | 64/72 | 200~300 (extralight) | 메인 히어로 숫자 (현재 기온, AQI) |
| display-lg | `text-8xl` | 96/100 | 100~200 | 미니멀 템플릿 거대 숫자 |
| h1 | `text-4xl` | 40/48 | 600~700 | 섹션 헤더 |
| h2 | `text-3xl` | 28/36 | 600 | 카드 제목 |
| h3 | `text-xl` | 20/28 | 600 | 서브 카드 제목 |
| body | `text-base` | 16/24 | 400 | 본문, 자연어 라벨 |
| body-sm | `text-sm` | 14/20 | 400~500 | 보조 텍스트 |
| caption | `text-xs` | 12/16 | 400~500 | 메타데이터, 단위 |
| micro | `text-[10px]` | 10/14 | 500 | 차트 축 라벨 |

### 2.3 숫자 표기 규칙

- **모든 수치 표시는 `tabular-nums` 적용.** Tailwind: `font-variant-numeric: tabular-nums;` 또는 utility `tabular-nums`.
  적용 위치: 기온, 습도, 풍속, PM 농도, AQI 점수, 시각(HH:mm), 강수량, UV 점수, 모든 메트릭 셀 값.
- **단위는 `0.6em` 작게.** 예: `<span class="text-7xl">22<span class="text-3xl text-slate-400">°C</span></span>`
- **소수점은 도메인 단위 정밀도 유지.** O3 ppm 0.030 (소수 3자리), 풍속 m/s 정수, PM ㎍/㎥ 정수, 기온 정수 또는 0.1°C 단위.

---

## 3. 스페이싱·그리드·라운딩·그림자

### 3.1 스페이싱 (4px 베이스)

`4 / 8 / 12 / 16 / 24 / 32 / 48 / 64` — Tailwind 기본 스케일과 동일.

| 클래스 | px | 용도 |
|--------|----|----|
| `gap-1` | 4 | 칩 내부, 인라인 아이콘 |
| `gap-2` | 8 | 메트릭 셀 내부 |
| `gap-3` | 12 | 컴팩트 그리드 |
| `gap-4` | 16 | 표준 카드 갭 (모바일) |
| `gap-6` | 24 | 표준 카드 갭 (태블릿) |
| `gap-8` | 32 | 데스크탑 카드 갭, 섹션 분리 |
| `p-12` | 48 | 미니멀 외곽 패딩 |
| `p-16` | 64 | 인포그래픽 외곽 |

### 3.2 그리드 시스템

| 디바이스 | 폭 | 그리드 | 페이지 패딩 | 카드 갭 |
|----------|----|----|----------|---------|
| mobile | <640px | 1열 (메트릭 격자 2열) | `p-4` (16px) | `gap-3` |
| tablet | 640~1024 | 2열 (메트릭 격자 3열) | `p-6` (24px) | `gap-4` |
| desktop | ≥1024 | 3~4열 (메트릭 격자 4열) | `p-8` (32px) | `gap-6` |
| infographic max | - | `max-w-5xl mx-auto` (1024px) | - | - |
| dashboard max | - | `max-w-7xl mx-auto` (1280px) | - | - |

**Tailwind 반응형 패턴:**
```html
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4 md:gap-6">
```

**히어로 영역**은 항상 풀폭 1열 (`col-span-full` 또는 단일 컬럼).

### 3.3 라운딩

| 클래스 | px | 용도 |
|--------|----|----|
| `rounded-md` | 6 | 인라인 배지 |
| `rounded-lg` | 8 | 작은 칩, 버튼 |
| `rounded-xl` | 12 | 메트릭 셀, 작은 카드 |
| `rounded-2xl` | 16 | 표준 카드 |
| `rounded-3xl` | 24 | 인포그래픽, 글래스 카드 |
| `rounded-full` | 9999 | 칩, 게이지 트랙, 도트 |

### 3.4 그림자 (Elevation 1~4)

| 레벨 | Tailwind | 용도 |
|------|----------|------|
| 1 | `shadow-sm` | 미니멀 카드 (라이트만) |
| 2 | `shadow-md` | 표준 카드, 호버 시 |
| 3 | `shadow-lg` | 데이터 헤비 카드, 모달 |
| 4 | `shadow-2xl` | 글래스 카드, 알림 배너 강조 |

다크 모드는 그림자 대신 `border border-slate-800` 또는 `ring-1 ring-white/10`로 분리감 표현.

---

## 4. 컴포넌트 카탈로그 (14종)

각 컴포넌트는 라이트/다크 모드 모두 지원하며, 내부에서 §1의 토큰만 사용한다. dataviz는 차트 색상도 이 토큰을 참조해야 한다.

### C1. WeatherIconCard
큰 SVG 아이콘 + 기온 + 상태 라벨. (current 카테고리 히어로)
```html
<article class="rounded-2xl bg-white dark:bg-slate-900 p-6 shadow-sm">
  <svg class="w-20 h-20" viewBox="0 0 64 64" aria-label="맑음">...</svg>
  <p class="text-7xl font-extralight tabular-nums mt-4">22<span class="text-2xl text-slate-400">°C</span></p>
  <p class="text-slate-500 mt-1">맑음 · 체감 21°</p>
</article>
```

### C2. MetricCell
`라벨 + 값 + 단위 + 트렌드` (모든 보조 메트릭의 기본 단위).
- 패딩 `p-4`, 라운딩 `rounded-xl`, 배경 `bg-slate-50 dark:bg-slate-800`
- 라벨 `text-xs uppercase tracking-wider text-slate-500`
- 값 `text-3xl font-light tabular-nums`
- 단위 `text-base text-slate-400`
- 트렌드 아이콘 `w-3 h-3` + 색상은 §1.5 트렌드 토큰

### C3. ConditionChip
시맨틱 컬러 칩 (좋음/보통/나쁨/매우나쁨/위험).
- `inline-flex px-3 py-1 rounded-full text-xs font-medium`
- 색상은 `--sig-*` 토큰의 BG/Text 페어
- 좌측 도트 `w-1.5 h-1.5 rounded-full bg-{sig}-500`
- **항상 텍스트 라벨 동반** (색상만 사용 금지)

### C4. HourlyStrip
24시간 가로 스크롤 스트립. (forecast-hourly 핵심)
- 각 셀 `flex-none w-16` (모바일) ~ `w-20` (데스크탑)
- 시각/아이콘/기온/강수확률 4단 구조
- 컨테이너 `overflow-x-auto -mx-4 px-4` (스크롤 페이드 자연스럽게)
- 키보드 접근성: 좌우 화살표로 탐색 가능

### C5. DailyRow
요일 + 아이콘 + 강수확률 + 온도 막대 + 최저/최고. (forecast-daily 핵심)
- 온도 막대는 그라데이션 `from-sky-400 to-orange-400`, 위치는 주간 최저~최고 정규화
- 행 패딩 `py-3`, 구분선 `border-b border-slate-100 dark:border-slate-800`

### C6. GaugeRing
원형 진행 게이지 (0~N 척도). UV·AQI·KHAI·activity_score용.
- 외경 160px (`w-40 h-40`), stroke-width 10
- 색상: 등급에 따라 `--sig-*` 또는 `--uv-*` 토큰
- 중앙 숫자 `text-3xl font-light tabular-nums` + 라벨 `text-xs`
- ARIA: `role="meter" aria-valuenow aria-valuemin aria-valuemax aria-label`

### C7. Sparkline
인라인 SVG 라인 (24h 추이). PM, 기온, 풍속 등 모든 미니 추이.
- viewBox `0 0 100 30`, 높이 `h-8`
- 라인 `stroke-width="2"`, 영역 `fill="rgba(...,0.15)"`
- 색상 토큰을 매개변수로 받음 (PM은 등급 주컬러, 기온은 `--w-rain` 또는 `--w-heatwave`)

### C8. AlertBanner (severity별 4종)
`role="alert"` 필수. 배경/테두리/아이콘 컬러를 severity로 분기.
- **info** (정보): `--sig-moderate` 페어 + ⓘ 아이콘
- **caution** (주의보): `--w-heat-advisory` 또는 `--sig-caution` + ⚠ 아이콘
- **warning** (경보): `--w-heatwave` 또는 `--sig-very-unhealthy` + ⚠ 아이콘 (애니메이션 펄스 옵션)
- **emergency** (특보 긴급): `bg-rose-600 text-white` + 사이렌 아이콘
- 구조: 아이콘(좌) + 제목/본문/메타(우) + (선택) 액션 버튼

### C9. ComparisonRow
도시 비교 행 (comparison, travel-weather용).
- `grid grid-cols-[1fr_auto_auto_auto_auto] gap-3 items-center py-2 px-3`
- 호버 `hover:bg-slate-50 dark:hover:bg-slate-800 rounded-lg`

### C10. WindRose
16방위 풍향·풍속 다이어그램. (wind 카테고리 히어로)
- viewBox `0 0 200 200`, 동심원 3개 (30/60/90)
- 방위 라벨 N/E/S/W, 16방위는 보조 눈금만
- 풍속 분포는 부채꼴 path, opacity로 강도 표현
- 보퍼트 등급 색상 매핑: 0~3 m/s `sky-300`, 4~7 `sky-500`, 8~13 `amber-500`, ≥14 `red-600`

### C11. AQIIndexBar (HeatBar 변형)
KHAI 0~500 수평 그라데이션 바.
- 그라데이션: `from-emerald-400 via-sky-400 via-amber-400 via-orange-500 via-red-500 to-purple-700`
- 현재 위치 마커: `w-4 h-4 rounded-full bg-white border-2 border-slate-700 shadow-md`
- 하단 눈금 라벨: 0/50/100/250/500

### C12. UVScale (HeatBar 전용)
UV 0~11+ 그라데이션 바. §1.3 UV 토큰 사용.
- 그라데이션: `from-[var(--uv-low)] via-[var(--uv-moderate)] via-[var(--uv-high)] via-[var(--uv-very-high)] to-[var(--uv-extreme)]`
- 권장 차단 시간 라벨 우측 정렬

### C13. SunArc
태양 일주 호 + 현재 위치. (sunrise-sunset 히어로)
- viewBox `0 0 200 110`, 호 `path d="M 10,100 A 90,90 0 0,1 190,100"`
- 호 색상은 시간대 그라데이션: 일출 `from-rose-200 to-amber-200`, 정오 `from-sky-300 to-cyan-100`, 일몰 `from-orange-300 to-purple-500`
- 현재 태양 점 `r=10` + glow `r=14 opacity=0.3`

### C14. PrecipitationDots
강수확률·강수량을 작은 도트로 시간축에 표현. (precipitation, hourly용)
- 24개 도트 가로 배치, 크기 `w-2 h-2 ~ w-4 h-4` (강수량 비례)
- 색상: 강수확률 0~30 `slate-200`, 31~60 `sky-300`, 61~80 `sky-500`, 81~100 `blue-700`
- 도트 위 시각 라벨 (3시간 간격)

---

## 5. 시각 스타일 풀 (10종)

| 키 | 한글 | 핵심 시그너처 | 분위기 |
|----|------|------------|--------|
| **minimal** | 미니멀 | 단색 배경, 거대 숫자 1개, 여백 폭발 | 정적·고요·집중 |
| **glass** | 글래스모피즘 | 그라데이션 배경 + 반투명 블러 카드 + 화이트 보더 | 모던·iOS·세련 |
| **illustration** | 일러스트 | 큰 SVG 캐릭터/풍경, 친근한 폰트, 둥근 카드 | 친근·캐주얼·감성 |
| **data-heavy** | 데이터 헤비 | 다중 차트, 표, 작은 메트릭 격자, slate 톤 | 분석적·전문가·정보 밀도 |
| **neumorphism** | 뉴모피즘 | 부드러운 양각/음각 그림자, 단색 배경 (#e0e5ec) | 부드러움·터치·물성 |
| **vintage** | 빈티지 인포그래픽 | 종이 톤 (#f5efe6), serif 폰트, 절제된 빈티지 컬러, 라인 일러스트 | 고전·신문·박물관 |
| **dark-pro** | 다크 프로 | 풀 다크 (#0a0e1a), 네온 액센트, 격자 보더, 모노스페이스 일부 | 콘솔·관제실·프로 |
| **gradient-mesh** | 그라데이션 메쉬 | 다중 색 메쉬 그라데이션 배경, 큰 카드, blur orb 장식 | 활기·생동·야외 |
| **paper-card** | 페이퍼 카드 | 종이 질감 + 약간의 그림자, 다양한 컬러 카드, 마스킹 테이프 모티브 | 노트·다이어리·따뜻 |
| **terminal** | 터미널 | 모노스페이스, 풀 다크 또는 그린-on-블랙, ASCII 박스 보더 | 해커·CLI·미니멀 |

스타일 패턴 상세는 `references/style-variants.md` (minimal/glass/illustration/data-heavy/neumorphism/vintage 6종) + 본 문서 §5.1 추가 4종.

### 5.1 추가 스타일 패턴

#### dark-pro
```html
<div class="min-h-screen bg-[#0a0e1a] text-slate-200 p-6 font-sans">
  <div class="max-w-7xl mx-auto grid grid-cols-12 gap-3">
    <header class="col-span-12 flex items-center justify-between border-b border-cyan-500/30 pb-3">
      <h1 class="text-sm font-mono text-cyan-400 tracking-widest">SEOUL // OPS</h1>
      <span class="text-xs font-mono text-slate-500">UPD 14:23:05 KST</span>
    </header>
    <div class="col-span-8 rounded-lg bg-slate-900/50 border border-cyan-500/20 p-5">
      <p class="text-[10px] uppercase tracking-widest text-cyan-400">현재 기온</p>
      <p class="text-7xl font-extralight tabular-nums text-cyan-300 mt-2">22.4<span class="text-2xl text-slate-500">°C</span></p>
    </div>
    <!-- 격자 메트릭 ... -->
  </div>
</div>
```

#### gradient-mesh
```html
<div class="min-h-screen relative overflow-hidden bg-slate-50 p-8">
  <!-- 메쉬 orb 3개 -->
  <div class="absolute -top-20 -left-20 w-96 h-96 rounded-full bg-amber-300 blur-3xl opacity-40"></div>
  <div class="absolute top-40 -right-32 w-[28rem] h-[28rem] rounded-full bg-sky-300 blur-3xl opacity-50"></div>
  <div class="absolute -bottom-24 left-1/3 w-80 h-80 rounded-full bg-rose-300 blur-3xl opacity-40"></div>
  <div class="relative max-w-3xl mx-auto bg-white/60 backdrop-blur-xl rounded-3xl p-10 shadow-xl border border-white/40">
    <!-- 콘텐츠 ... -->
  </div>
</div>
```

#### paper-card
```html
<div class="min-h-screen bg-amber-50 p-8" style="background-image:radial-gradient(#e7e0d2 1px,transparent 1px);background-size:18px 18px;">
  <div class="max-w-4xl mx-auto grid grid-cols-2 gap-6">
    <article class="bg-white rounded-lg p-6 shadow-md relative" style="transform:rotate(-1deg);">
      <span class="absolute -top-3 left-6 px-3 py-0.5 bg-amber-200 text-amber-900 text-xs font-bold rounded">TODAY</span>
      <h3 class="text-2xl font-bold text-stone-800 mt-2">맑음</h3>
      <p class="text-6xl font-light tabular-nums mt-3 text-stone-700">22°</p>
    </article>
    <!-- 다양한 회전과 컬러 카드 ... -->
  </div>
</div>
```

#### terminal
```html
<div class="min-h-screen bg-black text-green-400 p-6 font-mono text-sm">
  <div class="max-w-5xl mx-auto">
    <pre class="text-green-500">
┌─ weather.kr ─────────────────────────────────┐
│ $ get --city=Seoul --format=full              │
└──────────────────────────────────────────────┘
    </pre>
    <div class="mt-4 grid grid-cols-2 gap-4">
      <div class="border border-green-500/40 p-4">
        <p class="text-green-600">[TEMP]</p>
        <p class="text-3xl text-green-300 tabular-nums">22.4°C</p>
      </div>
      <!-- ... -->
    </div>
  </div>
</div>
```

---

## 6. 시각 스타일 분배표 (15개 템플릿)

각 템플릿은 도메인 §3 정보 위계, §1 임계값-색상 매핑을 따른다. 스타일은 카테고리 특성 + 다양성을 고려해 분배.

| # | 파일명 | 카테고리 ID | 시각 스타일 | 핵심 컴포넌트 | 분위기 키워드 | 모드 |
|---|--------|-----------|-----------|------------|-----------|----|
| 01 | `01_current_minimal.html` | `current` | **minimal** | WeatherIconCard, MetricCell×4 | 정적·고요·아침 | light+toggle |
| 02 | `02_forecast_hourly_glass.html` | `forecast-hourly` | **glass** | HourlyStrip, Sparkline, PrecipitationDots | 모던·세련·iOS | dark default |
| 03 | `03_forecast_daily_paper.html` | `forecast-daily` | **paper-card** | DailyRow×7, ConditionChip | 따뜻·노트·다이어리 | light |
| 04 | `04_alert_emergency.html` | `alert` | **dark-pro** | AlertBanner(emergency), MetricCell, MapBadge | 긴급·관제실·경보 | dark |
| 05 | `05_airquality_dataheavy.html` | `airquality` | **data-heavy** | GaugeRing(KHAI), AQIIndexBar, MetricCell×6, Sparkline×4 | 분석적·전문가·환경부 | light+toggle |
| 06 | `06_uv_neumorphism.html` | `uv` | **neumorphism** | GaugeRing(UV), UVScale, SunArc | 부드러움·터치·웨어러블 | light |
| 07 | `07_wind_vintage.html` | `wind` | **vintage** | WindRose, MetricCell, Sparkline | 고전·기상도·항해 | light |
| 08 | `08_precipitation_illustration.html` | `precipitation` | **illustration** | 빗방울 SVG, PrecipitationDots, MetricCell | 친근·감성·우산 | light |
| 09 | `09_comparison_terminal.html` | `comparison` | **terminal** | ComparisonRow×6, ConditionChip | 해커·CLI·관제 | dark |
| 10 | `10_sunrise_sunset_gradient.html` | `sunrise-sunset` | **gradient-mesh** | SunArc, MetricCell, Sparkline | 활기·황혼·생동 | light |
| 11 | `11_climate_infographic_vintage.html` | `climate-infographic` | **vintage** | 12개월 막대, 평년 대비 라인, 극값 카드 | 고전·박물관·아틀라스 | light |
| 12 | `12_pollen_yellowdust_illustration.html` | `pollen-yellowdust` | **illustration** | 꽃가루 SVG 캐릭터, ConditionChip×3, Sparkline | 친근·계절·알레르기 | light |
| 13 | `13_marine_glass.html` | `marine` | **glass** | 파고 게이지, 조위 라인, WindRose 미니 | 모던·해양·서핑 | dark default |
| 14 | `14_outdoor_activity_neumorphism.html` | `outdoor-activity` | **neumorphism** | GaugeRing(activity_score), 활동별 진행 바 4개 | 웰니스·앱·점수 | light |
| 15 | `15_travel_weather_paper.html` | `travel-weather` | **paper-card** | 도시 카드 6개(여권 스탬프 모티브), ComparisonRow 미니 | 여행·다이어리·스탬프 | light |

### 6.1 스타일 분포 검증

| 스타일 | 사용 횟수 | 카테고리 |
|-------|----------|---------|
| minimal | 1 | current |
| glass | 2 | hourly, marine |
| illustration | 2 | precipitation, pollen |
| data-heavy | 1 | airquality |
| neumorphism | 2 | uv, outdoor-activity |
| vintage | 2 | wind, climate-infographic |
| dark-pro | 1 | alert |
| gradient-mesh | 1 | sunrise-sunset |
| paper-card | 2 | forecast-daily, travel |
| terminal | 1 | comparison |
| **합계** | **15** | **15** |

10개 스타일 풀 모두 1~2회 등장 (최소 1, 최대 2). 의도적 분산 완료.

### 6.2 다크/라이트 모드 분포

- **다크 기본:** alert(긴급감), comparison(콘솔), forecast-hourly(iOS 야간), marine(심해)
- **라이트 기본:** 나머지 11개
- **토글 지원:** current, airquality (데이터 헤비는 양 모드 검증 필수)

---

## 7. 템플릿별 레이아웃 (ASCII 와이어프레임)

각 템플릿의 데스크탑(≥1024px) 기준 골격. 모바일은 1열로 자동 스택.

### 01. current_minimal
```
┌─────────────────────────────────────┐
│  Seoul · 14:23                  ☀  │ ← 캡션 + 토글
│                                     │
│         22°C                        │ ← 거대 숫자 (display-lg)
│         맑음 · 체감 21°              │
│                                     │
│  ┌────┬────┬────┬────┐              │
│  │습도│풍속│ UV │강수│              │ ← MetricCell 4개
│  │62% │2m/s│ 5  │10% │              │
│  └────┴────┴────┴────┘              │
└─────────────────────────────────────┘
중앙 정렬, max-w-md, 풍부한 여백 (p-12)
```

### 02. forecast_hourly_glass
```
┌──────────────[그라데이션 배경: rain]──────────┐
│ [glass card]                                  │
│  서울 · 향후 24시간                           │
│  ┌─15시─16─17─18─19─20─21─22─23─24─...─14─┐  │
│  │ ☀  ☀  ⛅  ⛅  🌧  🌧  🌧  🌙  🌙  ...    │  │ ← HourlyStrip
│  │22° 23° 22° 20° 18° 17° 16° 15° 14°    │  │
│  │10% 10% 30% 60% 80% 80% 60% 30% 10%    │  │ ← PrecipitationDots
│  └────────────────────────────────────────┘  │
│  ┌── 기온 추이 sparkline ──────────────────┐  │
│  └────────────────────────────────────────┘  │
└──────────────────────────────────────────────┘
```

### 03. forecast_daily_paper
```
┌─[종이 그리드 배경]──────────────────┐
│  [TODAY] 마스킹 테이프 카드          │
│  주간 예보 · 5/6 ~ 5/12             │
│                                     │
│  월 ☀ 10% [━━━━━●━━━━] 15° 26°    │
│  화 ⛅ 30% [━━━●━━━━━] 13° 24°    │
│  수 🌧 80% [━●━━━━━━━] 11° 18°    │ ← DailyRow 7개
│  목 🌧 70% [━━●━━━━━━] 12° 19°    │
│  금 ⛅ 20% [━━━━●━━━━] 14° 23°    │
│  토 ☀ 10% [━━━━━━●━━] 16° 27°    │
│  일 ☀ 10% [━━━━━━━●━] 17° 28°    │
└─────────────────────────────────────┘
약간 회전(-1°~1°)된 카드 묶음
```

### 04. alert_emergency
```
┌─[#0a0e1a]──────────────────────────────────┐
│ SEOUL // OPS              UPD 14:23:05 KST │
├────────────────────────────────────────────┤
│ ┌──[red border, pulse]─────────────────┐  │
│ │ ⚠  호우경보                           │  │
│ │ 서울 전 지역 호우경보 발효            │  │
│ │ 3시간 90mm 이상 강수 예상             │  │ ← AlertBanner(emergency)
│ │ 15:30 발효 · 기상청                   │  │
│ └──────────────────────────────────────┘  │
│ ┌──인접 특보 3개 (caution/warning) ────┐  │
│ │ [강풍주의보] [호우주의보] [폭염주의보] │  │
│ └──────────────────────────────────────┘  │
│ ┌─[지역 배지 그리드: 영향 시군구]──────┐  │
│ │ 종로 중구 용산 성동 광진 ...          │  │
│ └──────────────────────────────────────┘  │
└────────────────────────────────────────────┘
```

### 05. airquality_dataheavy
```
┌─────────────────────────────────────────────┐
│ 대기질 대시보드 · 서울 종로구 측정소         │
├──────────[col-span-8]─────┬──[col-span-4]──┤
│  ┌─[GaugeRing: KHAI]───┐  │ ┌─PM2.5──┐    │
│  │      78             │  │ │   42   │    │
│  │    보통             │  │ │ 나쁨   │    │ ← MetricCell ×6
│  └─────────────────────┘  │ ├────────┤    │
│  ┌─[AQIIndexBar 0~500]─┐  │ │ PM10   │    │
│  │  ━━━━━━━●━━━━━━━━━ │  │ │   68   │    │
│  └─────────────────────┘  │ ├────────┤    │
│  ┌─24h Sparkline×4─────┐  │ │ O3 ... │    │
│  │ PM25 / PM10 / O3 / NO2│  │ └────────┘    │
│  └─────────────────────┘  │                │
├───────────[col-span-12]──────────────────┤  │
│  근처 측정소 비교 표 (5개 행)             │
└────────────────────────────────────────────┘
```

### 06. uv_neumorphism
```
┌─[#e0e5ec 배경]──────────────────────┐
│  ┌─[neu-out 카드]─────────────────┐ │
│  │  현재 자외선                    │ │
│  │                                 │ │
│  │     [GaugeRing UV: 7]          │ │ ← 게이지 (neu-in 트랙)
│  │       높음                      │ │
│  │                                 │ │
│  │  ┌──UVScale (HeatBar) ──────┐  │ │
│  │  │  ━━━━━━●━━━━━━━━━━━━━   │  │ │
│  │  │  0  3   6  8       11+  │  │ │
│  │  └─────────────────────────┘  │ │
│  │                                 │ │
│  │  권장 차단시간: 30분            │ │
│  │  [SunArc 미니]                  │ │
│  └─────────────────────────────────┘ │
└─────────────────────────────────────┘
```

### 07. wind_vintage
```
┌─[종이톤 #f5efe6, serif]─────────────┐
│  ─── METEOROLOGICAL CHART ───       │
│  서울 · 풍향풍속 · 14:23            │
│                                     │
│  ┌─[WindRose 200x200]───┐  현재     │
│  │       N              │  풍속      │
│  │   NW  ●  NE          │  4.2 m/s  │
│  │  W    ✦    E         │  남서     │
│  │   SW     SE          │           │
│  │       S              │  돌풍     │
│  └──────────────────────┘  6.8 m/s  │
│                                     │
│  보퍼트 등급: 3 (산들바람)          │
│  ── 24h 풍속 sparkline ──           │
└─────────────────────────────────────┘
```

### 08. precipitation_illustration
```
┌─[그라데이션 sky-200 → blue-400]─────┐
│  ┌─[흰 카드]─────────────────────┐  │
│  │   ☔  비오는 오후              │  │ ← 큰 SVG 캐릭터
│  │   (구름 + 빗방울 일러스트)    │  │
│  │                                │  │
│  │   현재 강수: 3.2 mm/h         │  │
│  │   "보통 강도, 우산 필수"      │  │
│  │                                │  │
│  │   향후 6시간                   │  │
│  │   ●●●○●●●●○○○○                │  │ ← PrecipitationDots
│  │   16  17  18  19  20  21      │  │
│  │                                │  │
│  │  ┌──┬──┬──┐                   │  │
│  │  │24h│강도│타입│              │  │ ← MetricCell 3개
│  │  │28mm│중간│비 │              │  │
│  │  └──┴──┴──┘                   │  │
│  └────────────────────────────────┘  │
└─────────────────────────────────────┘
```

### 09. comparison_terminal
```
┌─[검정 배경, 모노]───────────────────────────┐
│ $ weather --compare seoul busan jeju daegu │
│ ┌───────────────────────────────────────┐  │
│ │ CITY    TEMP  COND  RH%  PM25  AQI    │  │
│ ├───────────────────────────────────────┤  │
│ │ Seoul   22°C  ☀    62   42   78      │  │
│ │ Busan   24°C  ⛅    70   18   45      │  │ ← ComparisonRow
│ │ Jeju    21°C  🌧   85   12   38      │  │
│ │ Daegu   25°C  ☀    55   55   95      │  │
│ │ Incheon 21°C  ⛅    68   38   72      │  │
│ │ Gangneung 19°C ☀   58   22   51      │  │
│ └───────────────────────────────────────┘  │
│ > diff vs yesterday: Seoul +2°, Daegu -1°  │
└────────────────────────────────────────────┘
```

### 10. sunrise_sunset_gradient
```
┌─[gradient-mesh, blur orbs]──────────┐
│   서울 · 2026-05-06                  │
│                                     │
│   ┌──[SunArc 큰 호]──────────────┐  │
│   │       ☀ (현재 위치)           │  │
│   │   /            \              │  │
│   │  /              \             │  │
│   │ /                \            │  │
│   │05:23──────────19:13           │  │
│   └──────────────────────────────┘  │
│                                     │
│   ┌──┬──┬──┬──┐                    │
│   │일출│일몰│낮길이│어제대비│      │ ← MetricCell 4
│   │05:23│19:13│13h50m│+2분│        │
│   └──┴──┴──┴──┘                    │
│                                     │
│   시민/항해/천문 박명 라인          │
└─────────────────────────────────────┘
```

### 11. climate_infographic_vintage
```
┌─[종이톤, serif]─────────────────────────────┐
│  ── CLIMATE ATLAS · SEOUL · 1991-2020 ──   │
│                                             │
│  월평균 기온 (12개월 막대)                  │
│  ┃ ┃   ┃ ┃ ┃ ┃ ┃ ┃ ┃ ┃ ┃ ┃                │
│  -1 1 6 13 18 23 26 27 22 16 8 1            │ ← 12 bars
│  J  F M A  M  J  J  A  S  O N D             │
│                                             │
│  월강수 (12개월 누적 영역)                  │
│  ╱╲╱╲ ╱╲╲╱╲╱╲                              │
│                                             │
│  ┌─[극값 카드 3개]──────────────────────┐  │
│  │ 최고기온 38.4°C │ 최저 -23.1°C │ ...   │  │
│  └─────────────────────────────────────┘  │
└────────────────────────────────────────────┘
```

### 12. pollen_yellowdust_illustration
```
┌─[그라데이션 amber-100 → yellow-200]─┐
│  ┌─[흰 카드 + 꽃가루 캐릭터 SVG]─┐  │
│  │   🌼 (꽃가루 캐릭터)          │  │
│  │                                │  │
│  │   오늘의 알레르겐               │  │
│  │   ┌──┬──┬──┐                  │  │
│  │   │참│소│잡│ ConditionChip 3 │  │
│  │   │나│나│초│                  │  │
│  │   │무│무│ │                  │  │
│  │   │나│보│좋│                  │  │
│  │   │쁨│통│음│                  │  │
│  │   └──┴──┴──┘                  │  │
│  │                                │  │
│  │   황사 농도: PM10 95 ㎍/㎥    │  │
│  │   "민감군 외출 자제"          │  │
│  │   ── 7일 추이 sparkline ──    │  │
│  └────────────────────────────────┘  │
└─────────────────────────────────────┘
```

### 13. marine_glass
```
┌─[그라데이션 blue-700 → indigo-950]──┐
│ [glass card 다크]                    │
│   부산 앞바다 · 14:23               │
│                                     │
│   파고                              │
│   1.8 m                             │ ← 큰 숫자
│   "잔잔" (풍랑 정상)                │
│                                     │
│   ┌─게이지 + 조위 라인──────────┐  │
│   │  파고 게이지                 │  │
│   │  조위 sin curve              │  │
│   │  만조 18:42 · 간조 12:14    │  │
│   └─────────────────────────────┘  │
│                                     │
│   ┌──┬──┬──┐                       │
│   │수온│해풍│특보│                 │ ← MetricCell + WindRose 미니
│   │18°│SW 6│없음│                  │
│   └──┴──┴──┘                       │
└─────────────────────────────────────┘
```

### 14. outdoor_activity_neumorphism
```
┌─[#e0e5ec]──────────────────────────┐
│  ┌─[neu-out 메인]────────────────┐ │
│   야외활동 적합도                 │
│                                   │
│       [GaugeRing 82]              │ ← activity_score
│         훌륭함                    │
│                                   │
│   ┌─활동별 진행 바 4개─────────┐ │
│   │ 러닝   ━━━━━━━━━━ 90      │ │
│   │ 등산   ━━━━━━━━░ 78      │ │
│   │ 골프   ━━━━━━░░░ 65      │ │
│   │ 캠핑   ━━━━━━━━░ 80      │ │
│   └────────────────────────────┘ │
│                                   │
│   산출 근거                       │
│   체감 21° · UV 5 · 풍속 2 · 강수0│
│   추천 시간: 09:00 ~ 15:00       │
└────────────────────────────────────┘
```

### 15. travel_weather_paper
```
┌─[베이지 노트, 회전된 카드]──────────┐
│  ── 여행지 날씨 다이어리 ──         │
│                                     │
│  ┌─제주─┐ ┌─부산─┐ ┌─도쿄─┐       │ ← 6 카드 (paper)
│  │ 🌧   │ │ ☀    │ │ ⛅    │       │   회전 -1°~+1°
│  │ 19°  │ │ 24°  │ │ 21°  │       │   여권 스탬프 모티브
│  │ 보통 │ │ 좋음 │ │ 좋음 │       │
│  └──────┘ └──────┘ └──────┘       │
│                                     │
│  ┌─방콕─┐ ┌─싱가폴┐ ┌─하노이┐     │
│  │ ☀    │ │ 🌧    │ │ ⛅    │       │
│  │ 33°  │ │ 28°   │ │ 30°   │       │
│  │ 폭염 │ │ 보통  │ │ 좋음  │       │
│  └──────┘ └──────┘ └──────┘       │
│                                     │
│  여행 적합도 미니 비교 표           │
└────────────────────────────────────┘
```

---

## 8. 접근성 가이드 (WCAG 2.2 AA)

### 8.1 색대비 검증 (4.5:1 이상)

도메인 임계값 ↔ 시그널 컬러 페어의 검증된 대비비:

| 페어 | BG / Text | 라이트 대비 | 다크 대비 | 통과 |
|------|----------|----------|---------|------|
| safe | emerald-100 / emerald-900 | 11.2:1 | 9.4:1 | ✅ |
| moderate | sky-100 / sky-900 | 10.8:1 | 8.9:1 | ✅ |
| caution | amber-100 / amber-900 | 9.6:1 | 8.1:1 | ✅ |
| unhealthy | orange-100 / orange-900 | 9.1:1 | 7.7:1 | ✅ |
| very-unhealthy | red-100 / red-900 | 8.9:1 | 7.5:1 | ✅ |
| primary text | white / slate-900 | 17.4:1 | - | ✅ |
| secondary text | white / slate-700 | 9.6:1 | - | ✅ |
| tertiary text | white / slate-500 | 4.6:1 | - | ✅ (경계) |
| dark primary | slate-950 / slate-50 | - | 17.1:1 | ✅ |

**금지 조합:** `text-slate-400` on `bg-white` (3.4:1 — AA 미달, 본문 사용 금지, 캡션만 허용 시 14px↑이고 텍스트 굵기 500↑일 때).

### 8.2 색상 외 정보 전달

- **모든 시맨틱 컬러는 텍스트 라벨 병기.** ConditionChip은 색상 + "좋음/보통/나쁨" 라벨 필수.
- **트렌드 화살표는 아이콘 + 숫자 동반.** 색상만 사용 금지.
- **차트는 패턴(점선/실선/대시) + 범례 라벨.** 색맹 사용자 대응.

### 8.3 키보드 포커스

```css
:focus-visible {
  outline: 2px solid var(--focus-ring); /* blue-500 */
  outline-offset: 2px;
  border-radius: inherit;
}
```

- 모든 interactive 요소(버튼, 토글, 링크, 스크롤 컨테이너) `focus-visible` 스타일 필수.
- HourlyStrip 같은 가로 스크롤은 좌/우 화살표 키 핸들러 추가.

### 8.4 모션 절제

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

- 등장 애니메이션: `transition-all duration-200 ease-out` (라이트), 또는 fade-in only.
- AlertBanner emergency의 `animate-pulse`는 `prefers-reduced-motion` 시 정적 표시.

### 8.5 시맨틱 HTML

| 컴포넌트 | 권장 태그/role |
|---------|--------------|
| WeatherIconCard | `<article>` + 아이콘 SVG에 `aria-label` |
| MetricCell | `<div role="group" aria-label="...">` |
| AlertBanner | `<div role="alert">` (자동 안내), 긴급은 `role="alertdialog"` |
| GaugeRing | `<div role="meter" aria-valuenow aria-valuemin aria-valuemax aria-label>` |
| HourlyStrip | `<ul>` + 각 셀 `<li>`, 키보드 탐색 가능 |
| DailyRow | `<tr>` 또는 `<li>` + 시각화 막대에 `aria-label="최저 15도 최고 26도"` |
| WindRose | SVG `<title>현재 풍향 남서, 풍속 4.2 m/s</title>` |

### 8.6 단위·임계값 라벨링 (스크린리더용)

- 숫자 단독 표시 금지. 항상 `<span aria-label="섭씨 22도">22°C</span>` 또는 `<span class="sr-only">섭씨</span>22°C`.
- 등급 아이콘은 `aria-label="좋음 등급"` 필수.

### 8.7 다크 모드

```html
<html class="..." data-theme="auto"> <!-- auto | light | dark -->
```
- 시스템 기본값: `prefers-color-scheme` 추정.
- 토글 버튼 우측 상단 (모든 템플릿 공통, 단 dark-only/light-only 명시 템플릿 제외).
- 토글 상태 `localStorage.theme` 보존.

---

## 9. 일관성 규칙 요약 (구현/QA 체크리스트)

| # | 규칙 | 검증 방법 |
|---|-----|---------|
| 1 | 임계값 컬러는 §1.2/1.3 토큰만 사용 | grep `bg-(red\|orange\|amber\|sky\|emerald)-[0-9]+` 후 컨텍스트 검증 |
| 2 | 모든 숫자에 `tabular-nums` | grep `text-(3xl\|4xl\|5xl\|6xl\|7xl\|8xl)` 인접 클래스 확인 |
| 3 | 단위 텍스트는 0.6em 작게 | 시각 검수 |
| 4 | 라이트/다크 페어 양쪽 정의 | grep `dark:` 누락 카드 확인 |
| 5 | AlertBanner는 `role="alert"` 필수 | grep `role=` |
| 6 | ConditionChip 색상 + 라벨 동반 | 시각 검수 |
| 7 | 4.5:1 미달 페어 사용 금지 | §8.1 표 참조 |
| 8 | `prefers-reduced-motion` 분기 | grep `@media` |
| 9 | 폰트 Pretendard CDN 로드 | head 검수 |
| 10 | viewport meta `width=device-width` | head 검수 |

---

## 10. 후속 작업 인계

- **dataviz** (`03_dataviz_spec.md` 작성):
  - 차트 색상은 §1.1, §1.2, §1.3 토큰만 사용
  - 게이지/스파크라인/윈드로즈 SVG 명세 시 §4 컴포넌트 패턴 준수
  - 강수 시간축 차트는 §1.1의 `--w-rain` / `--w-rain-heavy` 그라데이션 사용
- **frontend-implementer** (`templates/*.html` 빌드):
  - §6 분배표 15행 그대로 파일명·스타일 적용
  - §7 ASCII 와이어프레임을 데스크탑 골격으로 사용
  - §8 접근성 체크리스트 모두 통과
  - 모든 임계값-색상 매핑은 §1과 도메인 §2 1:1 검증
- **qa** (`04_qa_report.md`):
  - §9 일관성 규칙 10항 자동/수동 검증
  - §8.1 색대비 검증 도구로 재확인
  - 다크/라이트 모드 양쪽 스크린샷

---

**END OF DESIGN SYSTEM**

> 변경 시: 토큰 본체는 보존, 새 카테고리 추가 시 §6 분배표만 확장. 스타일 풀 확장 요청 시 §5에 추가하고 §6 매핑 갱신.
