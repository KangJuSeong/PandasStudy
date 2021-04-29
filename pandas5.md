## 데이터 사전 처리

### 1. 누락 데이터 처리
- 머신러닝 등 데이터 분석의 정확도는 분석 데이터의 품질에 의해 좌우된다.
- 데이터의 품질을 높이기 위해서는 누락 데이터, 중복 데이터 등 오류를 수정하고 분석 목적에 맞게 변형하는 과정이 필요.
- `DataFrame`에서는 누락된 데이터를 `NaN`으로 표시한다
- 머신러닝 분석 모형에 데이터를 입력하기 전에 반드시 누락 데이터를 제거하거나 다른 적절한 값으로 대체하는 과정이 필요.
- 누락 데이터가 많아지면 데이터의 품질이 떨어지고, 머신러닝 분석 알고리즘을 왜곡하는 현상 발생

#### 1. 누락 데이터 확인
- `column.value_counts(dropna=False)`를 이용하여 해당 column에 데이터 갯수를 볼수 있다 (dropna=False -> NaN 포함)
- `isnull()` and `notnull()`을 이용하여 NaN의 존재 여부를 확인할 수 있음.
```python
import pandas as pd
import seaborn as sns


df = sns.load_dataset('titanic')

# 누락 데이터 확인
# deck 열에 속하는 값들의 개수 출력 (dropna가 False라면 NaN의 개수도 출력)
nan_deck = df['deck'].value_counts(dropna=False)
# 해당 데이터가 NaN이면 True를 반환
df.head().isnull()
# 해당 데이터가 NaN이 아니면 True를 반환
df.head().notnull()
# NaN인 값이 모두 1이므로 합하면 NaN값의 개수를 알 수 있음(axis=0 이므로 열에서 계산)
df.isnull().sum(axis=0)
```

#### 2. 누락 데이터 제거
- 누락 데이터가 들어있는 열 또는 행을 삭제할 수 있음.
- 열을 삭제하면 분석 대상이 갖는 특성을 제거하고, 행을 삭제하면 분석 대상의 관측값을 제거함.
```python
# 열 제거
# deck 열에 NaN값이 688개 이므로 해당 열을 제외하는 것이 좋음 thresh=500(NaN이 500개 이상)
missing_df = df.isnull()
for col in missing_df.columns:
    missing_count = missing_df[col].value_counts()
    try:
        print(col, ': ', missing_count[True])
    except:
        print(col, ': ', 0)
# NaN의 개수가 500개 이상인 열을 제거        
df_thresh = df.dropna(axis=1, thresh=500)

# 행 제거
# age 데이터가 중요한 데이터라면 age가 NaN인 행을 제외하는 것이 좋음
# how=any 라면 NaN값이 하나라도 존재하는 행을 제거, how=all 이라면 모든 데이터가 NaN값일 때 해당 행을 제거
df_age = df_thresh.dropna(axis=0, subset=['age'], how='any')
```

#### 3. 누락 데이터 치환
- 데이터셋의 품지을 높일 목적으로 데이터를 무작정 제거한다면 어렵게 구한 데이터를 활용하지 못함.
- 데이터 품질도 중요하지만 양도 무시할 수 없음.
- 나머지 데이터를 최대한 살려서 누락된 데이터를 살릴 수 있음.
- 누락된 데이터를 대체할 때는 평균값, 최빈값, 이웃하는 값 등을 활용.
- `fillna()`메서드를 이용하여 편리하게 처리 가능
```python
import pandas as pd
import seaborn as sns


df = sns.load_dataset('titanic')

# NaN 값을 다른 데이터의 평균으로 변경하기
mean_age = df['age'].mean()  # 평균 구하기, NaN값은 제외
df['age'].fillna(mean_age, inplace=True)  # age 열에 존재하는 NaN 값들을 mean_age 로 변경

# NaN 값을 다른 데이터의 최빈값으로 변경하기
most_freq = df['embark_town'].value_counts(dropna=True).idxmax()  # 데이터 갯수 중 가장 많은 데이터 추출
df['embark_town'].fillna(most_freq, inplace=True)

# NaN 값을 이웃하고 있는 값으로 바꾸기
df['embark_town'].fillna(method='ffill', inplace=True)  # 바로 직전에 있는 데이터 값으로 변경, method=bfill 이라면 NaN이 있는 행 바로 다음 행 값으로 변경

# 누락 데이터가 NaN으로 표시 되어있지 않을 때
df.replace('특정값', np.nan, inplace=True)  # 특정값들이 NaN으로 변경됨
```

### 2. 중복 데이터 처리
- 하나의 데이터셋에서 동일한 관측값이 2개 이상 중복되는 경우 중복 데이터를 찾아서 삭제해야 함.
- 동일한 대상이 중복으로 존재하는 것이므로 분석 결과를 왜곡하기 때문이다.

#### 1. 중복 데이터 확인
- 동일한 관측값이 중복되는지 여부를 확인하기 위해 `duplicated()`메서드를 이용.
- 전에 나온 행들과 비교하여 중복되는 행이면 True 를 반환, 처음 나오는 행은 False 를 반환.
```python
import pandas as pd
import seaborn as sns


data = {'c1': [90, 90, 70],
        'c2': [100, 100, 50],
        'c3': [70, 70, 60],
        'c4': [30, 30, 10]}
index = ['r0', 'r1', 'r2']
df = pd.DataFrame(data=data, index=index, columns=columns)

# r0와 r1의 데이터가 같으므로 r1인덱스에서 True가 반환됨
df_dup = df.duplicated()
# 특정 열에서 중복값 찾기
col_dup = df['c2'].duplicated()  # c2열에서 중복되는 값을 가진 열에 True를 반환
```

#### 2. 중복 데이터 제거
- 중복 데이터를 제거하는 `drop_duplicates()`메서드가 존재.
- 중복되는 행을 제거하고 고유한 관측값을 가진 행들만 남김.
```python
# r0와 r1이 중복되므로 r1 행이 제거됨
df2 = df.drop_duplicates()
# c1과 c1 열이 중복되는 행들을 제거
df3 = df.drop_duplicates(subset=['c1', 'c2'])
```

### 3. 데이터 표준화
- 여러 곳에서 수집한 자료들은 단위 선택, 대소문자 구분, 약칭 활용 등 여러 가지 원인에 의해 다양한 형태료 표현됨.
- 동일한 대상을 표현하는 방법에 차이가 있으면, 분석의 정확도는 낮아짐. 따라서 데이터 포맷을 일관성 있게 표준화하는 작업이 필요.

#### 1. 단위 환산
- 같은 데이터셋 안에서 서로 다른 측정 단위를 사용한다면, 전체 데이터의 일관성 측면에서 문제가 발생.
- 측정 단위를 동일하게 맞추는 것이 필요.
```python
import pandas as pd
import seaborn as sns


df = pd.read_csv('auto-mpg.csv', header=None)

df.columns = ['mpg', 'cylinders', 'displacement', 'horsepower', 'weight',
              'acceleration', 'model year', 'origin', 'name']

# mpg_to_kpl을 이용하여 mpg를 kpl로 변환
mpg_to_kpl = 1.60934 / 3.78541

df['kpl'] = df['mpg'] * mpg_to_kpl
# 소수점 아래 둘째자리에서 반올림
df['kpl'] = df['kpl'].round(2)
```

#### 2. 자료형 변환
- `dtypes`속성을 사용하여 데이터프레임을 구성하는 각 열의 자료형을 확인.
- 숫자가 문자열로 저장된 경우에 숫자형으로 변환해줘야 함.
```python
import pandas as pd
import seaborn as sns
import numpy as np


df = pd.read_csv('auto-mpg.csv', header=None)

df.columns = ['mpg', 'cylinders', 'displacement', 'horsepower', 'weight',
              'acceleration', 'model year', 'origin', 'name']
# 각 열이 가지는 타입 출력
df.dtypes
# horsepower 열이 가지는 값 출력
df['horsepower'].unique()
# ? 값을 NaN 값으로 변경 후 NaN값을 가진 행 제거
df.replace('?', np.nan, inplace=True)
df.dropna(how='any', subset=['horsepower'], axis=0, inplace=True)
# 'float' 으로 타입 변환
df['horsepower'] = df['horsepower'].astype('float')
# 정수를 Object로 변환
df['origin'].replace({1: 'USA', 2: 'EU', 3: 'JPN'}, inplace=True)
# origin 내에서 같은 값들이 중복되서 나오므로 범주형 데이터로 표현하는것이 좋음
# 다시 Object로 바꾸려면 astype('str')
df['origin'] = df['origin'].astype('category')
```

### 4. 범주형 데이터 처리

#### 1. 구간 분할
- 데이터 분석 알고리즘에 따라서 연속 데이터를 그대로 사용하기 보다는 일정한 구간으로 나누어서 분석하는 것이 효율적인 경우 존재.
- 가격, 비용, 효율 등 연속적인 값을 일정한 수준이나 정도를 나타내는 이산적인 값으로 나타내어 구간별 차이를 드러내는 것.
- `cut()` 함수를 이용하여 연속 데이터를 여러 구간으로 나누고 범주형 데이터로 변환 가능.
```python
import pandas as pd
import seaborn as sns
import numpy as np


df = pd.read_csv('auto-mpg.csv', header=None)

df.columns = ['mpg', 'cylinders', 'displacement', 'horsepower', 'weight',
              'acceleration', 'model year', 'origin', 'name']
# 각 열이 가지는 타입 출력
df.dtypes
# horsepower 열이 가지는 값 출력
df['horsepower'].unique()
# ? 값을 NaN 값으로 변경 후 NaN값을 가진 행 제거
df.replace('?', np.nan, inplace=True)
df.dropna(how='any', subset=['horsepower'], axis=0, inplace=True)
# float으로 타입 변환
df['horsepower'] = df['horsepower'].astype('float')
# bins를 통해 구간의 개수를 정해주고 numpy의 histogram 함수를 이용하여 각 구간을 구분할 경계값을 구함
count, bin_dividers = np.histogram(df['horsepower'], bins=3)
# 각 구간의 명칭
bin_names = ['저출력', '보통출력', '고출력']
df['hp_bins'] = pd.cut(x=df['horsepower'],   # 데이터 배열
                       bins=bin_dividers,    # 구간의 경계 값 리스트
                       labels=bin_names,     # 구간의 이름
                       include_lowest=True)  # 첫 경계값 포함 여부
```

#### 2. 더미 변수
- 카테고리를 나타내는 범주형 데이터를 회귀분석 등 머신러닝 알고리즘에 바로 사용할 수 없는 경우가 있는데, 컴퓨터가 인식 가능한 값으로 변환해야 함.
- 숫자 0 또는 1 로 표현되는 더미 변수를 사용해야함.
- 해당 특성이 존재하면 1로 표현하고, 존재하지 않으면 0으로 구분.
- 숫자 0과 1로만 구성되는 원핫벡터로 변환한다고 해서 원핫인코딩이라고도 부름.
- `get_dummies()` 함수를 사용하면, 범주형 변수의 모든 고유값을 각각 새로운 더미 변수로 변환.
```python
# 저출력, 보통출력, 고출력에 대한 값이 0 또는 1로 변환
horsepower_dummies = pd.get_dummies(df['hp_bins'])
```

### 5. 정규화
- 각 변수에 들어있는 숫자 데이터의 상대적 크기 차이 때문에 머신러닝 분석 결과가 달라질 수 있음.
- 숫자 데이터의 상대적인 크기 차이를 제거할 필요가 있으므로 각 열에 속하는 데이터 값을 동일한 크기 기준으로 나눈 비율을 나타내는 것.
- 정규화 과정을 거친 데이터의 범위는 0~1 또는 -1~1이 됨.
```python
# 각 열에 데이터를 해당 열의 최대값(최대값의 절대값)으로 나누는 방법
df.horsepower = df.horsepower/abs(df.horsepower.max())
# 각 열의 데이터 중에서 최대값과 최소값을 뺀 값으로 나누는 방법
min_x = df.horsepower - df.horsepower.min()
min_max = df.horsepower.max() - df.horseposer.min()
df.horsepower = min_x / min_max
```
### 6. 시계열 데이터
- 주식, 환율 등 금융 데이터를 다루기 위해 시계열 데이터가 존재.
- 시계열 데이터를 `DataFrame`의 행 인덱스로 사용하면, 시간으로 기록된 데이터를 분석하는 것이 매우 편리.
- 가장 크게 특정한 시점을 기록하는 `Timestamp`와 두 시점 사이의 일정한 기간을 나타내는 `Period`가 존재.

#### 1. 다른 자료형을 시계열 객체로 변환
- 많은 시간 데이터들은 별도의 시간 자료형으로 기록되지 않고, 문자열 또는 숫자로 저장되는 경우가 많음.
- 다른 자료형으로 저장된 시간 데이터를 `Pandas` 시계열 객체인 `Timestamp`로 변환하는 함수 제공.
```python
import pandas as pd


df = pd.read_csv('stock-data.csv')
# to_datetime() 함수를 사용하여 문자열을 Timestamp 형으로 나타내기
df['new_Date'] = pd.to_datetime(df['Date'])
# new_Date 열을 index로 지정하고 기존 Date 열은 제거
df.set_index('new_Date', inplace=True)
df.drop('Date', axis=1, inplace=True)

# dates 배열을 판다스의 Timestamp로 변환
dates = ['2019-01-01', '2020-03-01', '2021-06-01']
ts_dates = pd.to_datetime(dates)
# Timestapm를 Period로 변환
# freq의 종류 => (D: 1일, W: 1주, M: 월말, MS: 월초, Q: 분기말, QS: 분기초, A: 연말, AS: 연초, B: 휴일 제외, H: 1시간, T: 1분, S: 1초)
pr_day = ts_dates.to_period(freq='D')
pr_month = ts_dates.to_period(freq='M')
pr_year = ts_dates.to_period(freq='A')
```
#### 2. 시계열 데이터 만들기
- `Pandas`의 `date_range()` 함수를 사용하면 여러 개의 날짜가 들어 있는 배열 형태의 시계열 데이터를 만들 수 있음.
- `Pandas`의 `period_range()` 함수를 사용하면 여러 개의 기간이 들어있는 배열 형태의 시계열 데이터를 만들 수 있음.
```python
import pandas as pd


ts_ms = pd.date_range(start='2019-01-01',  # 날짜 범위 시작
                      end=None,            # 날짜 범위 끝
                      periods=6,           # 생성할 Timestamp 개수
                      freq='MS',           # 시간 간격(월 초)
                      tz='Asia/Seoul')     # 시간대(timezone)

pr_m = pd.period_range(start='2019-01-01',  # 날짜 범위 시작
                       end=None,            # 날짜 범위 끝
                       periods=3,           # 생성할 Period 개수
                       freq='M')            # 기간의 길이
```

#### 3. 시계열 데이터 활용
- 날짜 데이터 분리하여 추출 가능.
- 날짜를 인덱스로 지정하면 인덱싱과 슬라이싱이 편리해 짐.
```python
import pandas as pd


df = pd.read_csv('stock-data.csv')

df['new_Date'] = pd.to_datetime(df['Date'])

# dt를 이용하여 각각의 연, 월, 일을 개별 추출 해낼 수 있음
df['Year'] = df['new_Date'].dt.year
df['Month'] = df['new_Date'].dt.month
df['Day'] = df['new_Date'].dt.day

# Timestamp를 Period로 변환 후 연, 월 또는 연도를 추출
df['Date_yr'] = df['new_Date'].dt.to_period(freq='A')
df['Date_m'] = df['new_Date'].dt.to_period(freq='M')

# 날짜 인덱싱과 슬라이싱
df.set_index('new_Date', inplace=True)
# 2018-07 이 속한 행 가져오기
df_y = df.loc['2018-07']
# 2018-07 행에서 Start와 High 열의 값 가져오기
df_ym_cols = df.loc['2018-07', 'Start':'High']
# 2018-06-25 부터 2018-06-20 사이의 행들 가져오기
df_ymd_range = df.loc['2018-06-25':'2018-06-20']

# 두 날짜 사이의 간격을 index로 하기
today = pd.to_datetime('2018-12-25')  # 오늘 날짜
df['time_delta'] = today - df.index  # 기존 데이터의 날짜와 오늘 날짜의 차이값
df.set_index('time_delta', inplace=True)  # 차이값을 index로 설정
df_180 = df['180 days':'189 days']  # 오늘 날짜 기준으로 지난 일수가 index가 되어 지난 일수를 통해 인덱싱
```