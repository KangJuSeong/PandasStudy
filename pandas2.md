## Matplotlib 기본 그래프 도구 사용
- 데이터의 크기가 수천, 수만 개를 넘어가는 경우 시각화 도구가 필요함.
- 다양한 관점에서 데이터에 대한 통찰력 제공.
- `Matplotlib`는 파이썬 표준 시각화 도구로서 평면 그래프에 다양한 포맷과 기능 지원.

### 1. 선 그래프
1. 그래프 그리기
   - 선을 이용하여 데이터 통계를 그래프로 표현.
```python
import pandas as pd
import matplotlib.pyplot as plt


df = pd.read_excel('시도별 전출입 인구수.xlsx', engine='openpyxl', header=0)
df = df.fillna(method='ffill')  # 누락된 행의 값을 바로 앞의 행 값을 대체
mask = (df['전출지별'] == '서울특별시') & (df['전입지별'] != '서울특별시')  # 서울특별시에서 타지역으로 전출 필터링
df_seoul = df[mask]
df_seoul = df_seoul.drop(['전출지별'], axis=1)  # 전출지별 열 제거
df_seoul.rename({'전입지별': '전입지'}, axis=1, inplace=True)  # 전입지별을 전입지로 이름 변겅
df_seoul.set_index('전입지', inplace=True)  # 전입지를 index로 설정
sr_one = df_seoul.loc['경기도']  # 서울에서 경기도로 이동한 인구수 값 출력
plt.figure(figsize=(14, 8))  # 그래프 사이즈 지정 가로 14, 세로 5인치
plt.xticks(rotation='vertical')  # xlabel 수직으로 표현
plt.plot(sr_one.index, sr_one.values)  # x축은 년도(index), y축은 전출 인구수(values)
plt.title('Seoul -> Gyungi')  # 제목
plt.xlabel('Term')  # x축 라벨
plt.ylabel('Moving population')  # y축 라벨
plt.legend(labels=['Seoul -> Gyungi'], loc='best')  # 범례 표시
plt.show()
```
![](D:\PandasStudy\img\Figure_1.png)
2. 그래프 스타일
   - `marker='o'`, `markersize=size` 를 통해 해당 값에 마커를 찍을 수 있음.
   - `annotate()` 메서드를 이용하여 그래프 내부에 주석을 표시할 수 있음.
   - 매개변수 `size`를 통해 fontsize를 조절할 수 있음.
```python
plt.figure(figsize=(14, 8))  # 그래프 사이즈 지정 가로 14, 세로 5인치
plt.xticks(size=10, rotation='vertical')  # xlabel 수직으로 표현
plt.plot(sr_one.index, sr_one.values, marker='o', markersize=10)  # x축은 년도(index), y축은 전출 인구수(values)
plt.title('Seoul -> Gyungi', size=30)  # 제목
plt.xlabel('Term', size=20)  # x축 라벨
plt.ylabel('Moving population', size=20)  # y축 라벨
plt.legend(labels=['Seoul -> Gyungi'], loc='best', fontsize=15)  # 범례 표시
plt.ylim(50000, 800000)  # y축 범위 지정 (최소값, 최대값)
plt.annotate('',
             xy=(20, 620000),       #화살표의 머리 부분(끝점)
             xytext=(2, 290000),    #화살표의 꼬리 부분(시작점)
             xycoords='data',       #좌표체계
             arrowprops=dict(arrowstyle='->', color='skyblue', lw=5), #화살표 서식
             )

plt.annotate('',
             xy=(47, 450000),       #화살표의 머리 부분(끝점)
             xytext=(30, 580000),   #화살표의 꼬리 부분(시작점)
             xycoords='data',       #좌표체계
             arrowprops=dict(arrowstyle='->', color='olive', lw=5),  #화살표 서식
             )
plt.annotate('Increase population(1970-1995)',  #텍스트 입력
             xy=(9, 400000),            #텍스트 위치 기준점
             rotation=34,                #텍스트 회전각도
             va='baseline',              #텍스트 상하 정렬
             ha='center',                #텍스트 좌우 정렬
             fontsize=15,                #텍스트 크기
             )

plt.annotate('Reduced population(1995-2017)',  #텍스트 입력
             xy=(40, 500000),            #텍스트 위치 기준점
             rotation=-16,               #텍스트 회전각도
             va='baseline',              #텍스트 상하 정렬
             ha='center',                #텍스트 좌우 정렬
             fontsize=15,                #텍스트 크기
             )
plt.show()
```
![](D:\PandasStudy\img\Figure_2.png)

3. 여러 그래프 그리기
   - 화면을 분할하여 여러 개 그래프를 그릴 수 있음.
   - `axe` 객체를 만들고, 분할된 화면마다 `axe` 객체를 하나씩 배정.
   - `figure()`함수를 사용하여 그래프를 그리는 틀을 만든다.
   - 해당 `figure()`에 `add_subplot()`메서드를 이용하여 그래프를 넣는다.
```python
fig = plt.figure(figsize=(10, 10))  # 그래프 틀 생성, 사이즈는 10x10
ax1 = fig.add_subplot(2, 1, 1)  # 그래프 생성
ax2 = fig.add_subplot(2, 1, 2)

ax1.plot(sr_one, 'o', markersize=10)
# 마커 모양: o, 마커 색상: 초록, 마커 사이즈: 10, 선 색: 올리브, 선 굵기: 2, 범례: Seoul -> Gyungi
ax2.plot(sr_one, marker='o', markerfacecolor='green', markersize=10, color='olive', linewidth=2, label='Seoul -> Gyungi')
ax2.legend(loc='best')

# y축 최소값, 최대값 지정
ax1.set_ylim(50000, 800000)
ax2.set_ylim(50000, 800000)

# 축 눈금 라벨 지정 및 75도 회전
ax1.set_xticklabels(sr_one.index, rotation=75)
ax2.set_xticklabels(sr_one.index, rotation=75)

plt.show()
```   
![](D:\PandasStudy\img\Figure_3.png)

4. 동일한 그림에 여러개의 그래프 추가
   - 동일한 그림에 여러개의 그래프를 추가할 수 있음.
```python
col_years = list(map(str, range(1970, 2018)))
df_3 = df_seoul.loc[['충청남도', '경상북도', '강원도'], col_years]

fig = plt.figure(figsize=(15, 5))
ax = fig.add_subplot(1, 1, 1)

ax.plot(col_years, df_3.loc['충청남도', :], marker='o', markerfacecolor='green',
        markersize=10, color='olive', linewidth=2, label='Seoul -> Chungnam')
ax.plot(col_years, df_3.loc['경상북도', :], marker='o', markerfacecolor='blue',
        markersize=10, color='skyblue', linewidth=2, label='Seoul -> Gyungbuk')
ax.plot(col_years, df_3.loc['강원도', :], marker='o', markerfacecolor='red',
        markersize=10, color='magenta', linewidth=2, label='Seoul -> Kangwon')

ax.legend(loc='best')
ax.set_title('Seoul -> Chungnam, Gyungbuk, Kangwon Population Movement', size=20)
ax.set_xlabel('Term', size=12)
ax.set_ylabel('Population Movement')
ax.set_xticklabels(col_years, rotation=90)

ax.tick_params(axis='x', labelsize=10)
ax.tick_params(axis='y', labelsize=10)

plt.show()
```   
![](D:\PandasStudy\img\Figure_4.png)

