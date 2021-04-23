## DataFrame 만들기
- `DataFrame`을 만들기 위해서는 같은 길이의 1차원 배열 여러 개가 필요.
- 여러개의 열을 모아 놓은 집합으로 이해.
- `dict()`에 여러개의 `list()`가 들어가있는 구조.
###입력 데이터
> |name|c1|c2|c3|c4|
> :---|:---:|:---:|:---:|:---
> r0|90|70|80|20
> r1|80|80|70|30
> r2|70|90|30|10
> 
###예제 코드
```python
data = {'c1': [90, 80, 70],
        'c2': [70, 80, 90],
        'c3': [80, 70, 30],
        'c4': [20, 30, 10]}
index = ['r0', 'r1', 'r2']
# 데이터 프레임 객체 생성, index에 row값, columns에 column값을 대입 가능
df = pd.DataFrame(data=data, index=index, columns=columns)
df.index = row  # 새로운 인덱스 배열 교체 가능
df.columns = column  # 새로운 열 배열 교체 가능
df.rename(columns={'c1':'c5'})  # rename 메서드로 행,열의 이름 변경 가능
```

## DataFrame 행, 열 삭제
- `DataFrame`의 행 또는 열을 삭제할 때 `drop()` 메서드를 사용.
- drop 후 제거된 `DataFrame`을 적용시킬 때는 `inplace=True`를 매개변수로 작성.
### 예제 코드
```python
df.drop('r0', axis=0)  # 행 제거
df.drop('c1', axis=1)  # 열 제거
df.drop('r1', axis=0, inplace=True)  # r1 행 제거 후 df에 저장(inplace=True)
```

## DataFrame 행, 열 선택
- `DataFrame`의 행을 선택할 때는 두 가지 방법으로 선택 가능
    1. `loc`을 이용한 라벨링 인덱싱
    2. `iloc`을 이용한 정수형 인덱싱
- 해당 `DataFrame`의 열 값을 가져올 때는 `dict()`형태로 가져올 수 있음.    
```python
df.loc['r0']  # r0 행의 값들을 가져옴
df.loc['r0', ['c1', 'c2']]  # r0 행의 값 중 c1과 c2 열 값을 가져옴
df.iloc[0, [0, 1]]  # 인덱스 값으로 가져오기  # 0번째 행의 0,1 번째 열 값을 가져옴
df['c1']  # c1열의 모든 값들을 가져옴
df[['c1', 'c2']]  # c1, c2 열의 값들을 가져옴
```

## DataFrame 행, 열 추가
- `DataFrame`에 행을 추가할 때는 추가하려는 행의 이름으로 된 `list()`를 `loc`을 이용해 넣어줄 수 있음.
- `DataFrame`에 열을 추가할 때는 추가하려는 행의 이름에 데이터를 추가해줄 수 있음.
```python
df.loc['r6'] = [90, 80, 70, 60]  # r6의 행을 추가, 리스트 내부 인덱스 순서대로 c1, c2, c3, c4
df['c5'] = [30, 45, 50]  # c5 열을 추가
df['c5'] = 30  # c5 열에 값이 모두 30으로 저장됨
```

## DataFrame 원소 값 변경
- 특정 원소를 선택하고 새로운 데이터 값을 지정해주면 값이 변경됨.
- 한개 또는 여러개를 변경해줄 수 있음.
```python
df.loc['r1']['c1'] = 150  # r1의 c1값을 150으로 변경
df.loc['r1', ['c1', 'c2']] = 200  # r1의 c1, c2 값을 200으로 변경
```

## 행, 열의 위치 변경
- 행과 열을 서로 바꿀 수 있음.
```python
df = df.transpose()  # 행과 열이 바뀜 
```

## 인덱스를 활용
- 특정 열을 행 인덱스로 설정 가능.
- 행 인덱스를 재배열 가능.
- 행 인덱스를 초기화 가능.
- 행 인덱스를 기준으로 `DataFrame` 정렬 가능
```python
df.set_index(['c1'])  # index를 c1으로 변경
df.reindex([new_index])  # new_index 리스트의 값들로 DataFrame의 인덱스를 변경
df.reset_index()  # index 값들이 1개의 열로 이동
df.sort_index(ascending=False)  # index를 기준으로 내림차순 정렬(ascending=True라면 오름차순)
```

