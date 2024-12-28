## 额度类型定义
### 占用释放配置
1、占用附加条件
* 监听单据字段（除状态外）变化时，处理额度的时机
* 支持的是TAC，返回值Map，key:字段名，value:字段值。可以多个字段。
* 实例
  以old.开头表示旧值匹配
  ```
       map=newHashMap()
       map.put("docstatus","20")
       map.put("old.docstatus","10")
       map
  ```
  
2、数据源
* 占用和释放取数SQL
* 支持配置TAC，或者直接写SQL
* SQL实例：
  ```
    select g.salshipgicode as innercode,m.credittype,m.corpbcode,g.bcode,g.wcode,m.salccode,m.fcode,g.zcny,m.salshipicoder,m.salshipcoder,m.etd,g.prjcode,g.prjicode from ft_trd_ssp m,ft_trd_sspg g where m.salshipicode=g.salshipicode
  ```
* TAC实例(必须以TAC开头)：
    ```
    TAC:
    sysOptionsService = getBean(snsoft.ft.trd.tx.sysopt.service.TrdTXSysOptionsService.beanName)
    cuicode=snsoft.context.AppContext.getUserSession(true).getUserCode()
    isSettInv=sysOptionsService.isSalShipTsOutSettCheckSalInv(cuicode)
    sql=null
    if isSettInv
    //销售发票数据
    sql = "select g.salinvgicode as innercode,m.credittype,im.corpbcode,g.bcode,g.wcode,im.salccode,im.fcode,g.zcny,g.salshipicoder,g.salshipcoder,m.etd,g.prjcode,g.prjicode"
    sql=sql+" from ft_trd_salinvg g inner join ft_trd_salinv im on im.salinvicode=g.salinvicode  inner join ft_trd_ssp m on m.salshipicoder=g.salshipicoder"
    sql=sql+" where im.redflag=0 and m.redflag=0"
    else
    //收入数据
    sql="select g.icmgicode as innercode,m.credittype,m.corpbcode,g.bcode,g.wcode,m.salccode,g.fcode,g.zcny,m.salshipicoder,m.salshipcoder,m.etd,g.prjcode,g.prjicode"
    sql=sql+" from ft_trd_icmg g inner join ft_trd_ssp m on m.salshipicoder=g.salshipicoder"
    sql=sql+" where m.redflag=0"
    end if
    sql
    ```

3、附加数据源
* 作用：是用来为数据源补数的。
* 实例
* 支持SQL补数，其中
* resultColumns：需要补数的列
* matchColumns：匹配列
* sql:补数sql，${salshipicode}，括号中salshipicode代表数据源查询出来的数据的某一列的值的集合
```
  list = newArrayList()
  data=newHashMap()
  data.put("resultColumns","tradetype,busimode")
  data.put("matchColumns","salshipicode")
  data.put("sql","select tradetype,busimode,salshipicode from ft_trd_ssp where salshipicode in (${salshipicode})")
  list.add(data)
  list
  ```

4、操作类型
* 记录此次占用记录的操作类型（字典：FT-CODE.CreditOccType）
  ```
  10：正常
  20：撤单
  30：红冲
  40：撤销红冲
  50：终结/完结
  60：取消终结/取消完结
  70：作废
  75：取消作废
  FA10：放款确认
  FA20：取消放款确认
  GU10：开立确认
  GU20：取消开立确认
  GU30：撤销
  GU40：取消撤销
  LC10：开证确认
  LC20：取消开证确认
  LC30：撤证
  LC40：取消撤证
  LS10：确认
  LS20：取消确认
  PD10：开立票据
  PD20：取消开立票据
  PD30：到期承兑
  PD40：取消到期承兑
  ```
* 支持配置TAC，或者直接写操作类型编码
* 直接写操作类型编码实例：
  ```
    10
  ```
* TAC实例(必须以TAC开头)：
* 参数 mainList：代表根据数据源查询出来的数据集
    ```
    TAC:
    for data in mainList
        status=data.get("status")
        data.put("occtype","75"==status?"95":"96")
    end for
    ```


    
