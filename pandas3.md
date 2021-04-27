### 2. 면적 그래프
- 각 열의 데이터를 선 그래프로 구현하는데, 선 그래프와 x축 사이의 공간에 색이 입혀짐.
- 색의 투명도는 기본값 0.5 이며 선 그래프를 그리는 `plot()`메서드에 `kind='area'`옵션을 추가하면 됨.
- 누적하여 그릴수 있는데, 기본값은 `stacked=True` 옵션이여서 기본적으로 누적됨.
```python
col_years = list(map(str, range(1970, 2018)))
df_4 = df_seoul.loc[['충청남도', '경상북도', '강원도', '전라남도'], col_years]
df_4 = df_4.transpose()
df_4.index = df_4.index.map(int)  # index를 정수형으로 변환

df_4.plot(kind='area', stacked=True, alpha=0.2, figsize=(20, 10))  # alpha는 투명도, stacked는 누적 여부, kind는 그래프 종류를 나타냄(area는 면적)
plt.title('Seoul -> Outside Population Movement', size=30)
plt.ylabel('Movement Population', size=20)
plt.xlabel('Term', size=20)
plt.legend(loc='best', fontsize=15)

plt.show()
```

1. 면적 누적 x
![](https://github.com/KangJuSeong/PandasStudy/blob/main/img/Figure_5.png)

2. 면적 누적 o
![](https://github.com/KangJuSeong/PandasStudy/blob/main/img/Figure_6.png)
   
### 3. 막대 그래프
- 데이터 값의 크기에 비례하여 높이를 갖는 직사각형 막대로 표현.
- 상대적 길이 차이를 통해 값의 크고 작음을 설멍.
- 세로형과 가로형 두 가지 종류가 있음.
```python
df_4.plot(kind='barh', width=0.7, color=['orange', 'green', 'skyblue', 'blue'], figsize=(20, 10))  # color는 막대들의 색, kind 막대를 나타내는 barh(가로), width는 막대 굵기
```

1. 세로형
![](https://github.com/KangJuSeong/PandasStudy/blob/main/img/Figure_7.png)   

2. 가로형
![](https://github.com/KangJuSeong/PandasStudy/blob/main/img/Figure_8.png)
   
### 4. 히스토그램
- 히스토그램은 변수가 하나인 단변수 데이터의 빈도수를 그래프로 표현.
- x축을 같은 크기의 여러 구간으로 나누고 각 구간에서 속하는 데이터 값의 개수를 y축에 표현.
- 구간을 나누는 간격의 크기에 따라 빈도가 달라지고 히스토그램의 모양이 변화됨.
```python
import pandas as pd
import matplotlib.pyplot as plt


df = pd.read_csv('auto-mpg.csv', header=None)
df.columns = ['mpg', 'cylinders', 'displacement', 'horsepower', 'weight',
              'acceleration', 'model year', 'origin', 'name']

# msg 열에 대한 히스토그램 그리기, bins는 기둥 개수, kind는 hist(히스토그램)
df['mpg'].plot(kind='hist', bins=10, color='coral', figsize=(10, 5))

plt.title('Histogram')
plt.xlabel('mpg')
plt.show()
```
![](https://github.com/KangJuSeong/PandasStudy/blob/main/img/Figure_9.png)

### 5. 산점도
- 