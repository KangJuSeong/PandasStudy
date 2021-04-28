## Folium 라이브러리 - 지도 활용

### 지도 만들기
- 지도 위에 시각화할 때 유용한 도구.
- 세계 지도를 기본 지원하고 다양한 스타일의 지도 이미지를 제공.
```python
import folium


# location 에 최초 위치 좌표, zoom_start에 확대 정도 값 대입
seoul_map = folium.Map(location=[37.55, 126.98], zoom_start=12)
# tiles를 이용하여 지도 스타일 변경 가능
seoul_map2 = folium.Map(location=[37.55, 126.98], tiles='Stamen Terrain', zoom_start=15)
seoul_map3 = folium.Map(location=[37.55, 126.98], tiles='Stamen Toner', zoom_start=15)
seoul_map.save('seoul1.html')
seoul_map2.save('seoul2.html')
seoul_map3.save('seoul3.html')
```
![](https://github.com/KangJuSeong/PandasStudy/blob/main/img/Folium_1.png)

### 마커 표시
- 지도에 위치 표시 가능.
- `Marker()` 함수에 위도, 경도 정보를 전달.
- `popup` 옵션을 추가하면 마커를 클릭했을 때 팝업창에 표시해주는 텍스트 삽입 가능
```python
import folium
import pandas as pd

# 서욷지역 대학교 위치 데이터 DataFrame에 넣기
df = pd.read_excel('서울지역 대학교 위치.xlsx', index_col=0)
# 서울 지역을 기준으로 지도 생성
seoul_map = folium.Map(location=[37.55, 126.98], zoom_start=12)

# DataFrame에서 대학명, 위도, 경도를 가져와서 마커로 표시하고 해당 마커를 seoul_map에 추가
for name, lat, lon in zip(df.index, df['위도'], df['경도']):
    folium.Marker([lat, lon], popup=name).add_to(seoul_map)
    # radius 원 반지름, color 마커색, fill 내부 채우기, fill_color 내부 색, fill_opacity 투명도, popup 팝업 내용

seoul_map.save('Seoul_Univ.html')
```
![](https://github.com/KangJuSeong/PandasStudy/blob/main/img/Folium_2.png)

### 지도 영역에 단게구분도 표시
- 지도 상의 어떤 경계에 둘러싸인 영역에 색을 칠하거나 음영 등으로 정보를 나타내는 시각화 방법.
- 전달하려는 정보의 값이 커지면 영역에 칠해진 색이나 음영이 진해짐.
```python
import folium
import json
import pandas as pd


file_path = '경기도인구데이터.xlsx'
df = pd.read_excel(file_path, index_col='구분')
df.columns = df.columns.map(str)

geo_path = '경기도행정구역경계.json'
try:
    geo_data = json.load(open(geo_path, encoding='utf-8'))
except:
    geo_data = json.load(open(geo_path, encoding='utf-8-sig'))

g_map = folium.Map(location=[37.5502, 126.982], zoom_start=9)

year = '2007'

folium.Choropleth(geo_data=geo_data,  # 지도 경계
                  data=df[year],  # 표시하려는 데이터
                  columns=[df.index, df[year]],  # 열 지정
                  fill_color='YlOrRd', fill_opacity=0.7, line_opacity=0.3,
                  key_on='feature.properties.name').add_to(g_map)

g_map.save('test.html')
```
![](https://github.com/KangJuSeong/PandasStudy/blob/main/img/Folium_3.png)