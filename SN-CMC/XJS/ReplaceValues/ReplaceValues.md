## DataSet值替换

作者：冀小雷  
审核：

### 概述

一般情况处理数据的远程调用后，客户端界面数据需要进行实时展示，但是为了友好型及性能、流量的考虑，不希望再次发远程刷新数据；
数据集DataSet提供标准的值替换功能，在远程调用后[存盘、远程服务等]可以同时返回修改后需要实时展示的数据，按照既定的数据规则或调用指定的方法进行界面更新，且不触发存盘。

### 数据格式

```
// 存盘
{
	"UI.[uiname]":
	{
		"ReplaceValues":
		[
			{
				key:"主键值1",
				col1:"替换列值1",
				col2:"替换列值2",
			},
			{
				key:"主键值2",
				col1:"替换列值1",
				col2:"替换列值2",
			}
		]
	}
}
// 远程调用
[
	{
		table:"界面名称/表名",
		replaceValues:
		[
			{
				key:"主键值1",
				col1:"替换列值1",
				col2:"替换列值2",
			},
			{
				key:"主键值2",
				col1:"替换列值1",
				col2:"替换列值2",
			}
		]
	}
]
```

### 时序图

#### 服务存盘

@startuml
存盘事件 -> DataSet : [saveChanges] \n收集存盘数据；
activate DataSet
	DataSet -> DataStore : [_saveData] \n远程调用，将存盘数据发送至UI层；
	activate DataStore
		DataStore -> UICompDataSaveService : [saveByService] \n将客户端JSON数据转成VO并调用存盘组件；
		UICompDataSaveService -> 业务存盘组件 : [存盘方法] \n调用DAO存储并处理返回值；
		业务存盘组件 -> UICompDataSaveService : [返回值] \n通过SaveResults.replacedValues传递；
		UICompDataSaveService -> UICompDataSaveService : [replaceDataStoreReplaceRow] \n格式转换
		UICompDataSaveService -> DataStore : [UI.name:{ReplaceValues:[{}]}] \n返回替换值
		DataStore -> DataSet : [saveChanges] \n接收替换值
		DataSet -> DataStore : [_replaceValues] \n将替换值替换到客户端存储中并显示
	DataStore -> DataSet
	deactivate DataSet
DataSet -> 存盘事件
deactivate DataSet
@enduml

#### UI存盘

@startuml
存盘事件 -> DataSet : [saveChanges] \n收集存盘数据；
DataSet -> DataStore : [_saveData] \n远程调用，将存盘数据发送至UI层；
DataStore -> UICompDataSaveService : [doSave] \n数据存盘；
UICompDataSaveService -> UIListener : [dataSetSaved] \n存盘后接口；
UIListener -> UIEvent : [putSaveInfo] \n设置替换值
UICompDataSaveService -> DataStore : [UI.name:{ReplaceValues:[{}]}] \n返回替换值
DataStore -> DataSet : [saveChanges] \n接收替换值
DataSet -> DataStore : [_replaceValues] \n将替换值替换到客户端存储中并显示
@enduml

#### 远程调用

@startuml
触发事件 -> 客户端
客户端 -> DataSet : 数据准备
客户端 -> 远程服务 : [远程方法]；
远程服务 -> TableRowValues : [构建值替换对象]；
远程服务 -> 客户端 : [返回值替换数据]；
客户端 -> DataSet : [replaceValuesByName] \n根据界面名称替换；
客户端 -> DataSet : [replaceValuesByTableName] \n根据表名替换
@enduml

### 示例

修改金额后将对应的信息回填到主子表的说明列上。

#### 服务存盘Demo说明

* 界面定义号：[SN-HELP.RV.RVService](uiinvoke/00/zh_CN/theme0/SN-HELP.RV.RVService.html)；
* 服务组件：SN-HELP.ReplaceValueService；

#### UI存盘Demo说明

* 界面定义号：[SN-HELP.RV.RVUIListener](uiinvoke/00/zh_CN/theme0/SN-HELP.RV.RVUIListener.html)；
* 服务组件：SN-HELP.ReplaceValueService；
* UI监听：snsoft.help.replacevalues.ReplaceValueListener；

#### 远程调用Demo说明

* 界面定义号：[SN-HELP.RV.RVRemoteable](uiinvoke/00/zh_CN/theme0/SN-HELP.RV.RVRemoteable.html)；
* 服务组件：SN-HELP.ReplaceValueService；
* JS监听：snsoft.help.replacevalues.ReplaceValueListener；