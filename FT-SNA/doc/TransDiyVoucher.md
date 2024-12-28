# 财务Tac配置
## 自定义转账凭证
### 常用宏
1. 记账部门:THETACCODE，THEKEPTBCODE
1. 年：THEYEAR
1. 月：THEMONTH
1. 进度条对象：progress
1. 参数面板对象：
1. 动态参数对象：otherDialogParam
1. 凭证DAO:VouDAO
### 常用取值方法
1. 取参数面板中某一字段的指例如:凭证类别
```
1、vtype = vou_vtype
2、vtype = VouParams.getVtype()
```
1. 取参数面板中动态参数值
```
1、 val = vau_a //vou_开头+动态参数字段名，其中a为动态参数字段名
2、 val = otherDialogParam.get("a")
```

> 示例1：
```text
 制凭证:  $vtype = VouParams.getVtype() , $keptbcode=VouParams.getKeptbcode()
            贷 "1001" :100 ,$bcode="fffff", $expl="结转银行存款"
            借 "1001" :100, $expl="结转银行存款"
  			//取动态参数规则 vou_开头+动态参数字段名
  			bcode = vou_a
  			otherDialogParam.get("a")
  			//记账部门宏
  			THETACCODE，THEKEPTBCODE
  			//进度条
  			progress.addProgressMessage("xxx")
  			progress.setProgress("正在结转...", 1, 100)
  			
  			
 end 制凭证
```
> 示例2：
```text
Sql="select acode ,fcode,ccode,bcode,wcode,prjcode,hcode,sum(scy*vdc) as rmb,sum(fcy*vdc) as fcy  from sna_ivou where year="+THEYEAR+"  and month=0  and acode like '1122%'   group by  acode,fcode,ccode,bcode,wcode,prjcode,hcode"
 r = sqlSelect3(Sql) 
 n =  arrayLength(r)
 if  n==0 then end

if(getPrepareVoucher()!=null)
       getPrepareVoucher().setDefaultPostPendingVoucherItemMergable(true)  //分录合并
end if
	//vou_vtype 取参数中的凭证类别
 制凭证: $vtype = vou_vtype ,$keptbcode=THEKEPTBCODE,$vappendix=0
  i = 0

  while i<n
    ac =  r[i][0].toString()
    fc = r[i][1].toString()
    cc = r[i][2].toString()
    bc = r[i][3].toString()
    wc   = r[i][4].toString()
    prjcode   = r[i][5].toString()
    hcode= r[i][6].toString()
	scy =  r[i][7]
	fcy =  r[i][8] 

	贷  ac: scy , $expl="结转应收账款", $fcode=fc,$ccode=cc,$bcode=bc,$wcode=wc,$prjcode=prjcode,$hcode=hcode,$fcy=fcy,$fserate=toCur(scy/fcy,6),$zcny=0,$fcerate=0,$frerate2=0,$zusd=0
		借"100101"   :      scy   , $expl="结转应收账款",$rcode="111"
	i = i+1
	end while
 end 制凭证
```

