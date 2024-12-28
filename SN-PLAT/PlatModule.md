## 平台功能模块

### 功能扩展

<table>
	<tr>
		<th align='center' colspan='4'>支持扩展的功能</th>
	</tr>
	<tr>
		<th align='center'>类型</th>
		<th align='center'>存储方式</th>
		<th align='center'>支持类型</th>
		<th align='center'>覆盖方式</th>
	</tr>
	<tr>
		<th>[单据复制](./Sheet/SheetCopy.md#覆盖标准配置)</th>
		<td>列值</td>
		<td>Class/Bean/单独脚本列</td>
		<td rowspan='5'>
			1、在资源路径下新建与配置编码相同的文件夹；<br/>
			2、将需要覆盖或新增的监听/Bean配置到该文件夹下的配置文件中即可；<br/>
			3、通过&lt;ExcludeListeners&gt;标签删除对应的监听(\*=所有)；<br/>
			4、单据复制/备份/红冲通过标准的监听&lt;Listeners&gt;标签按顺序增加新的监听；<br/>
			5、单据撤单、系统功能通过标准的子标签按顺序增加新的监听；<br/>
			<b>注意：【同步到数据库】时将合并后的配置保存到数据库；</b>
		</td>
	</tr>
	<tr>
		<th>[单据备份](./Sheet/VersionBackup.md#覆盖标准配置)</th>
		<td>列值</td>
		<td>Class/Bean/单独脚本列</td>
	</tr>
	<tr>
		<th>[单据红冲](./Sheet/SheetRed.md#覆盖标准配置)</th>
		<td>列值</td>
		<td>Class/Bean/单独脚本列</td>
	</tr>
	<tr>
		<th>[单据撤单](./Sheet/Retract.md#覆盖标准配置)</th>
		<td>子表</td>
		<td>Class/Bean/单独脚本列</td>
	</tr>
	<tr>
		<th>[系统功能](./Tools/SystemFunction.md#覆盖标准配置)</th>
		<td>子表</td>
		<td>Class/Bean/单独脚本行</td>
	</tr>
</table>

<table>
	<tr>
		<th align='center' colspan='3'>功能扩展接口说明<br/>ExcludeListener</th>
	</tr>
	<tr>
		<th align='center'>方法签名</th>
		<th align='center'>显示列名</th>
		<th align='center'>说明</th>
	</tr>
	<tr>
		<th>List&lt;T&gt; getChildren();</th>
		<td></td>
		<td>主节点：获取子节点配置对象</td>
	</tr>
	<tr>
		<th>String getListeners();</th>
		<td></td>
		<td>子节点：返回新增的监听</td>
	</tr>
	<tr>
		<th>String getExcludeListeners();</th>
		<td></td>
		<td>子节点：返回排除的监听</td>
	</tr>
	<tr>
		<th>void setListeners(String listeners)</th>
		<td></td>
		<td>主节点：同步到数据库使用（单据撤单/系统功能不适用）</td>
	</tr>
	<tr>
		<th>String getUimer()</th>
		<td>合并监听</td>
		<td>主节点：返回合并后的监听（界面查询/同步到数据库 使用）</td>
	</tr>
	<tr>
		<th>String getUisrc();</th>
		<td>原监听</td>
		<td>主节点：返回原来的监听（界面查询使用）</td>
	</tr>
	<tr>
		<th>String getUiadd();</th>
		<td>新加监听</td>
		<td>主节点：返回新增加的监听（界面查询使用）</td>
	</tr>
	<tr>
		<th>String getUiexc();</th>
		<td>删除监听</td>
		<td>主节点：返回删除的监听（界面查询使用）</td>
	</tr>
	<tr>
		<th>String getUinoe();</th>
		<td>不存在</td>
		<td>主节点：返回不存在的覆盖监听（界面查询使用）</td>
	</tr>
	<tr>
		<th>Set&lt;String&gt; getMergedListeners()</th>
		<td></td>
		<td>主节点：返回合并后的监听</td>
	</tr>
	<tr>
		<th>Set&lt;String&gt; getNotExistListeners()</th>
		<td></td>
		<td>主节点：返回不存在的指定覆盖的父监听。</td>
	</tr>
</table>