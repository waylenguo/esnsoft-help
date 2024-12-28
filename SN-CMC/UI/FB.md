## 前后端分离

|日期|作者|备注|
|------|------|------|
|2020-07-31|冀小雷|初步|

### 设计

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th align="center" colspan='1'>接入</th>
	</tr>
	<tr>
		<td>1. 接入器共用UIInvokeController；</td>
	</tr>
	<tr>
		<td>2. 使用远程调用接口：
		http://[host]:[port]/[path]/uiinvoke/[wsp]/[lang]/[theme]/sv-[Bean.Method]?_reqFmt_=[]&_resFmt_=[]；<br/>
			(1)\_reqFmt_：指定参数转换Bean（SN-UI.UIInvokeRequestConverter~）；<br/>
			(2)\_resFmt_：指定结果转换Bean（SN-UI.UIInvokeResponseConverter~）；<br/>
			**注意：参数及结果转换bean为非必须。**
		</td>
	</tr>
	<tr>
		<td>3. 所有调用的方法必须注解@Remoteable或接口注解@Remoteable；
		</td>
	</tr>
	<tr>
		<th align="center" colspan='1'>调试</th>
	</tr>
	<tr>
		<td>[查询]();[存盘]();[远程]()</td>
	</tr>
</table>

### 查询

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th align="center" colspan='2'>查询接口说明文档</th>
	</tr>
	<tr>
		<th align="center" rowspan='5'>格式</th>
		<td>http://~/sv-[Bean.Method]?_reqFmt=ForQuery&_resFmt_=ForQuery</td>
	</tr>
	<tr>
		<td>1. Bean.Method：查询方法，如"SN-HELP.SalorderUIService.queryEntryUI"
		<br/>(1)参数类型必须为QueryParams子类；
		<br/>(2)返回类型必须为SaveParams或其子类；
		</td>
	</tr>
	<tr>
        <td>2. \_reqFmt_=ForQuery：指定查询参数转换Bean，默认"SN-UI.UIInvokeRequestConverterForQuery"；
    </tr>
	<tr>
		<td>3. \_resFmt_=ForQuery：指定返回结果转换Bean，默认"SN-UI.UIInvokeResponseConverterForQuery"；
	</tr>
	<tr>
		<td>4. Post请求，Body为JSON格式，内容参见**固定参数**；
	</tr>
	<tr>
		<th align="center" rowspan='5'>固定参数</th>
		<td>1. rowsPerPage：分页行数</td>
	</tr>
	<tr>
		<td>2. pageNo：页数，从1开始，与rowsPerPage配合使用；</td>
	</tr>
	<tr>
		<td>3. cascade：是否级联查询，默认false；</td>
	</tr>
	<tr>
		<td>4. castbls：级联查询时指定级联查询的物理表名，多个逗号分隔；</td>
	</tr>
	<tr>
		<td>5. loadopts：加载选项；
			<br/>(1)1：加载列类型信息(2=浮点;4=整型;12=文本;91=日期)；
			<br/>(2)2：加载列校验信息(**格式及说明参见TODO**)；
			<br/>(3)4：加载列默认值；
		</td>
	</tr>
	<tr>
		<th align="center" rowspan='8'>返回结果</th>
		<td>1. data：查询结果数据，格式为[{column:value}]；
			<br/>(1)空值过滤；
		</td>
	</tr>
	<tr>
		<td>2. tableColumns：列信息，格式为<br/><pre>
		{<br/>
			tblname:{<br/>
				colname:{<br/>
					prop_name:prop_value<br/>
				}<br/>
			}<br/>
		}
		</pre></td>
	</tr>
	<tr>
		<td>3. pageSumCount：总页数</td>
	</tr>
	<tr>
		<td>4. totalRows：总行数</td>
	</tr>
	<tr>
		<td>5. clientProperties：返回到客户端的附加属性</td>
	</tr>
	<tr>
		<td>6. success：true=成功；</td>
	</tr>
	<tr>
		<td>7. code：异常编码</td>
	</tr>
	<tr>
		<td>8. message：异常信息</td>
	</tr>
</table>

### 存盘


<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th align="center" colspan='2'>存盘接口说明文档</th>
	</tr>
	<tr>
		<th align="center" rowspan='4'>格式</th>
		<td>http://~/sv-[Bean.Method]?_reqFmt_=ForSave&_resFmt=ForSave</td>
	</tr>
	<tr>
		<td>1. Bean.Method：查询方法，如"SN-HELP.SalorderUIService.saveOrderUI"
		<br/>(1)参数类型必须为SaveParams；
		<br/>(2)返回类型必须为SaveResults；
		</td>
	</tr>
	<tr>
		<td>2. 请求参数:
		<br/>(1)\_reqFmt_=ForSave：指定存盘参数转换Bean，默认"SN-UI.UIInvokeRequestConverterForSave"；
		<br/>(2)\_resFmt_=ForSave：指定返回结果转换Bean，默认"SN-UI.UIInvokeResponseConverterForSave"；
	</tr>
	<tr>
		<td>3. Post请求，Body为JSON格式，内容参见**固定参数**；
	</tr>
	<tr>
		<th align="center" rowspan='2'>固定参数</th>
		<td>1. values：存盘数据<br/>
		[{colname:colvalue,insertMode:true,updateMode:true,deleteMode:true}]
		</td>
	</tr>
	<tr>
		<td>2. clientParams：存盘附加参数，JSON格式；</td>
	</tr>
	<tr>
		<th align="center" rowspan='8'>返回结果</th>
		<td>1. replacedValues：存盘后需要带回到客户端的列值；<br/>
		[{table:'',replaceValues:[{}]}]
		</td>
	</tr>
	<tr>
		<td>2. success：true=成功；</td>
	</tr>
	<tr>
		<td>3. code：异常编码</td>
	</tr>
	<tr>
		<td>4. message：异常信息</td>
	</tr>
</table>

### 远程

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th align="center" colspan='2'>远程接口说明文档</th>
	</tr>
	<tr>
		<th align="center" rowspan='3'>格式</th>
		<td>http://~/sv-[Bean.Method]</td>
	</tr>
	<tr>
		<td>1. Post请求，Body为JSON数组，与方法参数对应；
	</tr>
	<tr>
		<td>2. 返回结果为任意类型；
	</tr>
	<tr>
		<th align="center" colspan='2'>常用远程方法</th>
	</tr>
	<tr>
		<th align="center" rowspan='5'>码表相关</th>
		<td>Bean名称:snsoft.bas.ui.service.codedata.CodeDataService<br/>
		码表参数：{CODEDEFID:'',JSONFILTER:{},,,}
		</td>
	</tr>
	<tr>
		<td>1. 获取多个名字：getCodeNames({},[code1,code2,,])</td>
	</tr>
	<tr>
		<td>2. 获取单个名字：getCodeName({},code)</td>
	</tr>
	<tr>
		<td>3. 获取码表列：getCodeDataColumns({})</td>
	</tr>
	<tr>
		<td>4. 获取码表数据：loadCodeData({})</td>
	</tr>
	<tr>
		<th align="center" rowspan='2'>分码</th>
		<td>1. 编码规则分码AccodeService<br/>
		    远程：sv-SN-BAS.AccodeService.makeCode<br/>
            参数：[表名,列名,编码规则/单据号,依赖数据(可为空)]<br/>
            返回：编码值
		</td>
	</tr>
	<tr>
		<td>2. 单据编码规则分码SheetAccodeService<br/>
		    远程：sv-SN-Busi.SheetAccodeService.getSheetAccodeInfo<br/>
            参数：{<br/>
                    "sheetcode":"",<br/>
                    "colname":"",<br/>
                    "needCodelike（是否需要返回codelike）":true/false,<br/>
                    "busibcode（匹配部门）":"",<br/>
                    "corpbcode（匹配公司）":"",<br/>
                    "refData（依赖数据(可为空)）":{}<br/>
                  }<br/>
            返回：编码信息
		</td>
	</tr>
	<tr>
        <th align="center" rowspan='1'>获取服务器时间</th>
        <td>远程：st-snsoft.commons.util.DateUtils.todayTime
        </td>
    </tr>
</table>