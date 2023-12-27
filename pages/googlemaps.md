## 奥维互动地图

https://www.ovital.com/download/

## Google 地图源

https://gac-geo.googlecnapps.cn:443/maps/vt?lyrs=s,m&gl=CN&x={$x/2}&y={$y/2}&z={$z-1}&src=app&scale=2&from=app

https://gac-geo.googlecnapps.cn:443/maps/vt?lyrs=s,m&gl=CN&hl=nl&x={$x/2}&y={$y/2}&z={$z-1}&src=app&scale=2&from=app

## 参数说明：

### `?lyrs=s,m`
图层类型

* m: 路网
* t: 地形图
* p: 带标签的地形图
* s: 卫星图
* y: 带标签的卫星图
* h: 标签层（路名、地名等）

### `&gl=CN` 和 `&x=、&y=、&z=`

谷歌地图针对中国有两套坐标，一套做了偏移，一套没有。经测试在url加入`gl=CN`地图会有偏移。
注意：奥维地图中`gl=CN`时需要分别在`x`、`y`后面加上`/2`，`z`后面加上`-1`。

### `&hl=zh-CN` 
设置地图注记文字语言类型，默认为`zh-CN`。`hl=nl`为原始语言。

### `&scale=2` 
字体缩放级别
