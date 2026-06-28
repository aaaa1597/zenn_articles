---
title: "3-⑤[AI][Kaggle][python]Kaggle入門(地理空間データ分析 5.近接分析 最終回)"
emoji: "🙆"
type: "tech"
topics:
  - "ai"
  - "python"
  - "kaggle"
  - "地理空間データ分析"
published: true
published_at: "2026-01-16 23:19"
---

[Kaggle入門3(地理空間データ分析 1.最初の地図)](https://zenn.dev/rg687076/articles/4028c3853f6ebf)
[Kaggle入門3(地理空間データ分析 2.座標参照系(Coordinate Reference Systems(CRS)))](https://zenn.dev/rg687076/articles/4de7e53d31b8e7)
[Kaggle入門3(地理空間データ分析 3.相互作用Maps)](https://zenn.dev/rg687076/articles/08cccf9d0895a4)
[Kaggle入門3(地理空間データ分析 4.地理空間データの操作)](https://zenn.dev/rg687076/articles/34daf31bc89485)
[Kaggle入門3(地理空間データ分析 5.近接分析 最終回)](https://zenn.dev/rg687076/articles/0ffdf4b5e0bfb4)

← [Kaggle入門2(Pandasライブラリの使い方)](https://zenn.dev/rg687076/articles/0467242dc7b343)

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;[Kaggle入門4(中級機械学習)](https://zenn.dev/rg687076/articles/4adfbc1ce1de20) →

## Abstract
Kaggle「地理空間データ分析の[近接分析](https://www.kaggle.com/code/alexisbcook/proximity-analysis)」の翻訳と実行方法の解説

## 近接分析
距離を測定し、地図上で近隣の地点を探索します。

## 理論編
### はじめに
このチュートリアルでは、近接分析のいくつかの手法を学習します。特に、次のような方法を学習します。 
- 地図上の点間の距離を測定する
- ある地物から一定の半径内にあるすべての点を選択する

まずは下記コードを実行して、準備を完了します。
```python
import folium
from folium import Marker, GeoJson
from folium.plugins import HeatMap

import pandas as pd
import geopandas as gpd
```

```
/opt/conda/lib/python3.7/site-packages/geopandas/_compat.py:115: UserWarning: The Shapely GEOS version (3.9.1-CAPI-1.14.2) is incompatible with the GEOS version PyGEOS was compiled with (3.10.4-CAPI-1.16.2). Conversions between both will be slow.
  shapely_geos_version, geos_capi_version_string
```

米国ペンシルベニア州フィラデルフィアにおける有毒化学物質の放出を追跡する米国環境保護庁 (EPA) のデータセットを使用します。
```python
releases = gpd.read_file("../input/geospatial-learn-course-data/toxic_release_pennsylvania/toxic_release_pennsylvania/toxic_release_pennsylvania.shp") 
releases.head()
```

||YEAR|CITY|COUNTY|ST|LATITUDE|LONGITUDE|CHEMICAL|UNIT_OF_ME|TOTAL_RELE|geometry|
|---|---|---|---|---|---|---|---|---|---|---|
|0|2016|PHILADELPHIA|PHILADELPHIA|PA|40.005901|-75.072103|FORMIC ACID|Pounds|0.160|POINT (2718560.227 256380.179)|
|1|2016|PHILADELPHIA|PHILADELPHIA|PA|39.920120|-75.146410|ETHYLENE GLYCOL|Pounds|13353.480|POINT (2698674.606 224522.905)|
|2|2016|PHILADELPHIA|PHILADELPHIA|PA|40.023880|-75.220450|CERTAIN GLYCOL ETHERS|Pounds|104.135|POINT (2676833.394 261701.856)|
|3|2016|PHILADELPHIA|PHILADELPHIA|PA|39.913540|-75.198890|LEAD COMPOUNDS|Pounds|1730.280|POINT (2684030.004 221697.388)|
|4|2016|PHILADELPHIA|PHILADELPHIA|PA|39.913540|-75.198890|BENZENE|Pounds|39863.290|POINT (2684030.004 221697.388)|

また、同じ市内の大気質監視ステーションからの測定値を含むデータセットも使用します。
```python
stations = gpd.read_file("../input/geospatial-learn-course-data/PhillyHealth_Air_Monitoring_Stations/PhillyHealth_Air_Monitoring_Stations/PhillyHealth_Air_Monitoring_Stations.shp")
stations.head()
```
||SITE_NAME|ADDRESS|BLACK_CARB|ULTRAFINE_|CO|SO2|OZONE|NO2|NOY_NO|PM10|...|PAMS_VOC|TSP_11101|TSP_METALS|TSP_LEAD|TOXICS_TO1|MET|COMMUNITY_|LATITUDE|LONGITUDE|geometry|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|0|LAB|1501 East Lycoming Avenue|N|N|Y|N|Y|Y|Y|N|...|Y|N|Y|N|y|N|N|40.008606|-75.097624|POINT (2711384.641 257149.310)|
|1|ROX|Eva and Dearnley Streets|N|N|N|N|N|N|N|N|...|N|N|Y|N|Y|N|N|40.050461|-75.236966|POINT (2671934.290 271248.900)|
|2|NEA|Grant Avenue and Ashton Street|N|N|N|N|Y|N|N|N|...|N|N|N|N|N|Y|N|40.072073|-75.013128|POINT (2734326.638 280980.247)|
|3|CHS|500 South Broad Street|N|N|N|N|N|N|N|N|...|N|N|Y|N|Y|N|N|39.944510|-75.165442|POINT (2693078.580 233247.101)|
|4|NEW|2861 Lewis Street|N|N|Y|Y|Y|N|Y|Y|...|N|Y|N|Y|N|Y|N|39.991688|-75.080378|POINT (2716399.773 251134.976)|

5 rows × 24 columns

### 距離の測定
2つの異なるGeoDataFrameにある点同士の距離を測定するには、まずそれらが同じ **座標参照系(CRS)** を使用していることを確認する必要があります。幸い、今回のケースでは両方とも EPSG 2272 が使用されています。
```python
print(stations.crs)
print(releases.crs)
```
**出力**
PROJCS["NAD83_Pennsylvania_South_ftUS",GEOGCS["GCS_North_American_1983",DATUM["D_North_American_1983",SPHEROID["GRS_1980",6378137,298.257222101]],PRIMEM["Greenwich",0],UNIT["Degree",0.0174532925199433]],PROJECTION["Lambert_Conformal_Conic_2SP"],PARAMETER["latitude_of_origin",39.3333333333333],PARAMETER["central_meridian",-77.75],PARAMETER["standard_parallel_1",40.9666666666667],PARAMETER["standard_parallel_2",39.9333333333333],PARAMETER["false_easting",1968500],PARAMETER["false_northing",0],UNIT["Foot_US",0.304800609601219],AXIS["Easting",EAST],AXIS["Northing",NORTH]]
PROJCS["NAD83_Pennsylvania_South_ftUS",GEOGCS["GCS_North_American_1983",DATUM["D_North_American_1983",SPHEROID["GRS_1980",6378137,298.257222101]],PRIMEM["Greenwich",0],UNIT["Degree",0.0174532925199433]],PROJECTION["Lambert_Conformal_Conic_2SP"],PARAMETER["latitude_of_origin",39.3333333333333],PARAMETER["central_meridian",-77.75],PARAMETER["standard_parallel_1",40.9666666666667],PARAMETER["standard_parallel_2",39.9333333333333],PARAMETER["false_easting",1968500],PARAMETER["false_northing",0],UNIT["Foot_US",0.304800609601219],AXIS["Easting",EAST],AXIS["Northing",NORTH]]

また、CRSをチェックして、使用されている単位(メートル、フィートなど)を確認します。今回の EPSG 2272 の単位は **フィート(feet)** です。

GeoPandasでの距離計算は非常にシンプルです。以下のコードでは、recent_release（特定の放出事故）と stations(観測局) GeoDataFrame内のすべての局との距離を計算しています。

```python
# 特定の放出事故を1つ選択
recent_release = releases.iloc[360]

# 放出地点から各観測局までの距離を測定
distances = stations.geometry.distance(recent_release.geometry)
distances
```
**出力**
0     44778.509761
1     51006.456589
2     77744.509207
3     14672.170878
4     43753.554393
5      4711.658655
6     23197.430858
7     12072.823097
8     79081.825506
9      3780.623591
10    27577.474903
11    19818.381002
dtype: float64

算出された距離を用いて、各局への平均距離などの統計量を得ることができます。
```python
print('観測局への平均距離: {} フィート'.format(distances.mean()))
```
**出力**
Mean distance to monitoring stations: 33516.28487007786 feet

もしくは、最も近い観測局を特定することも可能です。
```python
print('最も近い観測局 ({} フィート):'.format(distances.min()))
print(stations.iloc[distances.idxmin()][["ADDRESS", "LATITUDE", "LONGITUDE"]])
```
**出力**
Closest monitoring station (3780.623590556444 feet):
ADDRESS      3100 Penrose Ferry Road
LATITUDE                    39.91279
LONGITUDE                 -75.185448
Name: 9, dtype: object

### バッファ(Buffer)の作成
もし、ある点から一定の半径内にあるエリアを把握したい場合、最も簡単な方法はバッファを作成することです。 以下のコードでは、12箇所の各観測局を中心に、半径2マイル(2 * 5280フィート)のポリゴン(多角形)を含むGeoSeries two_mile_buffer を作成します。
```python
two_mile_buffer = stations.geometry.buffer(2*5280)
two_mile_buffer.head()
```
**出力**
0    POLYGON ((2721944.641 257149.310, 2721893.792 ...
1    POLYGON ((2682494.290 271248.900, 2682443.441 ...
2    POLYGON ((2744886.638 280980.247, 2744835.789 ...
3    POLYGON ((2703638.580 233247.101, 2703587.731 ...
4    POLYGON ((2726959.773 251134.976, 2726908.924 ...
dtype: geometry

folium.GeoJson() を使って、これらのポリゴンを地図上にプロットします。Foliumは緯度・経度を必要とするため、プロット前にCRSを EPSG 4326 に変換する必要がある点に注意してください
```python
# 放出事故と観測局のマップを作成
m = folium.Map(location=[39.9526,-75.1652], zoom_start=11)
HeatMap(data=releases[['LATITUDE', 'LONGITUDE']], radius=15).add_to(m)

for idx, row in stations.iterrows():
    Marker([row['LATITUDE'], row['LONGITUDE']]).add_to(m)
    
# 各ポリゴンを地図にプロット
folium.GeoJson(two_mile_buffer.to_crs(epsg=4326)).add_to(m)
m
```
**出力**
![](https://storage.googleapis.com/zenn-user-upload/42a8490b3ad3-20260115.png)

**包含判定（特定のエリアに含まれるか）**
ある放出事故がいずれかの観測局から2マイル以内で発生したかどうかを判定したい場合、通常は各ポリゴンに対して個別にテストを行う必要があります。 しかし、より効率的な方法は、unary_union 属性を使用して、すべてのポリゴンを1つの MultiPolygon オブジェクトに統合することです。
```python
# Turn group of polygons into single multipolygon
my_union = two_mile_buffer.geometry.unary_union
print('Type:', type(my_union))

# Show the MultiPolygon object
my_union
```
**出力**
![](https://storage.googleapis.com/zenn-user-upload/2142c1f8eee1-20260115.png)

contains() メソッドを使うと、マルチポリゴンが特定の点を含んでいるかどうかをチェックできます。先ほど計算した、最も近い局まで約3781フィート（2マイル以内）だった地点で試してみましょう。

```python
# 最も近い局が2マイル以内にある場合
my_union.contains(releases.iloc[360].geometry)
# 出力：True
```
もちろん、すべての事故が観測局の2マイル以内で起きているわけではありません。

```python
# 最も近い局が2マイル以上離れている場合
my_union.contains(releases.iloc[358].geometry)
# 出力：False
```

## 実践編
### はじめに
あなたは危機対応チームの一員であり、ニューヨーク市での衝突事故に対して病院がどのように対応してきたかを確認したいと考えています。
![](https://storage.googleapis.com/kaggle-media/learn/images/wamd0n7.png =400x)

始める前に、以下のコード セルを実行してすべてをセットアップします。
![](https://storage.googleapis.com/zenn-user-upload/f60786ced0ca-20260116.png)

マップを視覚化するために、embed_map() 関数も定義します。
```pypthon
def embed_map(m, file_name):
    from IPython.display import IFrame
    m.save(file_name)
    return IFrame(file_name, width='100%', height='500px')
```

### 演習
#### 1. 衝突データを視覚化します。
以下のコード セルを実行して、2013 ～ 2018 年の主要な自動車衝突を追跡する GeoDataFrame 衝突を読み込みます。
![](https://storage.googleapis.com/zenn-user-upload/50095f71b274-20260116.png)
5 rows × 30 columns

「緯度」と「経度」の列を使って、衝突データを視覚化するインタラクティブマップを作成してください。どのようなタイプのマップが最も効果的だと思いますか？
![](https://storage.googleapis.com/zenn-user-upload/1e67b8130ce0-20260116.png)

ヒントと答えです。
![](https://storage.googleapis.com/zenn-user-upload/d41bb68dab02-20260116.png)

#### 2. 病院の適用範囲を理解する。
次のコードセルを実行して病院データを読み込みます。
![](https://storage.googleapis.com/zenn-user-upload/50cfbe254cfb-20260116.png)

「緯度」列と「経度」列を使用して、病院の場所を視覚化します。
![](https://storage.googleapis.com/zenn-user-upload/0e530d797bed-20260116.png)

ヒントと答えです。
![](https://storage.googleapis.com/zenn-user-upload/49efaba7b910-20260116.png)

#### 3. 最寄りの病院が10キロ以上離れていたのはいつですか?
最も近い病院から 10 キロメートル以上離れた場所で発生した衝突事故のすべての行を含む DataFrame outside_range を作成します。
Note : 病院と衝突の両方で座標参照システムとして EPSG 2263 が使用され、EPSG 2263 の単位はメートルであることに注意してください。
![](https://storage.googleapis.com/zenn-user-upload/d49588b85215-20260116.png)

ヒントと答えです。
![](https://storage.googleapis.com/zenn-user-upload/a59c2ee1cb6b-20260116.png)


次は、最も近い病院から 10 キロメートル以上離れた場所で発生した衝突の割合を計算します。
![](https://storage.googleapis.com/zenn-user-upload/ab2765577730-20260116.png)

#### 4. 推薦者を作成します。
![](https://storage.googleapis.com/zenn-user-upload/f1db84001111-20260116.png)

ヒントと答えです。
![](https://storage.googleapis.com/zenn-user-upload/cebc8050254c-20260116.png)

#### 5. 最も需要が高い病院はどこですか?
Outside_range DataFrame 内の衝突のみを考慮すると、どの病院が最も推奨されますか？
回答は、4. で作成した関数によって返される病院名と完全に一致する Python 文字列である必要があります。
![](https://storage.googleapis.com/zenn-user-upload/5b492cae05fb-20260116.png)

ヒントと答えです。
![](https://storage.googleapis.com/zenn-user-upload/1ee5f5f023e6-20260116.png)

#### 6. 市はどこに新しい病院を建設すべきでしょうか?
次のコード セルを (変更せずに) 実行して、最も近い病院から 10 キロメートル以上離れた場所で発生した衝突に加えて、病院の場所を視覚化します。

![](https://storage.googleapis.com/zenn-user-upload/08e4b8511519-20260116.png)

地図上の任意の場所をクリックすると、対応する位置の緯度と経度を示すポップアップが表示されます。
ニューヨーク市は、2つの新しい病院の立地選定について、あなたに協力を求めています。特に、ステップ3で算出された割合が10%未満となるような立地選定について、あなたの協力を求めています。地図を使用して (ゾーニング法や病院を建設するためにどのような建物を撤去する必要があるかを気にせずに)、市がこの目標を達成するのに役立つ 2 つの場所を特定できますか?

病院 1 の提案緯度と経度をそれぞれ lat_1 と long_1 に入力します。(病院 2 についても同様です。)
次に、セルの残りの部分をそのまま実行して、新しい病院の影響を確認します。2つの新しい病院によって割合が10%未満になった場合、正解とみなされます。
![](https://storage.googleapis.com/zenn-user-upload/5b0e6f9fdfd1-20260116.png)

答えです。
![](https://storage.googleapis.com/zenn-user-upload/e17edfff692f-20260116.png)

## お疲れさまでした。
終了です。
地理空間分析マイクロコースを修了しました！お疲れ様でした！

