## 接入系统加密验签

|日期|作者|备注|
|------|------|------|
|2019-09-09|冯继亮|初稿|

### 概述
1. 令牌、签名、加密是数据安全相关的功能，每个接入方按照自身需求分别配置。
1. 令牌相当于临时通行证，令牌是密文传输所以需要配置加密方式，令牌申请一次默认生效两小时。
1. 加密和签名生成的信息均为一段密文字符串，区别主要在于：
	- 加密生成的密文信息，是可逆的，主要是为了防止信息泄露。
	- 签名生成的密文信息，是不可逆的，主要是为了防止信息被篡改。

### 令牌

#### 令牌获取

请求地址： `http://ip:port/应用名/token/{sender}`
如：http://127.0.0.1:1030/acs/token/SN-ACS

```json
{
    "status": "success",
    "data": "26bb23e4e497235349d3c09cf7707b17ad3f92c897f451192911ffad319f43e57ea76fecd66e75a188837ac9ddf667583ea702fd910c24ba"
}
```

<span style="color:red">注：申请令牌时返回的令牌为密文，数据传输时令牌为明文。</span>

### 签名

#### 生成方式
	
1. 请求内容的字符串+秘钥  进行MD5码编码。
1. 签名字符串头部信息（不包括签名Sign）、数据内容及密钥。
1. 签名字符串拼接前需按key排序。
1. 签名字符串过滤掉内容为空的列。

```
请求数据如下：
		{
    		"Head": {
		        "Sign": "97235349d3c09cf7707b17ad3f92c897f45",
		        "Token": "11ffad319f43e57ea76fecd66e75a188",
		        "Ccode": "000001",
		        "Version": ""
		    },
		    "Data": "{具体业务数据}"
		}
拼成的字符串：
		Ccode=000001&Data={具体业务数据}&SecretKey={接入方密钥}&Token=11ffad319f43e57ea76fecd66e75a188
```

### 加密

- 对称加密，双方持有同一密钥串，效率高，安全性低。
- 不对称加密，加密方持有公钥，解密方持有私钥，效率低，安全性高。
- 混合加密，使用不对称加密方式加密随机生成的对称加密密钥串，兼具以上两者的优点，建议使用此加密方式。

#### 对称加密

##### 加密方式
- AES
- DES

##### 密钥生成
1. 通过接入方界面的【设置密钥】按钮，录入密钥（不录入时会生成默认密钥串）。
1. 通过【使用新密钥】按钮启用密钥。

注：默认密钥串按接入方+当前时间生成。

#### 不对称加密

##### 加密方式
- RSA
- RSA2

注：RSA与RSA2的区别在于加密内容的大小，RSA（加117，解128），RSA2（加245，解256）。

##### 证书生成
1. 通过接入方界面的【生成证书】按钮生成，证书生成后会自动下载公钥证书用于提供给对应接入方进行加密。
1. 考虑到接入系统接出信息或发送回执，接入方可以通过【上传加密证书】按钮，上传公钥证书。

#### 混合加密


##### 加密方式
- RSA+AES
- RSA+DES

##### 证书及密钥
证书：[证书生成](help.html?helpFile=help/SN-ACS/acs/acsSecurity.md#证书生成)
密钥：优先使用接入方上定义的密钥串，若未定义会采用随机生成的密钥串。

注：建议采用随机密钥。

##### 密文信息说明
![密文信息说明](help/SN-ACS/acs/img/securityData.png "securityData")
详情参见[混合加密说明](help/SN-ACS/acs/attach/接入系统混合加密说明.doc)

### 时序图


#### 数据接入

@startuml

Title 数据接入
participant three   as "三方系统"
participant acs   as "接入系统"
participant client   as "业务系统(包含ACS/MQ客户端)"

activate three
three -> acs : 三方系统请求
activate acs
acs -> acs : 查找接入配置
acs -> acs : 解密
acs -> acs : 验签
acs -> acs : 验证令牌
acs -> client : 服务调用
activate client
client -> client : 服务执行
client -> acs : 返回处理结果
deactivate client
acs -> acs : 处理返回数据
acs -> acs : 返回数据加密
acs -> three : 响应三方系统
deactivate acs
deactivate three

@enduml

#### 数据接出

@startuml

Title 数据接出
participant client   as "业务系统"
participant acs   as "接入系统"
participant three   as "三方系统"

activate client
client -> acs : 数据接出请求
activate acs
acs -> acs : 查找接入配置
acs -> acs : 生成签名
acs -> acs : 数据加密
acs -> three : 服务调用
activate three
three -> three : 数据处理
three -> acs : 返回处理结果
deactivate three
acs -> acs : 回执数据解密
acs -> client : 响应业务系统
deactivate acs
deactivate client

@enduml
