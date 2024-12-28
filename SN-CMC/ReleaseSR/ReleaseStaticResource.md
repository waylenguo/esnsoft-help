## 静态资源发布

### 修改记录
|日期|作者|备注|
|------|------|------|
|2018-09-27|张东|初稿|

### 功能说明
此功能用于发布应用服务Web目录下的静态资源。
将Web目录下的静态资源发布到统一目录，由Nginx等服务负责加载这些静态资源，也就是说当使用此功能是，意味着应用服务器不承担加载这些静态资源的任务。

### 界面
<img src='help/SN-CMC/ReleaseSR/img/ReleaseSR.png' />

### 参数说明
目标目录：将Web资源拷贝到哪个目录下，多数情况为共享目录。

### 系统配置说明
```
WEB_FILE_TYPE：忽略的静态资源文件类型 默认：~.ftl,~.xml,~.jsp,~.jnlp
WEB_FILE_DIRS：忽略的静态资源文件目录 默认：WEB-INF,META-INF,jclasses,snc
FS.WEB_STATIC_RELEASE_PATH：用来做路径替换的宏 例：目标路径为/static/${FS.WEB_STATIC_RELEASE_PATH}/web
```