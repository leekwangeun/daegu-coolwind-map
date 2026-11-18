# 대구 밤의 찬바람길 웹 시각화 페이지 (`web/`)

2026 대구광역시 기후, 환경, 탄소중립 공공데이터 AI 활용대회(부문 2 공공데이터 분석, 시각화) 제출용 인터랙티브 지도 및 분석 시각화 페이지입니다.

## 1. 바로 실행하는 방법

### 방법 A: 로컬 파일로 바로 열기 (`file://` 지원)
별도의 웹 서버 없이 파일 탐색기에서 `web/index.html` 파일을 더블클릭해 브라우저(Chrome, Edge, Whale 등)로 바로 열면 동작합니다.
- 로컬 `file://` 환경에서 브라우저가 `fetch()` 요청을 차단하는 문제를 피하기 위해, `export_web.py`가 모든 GeoJSON과 분석 결과 JSON을 `web/data/web_data.js` 스크립트 파일로도 함께 내보내며, 래스터 오버레이(`citizen_grade_epsg4326.png`, `cflow_epsg4326.png`)는 Leaflet `L.imageOverlay`로 직접 읽습니다.
- 배경지도 타일(OpenStreetMap)과 Leaflet 라이브러리(unpkg CDN)를 불러오므로 인터넷 연결이 되어 있으면 지도 배경이 함께 표시됩니다.

### 방법 B: 로컬 정적 웹 서버로 열기
프로젝트 루트 또는 `web/` 폴더에서 파이썬 내장 HTTP 서버를 실행해 확인할 수도 있습니다.

```bash
# 프로젝트 루트에서 실행할 때
python -I -m http.server 8000
# 브라우저에서 http://localhost:8000/web/ 접속
```

## 2. 데이터 갱신 방법

분석 결과(`pipeline/coolwind/02_analysis_results.json`, `pipeline/coolwind/outputs/`)를 바탕으로 웹 시각화용 데이터를 다시 생성하려면 프로젝트 루트에서 아래 명령을 실행합니다.

```bash
# (선택) 전체 분석을 처음부터 다시 실행할 때
python -I analysis/coolwind/run_analysis.py

# 웹 시각화 데이터(web/data/, web/assets/) 내보내기
python -I analysis/coolwind/export_web.py
```

## 3. 생성되는 파일 구성

- `analysis/coolwind/export_web.py`: `pipeline/coolwind/02_analysis_results.json`과 격자 캐시(`analysis/coolwind/cache/base_layers.npz`)를 읽어 웹 데이터를 내보내는 스크립트
- `web/index.html`: 한 페이지 인터랙티브 지도(Leaflet + OpenStreetMap), 5단계 스토리텔링, 데이터 출처 표, 방법 요약, 한계 설명
- `web/data/`
  - `citizen_grade_epsg4326.png`: 시민 체감 찬바람길 등급(1~5등급) EPSG:4326 투명 배경 PNG 오버레이
  - `cflow_epsg4326.png`: 찬 공기 흐름 간이 지표(C_flow, 0~100) EPSG:4326 투명 배경 PNG 오버레이
  - `raster_bounds.json`: 래스터 오버레이의 EPSG:4326 경계 좌표 및 범례 메타데이터
  - `courses.geojson`: 추천 야간 산책 코스 8개 선형(LineString)과 속성
  - `corridors.geojson`: 도심 유입 핵심 길목 상위 15곳 영역(Polygon/MultiPolygon), 중심 좌표, 상류 경로와 속성
  - `hotspots.geojson`: 행정 검토용 길목 막힘 위험 지점 상위 15곳(Point)과 속성
  - `stations.geojson`: 기상청 관측소 14곳(Point, 실측 7곳의 야간 기온 포함)과 속성
  - `waterways.geojson`: 주요 하천 선형(LineString)
  - `districts.geojson`: 대구 8개 구군 행정경계(Polygon/MultiPolygon)와 구군별 녹지 및 지표 요약 속성
  - `web_data.js`: 로컬 `file://` 실행을 위한 통합 데이터 번들(`window.COOLWIND_DATA`)
- `web/assets/`: `pipeline/coolwind/outputs/`의 그래프(`chart_01`~`chart_04`) 및 지도(`map_01`~`map_05`) 이미지 사본
