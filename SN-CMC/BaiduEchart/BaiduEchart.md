### ECharts图表 

|日期|作者|备注|
|------|------|------|
|2018-09-28|陈泰林、 周鹏辉|初稿|
|2018-10-25|陈泰林|修改|

### 基础配置
```
1、底层实现统计图形参照百度图形（BaiduEChart）
2、基本案例参照：帮助中心-界面搭建-图形分析
```

#### 数据源
<img src="help/SN-CMC/BaiduEchart/img/BaiduEchart.png" />	
#### 数据源列
<img src="help/SN-CMC/BaiduEchart/img/BaiduEchart1.png" />	
#### 图形区使用数据源-配置
<img src="help/SN-CMC/BaiduEchart/img/BaiduEchart2.png" />	
<img src="help/SN-CMC/BaiduEchart/img/BaiduEchart4.png" />
#### 图形区使用数据源-数据
```
groupSource：X轴分组（$xx:$xx），按$xx:$xx分组，如果有码表：$nm$xx
seriesSource：Y轴数据（#score_%或者#score_1,#score_2），注意：#表示取列，匹配到多个表示有多个"柱子"，即一组中有多少个"柱子"
```
<img src="help/SN-CMC/BaiduEchart/img/BaiduEchart3.png" />
#### 图形区使用数据源
图形属性（chart属性：跟开源数据配置属性一样，实际开发可以参照百度开源的例子）
<img src="help/SN-CMC/BaiduEchart/img/BaiduEchart5.png" />
### 效果图
<img src="help/SN-CMC/BaiduEchart/img/BaiduEchart6.png" />

参考事例[动态时间轴](uiinvoke/00/zh_CN/theme0/91.UI.Charts.ChartTimerLine.html)
### JS监听（非必须配置）
如果根据大师的配置不满足一些复杂的界面数据时，需要使用此监听，自定义实现Chart属性。如图
<img src="help/SN-CMC/BaiduEchart/img/BaiduEchart8.png" />	
1、百度EChart参考：<a href="http://echarts.baidu.com/examples.html?qq-pf-to=pcqq.c2c" target="_blank" style="color: #3285e1;text-decoration: underline;">http://echarts.baidu.com/examples.html?qq-pf-to=pcqq.c2c</a>
2、百度EChart参考：<a href="http://echarts.baidu.com/echarts2/doc/example.html" target="_blank" style="color: #3285e1;text-decoration: underline;">http://echarts.baidu.com/echarts2/doc/example.html</a>
3、地图自定义绘制：<a href="http://geojson.io" target="_blank" style="color: #3285e1;text-decoration: underline;">http://geojson.io</a>
4、只要是百度EChart中的图形都可参照实现
5、具体图形相关的配置（chart属性）请参照百度EChart开源帮助文档说明
6、后台逻辑：
首先：由测试数据，保证数据格式是什么格式，界面能够产生正常的效果
最后：构造查询sql数据，与测试数据格式一致即可（注意后台逻辑尽量避免大量数据的循环）
### 相关程序
* 百度EChart的图形类型：xjs.chart.BaiduEChart
* 图形界面定义数据属性：xjs.chart.Chart
* 图形自定义实现监听：xjs.chart.DefaultChartListener
* 百度EChart属性（chart属性）：
	js.baidu.echart.EChartSerie
	js.baidu.echart.EChartOption
	js.baidu.echart.EChartLegend
	js.baidu.echart.EChartAxis
* 图形监听接口：xjs.chart.ChartListener
* 图形默认处理监听：xjs.chart.TableChartListener

	
### 类图

@startuml
Title 百度Echart图形类图

Chart <|.. BaiduEChart
ChartListener <|.. DefaultChartListener
ChartListener <|.. TableChartListener

class ChartListener{
+ public void createChart(final Chart chart);
+ public void dblClickChart(Chart chart);
+ public void onEChartRefresh(BaiduEChart chart,js.baidu.echart.EChartOption echartOpts);
+ public void onCanvasDomCreated(Chart chart);
+ public void predrawChart(Chart chart,js.CanvasRenderingContext2D ctx,final js.Rect chartRect);
}


@enduml

### 时序图

@startuml
Title 百度Echart图形时序图

actor 客户端

participant Table   as "TableChartListener"
participant ChartCanvas    as "ChartCanvas"
participant Chart    as "Chart"
participant BaiduEChart     as "BaiduEChart"



客户端 -> Table : [dataLoaded]\n加载数据
activate Table

	Table -> ChartCanvas : [drawChart]\n画图
	activate ChartCanvas
		ChartCanvas -> Chart : [getChart]\n获取Chart
		activate Chart
		    Chart -> Table : [createChart]\n创建Chart初始化图形数据
		    Table -> Table : [onChartCreating]\n创建图形之前触发事件onChartCreating
			Table -> Table : [buildChartLabels]\n构建图形数据
			Table -> Table : [onBuildChartLabels]\n构建图形数据之后触发事件onBuildChartLabels
			Table -> Table : [onChartCreated]\n构建图形数据加工处理之后触发事件onChartCreated
			Table -> Chart : 
			Chart -> Chart : [initChart]\n初始化图形
			Chart -> Chart : [draw]\n绘制图形
			Chart -> BaiduEChart : [drawLoading]\n绘图之前处理
			activate BaiduEChart
			BaiduEChart -> BaiduEChart : [redraw]\n绘图
			BaiduEChart -> BaiduEChart : [requireCallback]\n初始化参数请求
			BaiduEChart -> BaiduEChart : [_refresh]\n刷新
			BaiduEChart -> Table : [onEChartRefresh]\n构建图形参数中触发事件onEChartRefresh
			
			deactivate BaiduEChart
		deactivate Chart
	deactivate ChartCanvas
	Table -> Table : [onBaiduEChartRefresh]\n构建图形参数中触发事件onBaiduEChartRefresh
	
Table -> 客户端 :响应界面
deactivate Table
@enduml