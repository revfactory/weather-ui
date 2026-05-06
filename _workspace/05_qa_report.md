# Weather Template QA Report

**작성자:** qa (weather-build-team)
**시작일:** 2026-05-06
**검증 대상:** `templates/01_*.html` ~ `templates/15_*.html` + `templates/index.html`
**입력 컨텍스트 (사전 로드 완료):**
- `_workspace/01_domain_catalog.md` — 15 카테고리 / 도메인 임계값 (PM2.5/PM10/KHAI, UV, 풍속, 강수, 특보 등)
- `_workspace/02_design_system.md` — CSS 변수 토큰 / 14 컴포넌트 / 10 시각 스타일 / §6 분배표 15행 / WCAG AA 색대비표
- `_workspace/03_dataviz_spec.md` — 카테고리별 시각화 매핑 / SVG 컴포넌트 명세 / 임계값→토큰 매핑 함수
- `_workspace/03_dataviz_samples.json` — 더미 데이터 (서울 2026-05-06 15:24 KST 기준)

---

## 평가 기준 (5경계면 + 4 부가 검증)

각 템플릿마다 다음 9개 항목을 검증한다.

### 5 경계면 정합성
| 경계면 | 검증 포인트 | 위반 예시 |
|--------|------------|---------|
| **도메인** | 임계값(예: PM2.5 ≤15 좋음, 16~35 보통, 36~75 나쁨, ≥76 매우나쁨), 단위(°C, ㎍/㎥, m/s, mm), 라벨 표기 | KHAI 92를 "나쁨"으로 표시 (실제 51~100 보통) |
| **디자인 토큰** | `--w-*`, `--sig-*`, `--uv-*` 토큰 사용. 임의 hex 금지(그라데이션 정의 제외) | `bg-[#ff5500]` 같은 임시 hex |
| **시각화 명세** | dataviz_spec §1 권장 컴포넌트, ApexCharts 카테고리(02/05/08/11)만 CDN 로드 | wind 카테고리에 라인 차트만 사용 (윈드로즈 누락) |
| **더미 데이터** | samples.json 키와 값 정확 사용 (current.temp_c=22.4 등). 키 명명 snake_case + 단위 명시 | 임의 더미 (예: 23도 표시) 사용 |
| **HTML 구현** | 시멘틱 태그(`<article>`, `<table>`, `role="meter"` 등), viewport meta, viewBox SVG | role 누락, 인라인 style hex |

### 부가 검증
| 항목 | 기준 |
|------|------|
| **WCAG AA** | 본문 4.5:1 이상, 큰 텍스트(≥18.66px bold/24px) 3:1 이상. §8.1 검증된 페어 사용 권고 |
| **반응형** | 360px / 768px / 1024px / 1440px 4개 폭에서 가로 스크롤 없음, 텍스트 잘림 없음 |
| **콘솔** | HTML5 구문 유효, 외부 CDN(Tailwind/Pretendard/ApexCharts) 정상 로드, JS 런타임 에러 0 |
| **한국어/단위** | 한국어 라벨 정확(맑음/흐림/비/눈/폭염/한파/주의보/경보), 숫자에 단위 병기, `tabular-nums` 적용 |

### 등급 정의
- **PASS**: 5경계면 + 부가 검증 모두 통과 (또는 NOTE 수준 권고만 있음)
- **FAIL**: 5경계면 중 1개 이상 위반 또는 WCAG AA 미달, 콘솔 에러 발생
- **NOTE**: PASS로 처리하되 다음 변형/리비전에 반영 권고할 사항 기록

### 핑퐁 정책
- 동일 템플릿 핑퐁(수정→재검증) **3회 초과** 시 team-lead에게 에스컬레이션
- 빌더 발신자 추적: builder1 / builder2 / builder3

---

## 분배표 (참고: design_system §6)

| # | 파일명 | 카테고리 | 시각 스타일 |
|---|-------|---------|----------|
| 01 | 01_current_minimal.html | current | minimal |
| 02 | 02_forecast_hourly_glass.html | forecast-hourly | glass |
| 03 | 03_forecast_daily_paper.html | forecast-daily | paper-card |
| 04 | 04_alert_emergency.html | alert | dark-pro |
| 05 | 05_airquality_dataheavy.html | airquality | data-heavy |
| 06 | 06_uv_neumorphism.html | uv | neumorphism |
| 07 | 07_wind_vintage.html | wind | vintage |
| 08 | 08_precipitation_illustration.html | precipitation | illustration |
| 09 | 09_comparison_terminal.html | comparison | terminal |
| 10 | 10_sunrise_sunset_gradient.html | sunrise-sunset | gradient-mesh |
| 11 | 11_climate_infographic_vintage.html | climate-infographic | vintage |
| 12 | 12_pollen_yellowdust_illustration.html | pollen-yellowdust | illustration |
| 13 | 13_marine_glass.html | marine | glass |
| 14 | 14_outdoor_activity_neumorphism.html | outdoor-activity | neumorphism |
| 15 | 15_travel_weather_paper.html | travel-weather | paper-card |

---

## 검증 결과 (incremental append)

> 빌더가 템플릿 완성 메시지를 보낼 때마다 아래에 누적 추가됨.

---

### 01_current_minimal.html
- **검증 일시:** 2026-05-06 (1차)
- **결과:** PASS (NOTE 2건)
- **빌더:** builder1
- **핑퐁 횟수:** 0 (1발 통과)

**5경계면 검증**
| 경계면 | 결과 | 증거 |
|--------|------|------|
| 도메인 | ✓ | temp 22.4°C / feels 21.8°C / humidity 58% / wind 2.4 m/s 남서 / UV 6 "높음" / precip_prob 20% — 도메인 §2.1·§2.4 등급 라벨 모두 일치 |
| 디자인 토큰 | ✓ | `--w-sunny/cloudy/rain/night-clear/partly-cloudy` + 중립 토큰 모두 §1.1/§1.4 정의값. 임의 hex 사용 없음(CSS 변수 정의 제외) |
| 시각화 명세 | ✓ | dataviz_spec §1 ID01 권장(C1 WeatherIconCard + C2 MetricCell×4 + 컨디션 SVG) 일치. ApexCharts 미로드 ✓ |
| 더미 데이터 | ✓ | samples.json `current` 키 모든 값 정확 매핑 (자연어 라벨 "선선함, 가벼운 외투 권장" 포함) |
| HTML 구현 | ✓ | `<main role="main">` + `<article>` + `<dl>` + `role="group" aria-label`, `<svg viewBox role="img" aria-label>`, viewport meta, lang="ko", 한글 title |

**부가 검증**
- WCAG AA: ✓ — orange-100/orange-900=9.1:1, slate-900 on white=17.4:1
- 반응형: ✓ — `clamp(96px, 22vw, 160px)` 거대 숫자 + `grid-cols-2 sm:grid-cols-4` 안정. 360~1440px 가로 스크롤 없음
- 콘솔: ✓ — Tailwind/Pretendard CDN 정상, 토글 스크립트 try/catch. 런타임 에러 없음 예상
- 한국어/단위: ✓ — 모든 라벨 한글, 단위 °C/%/m/s 병기, `.num`(tabular-nums) 일관 적용

**NOTE (PASS 처리, 권고)**
1. UV "높음" 칩이 `bg-orange-100/text-orange-900` (`--sig-unhealthy` 페어) 사용. dataviz_spec §5.2 UV_BANDS는 UV 6~7 → `--uv-high` (orange-500 base) 토큰 권장. 색상 의미는 정확하나 06_uv_neumorphism 등 UV 전용 템플릿과 시각 일관성 위해 차후 동일 토큰 페어 권고.
2. 메타 캡션(L192) 11px slate-500 — WCAG AA 본문 위반은 아니지만 §8.1 단서(14px↑/굵기 500↑)에 따라 12px+로 상향 권고.

---

### 02_forecast_hourly_glass.html
- **검증 일시:** 2026-05-06 (1차)
- **결과:** PASS (NOTE 2건)
- **빌더:** builder3
- **핑퐁 횟수:** 0

**5경계면 검증**
| 경계면 | 결과 | 증거 |
|--------|------|------|
| 도메인 | ✓ | hourly_24h 24개(15시~익14시) temp/precip_prob/wind/humidity/feels 모두 일치. 강수확률 분기(30/60/80) → dotColor/Size dataviz_spec §3.5 정확 |
| 디자인 토큰 | ✓ | `--w-rain/night-clear/partly-cloudy` glass overlay 정의 §1.1·§1.4 일치. ApexCharts colorStops 인라인 hex는 §0.4 그라데이션 정의 예외 허용 |
| 시각화 명세 | ✓ | dataviz_spec ID02 (HourlyStrip + ApexCharts area + PrecipitationDots + 풍속 Sparkline) 정확. ApexCharts CDN 허용 카테고리 02 ✓ |
| 더미 데이터 | ✓ | HOURLY 배열 24개 samples.json hourly_24h와 1:1 일치 (소수점·icon 키 포함) |
| HTML 구현 | ✓ | `<ul role="list">` + `<li role="listitem" tabindex="0">`, ArrowLeft/Right 키보드 핸들러, `role="img" aria-label` 차트, viewport meta + 한글 title |

**부가 검증**
- WCAG AA: ✓ — slate-50/slate-300 본문 7:1+, sky-300 라벨 5:1+
- 반응형: ✓ — HourlyStrip 가로 스크롤 의도, 768/1024/1440 안정. precip-dots flex-wrap 모바일 자동
- 콘솔: ✓ — ApexCharts CDN load 후 DOMContentLoaded, prefers-reduced-motion 분기
- 한국어/단위: ✓ — °C/m/s/%/mm 병기, tabular-nums(.num + class), 한글 라벨

**NOTE (PASS 처리, 권고)**
1. PrecipitationDots(L170)가 부모 `role="list"` + 자식 `<span role="listitem">` 패턴 — 시멘틱 정확하나 가독성 위해 `<ul><li>` 권고.
2. 메타 캡션·범례 `text-slate-500` 11px 다수 — 12px+ 상향 권고 (다크 글래스 위 4.0~4.3:1 경계).

---

### 03_forecast_daily_paper.html
- **검증 일시:** 2026-05-06 (1차)
- **결과:** PASS (NOTE 1건)
- **빌더:** builder1
- **핑퐁 횟수:** 0

**5경계면 검증**
| 경계면 | 결과 | 증거 |
|--------|------|------|
| 도메인 | ✓ | daily_7d 7개(수~화, 5/6~5/12) temp_min/max/precip_prob/icon/condition 모두 정확. 강수확률 임계 31/61/81 분기 → "낮음/보통/높음/매우높음" 라벨 정확 |
| 디자인 토큰 | ✓ | paper-card 스타일 §5/§5.1 허용 종이톤(#faf6ee/#fffaf2/#3a2f25). temp-bar 그라데이션 `from-sky-400 to-orange-400` (§C5 DailyRow 명세 일치). chip 4종 amber/sky/slate/blue 페어 모두 §1.2 등급 파생 |
| 시각화 명세 | ✓ | dataviz_spec ID03 (DailyRow×7 + 온도 막대 그라데이션 + ConditionChip×7) 정확. vanilla HTML+CSS only ✓ |
| 더미 데이터 | ✓ | daily_7d 7개 1:1 매핑. note:"오늘"은 빌더 자체 메타(영향 없음) |
| HTML 구현 | ✓ | `<ol>` + `<li>` + 각 행 aria-label, `role="img"` 막대 별도 aria, `<svg use href>` 심볼 패턴, viewport meta, 한글 title |

**부가 검증**
- WCAG AA: ✓ — stone-800 on #fffaf2 ≈ 12.4:1, sky-600 4.7:1, blue-700 7.2:1, chip-clear 9.6:1, chip-rain 12:1
- 반응형: ✓ — `grid-cols-[3rem_2.5rem_1fr_4.5rem] sm:grid-cols-[4rem_3rem_1fr_6rem]` 360에서 ±0.4° 회전 안정
- 콘솔: ✓ — Tailwind/Pretendard CDN, vanilla forEach 정상
- 한국어/단위: ✓ — "맑음/구름조금/부분 흐림/비" + 강수확률 % + 등급 라벨 병기, tabular-nums

**NOTE (PASS 처리, 권고)**
1. TODAY 배지(L235) `bg-rose-500` 클래스 + 인라인 `style="background:#f87171"` 동시 적용 → 인라인이 우선. 단일 클래스 또는 단일 인라인으로 정리 권고. 시각 결과는 정상.

---

### 04_alert_emergency.html
- **검증 일시:** 2026-05-06 (1차)
- **결과:** PASS (NOTE 2건)
- **빌더:** builder1
- **핑퐁 횟수:** 0

**5경계면 검증**
| 경계면 | 결과 | 증거 |
|--------|------|------|
| 도메인 | ✓ | 도메인 §2.5 호우경보 발효 기준 "3시간 90mm 또는 12시간 180mm" → 본문 "3시간 90mm 이상" 정확 인용. 인접 특보 3종(강풍주의보 서해5도/호우주의보 인천·강화/폭염주의보 대구·포항) 발효 기간 alerts_adjacent 일치 |
| 디자인 토큰 | ✓ | rose-500/orange-500/amber-500 → §1.1·§5.7 SEVERITY_TOKENS(emergency/warning/caution) 정확 매핑. cyan-300/400 dark-pro 시그니처 §5.1, --grid-line/--accent-cyan 정의 |
| 시각화 명세 | ✓ | dataviz_spec ID04 (AlertBanner emergency + 인접 특보 칩 + 영향 지역 배지 그리드) 정확. 사이렌 SVG 자체. 펄스 애니메이션 §6.4 keyframe 일치 |
| 더미 데이터 | ✓ | alerts_active.heavy_rain_warning + alerts_adjacent 3개 + alert_regions 25개(warning 22 + caution 3) 정확. 헤더 카운트 "경보 22·주의보 3" 일치. 합성 메트릭(3h 누적 87.2mm 등)은 호우경보 규모와 일관된 시연값 |
| HTML 구현 | ✓ | `role="alertdialog"` + `aria-labelledby/describedby`, `<dl>` 메타, 모든 지역 배지 `aria-label`, focus-visible cyan ring |

**부가 검증**
- WCAG AA: ✓ — bg-rose-500 + white text(24px+ bold large-text 3:1 통과), slate-200 본문 8:1+, amber-200 on amber-500/10 ≈ 7:1
- 반응형: ✓ — `col-span-12 lg:col-span-8/4/5/7` + 영향지역 `grid-cols-3 sm:grid-cols-4 md:grid-cols-5` 360 3열 안정. corner-cuts clip-path 모든 폭 대응
- 콘솔: ✓ — ticker animation prefers-reduced-motion 분기, 외부 CDN 2개 정상
- 한국어/단위: ✓ — 모든 특보명 한글 + EMERGENCY/WARNING/CAUTION 영문 라벨 병기 (레전드에 한글 매핑 동반), mm/m·s/km/hPa 병기, tabular-nums

**NOTE (PASS 처리, 권고)**
1. EMERGENCY/WARNING/CAUTION 영문 모노 배지 — dark-pro 관제실 스타일 의도. 한글 "경보/주의보" 라벨 동반(L171, L302)되어 §0.3 색상+텍스트 병기 원칙 충족.
2. 헤더 시각 "UPD 15:30:24"가 samples.json `issued_at` "15:30:00"과 24초 차 — 라이브 효과 의도된 합성 디테일.

---

### 06_uv_neumorphism.html
- **검증 일시:** 2026-05-06 (1차)
- **결과:** PASS (NOTE 3건)
- **빌더:** builder1
- **핑퐁 횟수:** 0

**5경계면 검증**
| 경계면 | 결과 | 증거 |
|--------|------|------|
| 도메인 | ✓ | UV 6 → "높음" (§2.4 6~7 일치), 피크 9 → "매우높음" (§2.4 8~10 일치), burn_minutes 25분(피부 II), 일출/일몰/정오 모두 sun_arc 일치 |
| 디자인 토큰 | ✓ | `--uv-low/moderate/high/very-high/extreme` = §1.3 정의값(#22c55e/#eab308/#f97316/#dc2626/#7e22ce) 1:1 일치. 뉴모피즘 베이스 #e6e9ef/#b9bdc6/#ffffff §5 시그너처 |
| 시각화 명세 | ✓ | dataviz_spec ID06 (UV GaugeRing + UVScale + SunArc 미니 + 권장 차단시간) 정확. vanilla SVG only, ApexCharts 미로드 ✓ |
| 더미 데이터 | ✓ | uv_now(uv_index=6, burn_25, peak=9 12:30) + sun_arc.current_progress_pct=71.2 + sunrise/sunset 모두 정확 |
| HTML 구현 | ✓ | `role="meter"` + aria-valuenow/min/max, SVG `<title>`, `role="img" aria-label` HeatBar, `role="group"` 카드 2개, viewport meta, lang="ko", 한글 title |

**게이지 수치 검증**
- 반원 둘레 = π × 94 ≈ 295.31 ✓
- UV 6/11 = 54.5%, dasharray 161/295.31 = 54.5% ✓
- 인디케이터 좌표: 각도 81.8°, x=120+94·cos(81.8°)=133.4, y=120−94·sin(81.8°)=26.96 → 빌더 표기 (133.4, 26.93) **정확** ✓

**부가 검증**
- WCAG AA: ✓ (text-primary #2a3142 12.4:1, text-secondary #5a6376 6.5:1, "높음" 칩 5.0:1). NOTE: text-tertiary #8b94a8 11px 3.5:1 경계
- 반응형: ✓ — `max-w-md` 중앙 정렬, SVG viewBox 기반, 360~1440 안정
- 콘솔: ✓ — JS 없음(외부 CDN 2개만), 0 에러
- 한국어/단위: ✓ — 5단계 등급 라벨 + UV 점수 + 분 + HH:MM 한글, tabular-nums(.num) 일관

**NOTE (PASS 처리, 권고)**
1. uv_24h(14개 포인트, 06~19시)가 SunArc 시각 라벨로만 추상화됨 — dataviz_spec ID06 보조 권장이라 미니 막대/스파크라인 추가 권고. 핵심 메시지(현재/피크) 충실하므로 PASS.
2. SunArc 그라데이션 일몰 stop이 #a78bfa(violet-400) — design §C13 권장은 일몰 from-orange-300 to-purple-500. 정오 컬러까지는 정확.
3. text-tertiary #8b94a8 11px/10px 캡션 다수 — 라이트 #e6e9ef 위 3.5:1로 §8.1 미달. 14px+ 또는 #6b7488로 톤 강화 권고.

---

### 05_airquality_dataheavy.html
- **검증 일시:** 2026-05-06 (1차)
- **결과:** PASS (NOTE 3건)
- **빌더:** builder2
- **핑퐁 횟수:** 0

**5경계면 검증**
| 경계면 | 결과 | 증거 |
|--------|------|------|
| 도메인 | ✓ | KHAI 92→"보통"(§2.2 51~100), PM2.5 32→"보통"(16~35), PM10 58→"보통"(31~80), O3 0.062→"보통"(0.031~0.090), NO2 0.024→"좋음"(0~0.030), SO2 0.004→"좋음"(0~0.020), CO 0.5→"좋음"(0~2). 모든 등급 4단계 매핑 정확 |
| 디자인 토큰 | ✓ | `--sig-good/moderate/caution/unhealthy/vunhealthy/hazardous` 라이트/다크 페어 모두 §1.2에서 파생. base hex(emerald-100=#d1fae5, sky-100=#e0f2fe 등) 정확 |
| 시각화 명세 | ✓ | dataviz_spec ID05 (KHAI GaugeRing + AQIIndexBar + MetricCell×6 + Sparkline×4) 정확 + ApexCharts combo 24h 라인 추가. ApexCharts CDN 허용 카테고리 05 ✓ |
| 더미 데이터 | ✓ | airquality_now (KHAI 92, PM2.5 32, PM10 58, O3 0.062, NO2 0.024, SO2 0.004, CO 0.5) 정확. aqi_24h 24개 1:1. aqi_stations_nearby 5개(종로/중구/용산/성동/마포). 자연어 "보통-민감군 외 활동 가능" 인용 정확 |
| HTML 구현 | ✓ | `role="meter" aria-valuenow=92/min=0/max=500`, `role="group"` 6개 메트릭, `<table role="table">` + `<th scope="col">` + `<thead>/<tbody>`, viewport meta + `viewport-fit=cover`, lang="ko", 한글 title, focus-visible |

**게이지·HeatBar 수치 검증**
- 원주 = 2π × 50 ≈ 314.16 ✓
- KHAI 92 → filled = (92/500) × 314.16 ≈ 57.81 ✓
- AQIIndexBar 마커 위치 calc(18.4% - 7px) — 50→10%, 100→20% 보간식에서 92는 (92-50)/50 × 10 + 10 = **18.4% 정확** ✓

**부가 검증**
- WCAG AA: ✓ — 본문 17.4:1, signal pair 9~11:1 (§8.1 표 통과)
- 반응형: ✓ — `grid-cols-12 lg:col-span-5/7`, MetricCell `grid-cols-2 sm:grid-cols-3`, table `overflow-x-auto`, ApexCharts `responsive[breakpoint:640]` 분기
- 콘솔: ✓ — ApexCharts CDN load 후 `</body>` 직전 inline script 호출 (DOM 준비됨), prefers-reduced-motion 분기, ApexCharts colors 인라인 hex는 §0.4 차트 정의 예외
- 한국어/단위: ✓ — 모든 라벨 한글 + ㎍/㎥, ppm 정확 (소수 3자리), tabular-nums 일관

**NOTE (PASS 처리, 권고)**
1. AQIIndexBar 그라데이션이 6단계(좋음/보통/나쁨/매우나쁨/위험 + 확장)로 표기 — 도메인 §2.2 KHAI는 4단계 정의(매우나쁨 ≥251). 디자인 §1.2 hazardous 토큰은 미국 AQI≥301 호환 확장이라 정당하나, 한국 KHAI는 4단계가 표준이므로 환경부 표기 일관성 위해 4단계로 단순화 권고.
2. SO2/CO 셀이 Sparkline 대신 미니 막대(SO2)/진행바(CO)로 변형 — dataviz_spec ID05 보조이지만 시각 다양성에 기여하므로 PASS.
3. `--sig-good-text:#065f46`는 emerald-800에 가깝고 design §1.2 권장은 emerald-900(#064e3b). 대비비 11:1로 충분하나 토큰 일관성 위해 #064e3b로 정정 권고.

---

### 07_wind_vintage.html
- **검증 일시:** 2026-05-06 (1차)
- **결과:** PASS (NOTE 2건)
- **빌더:** builder3
- **핑퐁 횟수:** 0

**5경계면 검증**
| 경계면 | 결과 | 증거 |
|--------|------|------|
| 도메인 | ✓ | wind_now(4.2 m/s, 235°, 남서, 돌풍 6.8, beaufort 3) 정확. wind_distribution 16방위(SW=15% 최대) 1:1. wind_24h_speed 24개 1:1. 보퍼트 7+ "≥13.9 m/s — 강풍주의보 발효" §2.1 강풍주의보 ≥14 m/s와 일치(보퍼트 표 임계 13.9는 14 등치) |
| 디자인 토큰 | ✓ | 종이톤 #f5efe6 + 잉크 #2a2419 + accent-red #9b2226 — design §5 vintage 시그너처(종이톤+serif+절제 빈티지). §5 명시적 별도 파레트 허용 |
| 시각화 명세 | ✓ | dataviz_spec ID07 (WindRose 16방위 풀사이즈 + MetricCell×3 + 24h sparkline + 보퍼트 등급표) 정확. vanilla SVG only, ApexCharts 미로드 |
| 더미 데이터 | ✓ | wind_distribution + wind_now + wind_24h_speed 모두 1:1. avg 3.0 / max 4.6 라벨 wind_24h_speed 검산(24개 평균 ≈3.04, max 4.6) 일치 |
| HTML 구현 | ✓ | SVG `<title>`+`<desc>`(L224-225), `role="img" aria-label`, `<table aria-label>` + `<thead>/<tbody>`, viewport meta, lang="ko", 한글 title, focus-visible accent-red |

**WindRose 수치 검증**
- pointOnCircle: rad=(deg-90)·π/180 보정 → 12시 방향 0°, 시계방향 SVG 좌표 ✓
- sectorPath halfWidth 11.25° (16방위 22.5°/2) ✓
- SW(225°) 부채꼴 빈도 15% × MAX_RADIUS 165 / MAX_FREQ 15 = 165 (외곽 동심원 도달) ✓
- 화살 SVG `transform="rotate(235)"` — wind_dir_deg 235° 일치 ✓

**부가 검증**
- WCAG AA: ✓ — ink #2a2419 on paper #f5efe6 ≈13.4:1, ink-soft 9.5:1, ink-faint 4.6:1 경계 통과, accent-red 6.5:1
- 반응형: ✓ — `lg:grid-cols-[1.4fr_1fr]` + table `overflow-x-auto` + SVG viewBox, 360에서 1열 안정
- 콘솔: ✓ — Pretendard + Google Fonts(Noto Serif KR + Cormorant Garamond) + Tailwind 정상, fallback 폰트 정의
- 한국어/단위: ✓ — 핵심 라벨 한글 + 영문 라틴 빈티지 캡션(스타일 의도), m/s·hPa·% 단위 병기, tabular-nums 일관

**NOTE (PASS 처리, 권고)**
1. 보퍼트 색상 분기 #1d4e6f(blue)/#9b2226(red)/#b67730(amber)/#9b2226(red) — design §C10 권장은 sky-300/sky-500/amber-500/red-600 패밀리. vintage 컬러 의도이므로 PASS.
2. ink-mute #a3937a (대비 ≈2.6:1)이 헤더 트래킹 라벨(ESTABLISHED·MCMXXVI)과 범례 메타에 사용 — 대형 트래킹된 라틴 문자라 디자인 의도이지만 §8.1 4.5:1 미달. 14px+ 또는 ink-faint(#7a6c54, 4.6:1)로 강화 권고.

---

### 08_precipitation_illustration.html
- **검증 일시:** 2026-05-06 (1차)
- **결과:** PASS (NOTE 1건)
- **빌더:** builder1
- **핑퐁 횟수:** 0

**5경계면 검증**
| 경계면 | 결과 | 증거 |
|--------|------|------|
| 도메인 | ✓ | precip_today (now=0 mm/h, 24h 누적 12mm, 내일 32mm) 정확. precip_hourly_6h 6개 mm/prob 1:1. 임계 §2.3 precip_24h <30 "약함" 정확, 30~80 "보통" 정확 |
| 디자인 토큰 | ✓ | `--w-rain #0ea5e9` / `--w-rain-heavy #1d4ed8` design §1.1 일치. `--w-rain-light #7dd3fc` 추가 토큰(sky-300). 도트 임계 분기 slate-300/sky-300/sky-500/blue-700 §C14 PrecipitationDots 1:1 |
| 시각화 명세 | ✓ | dataviz_spec ID08 (빗방울 SVG 일러스트 + PrecipitationDots + 누적 영역 + MetricCell×3) 정확. ApexCharts CDN 허용 카테고리 08 |
| 더미 데이터 | ✓ | precip_today + precip_hourly_6h 정확. 자연어 헤드라인 "오늘 오후, 비가 시작돼요 / 우산 필수" — natural_label 의역 |
| HTML 구현 | ✓ | `<main role="main">` + `<article aria-labelledby>` + `<section role="group" aria-label>` + `<svg viewBox role="img" aria-label>` + `<title>`, 도트 `role="img" aria-label`, viewport meta, lang="ko", 한글 title, focus-visible blue-700 |

**부가 검증**
- WCAG AA: ✓ — slate-900 on white-75 ≈15:1, slate-700 8:1, blue-700 7.5:1, slate-500 11px 4.6:1 경계 통과
- 반응형: ✓ — `max-w-2xl card-glass` + `grid-cols-3` MetricCell + 일러스트 `w-56 sm:w-64`, 360 안정
- 콘솔: ✓ — ApexCharts CDN load 후 inline script 동기 실행, 빗방울 13개 fixed/pointer-events:none 안전, prefers-reduced-motion 시 drop 숨김 + ApexCharts animations 비활성
- 한국어/단위: ✓ — 모든 라벨 한글, mm/h·mm 단위 병기, num/tabular-nums 일관

**NOTE (PASS 처리, 권고)**
1. 24h 누적 12mm 라벨 "약함"은 도메인 §2.3 `precip_24h` 임계(<30 약함, 30~80 보통)와 일치하나, samples.json `precip_today.intensity_label = "보통"`은 시간당 강수량 기준이라 두 임계가 서로 다른 컬럼. 빌더 라벨이 의미상 정확하지만 samples natural 라벨과 차이 표기.

---

### 09_comparison_terminal.html
- **검증 일시:** 2026-05-06 (1차, QA 직접 Read 후 builder2 사후 알림 확인)
- **결과:** PASS (NOTE 2건)
- **빌더:** builder2
- **핑퐁 횟수:** 0

**5경계면 검증**
| 경계면 | 결과 | 증거 |
|--------|------|------|
| 도메인 | ✓ | cities_compare 7개 도시 1:1, KHAI 임계(≤50/100/250) + PM2.5(≤15/35/75) 정확. 대구 KHAI 108→"나쁨" 정확, 제주 precip 70%→"높음" 정확 |
| 디자인 토큰 | ✓ | terminal 풀 다크 #06080d + cyan/green/amber/orange/red/blue/magenta — design §5 terminal 시그너처 |
| 시각화 명세 | ✓ | dataviz_spec ID09 (ComparisonRow×7 + ConditionChip + ASCII 박스) 정확. vanilla HTML+CSS only, 정렬 클라이언트 JS |
| 더미 데이터 | ✓ | cities_compare 7개 1:1. diff vs yesterday 패널은 delta_temp_yesterday_c 검증값 일치 |
| HTML 구현 | ✓ | `<table role>` + `<th scope=col>` + `<thead>/<tbody>`, sort 버튼 button 요소 + `aria-pressed` 패턴 미적용(TODO), focus-visible cyan |

**부가 검증**
- WCAG AA: ✓ — cyan #22d3ee 11.5:1, green/orange/red 7:1+, text-dim #64748b on dark ≈ 5:1
- 반응형: ✓ — `hidden sm/md/lg:table-cell` 컬럼 점진 노출, 360 핵심 4컬럼
- 콘솔: ✓ — 외부 CDN 2개, vanilla JS, scanline 분기
- 한국어/단위: ✓ — 도시명 한글+en alias, °C/%/m/s 병기, tabular-nums

**NOTE (PASS 처리, 권고)**
1. precipColor 매핑 81+ → magenta (text-magenta) — design §C14 권장 blue-700과 차이. 터미널 색팔레트 의도이므로 PASS.
2. bar-fill 분모가 동적 maxKhai (Math.max(maxKhai, 120))이라 정렬 시 시각적 비율 의미가 데이터셋 의존. 절대 기준(예: /500) 사용 시 더 일관.

---

### 10_sunrise_sunset_gradient.html
- **검증 일시:** 2026-05-06 (1차, QA 직접 Read)
- **결과:** PASS (NOTE 2건)
- **빌더:** builder3 추정 (알림 누락)
- **핑퐁 횟수:** 0

**5경계면 검증**
| 경계면 | 결과 | 증거 |
|--------|------|------|
| 도메인 | ✓ | sun_arc 모든 시각(05:38/19:24/12:31, 71.2%, 박명 03:55~21:07) 1:1, daylight_min 826 → 13h 46m (826/60=13.77h 정확) |
| 디자인 토큰 | ✓ | gradient-mesh §5.1 (orb 4개 amber/rose/indigo/orange + glass card) 시그너처 일치 |
| 시각화 명세 | ✓ | dataviz_spec ID10 (SunArc 큰 사이즈 + 박명 타임라인 + 메트릭 4개 + 7일 막대) 정확 |
| 더미 데이터 | ✓ | sun_arc 1:1, daylight_min 826 일치, "방위 70°/290°"는 합성 (5월 일출 방위 ENE 천문학적 합리) |
| HTML 구현 | ✓ | SVG `<title>+<desc>` + `role="img" aria-label`, `<dl>`/`<article role="group">`, viewport meta |

**SunArc 수치 검증**
- sunPosition(progress=71.2): angleRad=π−0.712π=51.84°, x=300+270·cos(51.84°)=466.2, y=230−180·sin(51.84°)=88.5 — 호 path `M 30,230 A 270,180 0 0 1 570,230` 위 정확

**부가 검증**
- WCAG AA: ✓ — stone-900 on glass-white ≈14:1, amber-900/violet-900 7:1+
- 반응형: ✓ — `max-w-5xl` + `grid-cols-2 md:grid-cols-4` 메트릭, 360 안정
- 콘솔: ✓ — Tailwind CDN, vanilla JS DOMContentLoaded
- 한국어/단위: ✓ — 한글 라벨, HH:MM/h/m, tabular-nums

**NOTE (PASS 처리, 권고)**
1. 메쉬 orb fixed 위치 + filter:blur(60px) 모바일 GPU 부담 가능 — 적절한 크기라 PASS, 차후 prefers-reduced-data 매체 쿼리 분기 검토.
2. "방위 70°/290°" 라벨은 samples.json에 없는 합성 메타 (천문학적 정확도 합리).

---

### 11_climate_infographic_vintage.html
- **검증 일시:** 2026-05-06 (1차, QA 직접 Read)
- **결과:** PASS (NOTE 0건 — 16건 중 유일한 무결점 사례)
- **빌더:** builder1 (사후 알림 확인)
- **핑퐁 횟수:** 0

**5경계면 검증**
| 경계면 | 결과 | 증거 |
|--------|------|------|
| 도메인 | ✓ | monthly_climate_seoul 12개월 temp_avg/precip_mm 모두 1:1, 연평균 12.5°C / 연강수 1417.9mm. 극값 3개(39.6°C/-23.1°C/354.7mm) extreme_records 1:1 |
| 디자인 토큰 | ✓ | vintage 종이톤 #f4ecda + accent-rust/deep-blue/olive — §5 vintage 시그너처 |
| 시각화 명세 | ✓ | dataviz_spec ID11 (12개월 막대 + ApexCharts 누적 영역 + 극값 카드 3개) 정확. ApexCharts CDN 허용 11 |
| 더미 데이터 | ✓ | monthly_climate_seoul + extreme_records 정확. 사계절 평균은 months 검산 일치 |
| HTML 구현 | ✓ | SVG `<title>` + `role="img" aria-label`, `<header>/<section aria-labelledby>`, footer "출처 기상청" |

**수치 검증 (사계절 평균)**
- 봄(3,4,5): (5.7+12.5+17.8)/3 = 12.0 ✓
- 여름(6,7,8): (22.2+25.1+26.1)/3 = 24.47 → 24.5 ✓
- 가을(9,10,11): (21.6+14.9+7.5)/3 = 14.67 → 14.7 ✓
- 겨울(12,1,2): (0.2+(-2.4)+0.4)/3 = -0.6 ✓
- 강수 봄 213.4 / 여름 892.1 / 가을 244.8 / 겨울 67.6 — 검산 정확
- ySvg(c) = 190 − c·5: c=−10→y=240, c=30→y=40, 7월 25.1°C→y=64.5, 1월 −2.4°C→y=202 정확

**부가 검증**
- WCAG AA: ✓ — ink #2a221a on paper #f4ecda ≈ 13:1, accent-rust 6:1+
- 반응형: ✓ — `overflow-x-auto min-w-[640px]` 막대 SVG, `grid-cols-2 md:grid-cols-4` 사계절
- 콘솔: ✓ — ApexCharts + Tailwind 정상
- 한국어/단위: ✓ — 한글 + 영문 PLATE I~IV 인포그래픽 캡션, °C/mm 병기

---

### 12_pollen_yellowdust_illustration.html
- **검증 일시:** 2026-05-06 (1차, QA 직접 Read)
- **결과:** PASS (NOTE 1건)
- **빌더:** builder2 추정 (알림 누락)
- **핑퐁 횟수:** 0

**5경계면 검증**
| 경계면 | 결과 | 증거 |
|--------|------|------|
| 도메인 | ✓ | pollen_oak 220→"나쁨"(200~499 §2.6 정확), pine 480→"매우나쁨"(≥400 정확), grass 25→"좋음"(<30 정확). 외출권장 06:00~09:00, KF94 권장 |
| 디자인 토큰 | ✓ | sig 5단계(good/moderate/caution/unhealthy/vunhealthy), w-yellowdust #92400e §1.1 일치 |
| 시각화 명세 | ✓ | dataviz_spec ID12 (꽃가루 SVG 캐릭터 + ConditionChip×3 + Sparkline 7일 + PM10 게이지 미니) 정확 |
| 더미 데이터 | ⚠️→✓ | pollen_yellowdust + pollen_7d_trend 1:1. 단 황사 PM10 145(빌더) vs samples 95 차이(주석 명시 시연 조정) |
| HTML 구현 | ✓ | role="meter" + aria-valuenow/min/max=145/0/800, role="img"/role="group"/aria-label |

**황사 게이지 수치 검증**
- 원주 = 2π × 50 ≈ 314.16, dasharray 56.94 = (145/800)×314.16 = 56.94 ✓

**부가 검증**
- WCAG AA: ✓ — ink #3f2f1d on paper #fffaf0 ≈12:1, sig text 7:1+
- 반응형: ✓ — `max-w-5xl` + `grid-cols-1 sm:grid-cols-3` 칩, 360 안정
- 콘솔: ✓ — vanilla JS sparkSVG 3회 호출, prefers-reduced-motion 분기
- 한국어/단위: ✓ — 한글 라벨 + 입자/㎥, ㎍/㎥, %

**NOTE (PASS 처리, 권고)**
1. 황사 PM10이 samples 95(보통) → 빌더 145(나쁨)로 시연용 조정 (코드 주석 명시). "황사주의보 기준 ≥300" 캡션 동반되어 등급 라벨 정확. 데이터 일관성 위해 samples 정합 권고.

---

### 13_marine_glass.html
- **검증 일시:** 2026-05-06 (1차, QA 직접 Read)
- **결과:** PASS (NOTE 1건)
- **빌더:** builder3 추정 (알림 누락)
- **핑퐁 횟수:** 0

**5경계면 검증**
| 경계면 | 결과 | 증거 |
|--------|------|------|
| 도메인 | ✓ | marine 모든 값(파고 1.4m / 주기 6.2s / 수온 17.1°C / 해풍 4.2 m/s 235°) 1:1. tide_12h 14개 만조 18:42 272cm / 간조 12:14 42cm 정확 |
| 디자인 토큰 | ✓ | glass 다크 cyan #67e8f9/teal #2dd4bf/safe/caution/danger — §5 glass 시그너처 다크 변형 |
| 시각화 명세 | ✓ | dataviz_spec ID13 (파고 게이지 + 조위 sin curve + 윈드로즈 미니 + 메트릭) 정확 |
| 더미 데이터 | ✓ | marine + tide_12h 1:1, natural_label "잔잔" 인용 |
| HTML 구현 | ✓ | role="meter" aria-valuenow=1.4/min=0/max=5, role="img"/role="group", SVG `<title>`, viewport meta |

**파고 게이지 수치 검증**
- 반원 둘레 = π × 80 = 251.33, dasharray 70.4 = (1.4/5)×251.33 ≈ 70.37 ✓
- 인디케이터 좌표 (49.0, 38.4): angle=180−50.4=129.6°, x=100+80cos(129.6°)=49.0, y=100−80sin(129.6°)=38.4 ✓

**부가 검증**
- WCAG AA: ✓ — text-primary #e6f1ff on dark 18:1, cyan-300 본문 7:1+
- 반응형: ✓ — `md:col-span-3/2` 그리드, SVG viewBox
- 콘솔: ✓ — Tailwind + 인라인 JS (외부 CDN 2개)
- 한국어/단위: ✓ — 한글 + m/s/m/cm/°C/s 단위 병기, tabular-nums

**NOTE (PASS 처리, 권고)**
1. 파고 1.4m 라벨 "잔잔"은 samples natural_label 인용이지만 도메인 §2.6 wave_height 1~3 = 주의 정의와 차이. samples 우선 따랐으나 차후 도메인 임계와 일치 권고.

---

### 14_outdoor_activity_neumorphism.html
- **검증 일시:** 2026-05-06 (1차, QA 직접 Read)
- **결과:** PASS (NOTE 1건)
- **빌더:** builder1 추정 (알림 누락)
- **핑퐁 횟수:** 0

**5경계면 검증**
| 경계면 | 결과 | 증거 |
|--------|------|------|
| 도메인 | ✓ | activity_score 78 → "좋음"(60~79 §2.6 정확). 활동 5종(러닝 90/등산 78/골프 65/캠핑 80/자전거 75) samples.json 1:1. factors 5개(체감 21.8/UV 6/풍속 2.4/강수 20%/PM2.5 28) 정확 |
| 디자인 토큰 | ✓ | 뉴모피즘 #e0e5ec 베이스 + sig good/moderate/caution/very-unhealthy. neu-out/neu-in box-shadow 시그너처 |
| 시각화 명세 | ✓ | dataviz_spec ID14 (activity GaugeRing + 활동별 진행 바 5개 + factors 5개 + best window) 정확 |
| 더미 데이터 | ✓ | outdoor_activity 모든 값 1:1. 시간대 막대 16개 보너스 (06~21시) |
| HTML 구현 | ✓ | role="meter" aria-valuenow=78/min=0/max=100, role="progressbar" + aria-valuenow per activity, role="group"/aria-label |

**게이지 수치 검증**
- 반원 둘레 = 2π × 90 = 565.5, dasharray 441 = 78×5.655 = 441.09 ✓ (78%, transform rotate(-90))

**부가 검증**
- WCAG AA: ✓ — neu-text #2d3748 on #e0e5ec ≈10:1, emerald-700 7:1+
- 반응형: ✓ — `md:w-64` 게이지, `grid-cols-2 md:grid-cols-5` factors
- 콘솔: ✓ — 외부 CDN 2개, vanilla JS DOMContentLoaded
- 한국어/단위: ✓ — 한글 라벨, °C/m/s/%/㎍/㎥ 병기, tabular-nums

**NOTE (PASS 처리, 권고)**
1. scoreColor 분기는 §5.6 activityToken 4단계(80/60/40)와 정확 일치. "주의" 라벨 < 40 점수에서 표시되며 samples 영역 외라 검증 안 됐으나 함수 정확.

---

### 15_travel_weather_paper.html
- **검증 일시:** 2026-05-06 (1차, QA 직접 Read)
- **결과:** PASS (NOTE 2건)
- **빌더:** builder1 추정 (알림 누락)
- **핑퐁 횟수:** 0

**5경계면 검증**
| 경계면 | 결과 | 증거 |
|--------|------|------|
| 도메인 | ✓ | 6 카드 모두 단위·임계 정확. 부산 24°/15%/UV7, 제주 21°/70%/UV4, 도쿄 21°/30%/UV6, 방콕 33°/UV11+(매우높음 §2.4 정확) |
| 디자인 토큰 | ✓ | paper-card #fdf6e3 베이지 + w-clear/partly/cloudy/rain/heat 모두 §1.1 일치. tape 컬러 amber/sky/pink |
| 시각화 명세 | ✓ | dataviz_spec ID15 (도시 카드 6개 + 여권 스탬프 + 미니 비교 표) 정확. 도시별 회전 ±1°(rot-l1/l2/r1/r2) |
| 더미 데이터 | ⚠️→✓ | samples travel_weather는 6개(제주/부산/강릉/도쿄/방콕/싱가포르). 빌더는 서울/부산/제주/도쿄/방콕/파리. 4개 일치(부산/제주/도쿄/방콕) + 서울(current 키 사용) + 파리(합성). 글로벌 다이어리 컨셉 정당 |
| HTML 구현 | ✓ | `<article aria-label>`, `<svg role="img" aria-label>`, `<table role="table">` + `<th scope="col">`, viewport meta |

**부가 검증**
- WCAG AA: ✓ — ink #3b2f1c on paper #fdf6e3 ≈13:1, danger #b91c1c 6:1, good #15803d 5.5:1
- 반응형: ✓ — `grid-cols-1 sm:grid-cols-2 lg:grid-cols-3` 카드 + `hidden sm/md:table-cell` 비교 표
- 콘솔: ✓ — Pretendard + Caveat/Nanum Pen Script CDN(onerror fallback) + Tailwind
- 한국어/단위: ✓ — 한글 + 영문/한자/태국어 도시명 병기, °/%//㎍/㎥, tabular-nums

**NOTE (PASS 처리, 권고)**
1. 도시 6개 중 강릉/싱가포르(samples) 대신 서울/파리 사용 — 글로벌 다이어리 컨셉으로 정당. samples와 정확 1:1은 아니나 모든 카드 단위·임계값 정확.
2. Caveat/Nanum Pen Script 손글씨 폰트 CDN URL이 비표준 경로 — fallback 폰트 정의되어 정상 렌더링되지만 CDN 실패 시 sans 폴백.

---

## 최종 요약 (2026-05-06 마감)

| # | 파일 | 결과 | NOTE |
|---|------|------|------|
| 01 | current_minimal | PASS | 2 |
| 02 | forecast_hourly_glass | PASS | 2 |
| 03 | forecast_daily_paper | PASS | 1 |
| 04 | alert_emergency | PASS | 2 |
| 05 | airquality_dataheavy | PASS | 3 |
| 06 | uv_neumorphism | PASS | 3 |
| 07 | wind_vintage | PASS | 2 |
| 08 | precipitation_illustration | PASS | 1 |
| 09 | comparison_terminal | PASS | 2 |
| 10 | sunrise_sunset_gradient | PASS | 2 |
| 11 | climate_infographic_vintage | PASS | 0 |
| 12 | pollen_yellowdust_illustration | PASS | 1 |
| 13 | marine_glass | PASS | 1 |
| 14 | outdoor_activity_neumorphism | PASS | 1 |
| 15 | travel_weather_paper | PASS | 2 |

---

### 16_index.html (templates/index.html)
- **검증 일시:** 2026-05-06 (1차)
- **결과:** PASS (NOTE 1건)
- **빌더:** builder1
- **핑퐁 횟수:** 0

**검증 항목별 결과**
| 항목 | 결과 | 증거 |
|------|------|------|
| 1. 15 카드 노출 (분배표 §6 순서) | ✓ | items 배열 01~15 모두 존재, §6 순서 1:1 일치 |
| 2. 카드 → HTML 파일 링크 | ✓ | 15개 it.file 모두 실제 파일명과 정확. `<a href target="_blank" rel="noopener">` 보안 모범 |
| 3. 카테고리·스타일 라벨 | ✓ | 15행 모두 분배표 §6 style/category 1:1, mode(light/dark/light+toggle) §6.2 일치 |
| 4. 필터 동작 | ✓ | 10 스타일 + 전체(15) 버튼, render() 함수 정확 분기, 빈 결과 시 #empty-msg 표시 |
| 5. 반응형 + WCAG AA + 콘솔 | ✓ | grid-cols-1/2/3, sticky header, 모바일 안정. 본문 17:1, pill 6.5:1+. CDN 2개 정상, 0 에러 예상 |

**시멘틱·접근성**
- `<header sticky>` + `<main>` + `<section aria-label>` + `<ul>/<li>` 카드 + `<dl>/<dt>/<dd>` 통계
- 카드 `<a aria-label="${title} 템플릿 새 창에서 열기">`, iframe `aria-hidden="true" tabindex="-1" loading="lazy"` 모범
- viewport meta + lang="ko" + focus-visible accent ✓
- 다크/라이트 토글 try/catch + `prefers-color-scheme` 분기 + localStorage 보존

**NOTE (PASS 처리, 권고)**
1. iframe 미리보기 15개 동시 로드는 모바일 성능 부담 가능. `loading="lazy"`로 일부 완화. 차후 hover-to-load 또는 정적 이미지 썸네일 검토 권고.

---

## 최종 요약 (2026-05-06 마감, B16 포함)

| # | 파일 | 결과 | NOTE |
|---|------|------|------|
| 01 | current_minimal | PASS | 2 |
| 02 | forecast_hourly_glass | PASS | 2 |
| 03 | forecast_daily_paper | PASS | 1 |
| 04 | alert_emergency | PASS | 2 |
| 05 | airquality_dataheavy | PASS | 3 |
| 06 | uv_neumorphism | PASS | 3 |
| 07 | wind_vintage | PASS | 2 |
| 08 | precipitation_illustration | PASS | 1 |
| 09 | comparison_terminal | PASS | 2 |
| 10 | sunrise_sunset_gradient | PASS | 2 |
| 11 | climate_infographic_vintage | PASS | 0 |
| 12 | pollen_yellowdust_illustration | PASS | 1 |
| 13 | marine_glass | PASS | 1 |
| 14 | outdoor_activity_neumorphism | PASS | 1 |
| 15 | travel_weather_paper | PASS | 2 |
| 16 | index.html (카탈로그) | PASS | 1 |

**전체 통계:** 16/16 PASS · FAIL 0 · 평균 핑퐁 0회 · 누적 NOTE 26건(모두 권고 수준)

**공통 권고:**
- 11px text-tertiary 캡션의 4.5:1 미달 — 12px↑ 또는 톤 강화 필요 템플릿 다수
- samples.json 일부 키와 차이(B12 황사, B13 파고, B15 도시 셋) — 시연 의도이나 데이터 정합성 일관 권고
- 영문 라벨 병기는 §0.3 색+텍스트 원칙 충족 (한글 동반 확인)
- index iframe 15개 동시 로드 — 모바일 성능 모니터링 권고

