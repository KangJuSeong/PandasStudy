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
- 산점도는 서로 다른 두 변수 사이의 관계를 나타냄.
- 각 변수는 연속되는 값을 갖고, 일반적으로 정수형 또는 실수형 값임.
- 2개의 연속 변수를 각각 x축과 y축에 하나씩 놓고, 데이터 값이 위치하는 (x, y) 좌표를 찾아서 점으로 표시.
```python
df = pd.read_csv('auto-mpg.csv', header=None)
df.columns = ['mpg', 'cylinders', 'displacement', 'horsepower', 'weight',
              'acceleration', 'model year', 'origin', 'name']

# msg와 weight에 관한 산점도 그리기, kind는 sccater(산점도), x와 y에 각각 값 대입
# s=cylinders_size, alpha=0.3 을 추가하면 점의 크기 증가 및 투명도는 0.3
df.plot(kind='scatter', x='weight', y='mpg', c='coral', figsize=(10, 5))
        #,s=cylinders_size, alpha=0.3)

plt.title('Scatter Plot - mgp vs weight')
plt.xlabel('weight')
plt.ylabel('mpg')
plt.show()
```
![](https://github.com/KangJuSeong/PandasStudy/blob/main/img/Figure_10.png)

### 6. 파이 차트
- 원을 파이 조각처럼 나누어서 표현.
- 조각의 크기는 해당 변수에 속하는 데이터 값의 크기에 비례.
```python
import pandas as pd
import matplotlib.pyplot as plt


df = pd.read_csv('auto-mpg.csv', header=None)
df.columns = ['mpg', 'cylinders', 'displacement', 'horsepower', 'weight',
              'acceleration', 'model year', 'origin', 'name']

print(df.info())
df['count'] = 1  # 각 origin 의 개수를 파악하기 위해 추가
df_origin = df.groupby('origin').sum()  # origin을 기준으로 모든 정수, 실수 값들의 합 구하기
df_origin.index = ['USA', 'EU', 'JPN']  # 순서대로 origin이 1, 2, 3이 각각 USA, EU, JPN

df_origin['count'].plot(kind='pie',  # 그래프 모형을 파이
                        figsize=(7, 5),
                        autopct='%1.1f%%',  # 퍼센트 표시
                        startangle=10,  # 파이 조각을 나누는 시작점(각도 표시)
                        colors=['chocolate', 'bisque', 'cadetblue'])  # 색상 리스트
plt.title('Model Origin', size=20)
plt.axis('equal')  # 파이 차트의 비율을 같게 조정(원에 가깝게)
plt.legend(labels=df_origin.index, loc='upper right')  # 범례 표시
plt.show()
```
![](https://github.com/KangJuSeong/PandasStudy/blob/main/img/Figure_11.png)

### 7. 박스 플롯
- 박스 플롯은 범주형 데이터의 분포를 파악하는데 적합.
- 박스 플롯에서는 통계값(최대값, 중간값, 최소값 등)을 표현하는데 최적화됨.
```python
import pandas as pd
import matplotlib.pyplot as plt


df = pd.read_csv('auto-mpg.csv', header=None)
df.columns = ['mpg', 'cylinders', 'displacement', 'horsepower', 'weight',
              'acceleration', 'model year', 'origin', 'name']

fig = plt.figure(figsize=(15, 5))
ax1 = fig.add_subplot(1, 2, 1)
ax2 = fig.add_subplot(1, 2, 2)

ax1.boxplot(x=[df[df['origin'] == 1]['mpg'],
               df[df['origin'] == 2]['mpg'],
               df[df['origin'] == 3]['mpg']],
            labels=['USA', 'EU', 'JAPAN'])

ax2.boxplot(x=[df[df['origin'] == 1]['mpg'],
               df[df['origin'] == 2]['mpg'],
               df[df['origin'] == 3]['mpg']],
            labels=['USA', 'EU', 'JAPAN'],
            vert=False)

ax1.set_title('Distribution of fuel economy by country of manufacture (vertical)')
ax2.set_title('Distribution of fuel economy by country of manufacture (no vertical)')
plt.show()
```
![](https://github.com/KangJuSeong/PandasStudy/blob/main/img/Figure_12.png)
