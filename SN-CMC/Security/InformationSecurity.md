## 信息安全

### 功能说明

用于说明N9B底层提供的信息安全解决方案。

### 身份认证

#### 登录验证

##### 安全输入
````properties
密码不显示明文。
密码经非对称加密后，Post传输。
````
![密码安全传输](help/SN-CMC/Security/img/EncryptedTransmission.png)

##### 安全传输

1. 加密传输
```properties
加密算法：非对称算法目前采用RSA,加密等级(位数)可由企业自己生成密钥或证书控制。
配置方式：Login.PWDTransEncoderGenerator=snsoft.commons.util.security.RSATextEncoderGenerator.new?KeyGenMode=new&RSAKeySize=2048
    参数说明：
        KeyGenMode 使用新建密钥对，不使用默认密钥对。
        RSAKeySize 秘钥长度，推荐2048。
```

1. 加盐传输（防登录重放）
```properties
在对密码进行加密时，使用时间戳混合，时间戳由服务端下发，时间窗为1分钟，超过时间时报错。
```
**获取时间戳**
![密码安全传输](help/SN-CMC/Security/img/TIME.png)
**测试时间戳过期**
![密码时间窗超时](help/SN-CMC/Security/img/TIMEOUT.png)

##### 安全策略

1. 各种限制
```properties
系统具有口令复杂度限制功能，并在设置密码、修改密码、重置密码的时候在服务端进行密码复杂度校验：
（1）口令复杂度要求：(格式)
        密码强度配置：Login.PWDCHECK=正则匹配:提示信息
（2）不能使用和用户名相同的密码。
（3）用户首次登录时，强制用户修改密码：
        Login.ForceResetInitialPwd=true
（4）强制定期更改密码，并根据系统实际使用情况环境设置密码更新周期频率（单位：天）：
        APP.PWDVALIDDAYS=7
（5）设置密码、修改密码、重置密码界面提示“不建议将密码设置成与常用软件、网站相同或相似的用户名和密码组合”。
（6）修改后的密码不能与原密码一致。
```

1. 模糊错误提示（防暴力猜解）
```properties
身份认证时，防止暴力猜解账号口令：
（1）在登录认证失败时提示信息未明确给出账号不存在。
（2）记录尝试次数，当超过阈值时（例如五次），锁定用户，错误次数可配：
        Login.MaxPWDTimes 最大口令错误次数,超出该次数后不允许登录，登录成功后将清空登录的错误次数。
        Login.RedisPWDTimes 当天允许口令错误次数,超出该次数后24小时内不允许登录，登录成功后将清空登录的错误次数，默认5次。
（3）支持图片验证码或短信验证,验证码为一次验证（Spring-Config.xml配置）：
        <!--滑块验证码-->
        <bean id="snsoft.login.VerifyCodeService" class="snsoft.bas.ui.service.homepage.impl.SlideVerifyCodeServiceImpl"/>
 (4) 支持Google验证码验证, 底层命名beanId为:"snsoft.login.ReCaptchaVerifyCodeService" 
```


1. 防认证绕过
```properties
1)数据操作点均有身份认证,不仅限于最后一步
2)目前对于浏览器应用,"同一客户端"的认定,目前依赖于浏览器端Cookie对httponly,secure,samesite的控制情况,如用户自己基于开源的浏览器源码进行修改,使得这几个标志可随意设置的话,目前也无其他控制办法.
```
**HttpOnly标识**
![HttpOnly标识](help/SN-CMC/Security/img/SetCookie.png)


#### 登录提示

```properties
应在用户成功地建立会话后显示用户上一次成功建立会话或建立会话失败时的信息。
系统应记录每次的失败和成功登录日志，并在用户成功登录后，展示上一次成功或失败登录信息，至少包括登录时间、登录地点，用户可发现账号被盗用的情况，及时采取措施。
配置开启选项：
snsoft.PreLoginAudit=true
```

![登录提示](help/SN-CMC/Security/img/PreLoginAudit.png)

#### 防认证绕过

```properties
数据操作点均有身份认证,不仅限于最后一步。
目前对于浏览器应用,"同一客户端"的认定,目前依赖于浏览器端Cookie对httponly,secure,samesite的控制情况,如用户自己基于开源的浏览器源码进行修改,使得这几个标志可随意设置的话,目前也无其他控制办法。
```

### 会话安全

#### 会话标识
```properties
（1）会话标示生成机制和安全算法可信任.
（2）用户唯一性会话可配置.会话标识中有加密内容,无规律性.
```

**验证方式**
`curl -i "http://127.0.0.1:1020/N9Bhelp/uiinvoke/sv-SN-CORE.LoginUIService.refreshAuthcode"`
![会话标识设置](help/SN-CMC/Security/img/SetCookie.png)

#### 防固定会话攻击
```properties
（1）用户登录后，应重新分配会话标识，防止会话固定攻击。
    Login.ForceResetInitialPwd=true
（2）不允许同一用户ID的并发登录 。
    Login.MaxSessionPerUser=1
```

**验证方式**
（1）登录系统查看请求sessionid，退出登录重新登录后查看请求sessionid信息，前后对比
（2）使用浏览器登录系统，同时打开另一浏览器登录，登录成功，前者被挤掉线。

#### Cookie安全性

```properties
Cookie中不含有用户或业务敏感信息,不含有密码信息。
非专为JS设置的Cookie项,均有HttpOnly属性。
```

![会话标识设置](help/SN-CMC/Security/img/SetCookie.png)

#### 会话时间

```properties
会话时间可配置,默认30分钟。
选项：Login.Session.Interval（单位/秒）
```

#### 会话退出

```properties
退出登录后,会话立即终止.输入登录后的访问地址,最多能打开空白页面(页面静态化),无法查出数据。
空闲一定时间(可配置时间)后,服务端会话会删除。
```

**验证方式**
系统右上角退出登录，输入单据地址访问查看。

### 密码存储

#### 注意事项

**注意应用系统默认密码一定要使用系统加密方式加密后配置在配置文件中，系统默认的配置只是使用了默认加密方式进行了加密。**

默认密码配置：`DATA.DefaultPassword`

#### 加密方式

**使用RSA加盐并签名处理后存储 （推荐）**
```properties
密码存储加密：加盐并签名（SHA1WithRSA）
    Login.PWDStoreEncoder=snsoft.bas.auth.util.RSAPasswordSaltEncoder.new
参数： salt 盐
配置示例：Login.PWDStoreEncoder=snsoft.bas.auth.util.RSAPasswordSaltEncoder.new?salt=xx
默认盐串为长度1024的固定字符串。
```

**使用RSA签名处理后存储**
```properties
密码存储加密：签名（SHA1WithRSA）
    Login.PWDStoreEncoder=snsoft.bas.auth.util.RSAPasswordEncoder.new
```

#### 数据升级

**即将上线的项目可忽略此项，在本文档选用适当配置即可。**

##### 默认加密升级

**使用场景：**
应用服务之前使用默认加密方式，需要提升安全等级，密码今后不再存储明文，希望使用高轻度加密方式进行密码存储。

**升级程序为一次性的，跑过此升级程序的不可以再使用，否则会多重加密。**

**升级步骤及注意事项**
```properties
备份用户数据
默认密码加密后配置在系统配置中
升级程序升级（指定加密方法PWDStoreEncoder,自行修改）
新增Login.PlainPasswordLogin.use=false 关闭登录密码明文匹配
新增密码加密配置Login.PWDStoreEncoder
```

**升级程序**
```properties
PWDStoreEncoder = "snsoft.bas.auth.util.RSAPasswordSaltEncoder.new"
//获取密码加密方式
cfgDataSource = getUserSession().getConfigDataSource(true)
pwdEncoder = snsoftx.bas.auth.util.EncoderUtils.getPasseordEncoder(cfgDataSource)
//判断是否使用加密
if pwdEncoder == null
	param = newHashMap()
	param.put("DATASOURCE",cfgDataSource)
	encoder0=snsoft.commons.util.InvokeUtils.invokeJavaV(PWDStoreEncoder,4,param)
	processUserPwd(encoder0)
else
	println("当前系统使用的非默认加密方式，密码可能无法解密，升级程序停止！！！")
end if

proc processUserPwd(encoder)
	cfgDataSource = getUserSession().getConfigDataSource(true)
	ucfgDB=getDatabaseByDSID(snsoft.api.util.SnsoftConst.Dsid_UConfig)
	sqlCnt="select count(*) from users"
	cnt = ucfgDB.query1(sqlCnt)
	sql = "select usercode, password from users"
	rs = ucfgDB.queryReadDataSet(sql)
	userUpDate=new snsoft.dx.UpdateData()
	cntnum=1
	ucfgDB.beginTrans()
	rollback = true
	try
		while rs.nextRow()
			ucode = rs.getValue(0)
			pwd = rs.getValue(1)
			getProgress().setProgress("处理第【"+cntnum+"】个用户...",cntnum,cnt)
			if pwd != null
				pwd2 = pwd
				if !(pwd.charAt(0) == '`' and pwd.charAt(pwd.length() - 1) == '`')
					//未加密 直接 加密
					pwd2 = encoder.encodeText(pwd)
				else
					//已加密 进行 解密
					pwd1 = snsoft.commons.util.StrUtils.convertPasswordString(pwd, false)
					//加密
					pwd2 = encoder.encodeText(pwd1)
				end if
				data=newHashMap()
				data.put("usercode",ucode)
				data.put("password",pwd2)
				userUpDate.addUpdate("users",data)
				getProgress().addProgressMessage("正在加密第【"+cntnum+"】个用户...")
			end if
			cntnum=cntnum+1
			//批量处理
			if userUpDate.size() == 5000
				ucfgDB.updateData(userUpDate,true)
				userUpDate.clear()
			end if
		end while
		//剩余
		if userUpDate.size() > 0
			ucfgDB.updateData(userUpDate,true)
		end if
		rollback = false
	catch ex
		println("处理异常："+ex)
	finally
		rs.close()
		ucfgDB.commitTrans(rollback)
	end try
end proc
```

##### 明文密码升级

**使用场景：**
应用系统中有些密码使用明文存储（如默认密码或者直接sql修改的密码），需要将这些密码使用当前系统加密方式进行升级。

**本升级程序不能适用于使用如下加密方式，使用下面加密方式的请自行升级！！！**
`SpringPasswordEncoder`

**升级步骤及注意事项**
```properties
备份用户数据
默认密码加密后配置在系统配置中
(可选)新增Login.PlainPasswordLogin.use=false 关闭登录密码明文匹配
```

```properties
#升级程序
cfgDataSource = getUserSession().getConfigDataSource(true)
ucfgDB=getDatabaseByDSID(snsoft.api.util.SnsoftConst.Dsid_UConfig)
sqlCnt="select count(*) from users"
cnt = ucfgDB.query1(sqlCnt)
sql = "select usercode, password from users"
rs = ucfgDB.queryReadDataSet(sql)
userUpDate=new snsoft.dx.UpdateData()
cntnum=1
ucfgDB.beginTrans()
rollback = true
try
	while rs.nextRow()
		ucode = rs.getValue(0)
		pwd = rs.getValue(1)
		getProgress().setProgress("处理第【"+cntnum+"】个用户...",cntnum,cnt)
		if pwd != null and !(pwd.charAt(0) == '`' and pwd.charAt(pwd.length() - 1) == '`')
			pwd2 = snsoftx.bas.auth.util.EncoderUtils.encodePassword(cfgDataSource,pwd)
			data=newHashMap()
			data.put("usercode",ucode)
			data.put("password",pwd2)
			userUpDate.addUpdate("users",data)
			getProgress().addProgressMessage("正在加密第【"+cntnum+"】个用户...")
		else
			getProgress().addProgressMessage("第【"+cntnum+"】个用户已加密，跳过...")
		end if
		cntnum=cntnum+1
		//批量处理
		if userUpDate.size() == 5000
			ucfgDB.updateData(userUpDate,true)
			userUpDate.clear()
		end if
	end while
	//剩余
	if userUpDate.size() > 0
		ucfgDB.updateData(userUpDate,true)
	end if
	rollback = false
catch ex
	println("处理异常："+ex)
finally
	rs.close()
	ucfgDB.commitTrans(rollback)
end try
``` 

### 访问控制

#### 未授权访问

```properties
未登录的用户禁止访问登录后的资源（有一部分空白页面可以打开,但查不出数据内容）。
```

**验证方式**
未登录情况下打开单据。

#### 目录列表浏览

```properties
服务器默认未开启目录列表功能。
```

#### 越权

```properties
所有数据操作,均有数据权限校验，权限结构可配置。
```

参见权限文档：[权限定义](help.html?helpFile=help/SN-CMC/Limit/Datalimit.md#操作权限配置)

#### 防数据重放

```properties
机制：X-DUPPOSTCHK-TOKEN
在HTTP头中自定义属性，加入一个随机产生的token，并且该信息不存在于Cookie之中，服务端接收到请求后验证此属性，如果请求中token内容不正确，则认为可能是重放攻击而拒绝该请求。
校验开启配置：
#缓存Store
UI.DupPostTokenStore=springredis:redisTemplate
#token失效时间 /分钟 默认5分钟
UI.DupPostTokenStoreInv=5
```

**验证方式**
```properties
浏览器F12，在控制台执行如下代码得到Token
使用PostMan或者Cmd控制台将token添加到请求头中去：
示例：curl -H X-DUPPOSTCHK-TOKEN:_kocc29q8_123131 -X POST -i "http://127.0.0.1:1020/N9Bhelp/uiinvoke/sv-SN-CORE.LoginUIService.refreshAuthcode"
```

![防数据重放](help/SN-CMC/Security/img/Token.png)

### 输入校验

#### 输入验证

```properties
底层提供了统一的输入校验配置。
```

![输入校验](help/SN-CMC/Security/img/Valid.png)

数据校验文档参见：[有效性校验](help.html?helpFile=/help/SN-CMC/Service/VO/DataValid.md)。

#### 防sql注入

```properties
SQL传入参均使用SQL参数绑定方式处理,无拼接字符串。
```

**示例：sql被转义**
![防sql注入](help/SN-CMC/Security/img/sql1.png)
![防sql注入](help/SN-CMC/Security/img/sql2.png)

### 输出验证

**应用系统应防止XSS跨站脚本攻击。**

```properties
对输出页面显示的数据内容,有根据其拼接在Html或JS中,做相应的转义处理，例如，防止反射性和存储型 XSS 攻击。
```

测试示例：
数据库修改，备注列和文件地址列，客户端备注列正常展示，地址列使用HtmlRender

**修改数据：**

![修改数据](help/SN-CMC/Security/img/dbMy.png)

**备注展示：（脚本无法执行）**

![脚本无法执行](help/SN-CMC/Security/img/nuIn.png)\

**地址展示：（屏蔽不必要的脚本）**

![屏蔽不必要的脚本](help/SN-CMC/Security/img/render.png)


### 数据安全

#### 报文加密

```properties
一般仅使用HTTPS,并未额外加密。
对外接口N9B有标准的接入系统服务，规范了各种报文加密方式。
```

接入系统文档参见：[接入系统](help.html?helpFile=/help/SN-ACS/acs/acs.md)、[加密验签](help.html?helpFile=/help/SN-ACS/acs/acsSecurity.md)

#### 多余信息返回

**不同界面,不同功能,均返回不一样的数据内容.无用字段均返回空值或0。**

#### 敏感数据缓存

**浏览器端未缓存需要权限才能获取到的任何数据**

#### 敏感数据脱敏

**客户、用户及公司内部敏感数据在显示时，应进行脱敏处理。**
```properties
界面定义列配置Render：uiprops.renderer="new snsoft.sheet.render.DesensitizationCellRender({preKeep:2,endKeep:3})"
参数说明：
    preKeep：前保留字符个数
    endKeep：末尾保留字符个数
```
![敏感数据脱敏](help/SN-CMC/Security/img/Desensitization.png)

#### 个人信息

```properties
系统仅保存了必要的用户信息。
用户信息目前并无对外接口可以访问,仅为系统自用。
```

**业务层产品开发用户注册应遵循如下标准**

```properties
（1）应仅采集和保存业务必需的用户个人信息。
（2）应禁止未授权访问和非法使用用户个人信息 
（3）在收集个人信息前，如APP用户注册账号,WEB系统用户注册账号，应通过用户主动勾选【隐私声明】同意框获取用户同意。
用户注册同意功能的设计，应遵循以下标准：
1.同意的时机，在没有账号的用户首次进入APP或访问系统时，弹出【隐私声明】并收集用户同意；
2.同意的方式，正确的方式为用户主动勾选同意（Opt-in），不正确的方式包括缺省设置同意框勾选、点击注册按钮代表同意【隐私声明】、用户主动勾选不同意（Opt-out）、用户服务协议捆绑同意等方式；
3.同意的描述，同意按钮右侧可以描述为：“我已知晓本产品（或服务）收集上述个人信息，并同意对其的收集，使用行为”；
3.同意的方式，用户应将下拉框拖到最下面才能点击提交按钮，用户应以自由的方式同意对其某一个或某些个人信息的收集，数据控制者应避免进行‘一揽子’个人信息收集的同意，如用户必须同意APP获取麦克风权限，才可以使用APP的功能；
4.同意的记录，系统后台应记录用户同意的时间、同意的隐私声明版本号、用户标识ID、当前同意的状体等关键信息；
5 同意的撤回、APP或系统应提供给用户撤回同意的按钮或菜单；
```

### 安全审计

系统记录详细的审计日志：《管控中心》 -> 《安全管理》 -> 《安全审计日志》

![审计菜单](help/SN-CMC/Security/img/safe.png)

审计日志文件输出配置（logback）
```xml
<appender name="Security" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>sec-log.log</file>
    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
        <fileNamePattern>sec-log.%d{yyyy-MM-dd}.log</fileNamePattern>
        <maxHistory>365</maxHistory>
        <totalSizeCap>50GB</totalSizeCap>
    </rollingPolicy>
    <filter class="ch.qos.logback.core.filter.EvaluatorFilter">      
        <evaluator class="ch.qos.logback.classic.boolex.GEventEvaluator"> 
            <expression>
            e.loggerName.startsWith("snsoft.security.")
            </expression>
        </evaluator>
        <OnMismatch>DENY</OnMismatch>
        <OnMatch>ACCEPT</OnMatch>
    </filter>
    <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
        <charset>utf-8</charset>
        <pattern>%d|%level|%logger{0}|%msg%n</pattern>
    </encoder>
</appender>

<root>
    <level value="INFO" />
    <appender-ref ref="Security" />
    <appender-ref ref="Console" />
</root>
```

### 系统容错

```properties
底层提供统一的4xx.html和5xx.html错误界面，有需要的项目自行配置Tomcat和Nginx或者其他负载。
位置：静态资源根目录下error文件夹中。
```
[4xx](error/4xx.html)、[5xx](error/5xx.html)

### 互联网访问

**建议使用SSL 3.0以上相对高版本的协议**

### 文件上传

#### 防止任意文件上传

```properties
在服务端对文件大小、扩展名均有检验，并可配置。
    Attach.FileAcceptTypes=文件扩展名，默认bmp,jpg,jpeg,png,tiff,gif,xlsx,xls,docx,doc,pptx,ppt,txt,xml,pdf,zip
    Attach.FileSize=文件大小，默认10MB，单位/kb
上传文件一般有专用服务器或存储设备保存,不会放在web目录中,不会出现被以http的方式直接打开而被恶意利用钓鱼的情况。
上传的文件文件名目前为文件MD5摘要码。
```

#### 输入校验

```properties
输入输出均有校验和处理。
```

### 文件下载

#### 文件名称

```properties
上传文件如需指定文件名不可包含路径,文件内容存储并未使用原文件名。
```

#### 防任意文件下载

```properties
下载文件参数均为文件存储表的ID或文件的HashID,参数不可包含..
```

#### 文件越权下载

```properties
有权限控制的文件,下载时验证了用户权限,仅知道下载文件需要的参数无法下载文件。
配置：DYNDOCROOT.Permission=snsoft.ui.controller.DynAccessPermission.new
```

### 图形验证码

```xml
<!--登录:滑块验证码-->
<bean id="snsoft.login.VerifyCodeService" class="snsoft.bas.ui.service.homepage.impl.SlideVerifyCodeServiceImpl"/>
```
