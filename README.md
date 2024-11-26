# 🌊 창원시 침수지역 예측 프로젝트

**지역 특성을 활용하여 침수 위험 지역을 예측하고 대비 방안을 마련하는 모델 개발 프로젝트**

---

## 📖 프로젝트 개요

본 프로젝트는 **2022 창원 빅데이터 공모전**을 위한 연구로, 침수 위험 지역을 예측하기 위한 머신러닝 모델을 개발했습니다. 이를 통해 재난 대응 체계를 강화하고, 침수로 인한 피해를 최소화하는 것을 목표로 합니다.

### 주요 배경
- **강수량 증가**: 2000년대 이후 한국의 연평균 강수량이 7.4% 증가.
- **자연재해 피해**: 최근 10년간 호우와 태풍으로 인한 피해가 전체 재해 피해의 99.96%를 차지.
- **대상 지역**:
  - 서울시 강남구 및 서초구
  - 경상남도 밀양
  - 제주도 서귀포와 제주시

---

## 🔑 데이터 활용 및 처리 과정

### 데이터 수집
- **지형 데이터**: 국가공간정보포털 제공 수치표고모델(DEM) 데이터 활용.
- **토지피복도**: 환경공간정보서비스의 지형 데이터를 기반으로 지역별 토지 활용 상태 수집.
- **강수량 데이터**: 기상청 ASOS API를 통해 2010년부터 2020년까지의 일별 데이터를 수집.
- **침수 데이터**: 공공데이터포털의 침수위선 데이터를 기반으로 침수 피해 이력 수집.

### QGIS 활용
1. **수치표고모델(DEM) 전처리**:
   - 수치지형도상의 등고선 데이터를 `shp` 파일로 저장 후 QGIS에 불러옴.
   - DEM 데이터와 좌표를 중첩시키고, 벡터 공간 처리 도구를 사용해 `DEM` 데이터를 벡터로 변환.
   - 변환된 데이터를 폴리곤화하여 고도 데이터를 추출.
2. **토지피복도 전처리**:
   - 원본 데이터를 QGIS로 시각화하고 One-Hot-Encoding 적용.
   - 데이터의 과다한 변수를 환경공간정보서비스 기준에 따라 7개의 그룹(건조지역, 농업지역, 산림 등)으로 축소.
3. **거리 데이터 처리**:
   - 하천, 저수지, 해안가와 같은 지리적 위치 데이터를 QGIS로 시각화.
   - 레이어 좌표계를 OpenStreetMap과 병합하여 최단 거리 데이터를 생성.
4. **결과 시각화**:
   - 예측된 침수 위험 지역을 지도 상에 시각화하여 이해도를 높임.

---

## 📊 주요 데이터 설명 및 처리

| **변수**         | **설명**                                                               | **처리 방법**                   |
|-------------------|----------------------------------------------------------------------|--------------------------------|
| 고도             | 특정 지역의 고도 정보를 90m 단위로 제공                               | DEM 데이터를 폴리곤화 후 추출   |
| 경사도           | 지형의 수직거리와 수평거리 비율                                       | DEM 데이터를 활용한 벡터 변환   |
| 습윤지수         | 지역의 수분 축적 정도                                                | 산림 데이터와 지역별 분석       |
| 토지피복도       | 토지의 용도(예: 건조지역, 농업지역, 초지 등)                           | QGIS 기반 그룹화 후 One-Hot-Encoding |
| 하천 거리        | 특정 지역과 하천 간 최단 거리                                          | QGIS로 거리 분석 수행           |
| 당일/전일 강수량 | 일별 행정구역별 강수량                                               | 기상청 API를 통한 데이터 수집   |
| 침수 피해        | 침수 피해 지역에 대한 과거 데이터                                     | 공공데이터포털 침수위선 활용     |

---

## 🤖 분석 및 모델링

### 데이터 전처리
1. **강수량 데이터 정의**:
   - 침수는 당일 폭우뿐만 아니라 전날 강수량에도 영향을 받는다고 가정.
   - 당일 강수량, 전일 강수량, 합계 강수량 데이터를 결합하여 사용.
2. **데이터 결합**:
   - 기준 데이터를 90m DEM 고도 데이터로 설정.
   - 각 위치별로 경사도, 습윤지수, 토지피복도, 거리 데이터, 강수량 데이터를 결합.
3. **범주형 데이터 처리**:
   - 토지피복도 데이터를 그룹화하여 7개의 속성값으로 축소.
4. **클래스 불균형 해결**:
   - 오버샘플링을 통해 침수 발생 데이터를 증강.
5. **스케일링**:
   - RobustScaler를 사용하여 이상치에 민감하지 않도록 처리.

### 모델 선정
- **사용된 모델**: LGBM, XGBoost, RandomForest, Logistic Regression, GBM.
- **평가지표**:
  - 정확도(Accuracy)
  - F1-Score.
- **최적 모델**: LightGBM (Accuracy: 99%, F1-Score: 0.66).

---

## 🎨 분석 결과

1. **LGBM 모델 성능**:
   - 정확도: 99%
   - F1-Score: 0.66.
2. **주요 변수 중요도**:
   - 고도, 습윤지수, 경사도가 침수 예측에 가장 중요한 영향을 미침.

---

## 🤔 기대효과 및 활용 방안

1. **침수 예·경보 시스템 구축**:
   - 침수 취약 지역의 사전 경고 제공.
2. **도시 개발 규제**:
   - 침수 위험 지역에서의 개발 억제 및 녹지 유지.
3. **정책 지원**:
   - 국가와 지자체의 방재 정책 수립 지원.
4. **효율적 자원 배치**:
   - 침수 예상 지역에 방재 시설을 우선적으로 배치.

---

## 📝 참고 문헌 및 데이터 출처
- [국가공간정보포털](http://data.nsdi.go.kr): 고도 및 지형 데이터
- [환경공간정보서비스](https://egis.me.go.kr): 토지피복도 데이터
- [산림빅데이터거래소](https://www.bigdata-forest.kr): 습윤지수 데이터
- [기상청 ASOS 서비스](https://data.kma.go.kr): 강수량 데이터
- [공공데이터포털](https://www.data.go.kr): 침수 이력 데이터
- 추가 문헌:
  - [서울시 침수피해의 자연 및 환경적 요인분석](https://www.kci.go.kr)
  - [창원시 용도지역별 침수 피해에 따른 위험등급화 분석](https://www.kais99.org)
  - [도시 유역 유출 변화 연구](https://koreascience.kr)
