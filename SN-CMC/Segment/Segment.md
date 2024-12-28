## 分词

|日期|作者|备注|
|------|------|------|
|2019-08-27|吴小海|初稿|
|2019-10-10|吴小海|重构|

### 指南
#### 概念说明
* 本程序默认基于MMSeg4j中文分词
* 提供分词服务，返回分词数组(支持自定义分词)
* 提供敏感词处理服务，返回最高级别处理程序、敏感词、敏感词替换后内容(支持自定义敏感词)

#### 使用说明

##### 分词服务

```java
snsoft.bas.segment.service.SegmentWordService.wordSegment()
```
分词服务默认使用MMSeg4j中文分词词库;如分词不准确可支持自定义分词;

##### 敏感词服务

```java
snsoft.bas.segment.service.SegmentWordService.wordSentitiveSegment()
```
敏感词服务默认使用MMSeg4j分词逻辑;匹配自定义敏感词库的敏感词;使用此功能需要先自定义敏感词并且生成敏感词词库;

##### 相关配置

System.properties 需定义扩展分词库生成文件位置 DicRootPath 默认路径：D:\snsoft90\DicRootPath
线上环境需要根据实际情况调整

### 手册		
		
#### 分词服务

* 传入“文本内容”，输出分词后的List
* 支持分词词库扩展，参考：**自定义分词** 配置

#### 敏感词服务
* 传入“文本内容”，“敏感词类型”，输出为：
  ① 输出级别最高的处理结果，禁用>审核>替换
  ② 搜索到的敏感词List
  ③ 文本内容 替换后的结果
* 支持自定义敏感词，参考：**敏感词管理** 配置
* 支持敏感词替换格式定义，参考： **敏感词管理** 配置


### 实践

1. 参考界面 [自定义分词](uiinvoke/00/zh_CN/theme0/90.Segment.AppendWordMg.html) 【测试分词】按钮
1. 参考界面 [敏感词管理](uiinvoke/00/zh_CN/theme0/90.Segment.SensitiveWordMg.html) 【敏感词测试】按钮

### 设计

#### 接口设计

|包名类名|名称|主要功能|
|------|------|------|
|snsoft.bas.segment.service.SegmentWordService| 分词服务   |对外接口|
|snsoft.bas.segment.factory.SegementAnalysisServiceFactory|分析解析工厂|调用分析解析服务|
|snsoft.bas.segment.comp.SegementAnalysisService|分析解析服务|分词解析|

|接口名称|方法名|输入说明|输出说明|
|------|------|------|------|
|SegmentWordService|wordSegment|文本内容|分词list|
|SegmentWordService|wordSentitiveSegment|文本内容|输出级别最高的处理结果，禁用>审核>替换;搜索到的敏感词List;文本内容替换后的结果|
|SegementAnalysisServiceFactory|getDefaultAnalysisService||分词实现服务|
|SegementAnalysisService|segment|文本内容|分词list|
|SegementAnalysisService|segment|文本内容,是否使用扩展词典|分词list|
|SegementAnalysisService|getSensitiveHitWords|文本内容,敏感词内容|敏感词list|
|SegementAnalysisService|genSensitiveWordLib|敏感词词库,敏感类型|生成敏感词词库结果|
|SegementAnalysisService|genSegementWordLib|扩展词库|生成扩展分词词库结果|
|SegementAnalysisService|getSysDictPath||扩展分词词库路径|


#### 配置界面

###### 自定义分词
<img src='help/SN-CMC/Segment/image/1001.png' />

###### 敏感词管理
<img src='help/SN-CMC/Segment/image/1002.png' />
<img src='help/SN-CMC/Segment/image/1003.png' />


#### 替换词格式宏

参考配置：%FORMAT
可支持扩展： <a color='red' href='#'>%FORMAT</a>













