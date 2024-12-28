### 凭证优化

#### 凭证接口类型
`````
1.新增：凭证'业务主键字段'取值方法：单据制凭证工作台查询、单据生效自动生成凭证、制凭证单据数量服务 都调用该取值方法('业务主键字段'有：单据内码、记账部门、公司、商户)
<Attrs>
    <Aname>VBBusiDataMethod</Aname>
    <Title></Title>
    <Stdvalue>FT-TRD.Inv.PurInvVouService#queryPurInvVou</Stdvalue>
    <Remark>凭证'业务主键字段'取值方法</Remark>
</Attrs>

2.新增：固定过滤条件：用于上面取值方法及凭证模板tac
<Attrs>
    <Aname>FixedFilter</Aname>
    <Title></Title>
    <Stdvalue>{"ft_trd_purinv":"{prefix}status = '70' and {prefix}isvid=1","ft_trd_purinvg":"({prefix}tradetype='20' or {prefix}tradetype='40') and exists (select 1 from ft_trd_psp where {prefix}purshipicoder = ft_trd_psp.purshipicoder and (ft_trd_psp.invmode = '10' or ft_trd_psp.invmode is null))"}</Stdvalue>
    <Remark>
        1.凭证服务取值服务固定过滤条件,{prefix}内的字符是会被替换的；
        2.凭证模板会用${FixedFilter("ft_trd_purinv":"m.","ft_trd_purinvg":"g.")}进行替换；
        3.修改此处需要注意凭证模板的引用；
    </Remark>
</Attrs>

3.需修改：制凭证单据数量服务的参数赋值：新增'#'赋值逻辑，会将'#'后面字段的值赋值给':'前面的字段
<Attrs>
    <Aname>VBUIFilter</Aname>
    <Title></Title>
    <Stdvalue>{'checkmode':'30','limitsheetcode':'FT-TRD.Vou.SeVLImpPurInv','invdatefm':'#busidatefrom','invdateto':'#busidateto'}</Stdvalue>
    <Remark>制凭证单据数量服务的参数赋值</Remark>
</Attrs>

4.如果配了上面的，就不需要配置这些了（VBUNumber、VBAutoParamSql、busiDateFld） 

`````

#### 取值服务的参数
````
增加取值方法参数接口：
AutoVoucherBusiDataParam

如PurInvVouParams中需加2个字段（vminfcode、innercodes）
/**
 * 凭证接口类别(为了取固定过滤条件)
 */
@SqlColumn(filterBean = "FT-COMM.Code.SnaFixedFilter?[{'ft_trd_purinv':'m.','ft_trd_purinvg':'g.'}]")
private String              vminfcode;

/**
 * 单据内码（需指定具体单据内码字段）
 * 此字段主要用于单据生效自动生凭证
 */
@SqlColumn(column = "purinvicode")
private String[]            innercodes;
````

#### 固定过滤条件灵活取法
````
如需取具体表的过滤条件，参考如下：
VModelInf vModelInf=VouFilterUtils.getVModelInf(params.getVminfcode(),params.getUiCuicode());
String m1FixedFilter = VouFilterUtils.getFixedFilterByPrefix(vModelInf, "ft_trd_salinv", "m1.");
String g1FixedFilter = VouFilterUtils.getFixedFilterByPrefix(vModelInf, "ft_trd_salinvg", "g1.");
            
````

#### 获取权限过滤条件
````
参考如下：
//构建凭证权限
SqlExpr limitFilter1 = VouFilterUtils.buildLimitExpr("FT-TRD.Vou.SeVLImpPurInv");
//构建凭证权限（可带前缀）
SqlExpr limitFilter2 = VouFilterUtils.buildLimitExpr("FT-TRD.Vou.SeVLImpPurInv", "g");

````

#### 单据凭证工作台子表权限监听
````
1.说明：子表只进行权限校验，不进行权限过滤
2.配置：工作台界面中子表UI监听中配置监听 SnaVoucherDetailSheetLimitUIListener
如：snsoft.ft.sna.voucher.SnaVoucherDetailSheetLimitUIListener.new?limitSheetcode=FT-TRD.Vou.SeVLImpPurInv;

````


#### 凭证制单打开制凭证工作台时日期默认值处理
```
1.将制凭证工作台上的FTBusiFilterDateDefaultUIListener替换成SnaVoucherFilterDateDefaultUIListener
作用：打开制凭证工作台时vid有值时将日期字段（dateColumns）默认值置为1970-01-01， 并清空有些字段的默认值（nullColumns）
例：snsoft.ft.sna.voucher.SnaVoucherFilterDateDefaultUIListener.new?dateColumns=signdatefrom,xxfrom&type=0&nullColumns=xxx,yyy
2.vid要加索引

```

#### 制凭证工作台的凭证模板参数处理
```
用于多个凭证模板对应一个单据凭证工作台时，穿透时，制凭证工作台凭证模板参数需传到单据凭证工作台上，界面参数需配置vmcode列用于接受参数值
服务查询参数类中配置如下：

@SqlColumn(filterBean = "FT-COMM.Code.SnaVmcodeFilter", column = "sheetcode")
private String				vmcode;

Filter作用：用凭证模板对应的适用单据过滤数据

自定义构建过滤条件：
SqlExpr sheetcodeFilter = VouFilterUtils.getVmSheetCodeFilter(vmcode, cuicode, column.column());

```



#### 主要程序
```
单据数量服务：
SnaAutoVoucherWorkbenchUIServiceImpl   loadAutoVoucherBusiDataNums
自动凭证：
BusiCreateVoucherParamBuilder   findBusiKeys
穿透：
ViewDetailFT-SNA.xml
宏替换：
VModelMacro replaceFixedFilter
```




#### 测试
```
vminfcode="FT-TRD.Vou.SeVLImpPurInv"
cuicode="C000000074"
kvMap=newHashMap()
kvMap.put("ft_trd_purinv","m.")

str=snsoft.ft.bas.utils.VouFilterUtils.getVouFixedFilter(vminfcode, cuicode, kvMap)
println("sqlfilter:"+str)

```