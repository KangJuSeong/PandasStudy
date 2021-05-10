## 데이터프레임의 다양한 응용

### 1. 함수 매핑
- 함수 매핑은 `Series` or `DataFrame`의 개별 원소를 특정 함수에 일대일 대응시키는 과정.
- 사용자가 직접 만든 함수(lambda 함수 등)를 적용할 수 있기 때문에 `Pandas` 기본 함수로 처리하기 어려운 복잡한 연산을 할 수 있게 해줌.

#### 1. 개별 원소에 함수 매핑 
- 시리즈 객체에 `apply()` 메소드를 적용하면 인자로 전달하는 매핑 함수에 시리즈의 모든 원소를 하나씩 입력하고 암수의 리턴값을 받음.
- 데이터프레임의 개별 원소에 특정 함수를 매핑하려면 `applymap()` 메소드를 활용.
```python
import seaborn as sns


def add_10(n):
    return n + 10


def add_two_obj(a, b):
    return a + b


titanic = sns.load_dataset('titanic')
df = titanic.loc[:, ['age', 'fare']]
df['ten'] = 10

# apply를 통해 기존 정의 함수 또는 람다 함수를 적용하여 리턴받을 수 있음
# 모든 수에 적용
sr1 = df['age'].apply(add_10)
sr2 = df['age'].apply(add_two_obj, b=10)
sr3 = df['age'].apply(lambda x: add_10(x))

# applymap을 이용하여 각각의 원소를 함수에 대입하여 리턴받을 수 있음
# 각 원소마다 적용
df_map = df.applymap(add_10)
```

#### 2. 시리즈 객체에 함수 매핑
- 데이터프레임의 각 열에 함수를 매핑하기 위해서는 `apply(axis=0)` 메소드를 적용하면 모든 열을 하나씩 분리하여 매핑 함수의 인자로 각 열이 전달됨.
- 각 열에 함수를 매핑할 때는 `apply(axis=1)` 메소드를 적용하면 됨.
```python
import seaborn as sns


def min_max(x):
    return x.max() - x.min()


titanic = sns.load_dataset('titanic')
df = titanic.loc[:, ['age', 'fare']]

# 각 열에 함수를 매핑
result = df.apply(min_max, axis=0)
# 각 행에 함수를 매핑
df['ten'] = 10
df['add'] = df.apply(lambda x: add_two_obj(x['age'], x['ten']), axis=1)
```

#### 3. 데이터프레임 객체에 함수 매핑
- 데이터프레임 객체를 함수에 매핑하려면 `pipe()` 메소드를 활용.
- 사용하는 함수가 반환하는 리턴값에 따라 `pipe()` 메소드가 반환하는 객체의 종류가 결정.
- 데이터프레임을 반환하는 경우, 시리즈를 반환하는 경우, 개별 값을 반환하는 경우로 나눌 수 있음.
```python
import seaborn as sns


def missing_value(x):
    return x.isnull()


def missing_count(x):
    return missing_value(x).sum()


def total_number_missing(x):
    return missing_count(x).sum()


titanic = sns.load_dataset('titanic')
df = titanic.loc[:, ['age', 'fare']]

result_df = df.pipe(missing_value)
result_series = df.pipe(missing_count)
result_total = df.pipe(total_number_missing)
```
### 2. 열 재구성

#### 1. 열 순서 변경
- 열 이름을 원하는 순서대로 정리해서 리스트를 만들고 데이터프레임에서 열을 다시 선택하는 방식으로 열 순서를 바꿀 수 있음.
```python
import seaborn as sns


titanic = sns.load_dataset('titanic')
df = titanic.loc[0:4, 'survived': 'age']
# 슬라이싱 한 열들의 값을 list로 만들기
columns = list(df.columns.values)
# 열 이름을 정렬하여 정렬된 열 순서로 DataFrame 생성
columns_sorted = sorted(columns)
df_sorted = df[columns_sorted]
# 열 이름의 역순으로 정렬하여 DataFrame 생성
columns_reversed = list(reversed(columns))
df_reversed = df[columns_reversed]
# 열 이름을 임의로 재배치하여 DataFrame 생성
columns_customed = ['pclass', 'sex', 'age', 'survived']
df_customed = df[columns_customed]
```

#### 2. 열 분리
- 하나의 열이 여러 가지 정보를 담고 있을 때 각 정보를 서로 분리해서 사용하는 경우가 존재.
- 어떤 열에 '연월일' 정보가 있을 때 '연', '월', '일'을 구분하여 3개의 열을 만드는 것이나, 사람의 이름이 들어있는 열을 '성'과 '이름'을 구분하는 것.
- `df['column_name'].str.split('')`을 이용하여 문자열 구분 후 `data.str.get(index)`로 구분한 열의 해당 인덱스 값들을 전부 가져올 수 있음.
```python
import pandas as pd


df = pd.read_excel('주가데이터.xlsx')
# '연월일' 열에서 해당 열에 타입을 string 으로 변환 후 '-'를 기준으로 쪼개서 시리즈 객체 저장
df['연월일'] = df['연월일'].astype('str')
dates = df['연월일'].str.split('-')
# 추출한 값들을 열로 추가
df['연'] = dates.str.get(0)  # dates 변수의 원소 리스트의 0번째 값들 모두 가져오기
df['월'] = dates.str.get(1)
df['일'] = dates.str.get(2)
```

### 3. 필터링
- `Series` or `DataFrame`의 데이터 중에서 특정 조건식을 만족하는 원소만 따로 추출하는 개념.

#### 1. 불린 인덱싱
- 어떤 조건식을 적용하면 각 원소에 대한 True/False 를 판별하여 Boolean 값으로 구성된 시리즈를 반환.
- 참에 해당하는 데이터 값을 따로 선택할 수 있는데, 많은 데이터 중에서 어떤 조건을 만족하는 데이터만을 추출하는 필터링 기법의 한 유형.
- 조건식을 적용하면 각 원소가 조건을 만족하는지 여부를 참과 거짓 값으로 표시하여 Boolean 시리즈를 만들 수 있음.
```python
import pandas as pd
import seaborn as sns


titanic = sns.load_dataset('titanic')
# 조건식 생성
mask1 = (titanic.age >= 10) & (titanic.age < 20)
mask2 = (titanic.age > 10) & (titanic.sex == 'female')
# | -> or, & -> and
mask3 = (titanci.age < 10) | (titanic.age >= 60)
df_teenage = titanic.loc[mask1, :]
df_female_under10 = titanic.loc[mask2, :]
# 원하는 열만 선택하여 추출 가능
df_under10_morethan60 = titanic.loc[mask3, ['age', 'sex', 'alone']]
```

#### 2. isin() 메소드 활용
- `DataFrame`의 열에 `isin()` 메소드를 적용하면 특정 값을 가진 행들을 따로 추출 가능.
```python
import pandas as pd
import seaborn as sns


pd.set_option('display.max_columns', 15)

titanic = sns.load_dataset('titanic')
mask1 = titanic['sibsp'] == 3
mask2 = titanic['sibsp'] == 4
mask3 = titanic['sibsp'] == 5
# 여러개의 조건식을 모두 사용
df_boolean = titanic[mask1 | mask2 | mask3]

# isin 메서드를 이용하여 조건식을 줄이고 메서드 내부에 조건을 넣어 주기
isin_filter = titanic['sibsp'].isin([3, 4, 5])
df_isin = titanic[isin_filter]
```

### 4. 데이터프레임 합치기
- 데이터가 여러 군데 나누어져 있을 때 하나로 합치거나 데이터를 연결해야 하는 경우에 사용.
- 대표적으로 `concat()` or `merge()` or `join()` 함수와 메소드 존재.

#### 1. 데이터프레임 연결
- 서로 다른 `DataFrame`들의 구성 형태와 속성이 균일하다면, 행 또는 열중에서 어느 한 방향으로 이어 붙여도 데이터의 일관성 유지 가능.
- 기존 `DataFrame`의 형태를 유지하면서 이어 붙이는 `concat()`함수를 활용.
- `axis=1` or `axis=0`을 이용하여 밑으로 합칠지 옆으로 합칠지 정할 수 있음.
```python
import pandas as pd
import seaborn as sns


df1 = pd.DataFrame({'a': ['a0', 'a1', 'a2', 'a3'],
                    'b': ['b0', 'b1', 'b2', 'b3'],
                    'c': ['c0', 'c1', 'c2', 'c3']},
                   index=[0, 1, 2, 3])
df2 = pd.DataFrame({'a': ['a2', 'a3', 'a4', 'a5'],
                    'b': ['b2', 'b3', 'b4', 'b5'],
                    'c': ['c2', 'c3', 'c4', 'c5'],
                    'd': ['d2', 'd3', 'd4', 'd5']},
                   index=[2, 3, 4, 5])
# df1에 df2를 밑으로 합치기(axis=0), ignore_index=True 이면 중복되는 인덱스 없이 새로운 인덱스로 추가
result1 = pd.concat([df1, df2], ignore_index=True)
# axis=1로 했기 때문에 df1에 옆으로 df2를 합침, join='inner' 옵션을 설정하면 행 인덱스의 교집합을 기준으로 사용, 같은 인덱스는 합쳐짐
result2 = pd.concat([df1, df2], axis=1)
# 모든 열에 대한 값이 존재하는 행만 출력
result3 = pd.concat([df1, df2], axis=1, join='inner')
```

#### 2. 데이터프레임 병합
- `merge()` 함수는 SQL의 `join`명령과 비슷한 방식으로 어떤 기준에 의해 두 `DataFrame`을 병합하는 개념.
- 기준이 되는 열이나 인덱스를 `Key`라고 부르고, `Key`가 되는 열이나 인덱스는 반드시 양쪽 `DataFrame`에 존재해야 함.
```python
import pandas as pd
import seaborn as sns


pd.set_option('display.max_columns', 10)
pd.set_option('display.max_colwidth', 20)
pd.set_option('display.unicode.east_asian_width', True)

df1 = pd.read_excel('stock price.xlsx')
df2 = pd.read_excel('stock valuation.xlsx')

# 두 데이터프레임을 merge()함수에 전달
# on=None(default) 이면 두 데이터프레임에 공통으로 속하는 모든 열을 기준으로 병합
# how='inner'(default) 이면 기준이 되는 열의 데이터가 양쪽 데이터프레임에 공통으로 존재하는 교집합일 경우에만 추출
merge_inner = pd.merge(df1, df2, on='id')
# on='id' 이면 id를 기준으로 병합한다는 뜻, how='outer'은 기준이 되는 id 열의 데이터가 데이터프레임 중 어느 한쪽에만 속하더라도 포함한다는 뜻
merge_outer = pd.merge(df1, df2, how='outer', on='id')
# how='left' 이면 왼쪽 데이터프레임의 키 열에 속하는 데이터 값을 기준으로 병합, left_on과 right_on을 이용하여 좌우 데이터프레임에 각각 다르게 키를 지정할 수 있음
merge_left = pd.merge(df1, df2, how='left', left_on='stock_name', right_on='name')
merge_right = pd.merge(df1, df2, how='right', left_on='stock_name', right_on='name')
# 조건에 맞는 데이터프레임 추출 후 merge 가능
price = df1[df1['price'] < 50000]
value = pd.merge(price, df2)
```

#### 3. 데이터프레임 결합
- `merge()`를 기반으로 `join()` 메소드가 만들어 졌지만 `join()`은 두 `DataFrame`의 행 인덱스를 기준으로 결합하는 점에서 차이가 존재.
- `on=keys`옵션을 설정하여 행 인덱스 대신 다른 열을 기준으로 결합하는 것이 가능.
```python
import pandas as pd
import seaborn as sns


pd.set_option('display.max_columns', 10)
pd.set_option('display.max_colwidth', 20)
pd.set_option('display.unicode.east_asian_width', True)

df1 = pd.read_excel('stock price.xlsx', index_col='id')
df2 = pd.read_excel('stock valuation.xlsx', index_col='id')

# 데이터프레임 결합, default로 how='left'
df3 = df1.join(df2)
# how='inner'로 적용하여 두 데이터프레임에 공통으로 존재하는 행 인덱스를 기준으로 추출
df4 = df1.join(df2, how='inner')
```

### 5. 그룹 연산
- 복잡한 데이터를 어떤 기준에 따라 여러 그룹으로 나눠서 관찰하는 것도 좋은 방법.
- 특정 기준을 적용하여 몇 개의 그룹으로 분할하여 처리하는 것을 그룹 연산이라고 함.
- 그룹 연산은 데이터를 집계, 변환, 필터링하는데 효율적.
> 1단계(분할) : 데이터를 특정 조건에 의해 분할   
> 2단계(적용) : 데이터를 집계, 변환, 필터링하는데 필요한 메소드 적용  
> 3단계(결합) : 2단계의 처리 결과를 하나로 합침

#### 1. 그룹 객체 만들기(분할 단계)
- `groupby()`메소드는 `DataFrame`의 특정 열을 기준으로 분할하여 그룹 객체를 반환.
- 기준이 되는 열은 1개 or 여러 열을 리스트로 입력 가능.
```python
import pandas as pd
import seaborn as sns


pd.set_option('display.max_columns', 10)
pd.set_option('display.max_colwidth', 20)
pd.set_option('display.unicode.east_asian_width', True)

titanic = sns.load_dataset('titanic')
df = titanic.loc[:, ['age', 'sex', 'class', 'fare', 'survived']]

# class를 기준으로 그룹화
grouped = df.groupby(['class'])
for key, group in grouped:
    print("* key :", key)
    print("* number :", len(group))
    print(group.head())
    print('\n')

# 연산 메소드 적용, 그룹별 연산 적용
average = grouped.mean()

# 개별 그룹 선택
group3 = grouped.get_group('Third')

# 여러 열을 기준으로 그릅화
grouped_two = df.groupby(['class', 'sex'])

for key, group in grouped_two:
    print("* key :", key)
    print("* number :", len(group))
    print(group.head())
    print('\n')

# 멀티 인덱스를 이용하여 특정 그룹 추출
group3f = grouped_two.get_group(('Third', 'female'))
```

#### 2. 그룹 연산 메소드(적용-결합 단계)
- 각 그룹에 데이터들을 집계할 수 있음.
- 각 그룹의 연산의 결과를 원본 `DataFrame`과 같은 형태로 변형하여 정리하는 것이 가능.
- `agg()` 메소드를 이용하여 집계 연산을 처리하는 사용자 정의 함수를 그룹 객체에 적용 가능.  
- 그룹 객체에 `filter()` 메소드를 적용하여 조건이 참인 그룹만 추출 가능.
- 그룹 객체에 `apply()` 메소드를 이용하여 개별 원소를 특정 함수에 일대일로 매핑이 가능.
```python
import pandas as pd
import seaborn as sns


pd.set_option('display.max_columns', 10)
pd.set_option('display.max_colwidth', 20)
pd.set_option('display.unicode.east_asian_width', True)

titanic = sns.load_dataset('titanic')
df = titanic.loc[:, ['age', 'sex', 'class', 'fare', 'survived']]

# class를 기준으로 그룹화
grouped = df.groupby(['class'])

# 각 그룹에 대한 모든 열의 표준편차를 집계하여 데이터프레임으로 변환
std_all = grouped.std()
# 선택한 열에 대한 연산
std_fare = grouped.fare.std()
# agg() 메소드를 이용한 데이터 집계
def min_max(x):
    return x.max() - x.min()

agg_minmax = grouped.agg(min_max)

# 여러 함수를 각 열에 동일하게 적용하여 집계
agg_all = grouped.agg(['min', 'max'])
agg_sep = grouped.agg({'fare': ['min', 'max'], 'age': 'mean'})

# 그룹 연산 데이터 변환
age_mean = grouped.age.mean()
age_std = grouped.age.std()

for key, group in grouped.age:
    group_zscore = (group - age_mean.loc[key]) / age_std.loc[key]
    print('* origin :', key)
    print(group_zscore.head(3))
    print('\n')

# transform() 메소드를 사용하여 데이터 변환
def z_score(x):
    return (x - x.mean()) / x.std()

age_zscore = grouped.age.transform(z_score)
print(age_zscore.loc[[1, 9, 0]])
print(len(age_zscore))
print(age_zscore.loc[0:9])

# 그룹 객체 필터링
# 그룹의 데이터가 200개 이상인 그룹의 데이터만 출력
grouped_filter = grouped.filter(lambda x: len(x) >= 200)
print(grouped_filter.head())
# 그룹의 데이터의 age 평균이 30보다 작은 그룹의 데이터들만 출력
age_filter = grouped.filter(lambda x: x.age.mean() < 30)
print(age_filter.head())

# 그룹 객체에 함수 매핑
# 각 그룹별 describe() 함수 실행
agg_grouped = grouped.apply(lambda x: x.describe())
print(agg_grouped)
# z-score를 계산하는 함수 적용
age_zscore = grouped.age.apply(z_score)
print(age_zscore.head())

# filter에서 해당되지 않는 그룹은 false 해당되면 true로 저장
age_filter = grouped.apply(lambda x: x.age.mean() < 30)
print(age_filter)
for x in age_filter.index:
    if age_filter[x]:
        age_filter_df = grouped.get_group(x)
        print(age_filter_df.head())
```

### 6. 멀티 인덱스
- `groupby()`메소드에 여러 열을 리스트 형태로 전달하면 각 열들이 다중으로 행 인덱스를 구성하는 것이 멀티 인덱스.
- `Pandas`는 행 인덱스를 여러 레벨로 구현할 수 있도록 멀티 인덱스 클래스를 지원.
```python
grouped = df.groupby(['class', 'sex'])
gdf = grouped.mean()
# 전체 보기
print(gdf)
# 특정 그룹 선택
print(gdf.loc['First'])
# 튜플 형태를 이용하여 두 가지 그룹 선택
print(gdf.loc[('First', 'female')])
# xs인덱서 사용
print(gdf.xs('male', level='sex'))
```

### 7. 피벗
- `pibot_table()`함수는 액셀에서 사용하는 피벗테이블과 비슷한 기능을 처리.
- 피벗테이블을 구성하는 4가지 요소(행 인덱스, 열 인덱스, 데이터 값, 데이터 집계 함수)에 적용할 데이터프레임의 열을 각각 지정하여 함수의 인자로 전달.
```python
import pandas as pd
import seaborn as sns


pd.set_option('display.max_columns', 10)  # 출력할 최대 열의 개수
pd.set_option('display.max_colwidth', 20)  # 출력할 열의 너비

titanic = sns.load_dataset('titanic')
df = titanic.loc[:, ['age', 'sex', 'class', 'fare', 'survived']]

pdf1 = pd.pivot_table(df,              # 피벗할 데이터프레임
                      index='class',   # 행 위치에 들어갈 열
                      columns='sex',   # 열 위치에 들어갈 열
                      values='age',    # 데이터로 사용할 열
                      aggfunc='mean')  # 데이터 집계 함수
print(pdf1.head())
pdf2 = pd.pivot_table(df,
                      index='class',
                      columns='sex',
                      values='survived',
                      aggfunc=['mean', 'sum'])
print(pdf2.head())
pdf3 = pd.pivot_table(df,
                      index=['class', 'sex'],
                      columns='survived',
                      values=['age', 'fare'],
                      aggfunc=['mean', 'max'])
print(pdf3.head())
# pdf3의 행을 선택하기 위해 xs 인덱서 사용
print(pdf3.xs('First'))
# First이면서 female인 데이터 가져오기
print(pdf3.xs(('First', 'female')))
# 행 인덱스의 sex 레벨이 male인 행을 선택
print(pdf3.xs('male', level='sex'))
# Second, male 인 행을 선택
print(pdf3.xs(('Second', 'male'), level=[0, 'sex']))
# xs 인덱서를 사용하여 열을 선택하고 열 인덱스가 mean인 데이터를 선택
print(pdf3.xs('mean', axis=1))
# 열 인덱스가 ('mean', 'age')인 데이터 선택
print(pdf3.xs(('mean', 'age'), axis=1))
# 열 인덱스 레벨을 직접 지정
print(pdf3.xs(1, level='survived', axis=1))
# 열 인덱스 레벨0 에서 최대값을 나타내는 'max'를 가져오고, 레벨 1에서 객실요금을 나타내는 'fare'를 가져오기
print(pdf3.xs(('max', 'fare', 0), level=[0, 1, 2], axis=1))

```