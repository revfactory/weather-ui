# Weather Dataviz Spec (시각화 명세)

**작성일:** 2026-05-06
**작성자:** weather-dataviz-specialist (dataviz)
**입력:** `_workspace/01_domain_catalog.md` (15 카테고리 / 임계값) + `_workspace/02_design_system.md` (디자인 토큰 + 컴포넌트 14종 + 스타일 분배표 15행)
**소비자:** `frontend-implementer` (HTML 빌드), `qa` (정합성 검증)
**산출 페어:** `_workspace/03_dataviz_samples.json` (더미 데이터 카탈로그)

이 문서는 도메인 임계값 → 디자인 토큰 → 시각 컴포넌트 → 더미 데이터 4단계 파이프라인의 "시각 컴포넌트" 단을 결정한다. 모든 차트 색상은 디자인 시스템 §1.1 / §1.2 / §1.3 토큰만 사용한다.

---

## 0. 글로벌 시각화 원칙

1. **데이터-잉크 비율 우선.** 장식 픽셀 < 데이터 픽셀. 게이지의 배경 트랙·축 라벨·범례 외 장식 금지.
2. **단일 차트 자체 완결.** 제목 + 단위 + 범례 + 값 라벨이 차트 SVG 내부에 모두 존재.
3. **CDN 우선, vanilla SVG 차순위.** 게이지·윈드로즈·일러스트·스파크라인은 vanilla SVG. 라인/바/히트맵/누적영역은 ApexCharts 또는 Chart.js CDN.
4. **색상 + 텍스트 라벨 병기 (WCAG 1.4.1).** 색상 단독 의미 전달 금지. 등급 칩, 게이지, 막대 모두 텍스트 라벨 동반.
5. **반응형은 viewBox 기반.** SVG는 `viewBox + preserveAspectRatio="xMidYMid meet"` + `width=100% height=100%`. 라이브러리 차트는 ResizeObserver로 redraw.
6. **모션 절제.** 진입 애니메이션 ≤ 600ms, 데이터 트랜지션 ≤ 400ms. `prefers-reduced-motion: reduce` 시 모든 transition을 0.01ms로 단축.
7. **숫자 표기.** 차트 내 모든 수치 라벨은 `font-variant-numeric: tabular-nums`. 단위는 0.6em.

---

## 1. 카테고리별 시각화 매핑 (15 카테고리)

각 카테고리의 1순위 시각화 + 보조 차트 + 구현 기술을 명시. "데이터 → 시각 매핑 규칙" 컬럼은 도메인 임계값을 디자인 토큰으로 어떻게 변환하는지 정의.

| # | 카테고리 ID | 권장 1순위 시각화 | 보조 시각화 | 구현 기술 | 주 컴포넌트 | 데이터 → 시각 매핑 규칙 |
|---|------------|---------------|-----------|---------|-----------|-------------------|
| 01 | `current` | 거대 숫자 + 컨디션 아이콘 (`WeatherIconCard`) | `MetricCell`×4 + 미니 `Sparkline` | vanilla SVG (아이콘) | C1, C2, C7 | `temp` 색: 18~26→`--w-sunny`, ≥33→`--w-heat-advisory`, ≥35→`--w-heatwave`, ≤4→`--w-coldwave`. `condition` 키 → `--w-{key}` 토큰 직접 매핑. |
| 02 | `forecast-hourly` | 24h 가로 스트립 (`HourlyStrip`) + 영역 라인 차트 | `PrecipitationDots` + `Sparkline`(풍속) | ApexCharts (line+area), vanilla SVG (dots) | C4, C7, C14 | 기온 라인: stroke `--w-rain` (낮 시간) / `--w-night-clear` (야간). 영역 fill 0.15 opacity. PrecipitationDots: 확률 0~30→`slate-200`, 31~60→`sky-300`, 61~80→`sky-500`, 81+→`blue-700`. |
| 03 | `forecast-daily` | 7일 행 리스트 (`DailyRow`) + 온도 막대 | `ConditionChip` per row | vanilla HTML+CSS (막대 그라데이션) | C5, C3 | 일별 막대 좌표: 주간 min~max를 0~100% 정규화. 막대 그라데이션 `from-sky-400 to-orange-400`. precip_prob ≥60% 시 행 좌측에 빗방울 아이콘. |
| 04 | `alert` | 큰 배너 (`AlertBanner`) + 펄스 애니메이션 | 인접 특보 칩 그리드, 영향 지역 배지 | vanilla HTML+CSS, SVG 아이콘 | C8 | severity `emergency`→`bg-rose-600 text-white` + `animate-pulse`. `warning`→`--w-heatwave` 또는 `--sig-very-unhealthy`. `caution`→`--sig-caution`. `info`→`--sig-moderate`. |
| 05 | `airquality` | KHAI 게이지 링 (`GaugeRing`) + 수평 그라데이션 바 (`AQIIndexBar`) | `MetricCell`×6 + `Sparkline`×4 | vanilla SVG (게이지/바) + ApexCharts (스파크라인) | C6, C11, C2, C7 | 게이지 각도 = (khai/500)*360°. 색: 0~50→`--sig-good`, 51~100→`--sig-moderate`, 101~250→`--sig-unhealthy`, 251~500→`--sig-very-unhealthy`. PM2.5/PM10 등급 칩 동시 표시. |
| 06 | `uv` | UV 게이지 링 + UV 그라데이션 바 (`UVScale`) | `SunArc` 미니, 권장 차단시간 라벨 | vanilla SVG | C6, C12, C13 | UV 0~11+ → `--uv-low`(0~2)/`--uv-moderate`(3~5)/`--uv-high`(6~7)/`--uv-very-high`(8~10)/`--uv-extreme`(11+). 게이지 각도 = (uv/12)*360°. UVScale 마커 위치 = (uv/12)*100%. |
| 07 | `wind` | 윈드로즈 (`WindRose`, 16방위) | 24h 풍속 `Sparkline`, 보퍼트 등급 라벨 | vanilla SVG (polar coords) | C10, C2, C7 | 16방위 부채꼴 path. 각 섹터 fill opacity = freq/max_freq. 보퍼트 등급 stroke: 0~3 m/s `sky-300`, 4~7 `sky-500`, 8~13 `amber-500`, ≥14 `red-600`. |
| 08 | `precipitation` | 누적 영역 차트 (24h precip_24h) + `PrecipitationDots` | 빗방울 SVG 일러스트, `MetricCell`×3 | ApexCharts (area) + vanilla SVG (dots/일러스트) | C14, C2 | 영역 그라데이션 `--w-rain`→`--w-rain-heavy` (precip 1~5mm→light, ≥15mm→heavy). type=눈 시 `--w-snow` 페어로 자동 전환. |
| 09 | `comparison` | 정렬 가능 비교 표 (`ComparisonRow`×6~7) | 도시별 `ConditionChip` + 미니 막대 | vanilla HTML+CSS | C9, C3 | 컬럼: city/temp/cond/RH/PM25/AQI. 행 정렬은 클라이언트 JS sort. PM 컬럼 셀 BG = 등급 `--sig-*` /20 opacity. |
| 10 | `sunrise-sunset` | 태양 호 차트 (`SunArc`) + 현재 위치 마커 | `MetricCell`×4 (일출/일몰/낮길이/어제대비) | vanilla SVG | C13, C2 | 호 path: `M 10,100 A 90,90 0 0,1 190,100`. 태양 위치 = `current_progress_percent / 100` 호 비율. 일출~정오 그라데이션 `from-rose-200 to-amber-200`, 정오~일몰 `from-orange-300 to-purple-500`. |
| 11 | `climate-infographic` | 12개월 막대 (월평균 기온) + 12개월 누적 영역 (월강수) | 극값 카드 3개 (`MetricCell` 강조) | vanilla SVG (막대) + ApexCharts (영역) | C2 변형, C7 | 막대 색: 월평균 `temp_avg`을 한국 기온 분포에 매핑. ≤0°C `--w-coldwave`, 1~10 `--w-cold-advisory`, 11~25 `--w-sunny`, ≥26 `--w-heatwave`. 평년 대비 편차 점선 overlay. |
| 12 | `pollen-yellowdust` | 꽃가루 캐릭터 SVG 일러스트 + `ConditionChip`×3 (참나무/소나무/잡초) | `Sparkline` 7일 추이, PM10 게이지 미니 | vanilla SVG (일러스트) | C3, C7 | 꽃가루 등급 `<50/50~199/200~499/≥500`(참나무 기준)→ `--sig-good/moderate/unhealthy/very-unhealthy`. 황사 PM10 ≥300 → `--w-yellowdust` 배경 톤. |
| 13 | `marine` | 파고 게이지 + 조위 sin 곡선 | `WindRose` 미니, 수온 `MetricCell` | vanilla SVG (게이지/곡선) | C6, C2, C10 미니 | 파고 게이지: 0~5m. <1→`--sig-good`, 1~3→`--sig-caution`, ≥3→`--sig-very-unhealthy`(풍랑주의보). 조위 곡선은 12h 기간을 sin파로 시각화, 만조/간조 마커. |
| 14 | `outdoor-activity` | activity_score 게이지 (`GaugeRing` 큰 사이즈) | 활동별 진행 바 4개 (러닝/등산/골프/캠핑) | vanilla SVG (게이지) + vanilla HTML (진행 바) | C6, custom progress | 게이지 각도 = (score/100)*360°. ≥80→`--sig-good`, 60~79→`--sig-moderate`, 40~59→`--sig-caution`, <40→`--sig-very-unhealthy`. 진행 바도 동일 임계. |
| 15 | `travel-weather` | 도시 카드 그리드 (6 카드) + 여권 스탬프 모티브 | 적합도 미니 비교 표 | vanilla HTML+CSS + vanilla SVG (스탬프) | C9 미니, C1 미니 | 카드 회전 `transform: rotate(-1deg ~ +1deg)` 무작위. 도시별 `condition` → `--w-{key}` 카드 액센트 컬러. |

### 1.1 컴포넌트 코드 매핑

각 카테고리가 사용하는 디자인 시스템 §4 컴포넌트(C1~C14)를 한눈에:

| 컴포넌트 | 사용 카테고리 | 횟수 |
|---------|------------|----|
| C1 WeatherIconCard | 01 | 1 |
| C2 MetricCell | 01, 05, 07, 08, 10, 13 | 6 |
| C3 ConditionChip | 03, 09, 12, 15 | 4 |
| C4 HourlyStrip | 02 | 1 |
| C5 DailyRow | 03 | 1 |
| C6 GaugeRing | 05, 06, 13, 14 | 4 |
| C7 Sparkline | 02, 05, 07, 11, 12 | 5 |
| C8 AlertBanner | 04 | 1 |
| C9 ComparisonRow | 09, 15 | 2 |
| C10 WindRose | 07, 13(미니) | 2 |
| C11 AQIIndexBar | 05 | 1 |
| C12 UVScale | 06 | 1 |
| C13 SunArc | 06(미니), 10 | 2 |
| C14 PrecipitationDots | 02, 08 | 2 |

모든 컴포넌트가 1회 이상 등장 → 디자인 시스템 활용도 100%.

---

## 2. 라이브러리 선택 매트릭스

| 시각화 | 권장 도구 | CDN URL | 사용 카테고리 | 사유 |
|-------|----------|---------|------------|------|
| 게이지 링 | vanilla SVG | - | 05, 06, 13, 14 | stroke-dasharray 한 줄로 표현 가능. 라이브러리 오버킬. |
| 윈드로즈 | vanilla SVG | - | 07 | 16방위 path 정적 좌표 계산. 라이브러리 polar 차트는 라벨 자유도 부족. |
| 스파크라인 | vanilla SVG | - | 02, 05, 07, 11, 12 | 24~30 데이터 포인트, polyline 한 줄. |
| 일러스트 (구름·태양·꽃가루·빗방울) | vanilla SVG | - | 01, 08, 10, 12 | 정적 일러스트. 직접 그리기 + 조건부 fill. |
| HourlyStrip | vanilla HTML+CSS | - | 02 | 가로 flex + overflow-x-auto. SVG 불필요. |
| DailyRow | vanilla HTML+CSS | - | 03 | grid + 그라데이션 막대. |
| ComparisonRow / 표 | vanilla HTML+CSS | - | 09, 15 | 정렬·hover만 JS. 라이브러리 불요. |
| 라인 차트 (영역) | ApexCharts | `https://cdn.jsdelivr.net/npm/apexcharts` | 02 (기온 영역), 08 (강수 누적 영역) | tooltip + 부드러운 곡선 + 그라데이션 fill 기본 지원. |
| 막대 차트 (12개월) | vanilla SVG (단순) 또는 Chart.js | `https://cdn.jsdelivr.net/npm/chart.js` | 11 | 12개 막대는 SVG로 충분. 평년 대비 overlay만 필요하면 Chart.js. **선택: vanilla SVG (단순성).** |
| 누적 영역 (월강수) | ApexCharts | 위와 동일 | 11 | tooltip 필요. |
| 알림 배너 펄스 | vanilla CSS (`@keyframes pulse`) | - | 04 | Tailwind `animate-pulse` utility. |

**결론:** 15개 템플릿 중 ApexCharts가 필요한 것은 **2~3개** (02, 08, 11). 나머지 12~13개는 **순수 vanilla SVG + HTML+CSS**로 단일 HTML 파일 자체 완결 가능.

### 2.1 ApexCharts 옵션 표준

```js
// 카테고리 02: 기온 영역 차트
const opts = {
  chart: { type: 'area', height: 200, sparkline: { enabled: false }, toolbar: { show: false }, fontFamily: '"Pretendard Variable", sans-serif', animations: { enabled: true, speed: 400 } },
  series: [{ name: '기온', data: hourly_24h.map(h => h.temp) }],
  xaxis: { categories: hourly_24h.map(h => h.hour + '시'), labels: { style: { colors: 'var(--text-tertiary)', fontSize: '11px' } }, axisBorder: { show: false }, axisTicks: { show: false } },
  yaxis: { labels: { formatter: v => v.toFixed(0) + '°', style: { colors: 'var(--text-tertiary)', fontSize: '11px' } } },
  stroke: { curve: 'smooth', width: 2, colors: ['var(--w-rain)'] },
  fill: { type: 'gradient', gradient: { shadeIntensity: 1, opacityFrom: 0.4, opacityTo: 0.05, stops: [0, 100], colorStops: [{ offset: 0, color: 'var(--w-rain)', opacity: 0.4 }, { offset: 100, color: 'var(--w-rain)', opacity: 0 }] } },
  dataLabels: { enabled: false },
  grid: { borderColor: 'var(--border)', strokeDashArray: 3, padding: { left: 10, right: 10 } },
  tooltip: { theme: 'light', y: { formatter: v => v.toFixed(1) + '°C' } }
};
new ApexCharts(document.querySelector('#hourly_temp'), opts).render();
```

```js
// 카테고리 08: 강수 누적 영역
const opts = {
  chart: { type: 'area', height: 220, toolbar: { show: false }, fontFamily: '"Pretendard Variable", sans-serif' },
  series: [{ name: '강수량(mm)', data: precip_hourly_6h.map(p => p.precip) }],
  xaxis: { categories: precip_hourly_6h.map(p => p.hour + '시') },
  stroke: { curve: 'smooth', width: 2, colors: ['var(--w-rain-heavy)'] },
  fill: { type: 'gradient', gradient: { colorStops: [{ offset: 0, color: 'var(--w-rain-heavy)', opacity: 0.6 }, { offset: 100, color: 'var(--w-rain)', opacity: 0.05 }] } },
  dataLabels: { enabled: false },
  yaxis: { title: { text: 'mm/h', style: { color: 'var(--text-tertiary)', fontSize: '10px' } } }
};
```

---

## 3. SVG 컴포넌트 상세 명세

각 SVG 컴포넌트의 viewBox·좌표·계산식·ARIA를 정의한다. frontend-implementer는 이 명세를 그대로 코드로 옮길 수 있다.

### 3.1 GaugeRing (반원 / 풀원 게이지)

**용도:** UV·KHAI·activity_score·파고 등 0~N 척도 단일 값.

**SVG 구조:**

```html
<svg viewBox="0 0 120 120" class="w-40 h-40" role="meter"
     aria-valuenow="78" aria-valuemin="0" aria-valuemax="500" aria-label="KHAI 78 보통">
  <!-- 배경 트랙 -->
  <circle cx="60" cy="60" r="50" fill="none"
          stroke="var(--bg-elevated)" stroke-width="10"/>
  <!-- 값 호 -->
  <circle cx="60" cy="60" r="50" fill="none"
          stroke="var(--sig-moderate)" stroke-width="10"
          stroke-linecap="round"
          stroke-dasharray="49.0 314.16"
          transform="rotate(-90 60 60)"
          style="transition: stroke-dasharray 400ms ease-out;"/>
  <!-- 중앙 텍스트는 SVG 외부 div absolute로 오버레이 (또는 <text> 사용) -->
  <text x="60" y="65" text-anchor="middle"
        font-size="28" font-weight="300"
        fill="var(--text-primary)"
        style="font-variant-numeric: tabular-nums;">78</text>
  <text x="60" y="82" text-anchor="middle"
        font-size="10" fill="var(--text-tertiary)">보통</text>
</svg>
```

**계산식:**
- 원주 = `2π × r = 2 × 3.14159 × 50 ≈ 314.16`
- dasharray 채움 = `(value / max) × 314.16`
- 풀원: `transform="rotate(-90 60 60)"` (12시 시작), 시계방향
- 반원(180°만 사용): r=50 + path `d="M10,60 A50,50 0 0,1 110,60"` + dasharray `(value/max) × 157.08`

**색상 분기 함수 (JS):**

```js
function gaugeColorByKHAI(v) {
  if (v <= 50) return 'var(--sig-good)';
  if (v <= 100) return 'var(--sig-moderate)';
  if (v <= 250) return 'var(--sig-unhealthy)';
  return 'var(--sig-very-unhealthy)';
}
function gaugeColorByUV(v) {
  if (v <= 2) return 'var(--uv-low)';
  if (v <= 5) return 'var(--uv-moderate)';
  if (v <= 7) return 'var(--uv-high)';
  if (v <= 10) return 'var(--uv-very-high)';
  return 'var(--uv-extreme)';
}
function gaugeColorByActivityScore(v) {
  if (v >= 80) return 'var(--sig-good)';
  if (v >= 60) return 'var(--sig-moderate)';
  if (v >= 40) return 'var(--sig-caution)';
  return 'var(--sig-very-unhealthy)';
}
```

### 3.2 WindRose (16방위)

**용도:** 풍향 분포 시각화. 카테고리 07.

**SVG 구조:**

```html
<svg viewBox="0 0 200 200" class="w-full max-w-md" aria-label="윈드로즈: 남서 방향 빈도 최고">
  <title>현재 풍향 남서, 풍속 4.2 m/s</title>
  <!-- 동심원 3개 (5/10/15% 빈도 등급) -->
  <g stroke="var(--border)" fill="none" stroke-dasharray="2 4">
    <circle cx="100" cy="100" r="30"/>
    <circle cx="100" cy="100" r="60"/>
    <circle cx="100" cy="100" r="90"/>
  </g>
  <!-- 16방위 보조선 (16 lines from center) -->
  <g stroke="var(--border)" stroke-width="0.5">
    <!-- N/E/S/W 강조선 4개 + 12개 보조선 -->
    <line x1="100" y1="10" x2="100" y2="190"/>
    <line x1="10" y1="100" x2="190" y2="100"/>
    <!-- 대각선 NE/SE/SW/NW -->
    <line x1="36" y1="36" x2="164" y2="164"/>
    <line x1="164" y1="36" x2="36" y2="164"/>
  </g>
  <!-- 16개 부채꼴 (path) -->
  <g>
    <!-- 각 섹터: 22.5° 폭, 중심점에서 r=freq*scale 까지의 부채꼴 -->
    <!-- 예: SW 섹터 (deg=225, freq=15) -->
    <path d="M 100,100 L 36,164 A 90,90 0 0,1 100,190 Z"
          fill="var(--w-rain)" fill-opacity="0.6"/>
    <!-- ... 나머지 15개 섹터 동일 패턴 -->
  </g>
  <!-- 방위 라벨 -->
  <g font-size="11" font-weight="600" fill="var(--text-secondary)" text-anchor="middle">
    <text x="100" y="8">N</text>
    <text x="195" y="103">E</text>
    <text x="100" y="198">S</text>
    <text x="6" y="103">W</text>
  </g>
  <!-- 중앙 표시 (현재 풍속) -->
  <circle cx="100" cy="100" r="3" fill="var(--text-primary)"/>
</svg>
```

**섹터 좌표 계산 (JS):**

```js
function sectorPath(centerX, centerY, deg, frequency, maxFreq, maxRadius) {
  const halfWidth = 11.25; // 22.5°/2
  const startDeg = deg - halfWidth;
  const endDeg = deg + halfWidth;
  const radius = (frequency / maxFreq) * maxRadius;
  const x1 = centerX + radius * Math.sin(startDeg * Math.PI / 180);
  const y1 = centerY - radius * Math.cos(startDeg * Math.PI / 180);
  const x2 = centerX + radius * Math.sin(endDeg * Math.PI / 180);
  const y2 = centerY - radius * Math.cos(endDeg * Math.PI / 180);
  const largeArc = 0; // 22.5° < 180°
  return `M ${centerX},${centerY} L ${x1},${y1} A ${radius},${radius} 0 ${largeArc},1 ${x2},${y2} Z`;
}

// 보퍼트 등급 색상
function beaufortColor(speedMs) {
  if (speedMs <= 3) return 'var(--w-partly-cloudy)'; // sky-300/400
  if (speedMs <= 7) return 'var(--w-rain)';          // sky-500
  if (speedMs <= 13) return 'var(--w-heat-advisory)'; // amber/orange
  return 'var(--w-heatwave)';                         // red-600 (강풍)
}
```

### 3.3 SunArc (태양 호)

**용도:** 일출~일몰 사이 현재 태양 위치. 카테고리 06(미니), 10(메인).

**SVG 구조:**

```html
<svg viewBox="0 0 200 110" class="w-full" aria-label="현재 태양 위치 71%, 일출 05:38, 일몰 19:24">
  <defs>
    <linearGradient id="sky-grad" x1="0" y1="0" x2="1" y2="0">
      <stop offset="0%" stop-color="var(--w-sunny-bg-from)"/>
      <stop offset="50%" stop-color="var(--w-partly-cloudy)" stop-opacity="0.4"/>
      <stop offset="100%" stop-color="var(--w-night-clear)" stop-opacity="0.6"/>
    </linearGradient>
  </defs>
  <!-- 호 -->
  <path d="M 10,100 A 90,90 0 0,1 190,100"
        fill="none" stroke="url(#sky-grad)" stroke-width="3"
        stroke-dasharray="0 0"/>
  <!-- 지평선 -->
  <line x1="0" y1="100" x2="200" y2="100"
        stroke="var(--border-strong)" stroke-width="1"/>
  <!-- 일출/일몰 시각 라벨 -->
  <text x="10" y="108" font-size="10" fill="var(--text-tertiary)">05:38</text>
  <text x="190" y="108" font-size="10" fill="var(--text-tertiary)" text-anchor="end">19:24</text>
  <!-- 현재 태양 위치 -->
  <!-- progress 0~1 → 호 위 좌표 (반지름 90, 중심 100,100) -->
  <!-- angle = 180° - (progress * 180°), x = 100 + 90 cos(angle), y = 100 - 90 sin(angle) -->
  <circle id="sun-glow" cx="164" cy="38" r="14"
          fill="var(--w-sunny)" fill-opacity="0.25"/>
  <circle id="sun" cx="164" cy="38" r="9"
          fill="var(--w-sunny)"/>
</svg>
```

**좌표 계산 (JS):**

```js
function sunPosition(progressPercent) {
  // progress 0(일출)~100(일몰)
  const p = progressPercent / 100;
  const angleRad = Math.PI - p * Math.PI; // 180° → 0°
  const x = 100 + 90 * Math.cos(angleRad);
  const y = 100 - 90 * Math.sin(angleRad);
  return { x, y };
}
// 예: 71.2% → x=164, y=38
```

### 3.4 Sparkline (24h 추이)

**용도:** PM·기온·UV·풍속 등 미니 추이. 카테고리 02, 05, 07, 11, 12.

**SVG 구조:**

```html
<svg viewBox="0 0 100 30" class="w-full h-8" preserveAspectRatio="none"
     aria-label="PM2.5 24시간 추이, 평균 28">
  <defs>
    <linearGradient id="spark-fill" x1="0" y1="0" x2="0" y2="1">
      <stop offset="0%" stop-color="var(--sig-moderate)" stop-opacity="0.3"/>
      <stop offset="100%" stop-color="var(--sig-moderate)" stop-opacity="0"/>
    </linearGradient>
  </defs>
  <!-- 영역 -->
  <path d="M 0,15 L 4.2,12 L 8.3,14 ... L 100,18 L 100,30 L 0,30 Z"
        fill="url(#spark-fill)"/>
  <!-- 라인 -->
  <path d="M 0,15 L 4.2,12 ..." fill="none"
        stroke="var(--sig-moderate)" stroke-width="1.5"
        stroke-linecap="round" stroke-linejoin="round"/>
</svg>
```

**좌표 계산 (JS):**

```js
function sparklinePoints(values, width = 100, height = 30, padding = 2) {
  const min = Math.min(...values);
  const max = Math.max(...values);
  const range = max - min || 1;
  const stepX = (width - padding * 2) / (values.length - 1);
  return values.map((v, i) => ({
    x: padding + i * stepX,
    y: padding + (1 - (v - min) / range) * (height - padding * 2)
  }));
}
function pointsToPath(points) {
  return 'M ' + points.map(p => `${p.x.toFixed(1)},${p.y.toFixed(1)}`).join(' L ');
}
```

### 3.5 PrecipitationDots

**용도:** 시간축 강수확률 점 시퀀스. 카테고리 02, 08.

**구조 (HTML+CSS):**

```html
<div class="flex items-end gap-1.5">
  <!-- 24개 도트 -->
  <span class="w-2 h-2 rounded-full bg-slate-200" title="00시 5%"></span>
  <span class="w-2 h-2 rounded-full bg-sky-300" title="01시 35%"></span>
  <span class="w-3 h-3 rounded-full bg-sky-500" title="02시 70%"></span>
  <span class="w-4 h-4 rounded-full bg-blue-700" title="03시 95%"></span>
  <!-- ... -->
</div>
```

**크기·색상 매핑 (JS):**

```js
function dotSize(prob) {
  if (prob < 31) return 'w-2 h-2';
  if (prob < 61) return 'w-2.5 h-2.5';
  if (prob < 81) return 'w-3 h-3';
  return 'w-4 h-4';
}
function dotColor(prob) {
  if (prob < 31) return 'bg-slate-200 dark:bg-slate-700';
  if (prob < 61) return 'bg-sky-300';
  if (prob < 81) return 'bg-sky-500';
  return 'bg-blue-700';
}
```

### 3.6 AQIIndexBar (HeatBar)

**용도:** KHAI 0~500 수평 그라데이션 + 마커. 카테고리 05.

```html
<div class="relative w-full h-3 rounded-full overflow-hidden"
     role="meter" aria-valuenow="78" aria-valuemin="0" aria-valuemax="500"
     aria-label="KHAI 78 보통">
  <!-- 그라데이션 트랙 -->
  <div class="absolute inset-0 bg-gradient-to-r
              from-emerald-400 via-sky-400 via-amber-400
              via-orange-500 via-red-500 to-purple-700"></div>
  <!-- 현재 마커 -->
  <div class="absolute top-1/2 -translate-y-1/2 w-4 h-4 rounded-full
              bg-white border-2 border-slate-700 shadow-md"
       style="left: 15.6%;"></div>
</div>
<!-- 눈금 라벨 -->
<div class="flex justify-between text-[10px] text-slate-500 mt-1 tabular-nums">
  <span>0</span><span>50</span><span>100</span><span>250</span><span>500</span>
</div>
```

**위치 계산 (JS):** `markerLeftPercent = (khai / 500) * 100`. 78 → 15.6%.

### 3.7 UVScale

```html
<div class="relative w-full h-3 rounded-full overflow-hidden"
     role="meter" aria-valuenow="6" aria-valuemin="0" aria-valuemax="11" aria-label="UV 6 높음">
  <div class="absolute inset-0"
       style="background: linear-gradient(to right,
              var(--uv-low) 0%, var(--uv-low) 18%,
              var(--uv-moderate) 18%, var(--uv-moderate) 45%,
              var(--uv-high) 45%, var(--uv-high) 64%,
              var(--uv-very-high) 64%, var(--uv-very-high) 91%,
              var(--uv-extreme) 91%, var(--uv-extreme) 100%);"></div>
  <div class="absolute top-1/2 -translate-y-1/2 w-4 h-4 rounded-full bg-white border-2 border-slate-700"
       style="left: 54.5%;"></div>
</div>
<div class="flex justify-between text-[10px] text-slate-500 mt-1 tabular-nums">
  <span>0</span><span>3</span><span>6</span><span>8</span><span>11+</span>
</div>
```

### 3.8 컨디션 아이콘 SVG (24×24 base, 64×64 hero)

기상 상태별 인라인 SVG. 카테고리 01, 02, 03, 09, 15에서 사용. condition_en 키 → 아이콘 매핑:

| condition_en | 아이콘 | 핵심 path 요약 |
|------------|------|------------|
| `clear` | 태양 (8 광선) | `<circle r="8"/>` + 8개 line |
| `partly_cloudy` | 태양 + 구름 | clear 위 구름 ellipse 오버레이 |
| `cloudy` | 구름 (3 lobe) | `<path d="M ... cubic ..."/>` |
| `rain` | 구름 + 빗방울 3개 | cloudy + line[3] stroke-dasharray |
| `snow` | 구름 + 눈송이 3개 | cloudy + 6각 별 path |
| `thunderstorm` | 구름 + 번개 | cloudy + zigzag polygon |
| `fog` | 가로선 4개 | `<line>` × 4 |
| `night_clear` | 초승달 | path arc 2회 |

frontend-implementer는 `references/svg-icons.html` (별도 산출 권장) 또는 인라인으로 구현. 아이콘 색상은 컨디션 토큰을 `currentColor` 또는 `fill="var(--w-{key})"`로 주입.

---

## 4. 반응형 전략

### 4.1 SVG 자체 반응형

모든 정적 SVG는 viewBox 기반:

```html
<svg viewBox="0 0 120 120"
     preserveAspectRatio="xMidYMid meet"
     class="w-full h-auto max-w-[160px]"
     role="..." aria-label="...">
```

`width`/`height` 속성은 비워두고 Tailwind class로 컨테이너 크기 결정. `preserveAspectRatio="xMidYMid meet"`로 비율 유지 + 중앙 정렬.

**예외:** Sparkline은 `preserveAspectRatio="none"`로 가로 stretch (밀도 보존이 가독성 보존보다 중요).

### 4.2 ApexCharts 리사이즈

ApexCharts는 자체 ResizeObserver를 가지지만 부모 컨테이너에 명시적 height 필요:

```html
<div id="hourly_temp" class="w-full h-[200px] md:h-[240px] lg:h-[280px]"></div>
```

**모바일 분기 (필요시):**

```js
const isMobile = window.matchMedia('(max-width: 640px)').matches;
opts.chart.height = isMobile ? 160 : 240;
opts.xaxis.labels.show = !isMobile; // 모바일에서 x축 라벨 생략
```

### 4.3 컴포넌트별 반응형 패턴

| 컴포넌트 | mobile (<640) | tablet (640~1024) | desktop (≥1024) |
|---------|--------------|-----------------|----------------|
| HourlyStrip | flex-none w-14, 가로 스크롤 | w-16 | w-20, 24개 한 화면 |
| DailyRow | 2열 압축 (요일+아이콘 / 막대+온도) | 1행 풀 | 1행 풀 + 추가 메타 |
| GaugeRing | w-32 h-32 | w-40 h-40 | w-48 h-48 |
| WindRose | w-full max-w-[260px] | max-w-[320px] | max-w-[400px] |
| ComparisonRow | 핵심 3컬럼만 | 5컬럼 | 7컬럼 풀 |
| MetricCell 그리드 | grid-cols-2 | grid-cols-3 | grid-cols-4 |

### 4.4 다크 모드 차트 색상

ApexCharts: `chart.background: 'transparent'` + tooltip `theme: 'dark'` (다크 모드 detect). CSS 변수가 다크 페어로 자동 전환되므로 stroke/fill 색상은 `var(--w-rain)` 형식으로 작성하면 자동 적용.

---

## 5. 임계값 → 디자인 토큰 색상 매핑 규칙

도메인 카탈로그의 등급(좋음/보통/나쁨/매우나쁨)을 그대로 디자인 토큰으로 변환하는 단일 함수 카탈로그.

### 5.1 PM2.5 / PM10 / KHAI

```js
const AQI_BANDS = {
  pm25:  [{ max: 15,  token: '--sig-good',           label: '좋음' },
          { max: 35,  token: '--sig-moderate',       label: '보통' },
          { max: 75,  token: '--sig-unhealthy',      label: '나쁨' },
          { max: 999, token: '--sig-very-unhealthy', label: '매우나쁨' }],
  pm10:  [{ max: 30,  token: '--sig-good',           label: '좋음' },
          { max: 80,  token: '--sig-moderate',       label: '보통' },
          { max: 150, token: '--sig-unhealthy',      label: '나쁨' },
          { max: 999, token: '--sig-very-unhealthy', label: '매우나쁨' }],
  khai:  [{ max: 50,  token: '--sig-good',           label: '좋음' },
          { max: 100, token: '--sig-moderate',       label: '보통' },
          { max: 250, token: '--sig-unhealthy',      label: '나쁨' },
          { max: 999, token: '--sig-very-unhealthy', label: '매우나쁨' }]
};
function gradeOf(metric, value) {
  return AQI_BANDS[metric].find(b => value <= b.max);
}
```

### 5.2 UV 인덱스

```js
const UV_BANDS = [
  { max: 2,  token: '--uv-low',       label: '낮음' },
  { max: 5,  token: '--uv-moderate',  label: '보통' },
  { max: 7,  token: '--uv-high',      label: '높음' },
  { max: 10, token: '--uv-very-high', label: '매우높음' },
  { max: 99, token: '--uv-extreme',   label: '위험' }
];
```

### 5.3 강수확률

```js
const PRECIP_PROB_BANDS = [
  { max: 30, token: '--bg-elevated',       label: '낮음' },
  { max: 60, token: '--w-partly-cloudy',   label: '보통' },
  { max: 80, token: '--w-rain',            label: '높음' },
  { max: 100,token: '--w-rain-heavy',      label: '매우높음' }
];
```

### 5.4 풍속 (보퍼트 매핑)

```js
const WIND_BANDS = [
  { max: 3,  token: '--w-partly-cloudy',  label: '약함', beaufort: '0~2' },
  { max: 7,  token: '--w-rain',           label: '보통', beaufort: '3~4' },
  { max: 13, token: '--w-heat-advisory',  label: '강함', beaufort: '5~6' },
  { max: 20, token: '--w-heatwave',       label: '강풍주의보', beaufort: '7~8' },
  { max: 99, token: '--w-storm',          label: '강풍경보', beaufort: '9+' }
];
```

### 5.5 기온

```js
function tempColorToken(c) {
  if (c <= 4)   return '--w-coldwave';
  if (c <= 10)  return '--w-cold-advisory';
  if (c <= 26)  return '--w-sunny';
  if (c <= 32)  return '--w-heat-advisory';
  return '--w-heatwave';
}
```

### 5.6 활동 점수

```js
function activityToken(score) {
  if (score >= 80) return '--sig-good';
  if (score >= 60) return '--sig-moderate';
  if (score >= 40) return '--sig-caution';
  return '--sig-very-unhealthy';
}
```

### 5.7 기상 특보 severity → 컬러

```js
const SEVERITY_TOKENS = {
  info:      { bg: 'bg-sky-100',     text: 'text-sky-900',     border: 'border-sky-300' },
  caution:   { bg: 'bg-amber-100',   text: 'text-amber-900',   border: 'border-amber-400' },
  warning:   { bg: 'bg-orange-100',  text: 'text-orange-900',  border: 'border-orange-500' },
  danger:    { bg: 'bg-red-100',     text: 'text-red-900',     border: 'border-red-600' },
  emergency: { bg: 'bg-rose-600',    text: 'text-white',       border: 'border-rose-700', extra: 'animate-pulse' }
};
```

---

## 6. 애니메이션 가이드

### 6.1 진입 애니메이션

| 컴포넌트 | 애니메이션 | duration | easing |
|---------|---------|---------|--------|
| 카드 등장 | fade-in + translateY(8px → 0) | 200ms | ease-out |
| 게이지 호 | stroke-dasharray 0 → target | 600ms | cubic-bezier(0.4, 0, 0.2, 1) |
| 라인 차트 | left-to-right reveal | 400ms | ease-out (ApexCharts default) |
| 막대 | scaleY 0 → 1, transform-origin bottom | 300ms | ease-out |
| 알림 펄스 | opacity 1 → 0.6 → 1 | 1500ms loop | ease-in-out |
| 스파크라인 | stroke-dashoffset draw | 500ms | ease-out |

### 6.2 데이터 트랜지션 (값 변경 시)

| 종류 | duration | 비고 |
|------|---------|------|
| 게이지 값 변경 | 400ms | stroke-dasharray transition |
| 숫자 카운트업 | 600ms | requestAnimationFrame, easing ease-out |
| 색상 변경 (등급 전환) | 250ms | CSS color transition |
| HourlyStrip 스크롤 | 200ms | smooth scroll |

### 6.3 prefers-reduced-motion 처리

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

ApexCharts:

```js
opts.chart.animations = {
  enabled: !window.matchMedia('(prefers-reduced-motion: reduce)').matches,
  speed: 400
};
```

### 6.4 알림 배너 (emergency)

```css
@keyframes pulse-emergency {
  0%, 100% { opacity: 1; box-shadow: 0 0 0 0 rgba(244, 63, 94, 0.5); }
  50% { opacity: 0.95; box-shadow: 0 0 0 12px rgba(244, 63, 94, 0); }
}
.alert-emergency:not([data-reduced-motion]) {
  animation: pulse-emergency 1.5s ease-in-out infinite;
}
```

---

## 7. 15개 템플릿 시각화 매핑 (디자이너 분배표 기반)

디자인 시스템 §6 분배표의 15개 템플릿이 각각 사용할 시각화 컴포넌트 + 더미 데이터 키 + 라이브러리:

| # | 파일명 | 카테고리 | 시각 스타일 | 사용 컴포넌트 | 더미 데이터 키 | 라이브러리 |
|---|-------|---------|-----------|------------|------------|---------|
| 01 | `01_current_minimal.html` | current | minimal | C1 + C2×4 + 컨디션 아이콘 SVG | `current` | vanilla SVG |
| 02 | `02_forecast_hourly_glass.html` | forecast-hourly | glass | C4 HourlyStrip + 영역 라인 + C14 PrecipitationDots + C7 풍속 Sparkline | `hourly_24h` | **ApexCharts** + vanilla SVG |
| 03 | `03_forecast_daily_paper.html` | forecast-daily | paper-card | C5 DailyRow×7 + C3 ConditionChip×7 + 온도 막대 그라데이션 | `daily_7d` | vanilla HTML+CSS |
| 04 | `04_alert_emergency.html` | alert | dark-pro | C8 AlertBanner(emergency) + 인접 특보 칩 + 영향 지역 배지 그리드 | `alerts_active` | vanilla HTML+CSS + SVG 사이렌 아이콘 |
| 05 | `05_airquality_dataheavy.html` | airquality | data-heavy | C6 KHAI 게이지 + C11 AQIIndexBar + C2×6 + C7 Sparkline×4 | `current` + `aqi_24h` | vanilla SVG + **ApexCharts**(스파크라인 4개) |
| 06 | `06_uv_neumorphism.html` | uv | neumorphism | C6 UV 게이지 + C12 UVScale + C13 SunArc 미니 + 권장 차단시간 | `current` + `uv_24h` + `sun_arc` | vanilla SVG |
| 07 | `07_wind_vintage.html` | wind | vintage | C10 WindRose 풀 사이즈 + C2×3 + C7 풍속 Sparkline 24h | `wind_distribution` + `wind_24h` (현재값) | vanilla SVG |
| 08 | `08_precipitation_illustration.html` | precipitation | illustration | 빗방울 캐릭터 SVG + C14 PrecipitationDots + 누적 영역 + C2×3 | `precip_today` + `precip_hourly_6h` | vanilla SVG + **ApexCharts**(누적 영역) |
| 09 | `09_comparison_terminal.html` | comparison | terminal | C9 ComparisonRow×7 + C3 ConditionChip + ASCII 박스 보더 | `cities_compare` | vanilla HTML+CSS |
| 10 | `10_sunrise_sunset_gradient.html` | sunrise-sunset | gradient-mesh | C13 SunArc 큰 사이즈 + C2×4 + 박명 타임라인 | `sun_arc` + `twilight_marks` | vanilla SVG |
| 11 | `11_climate_infographic_vintage.html` | climate-infographic | vintage | 12개월 막대 SVG + 12개월 누적 영역 + 극값 카드 3개 | `monthly_climate_seoul` + `extreme_records` | vanilla SVG + **ApexCharts**(영역) |
| 12 | `12_pollen_yellowdust_illustration.html` | pollen-yellowdust | illustration | 꽃가루 캐릭터 SVG + C3 ConditionChip×3 + C7 Sparkline 7일 + PM10 게이지 미니 | `pollen_yellowdust` + `pollen_7d_trend` | vanilla SVG |
| 13 | `13_marine_glass.html` | marine | glass | 파고 게이지 + 조위 sin curve + C10 WindRose 미니 + C2×3 | `marine` + `tide_12h` | vanilla SVG |
| 14 | `14_outdoor_activity_neumorphism.html` | outdoor-activity | neumorphism | C6 activity 게이지 + 활동별 진행 바 4개 + 산출 근거 칩 | `outdoor_activity` | vanilla SVG + vanilla HTML+CSS |
| 15 | `15_travel_weather_paper.html` | travel-weather | paper-card | 도시 카드 6개 (paper) + 여권 스탬프 SVG + 적합도 미니 표 | `travel_weather` (6 cities) | vanilla HTML+CSS + vanilla SVG |

### 7.1 라이브러리 사용 요약

- **vanilla SVG only:** 01, 03, 04, 06, 07, 09, 10, 12, 13, 14, 15 (11개)
- **ApexCharts 추가:** 02, 05, 08, 11 (4개)
- **Chart.js 사용:** 없음 (요구 사항에 다중 축 차트 없음)

ApexCharts CDN 단일 import:

```html
<script src="https://cdn.jsdelivr.net/npm/apexcharts"></script>
```

11개 템플릿은 외부 JS 라이브러리 zero. 4개 템플릿만 ApexCharts CDN 1줄 추가.

---

## 8. 더미 데이터 카탈로그 (`03_dataviz_samples.json`)

별도 파일 `_workspace/03_dataviz_samples.json`에 다음 키 구조로 저장. 모든 카테고리 + 부속 데이터 포함:

```
{
  "meta": { "generated_at": "2026-05-06T15:30:00+09:00", "season": "초여름", "base_city": "서울" },

  // 카테고리 01
  "current": { ... },
  "current_busan": { ... },

  // 카테고리 02
  "hourly_24h": [ 24개 ],

  // 카테고리 03
  "daily_7d": [ 7개 ],

  // 카테고리 04
  "alerts_active": [ ... ],
  "alerts_adjacent": [ ... ],
  "alert_regions": [ ... ],

  // 카테고리 05
  "airquality_now": { ... },
  "aqi_24h": [ 24개 ],
  "aqi_stations_nearby": [ ... ],

  // 카테고리 06
  "uv_now": { ... },
  "uv_24h": [ 14개 (06~19시) ],

  // 카테고리 07
  "wind_now": { ... },
  "wind_distribution": [ 16방위 ],
  "wind_24h_speed": [ 24개 ],

  // 카테고리 08
  "precip_today": { ... },
  "precip_hourly_6h": [ 6개 ],
  "precip_matrix_24x7": { ... },

  // 카테고리 09
  "cities_compare": [ 7개 ],

  // 카테고리 10
  "sun_arc": { ... },

  // 카테고리 11
  "monthly_climate_seoul": { ... },
  "extreme_records": [ ... ],

  // 카테고리 12
  "pollen_yellowdust": { ... },
  "pollen_7d_trend": [ ... ],

  // 카테고리 13
  "marine": { ... },
  "tide_12h": [ ... ],

  // 카테고리 14
  "outdoor_activity": { ... },

  // 카테고리 15
  "travel_weather": [ 6 cities ]
}
```

### 8.1 단위·키 명명 규칙

- **snake_case** 일관 사용.
- **단위는 키명에 명시:** `temp_c`, `wind_ms`, `precip_mm`, `pm25_ugm3`, `pressure_hpa`, `wave_m`, `daylight_min`.
- **시간:** ISO 8601 + KST `+09:00` (`"observed_at": "2026-05-06T15:24:00+09:00"`). 시각 표기는 `HH:mm` 24시간.
- **5월 초 한국 현실값:**
  - 서울 18~24°C, 부산 20~25°C, 제주 19~22°C, 강릉 17~23°C, 대구 21~26°C
  - PM2.5 18~45 ㎍/㎥, PM10 30~80 ㎍/㎥, KHAI 45~110
  - UV 정오 6~9, 풍속 2~5 m/s
  - 강수확률 5월 평년 약 30%
- **세션 시각 기준:** 2026-05-06 15:24 KST (오후 관측).

### 8.2 도메인 카탈로그 매핑 검증

도메인 §2 카탈로그의 모든 지표가 더미 데이터 키에 매핑됨을 확인:

| 도메인 지표 | JSON 키 | 더미 값 (서울) |
|------------|--------|------------|
| temp | `current.temp_c` | 22.4 |
| feels_like | `current.feels_like_c` | 21.8 |
| humidity | `current.humidity_pct` | 58 |
| dewpoint | `current.dewpoint_c` | 13.2 |
| pressure | `current.pressure_hpa` | 1014 |
| visibility | `current.visibility_km` | 18 |
| wind_speed | `current.wind_ms` | 2.4 |
| wind_dir | `current.wind_dir_deg` | 235 |
| wind_gust | `current.wind_gust_ms` | 4.6 |
| condition | `current.condition_en` | "partly_cloudy" |
| pm25 | `current.pm25_ugm3` | 28 |
| pm10 | `current.pm10_ugm3` | 48 |
| o3 | `current.o3_ppm` | 0.045 |
| no2 | `current.no2_ppm` | 0.022 |
| so2 | `current.so2_ppm` | 0.004 |
| co | `current.co_ppm` | 0.4 |
| khai | `current.khai` | 92 |
| uv_index | `current.uv_index` | 6 |
| sunrise | `current.sunrise` | "05:38" |
| sunset | `current.sunset` | "19:24" |
| precip | `precip_today.precip_now_mm_h` | 0 |
| precip_prob | `current.precip_prob_pct` | 20 |
| precip_24h | `precip_today.accumulated_24h_mm` | 12 |
| wave_height | `marine.wave_height_m` | 1.4 |
| sea_temp | `marine.sea_temp_c` | 17.1 |
| pollen_oak | `pollen_yellowdust.pollen_oak_grade` | "나쁨" |
| pollen_pine | `pollen_yellowdust.pollen_pine_grade` | "매우나쁨" |
| activity_score | `outdoor_activity.activity_score` | 78 |

---

## 9. 일관성 체크리스트 (구현/QA 인계용)

| # | 규칙 | 검증 방법 |
|---|-----|---------|
| 1 | 차트 색상은 §5 토큰 매핑 함수만 사용 | grep 인라인 hex 색상 (제외: 그라데이션 정의) |
| 2 | 모든 SVG에 viewBox + role/aria-label | grep `<svg ` 후 속성 누락 확인 |
| 3 | GaugeRing은 role="meter" + aria-valuenow/min/max | grep `role="meter"` |
| 4 | AlertBanner는 role="alert" | grep `role="alert"` |
| 5 | 데이터 → 색상 매핑 함수 일관 적용 (예: KHAI 78 → 보통 → moderate) | 시각 검수 + 색대비 |
| 6 | prefers-reduced-motion 분기 존재 | grep `prefers-reduced-motion` |
| 7 | tabular-nums 모든 수치 라벨 | grep `tabular-nums` 인접 숫자 |
| 8 | 단위 텍스트 0.6em 작게 | 시각 검수 |
| 9 | ApexCharts 사용 4개 파일에만 CDN 로드 | grep `apexcharts` script |
| 10 | 더미 데이터 키 명명 snake_case + 단위 명시 | JSON 스키마 검증 |

---

## 10. 후속 작업 인계

### frontend-implementer

- 본 문서 §3 SVG 컴포넌트 명세를 그대로 코드로 옮긴다.
- §5 색상 매핑 함수를 각 템플릿 `<script>` 상단에 inline 정의 (또는 공통 utility 블록).
- §7 분배표 대로 15개 파일 빌드. ApexCharts CDN은 02/05/08/11 4개 파일에만 추가.
- 더미 데이터는 `03_dataviz_samples.json` 해당 키를 inline 객체로 복사 (자체 완결 HTML 원칙).

### qa

- §9 체크리스트 10항목 자동/수동 검증.
- 색대비: §5 토큰 페어가 디자인 시스템 §8.1 4.5:1 통과 여부 재확인.
- 등급 → 색상 매핑 일관성: 동일 KHAI 값이 모든 템플릿에서 동일 색상으로 표현되는지 교차 비교.
- 반응형: 320 / 768 / 1280 / 1920 4개 폭에서 깨짐 없는지 스크린샷.

---

**END OF DATAVIZ SPEC**

> 변경 시: §1 매핑표 보존, 신규 카테고리는 §1·§7·§8.2에 동시 추가. 새 시각화 형태 추가 시 §3에 SVG 명세 + §5에 색상 매핑 함수 동시 정의.
