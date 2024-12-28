## 服务配置加密

|日期|作者|备注|
|------|------|------|
|2020-06-22|张东 |初稿|

### 指南

#### 说明

**为了服务器应用配置（SN.ConfigPath目录配置文件）安全起见，底层提供了配置文件加密功能，在线上部署的时候可以将配置目录下的System.properties、WorkSpace.xml、ShardingRule.xm这些含有敏感信息的配置文件进行加密，在服务加载时再解密。**

#### 配置说明

1. 加密方式：采用密码串加密方式，有AES和DES两种，默认为AES加密。
1. 配置方式：采用JVM参数配置，-Dsn.configfile.cryptpwd=AES:密码串

#### 配置加密上传

配置好加密密码后，可在服务器文件维护功能加密上传配置文件。

功能菜单位置：《管控中心》 -> 《系统运维》 -> 《维护工具》 -> 《服务器文件维护》

### 手册

1. 加密规则：SNENC(加密后的字符)。

1. 解密规则：判断字符串是否符合加密规则，符合则解密；也可强行解密，解密失败会报错。

1. 加密工具类：snsoft.commons.security.SnConfigFileCryptUtils
    ```properties
    //是否需要解密，依据当前应用服务是否配置密码串
	public static boolean needDeCrypt()

    //加密标识
	public static boolean guessIsEnCrypt(String txt)

	//Value 加密 加密格式SNENC(加密的串)
    public static String enCryptValueIfOk(String value)
    public static String enCryptValue(String value, boolean checkNull)

    //Value 解密 判断格式SNENC(加密的串)
    public static String deCryptValueIfOk(String value)
    public static String deCryptValue(String value, boolean checkNull)

	//文件加密  加密格式SNENC(加密的串)
	public static String enCryptFileIfOk(InputStream in)
	public static String enCryptFile(InputStream in, boolean checkNull)

	//文件解密  判断格式SNENC(加密的串)
	public static String deCryptFileIfOk(InputStream in)
	public static String deCryptFile(InputStream in, boolean checkNull)
    ```

### 设计