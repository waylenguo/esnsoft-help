## 有效性校验

|日期|作者|备注|
|------|------|------|
|2020-04-19|冀小雷|初稿|

### 注解及适用

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th colspan="7" align="center">注解及适用</th>
	</tr>
	<tr>
		<th align="center">注解</th>
		<th align="center">文本</th>
		<th align="center">数字</th>
		<th align="center">日期</th>
		<th align="center">数组</th>
		<th align="center">校验器/元数据</th>
	</tr>
	<tr>
		<td>NotNull</td>
		<td>√</td>
		<td>√</td>
		<td>√</td>
		<td>√</td>
		<td>AssertNotNull</td>
	</tr>
	<tr>
		<td>NotEmpty</td>
		<td>√</td>
		<td></td>
		<td></td>
		<td>√</td>
		<td>AssertNotEmpty</td>
	</tr>
	<tr>
		<td>NotBlank</td>
		<td>√</td>
		<td></td>
		<td></td>
		<td></td>
		<td>AssertNotBlank</td>
	</tr>
	<tr>
		<td>Cuicode</td>
		<td>√</td>
		<td></td>
		<td></td>
		<td></td>
		<td>AssertNotNull</td>
	</tr>
	<tr>
		<td>CodeTable</td>
		<td>√</td>
		<td>√(整数)</td>
		<td></td>
		<td></td>
		<td>AssertCodeTable</td>
	</tr>
	<tr>
		<td>Size(min=0,max)</td>
		<td>√</td>
		<td></td>
		<td></td>
		<td>√</td>
		<td>AssertSize</td>
	</tr>
	<tr>
		<td>Length(min=0,max)</td>
		<td>√</td>
		<td></td>
		<td></td>
		<td></td>
		<td>AssertLength<br/>AssertSize<br/>AssertPattern</td>
	</tr>
	<tr>
		<td>Pattern</td>
		<td>√</td>
		<td></td>
		<td></td>
		<td></td>
		<td rowspan='4'>AssertPattern</td>
	</tr>
	<tr>
		<td>PatternEmail</td>
		<td>√</td>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<td>PatternMobile</td>
		<td>√</td>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<td>PatternIdcardno</td>
		<td>√</td>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<td>NotZero</td>
		<td></td>
		<td>√</td>
		<td></td>
		<td></td>
		<td>AssertNotZero</td>
	</tr>
	<tr>
		<td>VOField(maxdeci=&quot;${optid}&quot;)</td>
		<td></td>
		<td>√</td>
		<td></td>
		<td></td>
		<td colspan='2'>AssertDigits</td>
	</tr>
	<tr>
		<td>Digits(integer,fraction)</td>
		<td>√(数字.+-)</td>
		<td>√</td>
		<td></td>
		<td></td>
		<td>AssertDigits</td>
	</tr>
	<tr>
		<td>Range(min=0L,max)</td>
		<td>√(数字)</td>
		<td>√</td>
		<td></td>
		<td></td>
		<td rowspan='9'>AssertRange</td>
	</tr>
	<tr>
		<td>DecimalMax(value=&quot;&quot;)</td>
		<td>√(数字.+-)</td>
		<td>√</td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<td>DecimalMin(value=&quot;&quot;)</td>
		<td>√(数字.+-)</td>
		<td>√</td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<td>Max(value=0L)</td>
		<td></td>
		<td>√</td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<td>Min(value=0L)</td>
		<td></td>
		<td>√</td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<td>Negative</td>
		<td></td>
		<td>√</td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<td>NegativeOrZero</td>
		<td></td>
		<td>√</td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<td>Positive</td>
		<td></td>
		<td>√</td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<td>PositiveOrZero</td>
		<td></td>
		<td>√</td>
		<td></td>
		<td></td>
	</tr>
</table>

### 元数据缓存

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th colspan="7" align="center">元数据缓存</th>
	</tr>
	<tr>
		<th align="center">类名</th>
		<th align="center">说明</th>
		<th align="center">DB</th>
		<th align="center">VO</th>
		<th align="center">单据</th>
		<th align="center">属性</th>
		<th align="center">参数</th>
	</tr>
	<tr>
		<td><b>ValidMetaData</b></td>
		<td colspan='6'>元数据接口:tblname+colname+reskey(message)+groups+anno<br/>+toUIConfig(UIConfig cfg)</td>
	</tr>
	<tr>
		<td>MDNotNull</td>
		<td>非空</td>
		<td>√</td>
		<td>√</td>
		<td>√</td>
		<td>reskey:notnull</td>
		<td></td>
	</tr>
	<tr>
		<td>MDNotEmpty</td>
		<td>非空对象</td>
		<td></td>
		<td>√</td>
		<td>√</td>
		<td>reskey:notempty</td>
		<td></td>
	</tr>
	<tr>
		<td>MDNotBlank</td>
		<td>非空白文本</td>
		<td></td>
		<td>√</td>
		<td>√</td>
		<td>reskey:notblank</td>
		<td></td>
	</tr>
	<tr>
		<td>MDCodeTable</td>
		<td>码表</td>
		<td></td>
		<td>√</td>
		<td>√</td>
		<td>reskey:codetable<br/>id,cmparams</td>
		<td>%0=值<br/>%1=码表id<br/>%2=码表标题</td>
	</tr>
	<tr>
		<td>MDSize</td>
		<td>尺寸</td>
		<td>√</td>
		<td>√</td>
		<td>√</td>
		<td>reskey:
			&nbsp;&nbsp;size~
			&nbsp;&nbsp;charcnt~
			min,max</td>
		<td>%0=实际size<br/>%1=min<br/>%2=max</td>
	</tr>
	<tr>
		<td>MDLength</td>
		<td>字节长度</td>
		<td>√</td>
		<td>√</td>
		<td>√</td>
		<td>reskey:
			&nbsp;&nbsp;length~
			&nbsp;&nbsp;charcnt~
			min,max</td>
		<td>%0=实际字符个数<br/>%1=min<br/>%2=max<br/>%3=最大字符个数<br/>%4=实际字节个数</td>
	</tr>
	<tr>
		<td>MDPattern</td>
		<td>正则</td>
		<td></td>
		<td>√</td>
		<td>√</td>
		<td>reskey:[patternName]<br/>regex</td>
		<td>%0=值<br/>%1=regex</td>
	</tr>
	<tr>
		<td>MDDigits</td>
		<td>数字</td>
		<td>√</td>
		<td>√</td>
		<td>√</td>
		<td>reskey:digits<br/>integer,fraction</td>
		<td>%0=值<br/>%1=integer<br/>%2=fraction</td>
	</tr>
	<tr>
		<td>MDRange</td>
		<td>数值范围</td>
		<td>√</td>
		<td>√</td>
		<td>√</td>
		<td>reskey:range~<br/>min,max</td>
		<td>%0=值<br/>%1=min<br/>%2=max</td>
	</tr>
	<tr>
		<td>MDNotZero</td>
		<td>非零</td>
		<td></td>
		<td>√</td>
		<td>√</td>
		<td>reskey:notzero</td>
		<td></td>
	</tr>
</table>

### 校验器

#### Types

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th colspan="4" align="center">校验器类型</th>
	</tr>
	<tr>
		<td>AssertValid</td>
		<td colspan='3'>父类：<br/>
			#meta:ValidMetaData<br/>
			+validate(ValidContext ctx,Object v):ValidEntity<br/>
		</td>
	</tr>
	<tr>
		<td>AssertNotNull</td>
		<td>非空</td>
		<td>nonBlank/nonBlankOnSubmit</td>
		<td></td>
	</tr>
	<tr>
		<td>AssertNotEmpty</td>
		<td>非空且非空对象</td>
		<td>xjs.NotEmptyValidCheck</td>
		<td></td>
	</tr>
	<tr>
		<td>AssertNotBlank</td>
		<td>非空白文本</td>
		<td>nonBlank/nonBlankOnSubmit</td>
		<td></td>
	</tr>
	<tr>
		<td>AssertCodeTable</td>
		<td>存在码表值</td>
		<td>editable</td>
		<td></td>
	</tr>
	<tr>
		<td>AssertSize</td>
		<td>数组/文本<br/>长度范围</td>
		<td>xjs.SizeValidCheck</td>
		<td>
			vld_size_min<br/>
			vld_size_max<br/>
			vld_size_prompt
		</td>
	</tr>
	<tr>
		<td>AssertLength</td>
		<td>文本字节长度</td>
		<td>xjs.LengthValidCheck</td>
		<td>
			vld_length_min<br/>
			vld_length_max<br/>
			vld_length_prompt<br/>
		</td>
	</tr>
	<tr>
		<td>AssertPattern</td>
		<td>正则表达式</td>
		<td>xjs.PatternValidCheck</td>
		<td>
			vld_pattern<br/>
			vld_pattern_prompt
		</td>
	</tr>
	<tr>
		<td>AssertDigits</td>
		<td>整部及小部</td>
		<td>xjs.DigitsValidCheck</td>
		<td>
			vld_digits_interger<br/>
			vld_digits_fraction<br/>
			vld_digits_prompt<br/>
		</td>
	</tr>
	<tr>
		<td>AssertRange</td>
		<td>数值范围</td>
		<td>xjs.RangeValidCheck</td>
		<td>
			vld_range_min<br/>
			vld_range_max<br/>
			vld_range_include<br/>
			vld_range_prompt<br/>
		</td>
	</tr>
	<tr>
		<td>AssertNotZero</td>
		<td>数值范围</td>
		<td>xjs.NotZeroValidCheck</td>
		<td>
			vld_notzero<br/>
			vld_notzero_prompt<br/>
		</td>
	</tr>
</table>

#### Size/Length

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th colspan="7" align="center">Size/Length说明</th>
	</tr>
	<tr>
		<th colspan='2' align="center">Size</th>
		<th colspan='4' align="center">Length</th>
		<th rowspan='2' align="center">说明</th>
	</tr>
	<tr>
		<th align="center">Collection<br/>Map<br/>Array</th>
		<th align="center">String</th>
		<th align="center">JavaField<br/>(非物理列)</th>
		<th align="center">NCHAR<br/>NVARCHAR<br/>LONGNVARCHAR</th>
		<th align="center">CHAR/VARCHAR<br/>LONGVARCHAR<br/><b>(szChChar=1)</b></th>
		<th align="center">CHAR<br/>VARCHAR<br/>LONGVARCHAR</th>
	</tr>
	<tr>
		<td>size</td>
		<td colspan='4'>charcnt</td>
		<td>length</td>
		<td>范围：min~max</td>
	</tr>
	<tr>
		<td>size.min</td>
		<td colspan='4'>charcnt.min</td>
		<td>length.min</td>
		<td>至少：>=min</td>
	</tr>
	<tr>
		<td>size.max</td>
		<td colspan='4'>charcnt.max</td>
		<td>length.max</td>
		<td>至多：<=max</td>
	</tr>
	<tr>
		<td>size.fix</td>
		<td colspan='4'>charcnt.fix</td>
		<td>length.fix</td>
		<td>固定：min=max>0</td>
	</tr>
	<tr>
		<td colspan='5'>%0=实际size<br/>
			%1=min<br/>
			%2=max</td>
		<td>%0=实际字符个数<br/>
			%1=min<br/>
			%2=max<br/>
			%3=最大字符个数<br/>
			%4=实际字节个数</td>
		<td>根据项目需要<br/>使用对应参数</td>
	</tr>
</table>

#### Range

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th align="center">注解</th>
		<th align="center">Reskey</th>
		<th align="center">说明</th>
		<th align="center">范围</th>
	</tr>
	<tr>
		<td>Range</td>
		<td>reskey:range</td>
		<td>min:String/long<br/>max:String/long</td>
		<td>min<=value<=max</td>
	</tr>
	<tr>
		<td>DecimalMax</td>
		<td>reskey:range.max</td>
		<td>value:String<br/>inclusive:true</td>
		<td><[=]value</td>
	</tr>
	<tr>
		<td>DecimalMin</td>
		<td>reskey:range.min</td>
		<td>value:String<br/>inclusive:true</td>
		<td>>[=]value</td>
	</tr>
	<tr>
		<td>Max</td>
		<td>reskey:range.max</td>
		<td>value:long</td>
		<td><=value</td>
	</tr>
	<tr>
		<td>Min</td>
		<td>reskey:range.min</td>
		<td>value:long</td>
		<td>>=value</td>
	</tr>
	<tr>
		<td>Negative</td>
		<td>reskey:range.neg</td>
		<td></td>
		<td><0</td>
	</tr>
	<tr>
		<td>NegativeOrZero</td>
		<td>reskey:range.neg0</td>
		<td></td>
		<td><=0</td>
	</tr>
	<tr>
		<td>Positive</td>
		<td>reskey:range.pos</td>
		<td></td>
		<td>>0</td>
	</tr>
	<tr>
		<td>PositiveOrZero</td>
		<td>reskey:range.pos0</td>
		<td></td>
		<td>>=0</td>
	</tr>
</table>


### 文本校验

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th align="center">NotNull</th>
		<th align="center">NotEmpty</th>
		<th align="center">NotBlank</th>
		<th align="center">Size</th>
		<th align="center">Length</th>
	</tr>
	<tr>
		<td>不可为空</td>
		<td>不可为空<br/>不可以为空串</td>
		<td>不可为空<br/>不可为空串<br/>至少一个非空字符</td>
		<td>可以为空<br/>使用min/max限制<br/>不限制空白字符</td>
		<td>可以为空<br/>使用min/max限制<br/>不限制空白字符</td>
	</tr>
</table>

### 方法校验

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th align="center" colspan='4'>远程方法校验注解配置<br/>(接口需要注解ValidResource)</th>
	</tr>
	<tr>
		<th align="center">注解方式</th>
		<th align="center">Demo</th>
		<th align="center">结果</th>
		<th align="center">说明</th>
	</tr>
	<tr>
		<td>@Anno(message=&quot;{reskey}&quot;)</td>
		<td>void invoke(@NotNull(message=&quot;{sheetcode}&quot;) String sheetcode);</td>
		<td>‘单据类型’为空!</td>
		<td>reskey+anno</td>
	</tr>
	<tr>
		<td>@Anno(message=&quot;{tbl.col}&quot;)</td>
		<td>void invoke(@NotNull(message=&quot;{tbl.col}&quot;) String sheetcode);</td>
		<td>‘col’为空!</td>
		<td>tbl.col+anno</td>
	</tr>
	<tr>
		<td>@Anno(message=&quot;#常量#&quot;)</td>
		<td>void invoke(@NotNull(message=&quot;#单据类型不可为空!#&quot;) String sheetcode);</td>
		<td>单据类型不可为空!</td>
		<td>常量提示信息(推荐)</td>
	</tr>
	<tr>
		<td>@Anno(message=&quot;常量&quot;)</td>
		<td>void invoke(@NotNull(message=&quot;单据类型不可为空!&quot;) String sheetcode);</td>
		<td>单据类型不可为空!</td>
		<td>常量提示信息(不推荐)</td>
	</tr>
</table>

### ValidService

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th align="center">类别</th>
		<th align="center">普通表[二开]</th>
		<th align="center">单据表[二开]</th>
		<th align="center">普通VO</th>
		<th align="center">单据VO</th>
	</tr>
	<tr>
		<th>获取校验缓存</th>
		<td>getAssertValid(tblname)</td>
		<td>getAssertValid(sheetcode,tblname)</td>
		<td>getAssertValid(voclass,includedb)</td>
		<td>getAssertValid(sheetcode,voclass,includedb)</td>
	</tr>
	<tr>
		<th>说明</th>
		<td>界面定义实现读写<br/>
			主键->非空<br/>
			非空->非空<br/>
			文本->Size/Length<br/>
			整数-><br/>
			数值-><br/>
		</td>
		<td>单据+界面定义实现读写<br/>
			所有支持的注解(按类型覆盖表校验)
		</td>
		<td>VO服务实现读写<br/>
			所有支持的注解(按类型覆盖表校验)
		</td>
		<td>单据+VO服务实现读写<br/>
			所有支持的注解(按类型覆盖VO校验)
		</td>
	</tr>
</table>

### 客户端列属性对应

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th align="center">类别</th>
        <th align="center">类型</th>
        <th align="center">属性</th>
        <th align="center">说明</th>
        <th align="center">客户端判断逻辑<br>VUE判断逻辑使用下边描述逻辑即可</th>
	</tr>
	<tr>
		<th align="center" rowspan='3'>字符个数</th>
		<td>v_sz_i</td>
		<td>整数</td>
		<td>vld_size_min：最小个数</td>
        <th rowspan="3">一、XJS框架处理类：SizeValidCheck<br>
            注：如果v_sz_p指定具体提示信息key，以此为准。否则走下边逻辑判断。<br>
            二、XJS取提示信息key逻辑：<br>
            如果v_sz_i==null，取charcnt.max，内容：字符或汉字个数禁止大于‘%2’，当前值：‘%0’!<br>
            如果v_sz_a==null，取charcnt.min，内容：字符或汉字个数禁止小于‘%1’，当前值：‘%0’!<br>
            如果v_sz_i=v_sz_a，取 charcnt.fix，内容：字符或汉字个数必须等于‘%1’，当前值：‘%0’!<br>
            其它情况，取：charcnt，内容：字符或汉字个数应在‘%1’到‘%2’之间，当前值：‘%0’!
        </th>
	</tr>
	<tr>
		<td>v_sz_a</td>
		<td>整数</td>
		<td>vld_size_max：最大个数</td>
	</tr>
	<tr>
		<td>v_sz_p</td>
		<td>字符串</td>
		<td>vld_size_prompt：提示信息</td>
	</tr>
	<tr>
		<th align="center" rowspan='4'>文本字节数</th>
		<td>v_ln_i</td>
		<td>整数</td>
		<td>vld_length_min：最小长度</td>
        <th rowspan="4">一、XJS框架处理类：LengthValidCheck<br>
            注：如果v_ln_p指定具体提示信息key，以此为准。否则走下边逻辑判断。<br>
            二、XJS取提示信息key逻辑：<br>
(1)当v_ln_s值为1时：<br>
如果v_ln_i==null，取charcnt.max，内容：字符或汉字个数禁止大于‘%2’，当前值：‘%0’!<br>
如果v_ln_a==null，取charcnt.min，内容：字符或汉字个数禁止小于‘%1’，当前值：‘%0’!<br>
如果v_ln_i=v_ln_a，取 charcnt.fix，内容：字符或汉字个数必须等于‘%1’，当前值：‘%0’!<br>
其它情况，取：charcnt，内容：字符或汉字个数应在‘%1’到‘%2’之间，当前值：‘%0’!<br>
(2)当v_ln_s无值或不为1时：<br>
如果v_ln_i==null,取length.max，内容：字符个数禁止大于‘%2’（一个汉字等于3个字符），当前值：‘%3’!<br>
如果v_ln_a==null，取length.min，内容：字符个数禁止小于‘%1’（一个汉字等于3个字符），当前值：‘%3’!<br>
如果v_ln_i=v_ln_a，取length.fix，内容：字符个数必须等于‘%1’（一个汉字等于3个字符），当前值：‘%3’!<br>
其它情况，取：length，内容：字符个数应在‘%1’到‘%2’之间（一个汉字等于3个字符），当前值：‘%3’!<br>
        </th>
	</tr>
	<tr>
		<td>v_ln_a</td>
		<td>整数</td>
		<td>vld_length_max：最大长度</td>
	</tr>
	<tr>
		<td>v_ln_s</td>
		<td>整数</td>
		<td>vld_length_szChChar：编码类型（中文字符占用个数）</td>
	</tr>
	<tr>
		<td>v_ln_p</td>
		<td>字符串</td>
		<td>vld_length_prompt：提示信息</td>
	</tr>
	<tr>
		<th align="center" rowspan='2'>正则校验</th>
		<td>v_pn</td>
		<td>字符串</td>
		<td>vld_pattern：正则</td>
        <th rowspan="2">一、XJS框架处理类：PatternValidCheck<br>
            注：如果v_pn_p指定具体提示信息key，以此为准。否则走下边逻辑判断。<br>
            二、XJS取提示信息key逻辑：<br>
            (1)取pattern
        </th>
	</tr>
	<tr>
		<td>v_pn_p</td>
		<td>字符串</td>
		<td>vld_pattern_prompt：提示信息</td>
	</tr>
	<tr>
		<th align="center" rowspan='3'>数值精度</th>
		<td>v_dg_i</td>
		<td>整数</td>
		<td>vld_digits_integer：整数部分长度</td>
        <th rowspan="3">一、XJS框架处理类：DigitsValidCheck<br>
            注：如果v_dg_p指定具体提示信息key，以此为准。否则走下边逻辑判断。<br>
            二、XJS取提示信息key逻辑：<br>
            (1)整数部分或小数部分任意不满足，取：digits，内容：整数部分不能超过‘%1’位，小数部分不能超过‘%2’位!
        </th>
	</tr>
	<tr>
		<td>v_dg_f</td>
		<td>整数</td>
		<td>vld_digits_fraction：小数部分长度</td>
	</tr>
	<tr>
		<td>v_dg_p</td>
		<td>字符串</td>
		<td>vld_digits_prompt：提示信息</td>
	</tr>
	<tr>
		<th align="center" rowspan='4'>数值范围</th>
		<td>v_rg_i</td>
		<td>整数</td>
		<td>vld_range_min：最小值</td>
        <th rowspan="4">一、XJS框架处理类：RangeValidCheck<br>
            注：如果v_rg_p指定具体提示信息key，以此为准。否则走下边逻辑判断。<br>
            二、XJS取提示信息key逻辑：<br>
            (1)min==0 并且 max为空基础上：<br>
                v_rg_c为不空，取：range.pos0，内容：只能为正数或零!<br>
                v_rg_c为空，取：range.pos,内容：只能为正数!<br>
            (2)max==0 并且 min为空基础上：<br>
                v_rg_c为不空，取：range.neg0，内容:只能为负数或零!<br>
                v_rg_c为空，取：range.neg，内容：只能为负数!<br>
            (3)min为空，取：range.max，内容：不能大于%1!<br>
            (4)max为空，取：range.min，内容：不能小于%1!<br>
            (3)min==max，取：range.fix，内容：值必须等于%1!<br>
            (6)其它，取：range，内容：值范围应在%1-%2之间!
        </th>
	</tr>
	<tr>
		<td>v_rg_a</td>
		<td>整数</td>
		<td>vld_range_max：最大值</td>
	</tr>
	<tr>
		<td>v_rg_c</td>
		<td>整数</td>
		<td>vld_range_include：边界包含</td>
	</tr>
	<tr>
		<td>v_rg_p</td>
		<td>字符串</td>
		<td>vld_range_prompt：提示信息</td>
	</tr>
	<tr>
		<th align="center" rowspan='2'>非零校验</th>
		<td>v_nz</td>
		<td>整数</td>
		<td>vld_notzero：不等于零</td>
        <th rowspan="2">一、XJS框架处理类：NotZeroValidCheck<br>
            注：如果v_nz_p指定具体提示信息key，以此为准。否则走下边逻辑判断。<br>
            二、XJS取提示信息key逻辑：<br>
            (1)取notzero，内容：禁止为零！
        </th>
	</tr>
	<tr>
		<td>v_nz_p</td>
		<td>字符串</td>
		<td>vld_notzero_prompt：提示信息</td>
	</tr>
</table>

### 开发注意事项

1. 查询页面小数位展示；
	* 勾选限制存盘，数据库配置不会设置到界面上；
	* 配置VOListener将系统选项中的小数位设置到界面上；