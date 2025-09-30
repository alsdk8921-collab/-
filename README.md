# 펀드 비중 데이터 전처리
> 컨설팅1실 해외팀 이민아

## 프로젝트 개요
> 운용사에서 받은 펀드 비중 엑셀 파일을 채권형/주식형 자산으로 분류하여 표준화된 형식으로 변환하는 데이터 전처리 파이프라인입니다.

## 주요 기능
### 1. 데이터 로딩 및 시트 처리
- 대상 파일: `1년치 취합.xlsx`
- 처리 기간: 2023년 7월 ~ 2025년 6월 (sheet명: 202307 ~ 202506)
- 각 월별 시트에서 필요한 데이터만 수집하기 위하여
> 1) 당월 데이터: 빈 행을 기준으로 슬라이싱
> 2) 채권형/주식형 분리: 빈 컬럼을 기준으로 슬라이싱

### 2. 채권형 데이터 처리
- 원본 데이터(20xxxx_채권)에서 펀드코드별로 데이터 추출
  
| |KRL223106106| |
|--|--|--|
|0-3Y|값|값|
|Emerging|값|값|
|1|값|국가1|
|2|값|국가2|
|3|값|국가3|

- 다음과 같이 2개 컬럼을 묶어서 처리(펀드코드 + 비중)
- 첫번째 컬럼이 String이면 홀수 컬럼에 해당하는 값을 가져옴
- 그 외(정수)라면 짝수 컬럼에 해당하는 값을 비중으로 가져옴
- 이후 카테고리 매핑을 통한 자산 분류

#### 자산 분류 체계
> - **Fixed Income_만기**: 0-3Y, 3-5Y, 5-10Y, >10Y
> - **Fixed Income_신용등급**: AAA, AA, A, BBB, <BBB, NR
> - **Fixed Income_섹터**: Treasury, Government, Corporate, Securitized
> - **Fixed Income_Region1**: Developed, Emerging
> - **Fixed Income_Region2**: North America, Europe, Japan, Asia Pacific 등
> - **Fixed Income_Country**: 60개 이상의 국가별 분류

> 💡**Japan: 권역과 국가에 동일명으로 적용되어 있어 사후처리**
> - 동일 펀드에서 Japan 비중의 첫번째 행은 'Fixed Index_Region2'로 값 변경

### 3. 주식형 데이터 처리
- 원본 데이터(20xxxx_주식)에서 펀드코드별로 데이터 추출
  
| |KRL301106105| |
|--|--|--|
|Consumer Discretionary|값|값|
|Europe|값|값|
|Developed|값|값|
|1|값|국가1|
|2|값|국가2|
|3|값|국가3|

- 처리 방식은 채권형과 동일
- 비중 정보가 0 또는 N/A인 행 제거

#### 자산 분류 체계
> - **Equity_Sector**: Consumer Discretionary, Consumer Staples, Energy, Financials, Health Care,
> Industrials, IT, Materials, Communication Services, Utilities, Real Estate
> - **Equity_Region1**: Developed, Emerging
> - **Equity_Region2**: Africa/Middle East, Asia Pacific, Europe, Latin America, North America
> - **Equity_Country**: 60개 이상의 국가별 분류

> 💡**Japan: 권역과 국가에 동일명으로 적용되어 있어 사후처리**
> - 동일 펀드에서 Japan 비중의 첫번째 행은 'Equity_Region2'로 값 변경

> #### 💡Japan 데이터 중복 처리
> - Japan 비중이 권역 또는 국가 중 하나만 있는 경우
>   
>  :  행 복제 후 첫 번째는 `Equity_Region2`, 두 번째는 `Equity_Country`로 분류

>#### 💡동일 펀드 중복된 경우 처리
> 모든 컬럼이 일치하는 경우 중복 제거

### 최종 데이터 구조
<img width="953" height="89" alt="image" src="https://github.com/user-attachments/assets/62240e0b-d947-4099-b0bd-d8cf07da5427" />

### 출력 파일
- merged_df_채권
- merged_df_주식
- merged_all_final
- 각각 Excel 파일로 저장되어 보고/분석에 활용 가능


## 기대 효과
- 복잡한 원본 데이터 구조를 표준화하여 **가독성 향상**
- 반복적인 전처리 작업을 자동화하여 **업무 효율성 개선**
- 자산 구분 체계를 통일하여 **데이터 일관성 확보**
- 향후 보고 및 리스크 분석 업무에 **직접 활용 가능**


