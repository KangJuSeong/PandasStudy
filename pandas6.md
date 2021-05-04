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

#### 2. 열 분리

### 3. 필터링

#### 1. 불린 인덱싱

#### 2. isin() 메소드 활용

### 4. 데이터프레임 합치기

#### 1. 데이터프레임 연결

#### 2. 데이터프레임 병합

#### 3. 데이터프레임 결합

### 5. 그룹 연산

#### 1. 그룹 객체 만들기(분할 단계)

#### 2. 그룹 연산 메소드(적용-결합 단계)

### 6. 멀티 인덱스

### 7. 피벗