## 图形码

|日期|作者|备注|
|------|------|------|
|2019-11-12|郭鑫|修改|

### 指南

#### 概念说明

图形码包括条形码和二维码，底层支持图形码引入了谷歌的zxing；zxing：提供了服务端生成图形码和解析图形码的功能。
JS端图形码引入了QRCode.js插件（二维码）和JsBarcode插件(条形码)，仅支持显示。

#### 使用说明

##### 条形码

* zxing

支持条形码类型：
CODABAR、CODE_128、CODE_39、EAN_13、EAN_8、ITF、UPC_A

1. 生成条形码实现：
byte[] snsoft.bas.barcode.service.BarCodeService.generateBarCode(BarCodeParam)

参数：参考BarCodeParam

1. 解析条形码方法：
snsoft.bas.barcode.service.BarCodeService.decodeImg(byte[])

* JS端条形码

参考：https://github.com/lindell/JsBarcode

##### 二维码


* zxing

支持二维码类型：
QR_CODE、AZTEC、DATA_MATRIX、PDF_417

1. 生成二维码实现：
byte[] snsoft.bas.barcode.service.BarCodeService.generateQRCode(BarCodeParam)

参数：参考BarCodeParam

1. 解析二维码方法：
snsoft.bas.barcode.service.BarCodeService.decodeImg(byte[])


* JS端二维码

参考:http://code.ciaoca.com/javascript/qrcode/


### 手册

### 实践

#### 条形码

[条形码示例](uiinvoke/00/zh_CN/theme0/SN-HELP.Util.BarCode.html)。

#### 二维码

[二维码示例](uiinvoke/00/zh_CN/theme0/SN-HELP.Util.QrCode.html)。

### 设计




