# Weather UI Template Catalog

> 한국 기상 환경에 맞춘 **15종 날씨 UI 템플릿** + 인터랙티브 카탈로그.
> 단일 자체 완결 HTML로, 더블클릭만으로 즉시 렌더링됩니다.

[![License: Apache 2.0](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Templates](https://img.shields.io/badge/templates-15%2F15%20PASS-success)](./_workspace/05_qa_report.md)
[![Stack](https://img.shields.io/badge/stack-Tailwind%20CDN%20%2B%20SVG-38bdf8)](https://tailwindcss.com/)
[![No Build](https://img.shields.io/badge/build-none-lightgrey)](#)

🌐 **Live Demo:** https://revfactory.github.io/weather-ui/

---

## ✨ 특징

- **15가지 카테고리** × **10가지 시각 스타일** 의도적 다양화
- **단일 HTML 자체 완결** — Tailwind CDN + 인라인 SVG (일부 ApexCharts CDN). 빌드 불필요
- **한국 기상청·환경부 임계값** 디자인 토큰에 1:1 매핑 (PM2.5/KHAI/UV/특보 등급)
- **WCAG AA** 색대비 + 키보드 접근성 + ARIA + `prefers-reduced-motion`
- **반응형** 360px ~ 1440px + 다크/라이트 모드
- **다국어 라벨** 한국어 우선, 영문 식별자 병기

---

## 📦 카탈로그

| # | 카테고리 | 시각 스타일 | 모드 | 핵심 컴포넌트 |
|---|---------|-----------|------|------------|
| 01 | 현재 날씨 | minimal | light + toggle | WeatherIconCard, MetricCell×4 |
| 02 | 시간별 예보 | glass | dark | HourlyStrip, Sparkline, PrecipitationDots |
| 03 | 주간 예보 다이어리 | paper-card | light | DailyRow×7, ConditionChip |
| 04 | 호우경보 관제 | dark-pro | dark | AlertBanner(emergency), MapBadge |
| 05 | 대기질 대시보드 | data-heavy | light + toggle | KHAI GaugeRing, AQIIndexBar, MetricCell×6 |
| 06 | 자외선 지수 | neumorphism | light | UV GaugeRing, UVScale, SunArc |
| 07 | 풍향풍속 | vintage | light | WindRose 16방위, 보퍼트 등급 |
| 08 | 강수 (비 오는 오후) | illustration | light | 빗방울 SVG, PrecipitationDots |
| 09 | 도시 비교 | terminal | dark | ComparisonRow×7, ASCII 박스 |
| 10 | 일출 · 일몰 | gradient-mesh | light | SunArc 큰 호, 박명 타임라인 |
| 11 | 기후 아틀라스 | vintage | light | 12개월 막대, 평년 대비 라인 |
| 12 | 꽃가루 · 황사 | illustration | light | 꽃가루 캐릭터, ConditionChip×3 |
| 13 | 해양 (해운대) | glass | dark | 파고 게이지, 조위 곡선 |
| 14 | 야외활동 적합도 | neumorphism | light | 활동 점수 게이지, 진행 바 4개 |
| 15 | 여행지 날씨 | paper-card | light | 도시 카드 6개 (여권 스탬프) |

---

## 🚀 빠른 시작

```bash
git clone git@github.com:revfactory/weather-ui.git
cd weather-ui
open index.html      # macOS
xdg-open index.html  # Linux
start index.html     # Windows
```

또는 정적 호스팅이 필요하면:

```bash
npx serve .
# http://localhost:3000
```

빌드 단계, 패키지 설치, 환경 변수 — **모두 필요 없습니다.**

---

## 🗂️ 프로젝트 구조

```
weather-ui/
├── index.html                 # 카탈로그 페이지 (필터 + 다크 토글)
├── templates/                 # 15개 템플릿 HTML
│   ├── 01_current_minimal.html
│   ├── 02_forecast_hourly_glass.html
│   ├── ...
│   └── 15_travel_weather_paper.html
├── _workspace/                # 산출 과정 (감사 추적)
│   ├── 00_input/request.md            # 사용자 요청
│   ├── 01_domain_catalog.md           # 도메인 카탈로그 (15 카테고리)
│   ├── 02_design_system.md            # 디자인 시스템 (토큰 41 + 컴포넌트 14)
│   ├── 03_dataviz_spec.md             # 시각화 명세
│   ├── 03_dataviz_samples.json        # 더미 데이터
│   └── 05_qa_report.md                # QA 보고서 (16/16 PASS)
├── .nojekyll                  # GitHub Pages Jekyll 비활성화
├── CLAUDE.md                  # 하네스 정의 (재현/확장용)
├── LICENSE                    # Apache 2.0
└── README.md
```

---

## 🎨 디자인 시스템

### 시맨틱 컬러 토큰
- **기상 상태:** `--w-sunny`, `--w-cloudy`, `--w-rain`, `--w-snow`, `--w-fog`, `--w-storm`, `--w-heatwave`, `--w-coldwave`, `--w-dust` 외 18종
- **시그널 6단계:** `--sig-safe`, `--sig-moderate`, `--sig-caution`, `--sig-unhealthy`, `--sig-very-unhealthy`, `--sig-hazardous`
- **UV 5단계:** `--uv-low`, `--uv-moderate`, `--uv-high`, `--uv-very-high`, `--uv-extreme`

### 임계값 매핑 (한국 환경부 기준)
| 지표 | 좋음 | 보통 | 나쁨 | 매우나쁨 |
|------|------|------|------|---------|
| PM2.5 (㎍/㎥) | ≤ 15 | 16~35 | 36~75 | ≥ 76 |
| PM10 (㎍/㎥) | ≤ 30 | 31~80 | 81~150 | ≥ 151 |
| KHAI | 0~50 | 51~100 | 101~250 | ≥ 251 |

### 시각 스타일 풀
`minimal` · `glass` · `paper-card` · `dark-pro` · `data-heavy` · `neumorphism` · `vintage` · `illustration` · `terminal` · `gradient-mesh`

---

## 🛠️ 기술 스택

- **Tailwind CSS** v3 (CDN) — 디자인 토큰 시스템
- **Pretendard Variable** (CDN) — 한국어 최적 폰트
- **인라인 SVG** — 차트, 일러스트, 아이콘
- **ApexCharts** v3 (CDN, 일부 템플릿) — 복합 차트
- **Vanilla JS** — 필터·테마 토글
- **외부 빌드 도구 0개**

---

## 🤖 자동 생성 워크플로우

본 카탈로그는 **5인 에이전트 팀**(도메인 전문가 + UX 디자이너 + 데이터 시각화 전문가 + 프론트엔드 구현가 + QA)이 자동 협업하여 생성했습니다. 자세한 하네스 구성은 [`CLAUDE.md`](./CLAUDE.md) 와 `.claude/skills/weather-template-orchestrator/` 를 참고하세요.

| 단계 | 산출 | 담당 |
|-----|-----|------|
| 1. 도메인 분석 | `_workspace/01_domain_catalog.md` | weather-domain-expert |
| 2. 디자인 시스템 | `_workspace/02_design_system.md` | weather-ux-designer |
| 3. 시각화 명세 | `_workspace/03_dataviz_spec.md` + `.json` | weather-dataviz-specialist |
| 4. HTML 빌드 | `templates/*.html` (15개) | weather-frontend-implementer × 3 (병렬) |
| 5. QA 검증 | `_workspace/05_qa_report.md` (16/16 PASS) | weather-template-qa |

**검증 기준 (5경계면):** 도메인 임계값 / 디자인 토큰 / 시각화 명세 / 더미 데이터 / HTML 구현 + WCAG AA + 반응형 + 콘솔 에러 0 + 한국어/단위.

---

## 📊 QA 결과

```
전체: 16 / 16 PASS
FAIL: 0
평균 핑퐁: 0회
누적 NOTE: 26건 (모두 권고 수준)
```

상세는 [`_workspace/05_qa_report.md`](./_workspace/05_qa_report.md) 참고.

---

## 🔧 커스터마이징

각 템플릿은 자체 완결이라 단일 파일만 수정하면 됩니다:

1. **데이터 교체:** 각 HTML 상단 `<script>` 의 `data` 객체를 실제 API 응답으로 치환
2. **컬러 토큰 변경:** `:root { --w-sunny: ... }` 블록 수정 → 프로젝트 전체 일관성 유지
3. **카테고리 추가:** `_workspace/01_domain_catalog.md` 갱신 → 분배표(`02_design_system.md §6`)에 행 추가 → 새 HTML 빌드

---

## 🌍 GitHub Pages 배포

이미 `.nojekyll` 포함되어 있어 별도 설정 불필요:

1. 리포지토리 **Settings → Pages**
2. **Source:** `Deploy from a branch`
3. **Branch:** `main` / `/ (root)`
4. 1분 후 `https://revfactory.github.io/weather-ui/` 접근

---

## 📚 데이터 출처

- **기상 데이터:** 기상청(KMA) 기상특보 발표 기준
- **대기질:** 환경부 / [에어코리아](https://www.airkorea.or.kr/)
- **UV 지수:** WHO Global Solar UV Index
- **천문:** 한국천문연구원(KASI) 일출일몰
- **모든 더미 데이터는 시연 목적**이며 실제 관측값이 아닙니다.

---

## 🤝 기여

이슈와 PR 환영합니다. 새 카테고리·시각 스타일 제안 시 [`_workspace/01_domain_catalog.md`](./_workspace/01_domain_catalog.md) 와 [`_workspace/02_design_system.md`](./_workspace/02_design_system.md) 의 기존 패턴을 참고해 주세요.

---

## 📄 라이선스

Apache License 2.0 — 자유롭게 사용·수정·배포·상업적 이용 가능합니다.
출처 표기와 변경 사항 명시만 해주세요. 자세한 내용은 [`LICENSE`](./LICENSE) 참고.

```
Copyright 2026 revfactory

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0
```
