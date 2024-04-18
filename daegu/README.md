# 대구 교통사고 피해 예측 AI 경진대회
- https://dacon.io/competitions/official/236193/overview/description


## 결과
Private : 0.42698 / Public : 0.42661

## 작업 환경
- Mac
- VSCODE
- python version : 3.8.16

## 사용 모델
1. AutoML
    - Miljar
    - AutoGloun
2. Catboost 

[최종적으로 Miljar가 제일 좋은 점수를 획득]

## 진행 방식
1. 베이스라인으로 코드 공유의 전처리 참고하여 Catboost 사용
2. 제공 데이터 모두 사용(보안등, 어린이 보호구역, 주차장, CCTV정보)
3. EDA 및 데이터 전처리
    - 주소지 정규표현식 사용, 분리하여 각 데이터 끼리 합쳐줌 (코드공유 참고)
    ```python
        def parse(df):
        
        time_pattern = r'(\d{4})-(\d{1,2})-(\d{1,2}) (\d{1,2})' 
        df[['연', '월', '일', '시간']] = df['사고일시'].str.extract(time_pattern)
        df[['연', '월', '일', '시간']] = df[['연', '월', '일', '시간']].apply(pd.to_numeric) 
        df = df.drop(columns=['사고일시']) 
        
        location_pattern = r'(\S+) (\S+) (\S+)'

        df[['도시', '구', '동']] = df['시군구'].str.extract(location_pattern)
        df = df.drop(columns=['시군구'])
        
        road_pattern = r'(.+) - (.+)'
        df[['도로형태1', '도로형태2']] = df['도로형태'].str.extract(road_pattern)
        df = df.drop(columns=['도로형태'])

        return df

    ```
    - 파생 변수 추가(ex.노면상태 침수여부/ 주말여부)
    ```python
    train_df['노면상태_침수여부'] = train_df.apply(lambda x: 1 if (x['노면상태']=='침수')else 0, axis=1)
    train_df['주말여부'] = train_df.apply(lambda x : 1 if ((x['요일'] == '토요일')| (x['요일']=='일요일'))else 0, axis=1)
    test_df['노면상태_침수여부'] = test_df.apply(lambda x: 1 if (x['노면상태']=='침수')else 0, axis=1)
    test_df['주말여부'] = test_df.apply(lambda x : 1 if ((x['요일'] == '토요일')| (x['요일']=='일요일'))else 0, axis=1)
    ```
    자세한 부분은 코드 참고
4. 전처리 된 데이터로 AutoML 모델들 돌린 후 비교
    - Miljar 
        최고 점수 : 0.4266132906
    - AutoGloun 
        최고 점수 : 0.4356183729
5. AutoML 시간, 파라미터 수정 후 테스트


