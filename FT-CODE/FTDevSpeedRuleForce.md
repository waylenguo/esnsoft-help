## 开发规范(效率篇)

### 开发环境准备

> 开发环境必须打开效率监控日志

```xml
    <x>
        <logger name="snsoft.ft">
            <level value="DEBUG" />
        </logger>
        <logger name="snsoft.plat">
            <level value="DEBUG" />
        </logger>
    </x>
```

>[帮助中心效率开发](https://n.esnsoft.cn/N9Bhelp/help.html?helpFile=/help/SN-HELP/Standard/DevCode/PerformCode.md)

### 效率关注点

* 单据入口
1. 打开
* 单据详情
1. 打开
1. 新建
1. 草拟
1. 生效
1. 存盘
1. 提交
    
### 目标要求

* 请求次数
    1. 打开（草拟、新建、生效），不能大于2次；
    1. 存盘，不能大于1次；
    1. 提交，不能大于2次；
* 请求返回数据量的大小
    1. 不该返回的不要返回；
    1. 减少重复的返回值；
    1. 值为空的不要返回，例如：null，""；
    1. 返回JSON数组的，使用新的数据格式MapArray,减少key=value中的key占用空间；
* 服务端日志：看时间是否存在不在同一个数量级的监听；
    1. InitComponent中的监听要求在1毫秒内完成
    1. LoadData等要求如存在查询要求200毫秒内，如不存在不要超过100毫秒。
* 所有工作台、单据入口都要增加日期默认值当前日期倒推（系统选项配置倒推月数），并为该日期字段添加索引。（切记这个日期字段必须是一个散列的）
    >正例
    snsoft.ft.comm.busi.FTBusiFilterDateDefaultUIListener.new?dateColumns=predatefrom
* 所有大数据量的码表并且会变化的（客商）需要使用增量模式缓存
* 所有配置表配置表的表号要大于20000，采用缓存的添加缓存标记的方式定义
* 所有关联列都要增加索引
* 主表附表模式的要把常用的字段放到主表不常用的放到附表
* 如遇到实际情况确实无法满足第3大点中要求的，需要向上级报备并填写在线编辑文件[《N9B产品效率报备清单》](https://docs.qq.com/sheet/DYmd0bHBvU2RtYnJW)