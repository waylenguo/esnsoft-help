
### 单据提交

1. 界面配置：提交相关按钮
    >正例：
    ```
    <ToolbarBtn name="表名_btn_submitappr"  noClientComponent="true" title="提交"     uiprops.cellClassName="ui-head-btn" xprops.iconClassName="icons-btn-submit" />
    <ToolbarBtn name="表名_btn_backsubmit"  noClientComponent="true" title="取消提交"   uiprops.cellClassName="ui-head-btn"  xprops.iconClassName="icons-btn-recall" />
    <ToolbarBtn name="表名_btn_ratify"      noClientComponent="true" title="审批" uiprops.cellClassName="ui-head-btn"        xprops.iconClassName="icons-btn-approve" />
    <ToolbarBtn name="表名_btn_checkError"  noClientComponent="true" title="错误检查"       uiprops.cellClassName="ui-head-btn" xprops.iconClassName="icons-btn-checkerror" />
    ```
    
2. UI监听

    * （1）FT公共提交、审批监听：
    >正例：
        
    ```
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.ft.code.wf.FTApprSubmitJSListener({tgtUINames:["主表表名"],"taskTblName":"内嵌审批情况表名"})</Funcimpl>
            <Remark>FT提交监听</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.wf.TaskPerformListener({tgtUINames:["主表表名"]})</Funcimpl>
            <Remark>审批链-提交</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.UI</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>
                snsoft.approval.ui.UITaskPerformListener.new
            </Funcimpl>
            <Remark>审批链-审批</Remark>
        </Functions>
    ```
    
    * （2）错误检查处理监听：处理【错误检查】按钮
    >正例：
    
    ```
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.plat.sheet.chk.ApprCheckErrListener({})</Funcimpl>
            <Remark>错误件查监听</Remark>
        </Functions>
    ```
    
   
3. SV监听
   
   * （1）FT公共提交、审批监听：
   >正例：
   ```
    <Functions>
        <Functype>SN-PLAT.SV</Functype>
        <Subtype>1</Subtype>
        <Funcimpl><![CDATA[
        SN-PLAT.SubmitCheckSVListener
        ]]></Funcimpl>
        <Remark>提交检查</Remark>
    </Functions>
   ```
   
   * （2）组织关系生效效验监听：用于校验单据组织关系是否合法
   >正例：
   ```
        <Functions>
                <Functype>SN-PLAT.SV</Functype>
                <Subtype>2</Subtype>
                <Funcimpl>
                    <![CDATA[
            snsoft.ft.code.comm.service.impl.SheetBcodeCheckSVListener.new?hasCostCol=true
            ]]>
                </Funcimpl>
                <Remark>组织关系生效效验</Remark>
        </Functions>
   ```
   
   * （3）单据外码提交检查监听：用于校验单据外码是否符合编码规则
   >正例：
   ```
        <Functions>
            <Functype>SN-PLAT.SV</Functype>
            <Subtype>2</Subtype>
            <Funcimpl><![CDATA[
            snsoft.ft.code.comm.service.impl.FTOutCodeCheckSVListener.new
            ]]></Funcimpl>
            <Remark>单据外码提交检查</Remark>
        </Functions>
   ```

4. 表结构说明
   * （1）生效时间：单据生效时间（版本修改单据始终为第一版本生效时间）
   * （2）提交时间：单据提交时间
   * （3）审核时间：单据最新审批时间
   * （4）本版本生效时间：当前版本单据生效时间
   >正例：
   ```
        <column name="curratifydate" title="本版本生效时间" type="DATE"/>
        <column name="ratifydate" title="生效时间" type="DATE"/>
        <column name="submitdate" title="提交时间" type="DATE"/>
        <column name="performdate" title="审核时间" type="DATE"/>
   ```
5. 服务说明
    * （1）服务接口需继承FTBusiAccessService接口
    * （2）服务实现需继承FTBusiAccessServiceImpl类
    * （3）存盘监听实现FTFunctionSaveListener接口（可选）：用于实现提交检查前的业务处理
    >正例：
    ```
    提交处理按以下时序处理：
        FTFunctionSaveListener.beforeSubmit
        FunctionSaveListener.submitCheck
        AbstractStatusListener.beforeSubmit
    ```
