### 客户端监听

|日期|作者|备注|
|------|------|------|
|2018-10-18|陈泰林|初稿|

### 常用监听
table表格监听：DefaultListener
TabPanel监听：DefaultTabPanelListener
DialogPane监听：DefaultDialogPaneListener
ProgressPane监听：DefaultProgressPaneListener
百度EChart监听：DefaultChartListener

### DefaultTabPanelListener
```
public void onTabbedSelecting(TabPanel comp,int selectItem);--->tab切换之前触发
public void onTabbedSelected(TabPanel comp,int oldSelected);--->tab切换之后触发
```
### DefaultDialogPaneListener
```
    public void onOk(T dialog,Object src,String cmd);--->弹出框确定时触发
	public void onClosed(T dialog);--->弹出框关闭之后触发
	public void onClosing(T dialog,String command);--->弹出框关闭之前触发
	public void itemValueChanged(T dialog,UIEvent e);--->弹出框中值改变触发
	public void itemAidInputing(T dialog,UIEvent e);--->弹出框辅助录入触发
	public boolean validateValue(T comp);-->弹出框校验数据时触发
	public void valueExchange(T comp,js.JSObject values,int xflags);
	public void onRestortInitValues(T comp,js.JSObject initVals);
	public void onPaneCollapse(T comp,boolean collapsed);
```

### DefaultListener
```
DefaultListener（客户端JS监听父类，里面包含了许多客户端事件方法。
==============================
DefaultListener是客户端JS监听的父类，在编写客户端监听时需要继承此类，它提供了诸多的客户端事件方法。
DefaultListener常用的方法及说明：
	dataSetFieldPosting(DataSet dataSet,DataSetEvent event){}--->行提交前执行此方法，通过参数可以获取如下属性值。
	 * event.columnIndex : 列，
	 * event.value : oldValue
	 * String columnName = dataSet.getColumn(event.columnIndex).name;
	dataSetFieldPosted(DataSet dataSet,DataSetEvent event){}--->行提交后执行此方法。
	dataSetRowNavigating(DataSet dataSet,DataSetEvent e){}--->行移动前执行此方法。
	dataSetRowNavigated(DataSet dataSet,DataSetEvent e){}--->行移动后执行此方法。
	dataSetRowAdded(DataSet dataSet,DataSetEvent e){}--->添加行后执行此方法。
	dataSetRowDeleting(DataSet dataSet,DataSetEvent e){}--->行删除前执行此方法。
	dataSetRowDeleted(DataSet dataSet,DataSetEvent e){}--->行删除后执行此方法。
	dataSetSaving(DataSet dataSet,DataSetEvent e){}--->存盘前执行此方法。
	dataSetSaved(DataSet dataSet,DataSetEvent e){}--->存盘后执行此方法。
	dataSetRefreshing(DataSet dataSet,DataSetEvent e){}--->dataset刷新前执行此方法。
	dataRefreshed(DataSet dataSet,DataSetEvent e){}--->dataset刷新后执行此方法。
	dataLoading(DataSet dataSet,DataSetEvent e){}--->数据加载前执行此方法。
	onDataLoad(DataSet dataSet,DataSetEvent e){}--->数据加载后执行此方法。
	dataLoaded(DataSet dataSet,DataSetEvent e){}--->数据加载后执行此方法。
	dataSetRowSelected(DataSet dataSet,DataSetEvent e){}--->行选中后执行此方法。
	dataSetRowSelecting(DataSet dataSet,DataSetEvent e){}--->行选中前执行此方法。
	dataSetAllRowSelecting(DataSet dataSet,DataSetEvent e){}--->全选前执行此方法。
	dataSetAllRowSelected(DataSet dataSet,DataSetEvent e){}--->全选后执行此方法。
	
例子样式如界面，界面定义窗口号为[91.DefaultListenerDoc.10]
```
### DefaultChartListener
```
    public void createChart(Chart chart);--->创建Chart事件
	public void dblClickChart(Chart chart);--->双击图形事件
	public void onEChartRefresh(BaiduEChart chart,js.baidu.echart.EChartOption echartOpts);--->图形刷新事件（常用）
```
