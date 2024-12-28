## SqlType

### 数据类型

#### SQLServer

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th align="center">类型</th>
		<th align="center" colspan='2'>明细</th>
		<th align="center">说明</th>
	</tr>
	<tr>
		<th align="center" rowspan='5'>整型</th>
		<td>tinyint</td>
		<td>1字节</td>
		<td rowspan='5'></td>
	</tr>
	<tr>
		<td>smallint</td><td>2字节</td>
	</tr>
	<tr>
		<td>int</td><td>4字节</td>
	</tr>
	<tr>
		<td>bigint</td><td>8字节</td>
	</tr>
	<tr>
		<td>bit</td><td>0/1[bool]</td>
	</tr>
	<tr>
		<th align="center" rowspan='4'>浮点</th>
		<td>real</td><td></td>
		<td rowspan='4'>
		1. real，四个字节;<br/>
		2. float([n])：<br/>
		　　0<=n<=53，默认53;<br/>
		　　n<24时，与real相同;<br/>
		3. (p[,s])：<br/>
		　　p：精度，最大值38，默认18;<br/>
		　　s：小数，最大p，默认0;<br/>
		</td>
	</tr>
	<tr>
		<td>float</td><td>([n])</td>
	</tr>
	<tr>
		<td>decimal</td><td>(p[,s])</td>
	</tr>
	<tr>
		<td>numeric</td><td>(p[,s])</td>
	</tr>
	<tr>
		<th align="center" rowspan='4'>字符</th>
		<td>char</td><td>1~8000</td>
		<td rowspan='4'></td>
	</tr>
	<tr>
		<td>varchar</td><td>1~8000</td>
	</tr>
	<tr>
		<td>nchar</td><td>1~4000</td>
	</tr>
	<tr>
		<td>nvarchar</td><td>1~4000</td>
	</tr>
	<tr>
		<th align="center" rowspan='3'>日期</th>
		<td>date</td><td>YYYY-MM-DD</td>
		<td rowspan='3'></td>
	</tr>
	<tr>
		<td>time</td><td>hh:mm:ss</td>
	</tr>
	<tr>
		<td>datetime</td><td>YYYY-MM-DD hh:mm:ss</td>
	</tr>
	<tr>
		<th align="center" rowspan='3'>LOB</th>
		<td>text</td><td>CLOB</td>
		<td rowspan='3'></td>
	</tr>
	<tr>
		<td>ntext</td><td>NCLOB</td>
	</tr>
	<tr>
		<td>image</td><td>BLOB</td>
	</tr>
</table>

#### Oracle


<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th align="center">类型</th>
		<th align="center" colspan='2'>明细</th>
		<th align="center">说明</th>
	</tr>
	<tr>
		<th align="center" rowspan='2'>整型</th>
		<td>number</td><td>(n,0)</td>
		<td rowspan='2'>number为内置类型</td>
	</tr>
	<tr>
		<td>int/integer/samllint</td><td>number(38)</td>
	</tr>
	<tr>
		<th align="center" rowspan='5'>浮点</th>
		<td>**number**</td><td>(p,s)</td>
		<td rowspan='5'>number为内置类型</td>
	</tr>
	<tr>
		<td>decimal</td><td>(p,s)</td>
	</tr>
	<tr>
		<td>numeric</td><td>(p,s)</td>
	</tr>
	<tr>
		<td>binary_float</td><td>E38</td>
	</tr>
	<tr>
		<td>binary_double</td><td>E308</td>
	</tr>
	<tr>
		<th align="center" rowspan='4'>字符</th>
		<td>char</td><td>1~2000(bytes)</td>
		<td rowspan='4'></td>
	</tr>
	<tr>
		<td>varchar2</td><td>1~4000(bytes)</td>
	</tr>
	<tr>
		<td>nchar</td><td>1~2000(bytes)</td>
	</tr>
	<tr>
		<td>nvarchar2</td><td>1~4000(bytes)</td>
	</tr>
	<tr>
		<th align="center" rowspan='1'>日期</th>
		<td>DATE</td><td>YYYY-MM-DD hh24:mi:ss</td>
		<td rowspan='1'></td>
	</tr>
	<tr>
		<th align="center" rowspan='3'>LOB</th>
		<td>CLOB</td><td>CLOB</td>
		<td rowspan='3'></td>
	</tr>
	<tr>
		<td>NCLOB</td><td>NCLOB</td>
	</tr>
	<tr>
		<td>BLOB</td><td>BLOB</td>
	</tr>
</table>


#### MySQL

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th align="center">类型</th>
		<th align="center" colspan='2'>明细</th>
		<th align="center">说明</th>
	</tr>
	<tr>
		<th align="center" rowspan='5'>整型</th>
		<td>tinyint</td>
		<td>1字节</td>
		<td rowspan='5'></td>
	</tr>
	<tr>
		<td>smallint</td><td>2字节</td>
	</tr>
	<tr>
		<td>mediumint</td><td>3字节</td>
	</tr>
	<tr>
		<td>int/integer</td><td>4字节</td>
	</tr>
	<tr>
		<td>bigint</td><td>8字节</td>
	</tr>
	<tr>
		<th align="center" rowspan='3'>浮点</th>
		<td>float</td><td></td>
		<td rowspan='3'>
		</td>
	</tr>
	<tr>
		<td>double</td><td></td>
	</tr>
	<tr>
		<td>decimal</td><td>(p,s)</td>
	</tr>
	<tr>
		<th align="center" rowspan='2'>字符</th>
		<td>char</td><td>1~255(等价于nchar)</td>
		<td rowspan='2'>都是指字符个数</td>
	</tr>
	<tr>
		<td>varchar</td><td>1~65535(等价于nvarchar)</td>
	</tr>
	<tr>
		<th align="center" rowspan='3'>日期</th>
		<td>date</td><td>YYYY-MM-DD(3字节)</td>
		<td rowspan='3'></td>
	</tr>
	<tr>
		<td>time</td><td>hh:mm:ss(3字节)</td>
	</tr>
	<tr>
		<td>datetime</td><td>YYYY-MM-DD hh:mm:ss(8字节)</td>
	</tr>
	<tr>
		<th align="center" rowspan='6'>LOB</th>
		<td>text</td><td>64K</td>
		<td rowspan='6'></td>
	</tr>
	<tr>
		<td>mediumtext</td><td>16M</td>
	</tr>
	<tr>
		<td>longtext</td><td>4G</td>
	</tr>
	<tr>
		<td>blob</td><td>64K</td>
	</tr>
	<tr>
		<td>mediumblob</td><td>16M</td>
	</tr>
	<tr>
		<td>longblob</td><td>4G</td>
	</tr>
</table>

#### DB2


<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th align="center">类型</th>
		<th align="center" colspan='2'>明细</th>
		<th align="center">说明</th>
	</tr>
	<tr>
		<th align="center" rowspan='3'>整型</th>
		<td>smallint</td><td>2字节</td>
		<td rowspan='3'></td>
	</tr>
	<tr>
		<td>integer</td><td>4字节</td>
	</tr>
	<tr>
		<td>bigint</td><td>8字节</td>
	</tr>
	<tr>
		<th align="center" rowspan='3'>浮点</th>
		<td>real</td><td>4字节(E75)</td>
		<td rowspan='3'></td>
	</tr>
	<tr>
		<td>double/float</td><td>4字节(E75)</td>
	</tr>
	<tr>
		<td>decimal/numeric</td><td>(p,s)</td>
	</tr>
	<tr>
		<th align="center" rowspan='4'>字符</th>
		<td>char</td><td>1~255</td>
		<td rowspan='4'></td>
	</tr>
	<tr>
		<td>varchar</td><td>1~32704</td>
	</tr>
	<tr>
		<td>graphic</td><td>1~127</td>
	</tr>
	<tr>
		<td>vargraphic</td><td>1~16352</td>
	</tr>
	<tr>
		<th align="center" rowspan='3'>日期</th>
		<td>date</td><td>YYYY-MM-DD</td>
		<td rowspan='3'></td>
	</tr>
	<tr>
		<td>time</td><td>hh:mm:ss</td>
	</tr>
	<tr>
		<td>timestamp</td><td>YYYY-MM-DD hh:mm:ss</td>
	</tr>
	<tr>
		<th align="center" rowspan='3'>LOB</th>
		<td>CLOB</td><td>CLOB</td>
		<td rowspan='3'></td>
	</tr>
	<tr>
		<td>DBCLOB</td><td>NCLOB</td>
	</tr>
	<tr>
		<td>BLOB</td><td>BLOB</td>
	</tr>
</table>

### Java映射

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th align="center" rowspan='2'>分类</th>
		<th align="center" colspan='3'>SqlType</th>
		<th align="center" rowspan='2'>SQLServer</th>
		<th align="center" rowspan='2'>Oracle</th>
		<th align="center" rowspan='2'>MySQL</th>
		<th align="center" rowspan='2'>DB2</th>
	</tr>
	<tr>
		<td>Name</td>
		<td>Value</td>
		<td>JavaType</td>
	</tr>
	<tr>
		<td rowspan='5'>整形</td>
		<td>TINYINT</td>
		<td>-6</td>
		<td>byte/Byte</td>
		<td>tinyint</td>
		<td>number(3)</td>
		<td>tinyint</td>
		<td>decimal(3)</td>
	</tr>
	<tr>
		<td>SMALLINT</td>
		<td>5</td>
		<td>int/Integer</td>
		<td>smallint</td>
		<td>number(5)</td>
		<td>smallint</td>
		<td>smallint</td>
	</tr>
	<tr>
		<td>INTEGER</td>
		<td>4</td>
		<td>int/Integer</td>
		<td>int</td>
		<td>number(10)</td>
		<td>int</td>
		<td>integer</td>
	</tr>
	<tr>
		<td>BIGINT</td>
		<td>-5</td>
		<td>long/Long</td>
		<td>bigint</td>
		<td>number(19)</td>
		<td>bigint</td>
		<td>bigint</td>
	</tr>
	<tr style="color:#f00">
		<td>BIT</td>
		<td>-7</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<td rowspan='5'>浮点</td>
		<td>FLOAT</td>
		<td>6</td>
		<td>BigDecimal</td>
		<td>float</td>
		<td>binary_float</td>
		<td>float</td>
		<td>float</td>
	</tr>
	<tr style="color:#f00">
		<td>REAL</td>
		<td>7</td>
		<td>BigDecimal</td>
		<td>real</td>
		<td></td>
		<td></td>
		<td>real</td>
	</tr>
	<tr>
		<td>DOUBLE</td>
		<td>8</td>
		<td>BigDecimal</td>
		<td>double</td>
		<td>binary_double</td>
		<td>double</td>
		<td>double</td>
	</tr>
	<tr>
		<td>NUMERIC</td>
		<td>2</td>
		<td><b>BigDecimal</b></td>
		<td>numeric</td>
		<td>number</td>
		<td>decimal</td>
		<td>numeric</td>
	</tr>
	<tr>
		<td>DECIMAL</td>
		<td>3</td>
		<td>BigDecimal</td>
		<td>decimal</td>
		<td>number</td>
		<td>decimal</td>
		<td>decimal</td>
	</tr>
	<tr>
		<td rowspan='6'>字符</td>
		<td>CHAR</td>
		<td>1</td>
		<td>String</td>
		<td>char</td>
		<td>char</td>
		<td>char</td>
		<td>char</td>
	</tr>
	<tr>
		<td>VARCHAR</td>
		<td>12</td>
		<td>String</td>
		<td>varchar</td>
		<td>varchar2</td>
		<td>varchar</td>
		<td>varchar</td>
	</tr>
	<tr>
		<td>LONGVARCHAR</td>
		<td>-1</td>
		<td>String</td>
		<td>varchar(4000)</td>
		<td>varchar2(4000)</td>
		<td>text</td>
		<td>varchar(4000)</td>
	</tr>
	<tr>
		<td>NCHAR</td>
		<td>-15</td>
		<td>String</td>
		<td>nchar</td>
		<td>nchar</td>
		<td>char</td>
		<td>graphic</td>
	</tr>
	<tr>
		<td>NVARCHAR</td>
		<td>-9</td>
		<td>String</td>
		<td>nvarchar</td>
		<td>nvarchar</td>
		<td>varchar</td>
		<td>vargraphic</td>
	</tr>
	<tr>
		<td>LONGNVARCHAR</td>
		<td>-16</td>
		<td>String</td>
		<td>nvarchar(2000)</td>
		<td>nvarchar(2000)</td>
		<td>text</td>
		<td>vargraphic(2000)</td>
	</tr>
	<tr>
		<td rowspan='3'>日期</td>
		<td>DATE</td>
		<td>91</td>
		<td>Date</td>
		<td>datetime</td>
		<td>date</td>
		<td>datetime</td>
		<td>timestamp</td>
	</tr>
	<tr style="color:#f00">
		<td>TIME</td>
		<td>92</td>
		<td>Date</td>
		<td>time</td>
		<td></td>
		<td>time</td>
		<td>time</td>
	</tr>
	<tr>
		<td>TIMESTAMP</td>
		<td>93</td>
		<td>Date</td>
		<td>datetime</td>
		<td>date</td>
		<td>datetime</td>
		<td>timestamp</td>
	</tr>
	<tr>
		<td rowspan='4'>LOB</td>
		<td>LONGVARBINARY</td>
		<td>-4</td>
		<td>byte[]</td>
		<td>image</td>
		<td>blob</td>
		<td>longblob</td>
		<td>blob</td>
	</tr>
	<tr>
		<td>BLOB</td>
		<td>2004</td>
		<td>byte[]</td>
		<td>image</td>
		<td>blob</td>
		<td>longblob</td>
		<td>blob</td>
	</tr>
	<tr>
		<td>CLOB</td>
		<td>2005</td>
		<td>String</td>
		<td>text</td>
		<td>clob</td>
		<td>longtext</td>
		<td>clob</td>
	</tr>
	<tr>
		<td>NCLOB</td>
		<td>2011</td>
		<td>String</td>
		<td>ntext</td>
		<td>nclob</td>
		<td>longtext</td>
		<td>dbclob</td>
	</tr>
</table>

注意：
1. LONGVARCHAR/LONGNVARCHAR：MySQL使用text是因为mui表;
1. LONGVARBINARY：一个被废除的类型，官方建议使用BLOB。

### 帮助中心初始化

#### Init for SQLServer

```sql
use master
--创建登录用户
create login cu_bas with password=N'abAB12!@',default_database=master,check_expiration=off,check_policy=off;
create login cu_cfg with password=N'abAB12!@',default_database=master,check_expiration=off,check_policy=off;
create login cu_ufg with password=N'abAB12!@',default_database=master,check_expiration=off,check_policy=off;
--创建数据库
create database cu_bas collate Chinese_PRC_BIN/Chinese_PRC_CS_AS;
create database cu_cfg collate Chinese_PRC_BIN/Chinese_PRC_CS_AS;
create database cu_ufg collate Chinese_PRC_BIN/Chinese_PRC_CS_AS;
--创建数据库用户cu_bas并授权
use cu_bas;
create user cu_bas for login cu_bas;
--grant create table,create view,create function to cu_bas;
grant insert,delete,update,select on schema::dbo to cu_bas;
--创建数据库用户cu_cfg并授权
use cu_cfg;
create user cu_cfg for login cu_cfg;
grant create table,create view,create function to cu_cfg;
grant control on schema::dbo to cu_cfg;
--创建数据库用户cu_ufg并授权
use cu_ufg;
create user cu_ufg for login cu_ufg;
grant create table,create view,create function to cu_ufg;
grant control on schema::dbo to cu_ufg;
```

[MSSQL grant](https://docs.microsoft.com/en-us/sql/t-sql/statements/grant-transact-sql?view=sql-server-2017)

```xml
	<workspace id="dev_sql2017" title="SQL2017" startTimerTask="false">
		<datasource host="192.168.1.100" port="1433" database="cu_bas" user="cu_bas" password="abAB12!@" type="sqlserver" id="BASDATA"   />
		<datasource host="192.168.1.100" port="1433" database="cu_cfg" user="cu_cfg" password="abAB12!@" type="sqlserver" id="CONFIG"    />
		<datasource host="192.168.1.100" port="1433" database="cu_ufg" user="cu_ufg" password="abAB12!@" type="sqlserver" id="UCODECFG"  />
		<datasource host="192.168.1.100" port="1433" database="cu_bas" user="cu_bas" password="abAB12!@" type="sqlserver" id="HELP"      />
		<datasource host="192.168.1.100" port="1433" database="cu_bas" user="cu_bas" password="abAB12!@" type="sqlserver" id="APPR"      />
		<datasource host="192.168.1.100" port="1433" database="cu_bas" user="cu_bas" password="abAB12!@" type="sqlserver" id="SN-MDM_W"  />
		<datasource host="192.168.1.100" port="1433" database="cu_bas" user="cu_bas" password="abAB12!@" type="sqlserver" id="CMS_W"     />
		<datasource host="192.168.1.100" port="1433" database="cu_bas" user="cu_bas" password="abAB12!@" type="sqlserver" id="PAY_W"     />
		<datasource host="192.168.1.100" port="1433" database="cu_bas" user="cu_bas" password="abAB12!@" type="sqlserver" id="PAY_R"     />
		<datasource host="192.168.1.100" port="1433" database="cu_bas" user="cu_bas" password="abAB12!@" type="sqlserver" id="SHARRDING" />
	</workspace>
```

#### Init for Oracle

```sql
alter system set db_create_file_dest='/data/oradata';

CREATE TABLESPACE SNSYSDATA_TBS;
CREATE TABLESPACE SNSYSINDX_TBS;
CREATE TABLESPACE RATIFY_TBS;
CREATE TABLESPACE SNPAYDATA_TBS;
CREATE TABLESPACE SNPAYINDX_TBS;
CREATE TABLESPACE SNCMSDATA_TBS;
CREATE TABLESPACE SNCMSINDX_TBS;

create user cu_bas identified by snsoft2020 default tablespace SNSYSDATA_TBS;
create user cu_cfg identified by snsoft2020 default tablespace SNSYSDATA_TBS;
create user cu_ufg identified by snsoft2020 default tablespace SNSYSDATA_TBS;
--开发权限(增删改查+DDL)
grant connect,resource,create view,unlimited tablespace to cu_bas;
grant connect,resource,create view,unlimited tablespace to cu_cfg;
grant connect,resource,create view,unlimited tablespace to cu_ufg;
--应用权限(增删改查)
grant connect,unlimited tablespace to cu_bas;
grant connect,unlimited tablespace to cu_cfg;
grant connect,unlimited tablespace to cu_ufg;
```

[Oracle Grant](https://docs.oracle.com/en/database/oracle/oracle-database/21/admin/managing-users-and-securing-the-database.html#GUID-A294CD5E-BC73-4FA7-980A-8A2538950F2D)

```xml
	<workspace id="dev_orcl" title="Oracle11g" startTimerTask="false">
		<datasource host="192.168.1.100" port="1521" database="orcl" user="cu_bas" password="snsoft2020" type="oracle#1" id="BASDATA"   />
		<datasource host="192.168.1.100" port="1521" database="orcl" user="cu_cfg" password="snsoft2020" type="oracle#1" id="CONFIG"    />
		<datasource host="192.168.1.100" port="1521" database="orcl" user="cu_ufg" password="snsoft2020" type="oracle#1" id="UCODECFG"  />
		<datasource host="192.168.1.100" port="1521" database="orcl" user="cu_bas" password="snsoft2020" type="oracle#1" id="HELP"      />
		<datasource host="192.168.1.100" port="1521" database="orcl" user="cu_bas" password="snsoft2020" type="oracle#1" id="APPR"      />
		<datasource host="192.168.1.100" port="1521" database="orcl" user="cu_bas" password="snsoft2020" type="oracle#1" id="SN-MDM_W"  />
		<datasource host="192.168.1.100" port="1521" database="orcl" user="cu_bas" password="snsoft2020" type="oracle#1" id="CMS_W"     />
		<datasource host="192.168.1.100" port="1521" database="orcl" user="cu_bas" password="snsoft2020" type="oracle#1" id="PAY_W"     />
		<datasource host="192.168.1.100" port="1521" database="orcl" user="cu_bas" password="snsoft2020" type="oracle#1" id="PAY_R"     />
		<datasource host="192.168.1.100" port="1521" database="orcl" user="cu_bas" password="snsoft2020" type="oracle#1" id="SHARRDING" />
	</workspace>
```

#### Init for MySQL

```sql
create user 'sn'@'%' identified by 'abAB12!@';
create database cu_bas default character set utf8mb4 collate utf8mb4_bin/utf8mb4_zh_0900_as_cs/utf8mb4_0900_as_cs;
create database cu_cfg default character set utf8mb4 collate utf8mb4_bin/utf8mb4_zh_0900_as_cs/utf8mb4_0900_as_cs;
create database cu_ufg default character set utf8mb4 collate utf8mb4_bin/utf8mb4_zh_0900_as_cs/utf8mb4_0900_as_cs;
grant all privileges on cu_bas.* to 'sn'@'%';
grant all privileges on cu_cfg.* to 'sn'@'%';
grant all privileges on cu_ufg.* to 'sn'@'%';
```

```xml
	<workspace id="dev_mysql" title="MySQL8" startTimerTask="false">
		<datasource host="192.168.1.100" port="3306" database="cu_bas" user="sn" password="abAB12!@" type="mysql" id="BASDATA"   />
		<datasource host="192.168.1.100" port="3306" database="cu_cfg" user="sn" password="abAB12!@" type="mysql" id="CONFIG"    />
		<datasource host="192.168.1.100" port="3306" database="cu_ufg" user="sn" password="abAB12!@" type="mysql" id="UCODECFG"  />
		<datasource host="192.168.1.100" port="3306" database="cu_bas" user="sn" password="abAB12!@" type="mysql" id="HELP"      />
		<datasource host="192.168.1.100" port="3306" database="cu_bas" user="sn" password="abAB12!@" type="mysql" id="APPR"      />
		<datasource host="192.168.1.100" port="3306" database="cu_bas" user="sn" password="abAB12!@" type="mysql" id="SN-MDM_W"  />
		<datasource host="192.168.1.100" port="3306" database="cu_bas" user="sn" password="abAB12!@" type="mysql" id="CMS_W"     />
		<datasource host="192.168.1.100" port="3306" database="cu_bas" user="sn" password="abAB12!@" type="mysql" id="PAY_W"     />
		<datasource host="192.168.1.100" port="3306" database="cu_bas" user="sn" password="abAB12!@" type="mysql" id="PAY_R"     />
		<datasource host="192.168.1.100" port="3306" database="cu_bas" user="sn" password="abAB12!@" type="mysql" id="SHARRDING" />
	</workspace>
```

#### Init for DB2

```sql
create database db2 pagesize 32768
connect to db2
CREATE TABLESPACE SNSYSDATA_TBS
CREATE TABLESPACE SNSYSINDX_TBS
CREATE TABLESPACE RATIFY_TBS
CREATE TABLESPACE SNPAYDATA_TBS
CREATE TABLESPACE SNPAYINDX_TBS
CREATE TABLESPACE SNCMSDATA_TBS
CREATE TABLESPACE SNCMSINDX_TBS

create schema cu_bas authorization db2user
create schema cu_cfg authorization db2user
create schema cu_ufg authorization db2user
```

```xml
	<workspace id="dev_db2" title="DB2V9.7" startTimerTask="false">
		<datasource host="192.168.1.100" port="50000" database="db2" user="db2user" password="snjixl" type="db2" currentSchema="cu_bas" id="BASDATA"   />
		<datasource host="192.168.1.100" port="50000" database="db2" user="db2user" password="snjixl" type="db2" currentSchema="cu_cfg" id="CONFIG"    />
		<datasource host="192.168.1.100" port="50000" database="db2" user="db2user" password="snjixl" type="db2" currentSchema="cu_ufg" id="UCODECFG"  />
		<datasource host="192.168.1.100" port="50000" database="db2" user="db2user" password="snjixl" type="db2" currentSchema="cu_bas" id="HELP"      />
		<datasource host="192.168.1.100" port="50000" database="db2" user="db2user" password="snjixl" type="db2" currentSchema="cu_bas" id="APPR"      />
		<datasource host="192.168.1.100" port="50000" database="db2" user="db2user" password="snjixl" type="db2" currentSchema="cu_bas" id="SN-MDM_W"  />
		<datasource host="192.168.1.100" port="50000" database="db2" user="db2user" password="snjixl" type="db2" currentSchema="cu_bas" id="CMS_W"     />
		<datasource host="192.168.1.100" port="50000" database="db2" user="db2user" password="snjixl" type="db2" currentSchema="cu_bas" id="PAY_W"     />
		<datasource host="192.168.1.100" port="50000" database="db2" user="db2user" password="snjixl" type="db2" currentSchema="cu_bas" id="PAY_R"     />
		<datasource host="192.168.1.100" port="50000" database="db2" user="db2user" password="snjixl" type="db2" currentSchema="cu_bas" id="SHARRDING" />
	</workspace>
```

#### Init for 武汉达梦

```sql
--初始化存储及用户
CREATE TABLESPACE SNSYSDATA_TBS datafile 'snsysdata_tbs.dbf' size 150;
CREATE TABLESPACE SNSYSINDX_TBS datafile 'snsysindx_tbs.dbf' size 150;
CREATE TABLESPACE RATIFY_TBS    datafile 'ratify_tbs.dbf'    size 150;
CREATE TABLESPACE SNPAYDATA_TBS datafile 'snpaydata_tbs.dbf' size 150;
CREATE TABLESPACE SNPAYINDX_TBS datafile 'snpayindx_tbs.dbf' size 150;
CREATE TABLESPACE SNCMSDATA_TBS datafile 'sncmsdata_tbs.dbf' size 150;
CREATE TABLESPACE SNCMSINDX_TBS datafile 'sncmsindx_tbs.dbf' size 150;

create user cu_bas identified by snsoft2020 default tablespace SNSYSDATA_TBS;
create user cu_cfg identified by snsoft2020 default tablespace SNSYSDATA_TBS;
create user cu_ufg identified by snsoft2020 default tablespace SNSYSDATA_TBS;
grant resource to cu_bas;
grant resource to cu_cfg;
grant resource to cu_ufg;
```

```xml
	<workspace id="dev_dm" title="dm-dev" startTimerTask="false">
		<datasource host="192.168.1.100" port="5236" user="cu_bas" password="snsoft2020" type="dm" id="BASDATA"   />
		<datasource host="192.168.1.100" port="5236" user="cu_cfg" password="snsoft2020" type="dm" id="CONFIG"    />
		<datasource host="192.168.1.100" port="5236" user="cu_ufg" password="snsoft2020" type="dm" id="UCODECFG"  />
		<datasource host="192.168.1.100" port="5236" user="cu_bas" password="snsoft2020" type="dm" id="HELP"      />
		<datasource host="192.168.1.100" port="5236" user="cu_bas" password="snsoft2020" type="dm" id="APPR"      />
		<datasource host="192.168.1.100" port="5236" user="cu_bas" password="snsoft2020" type="dm" id="SN-MDM_W"  />
		<datasource host="192.168.1.100" port="5236" user="cu_bas" password="snsoft2020" type="dm" id="CMS_W"     />
		<datasource host="192.168.1.100" port="5236" user="cu_bas" password="snsoft2020" type="dm" id="PAY_W"     />
		<datasource host="192.168.1.100" port="5236" user="cu_bas" password="snsoft2020" type="dm" id="PAY_R"     />
		<datasource host="192.168.1.100" port="5236" user="cu_bas" password="snsoft2020" type="dm" id="SHARRDING" />
	</workspace>
```

#### Init for 人大金仓

```sql
--注意：路径为目录，目录与表空间一一对应
--详细参见：https://help.kingbase.com.cn/v8/admin/general/administrator-guide/11-Managing-Tablespaces.html
CREATE TABLESPACE snsysdata_tbs OWNER system LOCATION '/home/kingbase/kdb/snsysdata';
CREATE TABLESPACE snsysindx_tbs OWNER system LOCATION '/home/kingbase/kdb/snsysindx';
CREATE TABLESPACE ratify_tbs    OWNER system LOCATION '/home/kingbase/kdb/ratify';
--创建用户及授权：https://help.kingbase.com.cn/v8/safety/safety-guide/Managing-User-Authentication.html#id17
```

```xml
	<workspace id="dev_kb" title="KingbaseV8" startTimerTask="false">
		<datasource serverName="192.168.1.100" port="54321" databaseName="dlct" user="system" password="snsoft2020" type="kingbase" id="BASDATA"   />
		<datasource serverName="192.168.1.100" port="54321" databaseName="dlct" user="system" password="snsoft2020" type="kingbase" id="CONFIG"    />
		<datasource serverName="192.168.1.100" port="54321" databaseName="dlct" user="system" password="snsoft2020" type="kingbase" id="UCODECFG"  />
		<datasource serverName="192.168.1.100" port="54321" databaseName="dlct" user="system" password="snsoft2020" type="kingbase" id="HELP"      />
		<datasource serverName="192.168.1.100" port="54321" databaseName="dlct" user="system" password="snsoft2020" type="kingbase" id="APPR"      />
		<datasource serverName="192.168.1.100" port="54321" databaseName="dlct" user="system" password="snsoft2020" type="kingbase" id="SN-MDM_W"  />
		<datasource serverName="192.168.1.100" port="54321" databaseName="dlct" user="system" password="snsoft2020" type="kingbase" id="CMS_W"     />
		<datasource serverName="192.168.1.100" port="54321" databaseName="dlct" user="system" password="snsoft2020" type="kingbase" id="PAY_W"     />
		<datasource serverName="192.168.1.100" port="54321" databaseName="dlct" user="system" password="snsoft2020" type="kingbase" id="PAY_R"     />
		<datasource serverName="192.168.1.100" port="54321" databaseName="dlct" user="system" password="snsoft2020" type="kingbase" id="SHARRDING" />
	</workspace>
```

#### Init for PostgreSQL

```sql
--初始化存储及用户
psql
CREATE DATABSE n9b;
CREATE USER cu_bas WITH PASSWORD 'snsoft2020';
CREATE USER cu_cfg WITH PASSWORD 'snsoft2020';
CREATE USER cu_ufg WITH PASSWORD 'snsoft2020';
--创建schema
psql -d n9b
CREATE SCHEMA AUTHORIZATION cu_bas;
CREATE SCHEMA AUTHORIZATION cu_cfg;
CREATE SCHEMA AUTHORIZATION cu_ufg;
```

```xml
	<workspace id="dev_dm" title="dm-dev" startTimerTask="false">
		<datasource host="192.168.1.100" port="5432" database="n9b" user="cu_bas" password="snsoft2020" type="pg" id="BASDATA"   />
		<datasource host="192.168.1.100" port="5432" database="n9b" user="cu_cfg" password="snsoft2020" type="pg" id="CONFIG"    />
		<datasource host="192.168.1.100" port="5432" database="n9b" user="cu_ufg" password="snsoft2020" type="pg" id="UCODECFG"  />
		<datasource host="192.168.1.100" port="5432" database="n9b" user="cu_bas" password="snsoft2020" type="pg" id="HELP"      />
		<datasource host="192.168.1.100" port="5432" database="n9b" user="cu_bas" password="snsoft2020" type="pg" id="APPR"      />
		<datasource host="192.168.1.100" port="5432" database="n9b" user="cu_bas" password="snsoft2020" type="pg" id="SN-MDM_W"  />
		<datasource host="192.168.1.100" port="5432" database="n9b" user="cu_bas" password="snsoft2020" type="pg" id="CMS_W"     />
		<datasource host="192.168.1.100" port="5432" database="n9b" user="cu_bas" password="snsoft2020" type="pg" id="PAY_W"     />
		<datasource host="192.168.1.100" port="5432" database="n9b" user="cu_bas" password="snsoft2020" type="pg" id="PAY_R"     />
		<datasource host="192.168.1.100" port="5432" database="n9b" user="cu_bas" password="snsoft2020" type="pg" id="SHARRDING" />
	</workspace>
```

### 数据库功能

#### 安装[Install]

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th align="center" rowspan='2'>分类</th>
		<th align="center" colspan='2'>安装[Install]</th>
	</tr>
	<tr>
		<th align="center">图形[GUI]</th>
		<th align="center">静默[Silent]</th>
	</tr>
	<tr>
		<th>SQLServer</th>
		<td></td>
		<td>
		curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/7/mssql-server-2017.repo
		<br/>yum install -y mssql-server
		<br/>====================================or
		<br/>　　bzip2-1.0.6-13.el7.x86_64 
		<br/>　　libsss_nss_idmap-1.16.4-37.0.1.el7_8.4.x86_64 
		<br/>　　gdb-7.6.1-119.0.1.el7.x86_64 
		<br/>　　cyrus-sasl-2.1.26-23.el7.x86_64 
		<br/>　　cyrus-sasl-gssapi-2.1.26-23.el7.x86_64
		<br/>　　glibc-2.18.tar.gz(参见表后说明)
		<br/>　　mssql-server-14.0.3335.7-17.x86_64
		<br/>====================================
		<br/>
		<br/>systemctl stop mssql-server
		<br/>/opt/mssql/bin/mssql-conf setup
		<br/>/opt/mssql/bin/mssql-conf set-collation
		<br/>　　Chinese_PRC_BIN/Chinese_PRC_CS_AS
		<br/>systemctl status mssql-server
	    <br/>
	    <br/>[支持的配置](https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-configure-mssql-conf?view=sql-server-linux-2017)
	    <br/>[安装](https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-setup?view=sql-server-linux-2017)
	    <br/>[卸载](https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-setup?view=sql-server-linux-2017#uninstall)
	    <br/>
		<br/>curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/7/prod.repo
		<br/>yum install -y mssql-tools unixODBC-devel
		<br/>echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
		<br/>echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
		<br/>source ~/.bashrc
		<br/>
		<br/>sqlcmd -S 127.0.0.1 -U sa -P '\*\*\*\*\*\*\*\*'
		<br/>&gt;select name from sys.databases;
		<br/>&gt;go
		</td>
	</tr>
	<tr>
		<th>Oracle11g</th>
		<td></td>
		<td>
		cd /etc/yum.repos.d
		<br/>wget http://public-yum.oracle.com/public-yum-ol7.repo
		<br/>wget http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol7 -O /etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
		<br/>yum -y install oracle-rdbms-server-11gR2-preinstall
		<br/>yum -y install elfutils-libelf-devel
		<br/>root>vi /etc/oraInst.loc
		<br/>　　inventory_loc=/opt/oracle/oraInventory
		<br/>　　inst_group=oinstal
		<br/>passwd oracle
		<br/>
		<br/>环境变量设置（oracle用户下.bash_profile）
		<br/>export ORACLE_HOSTNAME=dev.snsoft
		<br/>export ORACLE_UNQNAME=orcl11g
		<br/>export ORACLE_BASE=/opt/oracle
		<br/>export ORACLE_HOME=$ORACLE_BASE/product/11.2/db
		<br/>export ORACLE_SID=orcl11g
		<br/>
		<br/>(注意：/etc/hosts中的hostname/ip必须与真实的oracle_hostname/ip对应)
		<br/>(注意：响应文件可以从./response目录下拷贝模板并修改)
		<br/>安装软件包：./runInstaller -ignorePrereq -silent -responseFile /opt/oracle/database/orcl11g_db_install.rsp
		<br/>启动监听：lsnrctl start
		<br/>安装数据库：dbca -silent -createDatabase -templateName General_Purpose.dbc -gdbName orcl11g -sid orcl11g -characterSet AL32UTF8 -responseFile /opt/oracle/database/orcl11g_dbca.rsp
		<br/>单独安装EM：emca -config dbcontrol db -repos create/recreate
		<br/>
		<br/> vim sqlplus/admin/glogin.sql
		<br/> set sqlprompt "_user'@'_connect_identifier> "
		<br/>
		<br/>sqlplus / as sysdba
		<br/>&gt;alter system set db_create_file_dest='/data/oracle/oradata' --指定OMF表空间目录
		<br/>
		<br/>设置自动启动
		<br/>vi /etc/systemd/system/oracle-rdbms.service
		<br/>　　# /etc/systemd/system/oracle-rdbms.service
		<br/>　　# Invoking Oracle scripts to start/shutdown Instances defined in /etc/oratab
		<br/>　　# and starts Listener and starts emconsole
		<br/>　　[Unit]
		<br/>　　Description=Oracle Database(s) and Listener
		<br/>　　Requires=network.target
		<br/>　　[Service]
		<br/>　　Type=forking
		<br/>　　Restart=no
		<br/>　　ExecStart=/opt/oracle/product/11.2/db/bin/dbstart /opt/oracle/product/11.2/db
		<br/>　　ExecStop =/opt/oracle/product/11.2/db/bin/dbshut  /opt/oracle/product/11.2/db
		<br/>　　User=oracle
		<br/>　　[Install]
		<br/>　　WantedBy=multi-user.target
		<br/>
		<br/>systemctl start/status/stop oracle-rdbms
		</td>
	</tr>
	<tr>
		<th>MySQL</th>
		<td></td>
		<td>
		参见：https://dev.mysql.com/doc/refman/8.0/en/linux-installation-yum-repo.html
		<br/>grep 'temporary password' /var/log/mysqld.log
		<br/>mysql -u root -p
		<br/>ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!';
		<br/>show databases;
		<br/>
		<br/>[配置文件](https://dev.mysql.com/doc/refman/8.0/en/option-files.html)与[默认配置](https://dev.mysql.com/doc/refman/8.0/en/server-configuration-defaults.html)
		<br/>
		</td>
	</tr>
	<tr>
		<th>DB2</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>武汉达梦</th>
		<td></td>
		<td>
			**添加用户组**
			<br/> groupadd dinstall
			<br/> useradd -g dinstall -m -d /home/dmdba -s /bin/bash dmdba
			<br/> passwd dmdba
			<br/> **文件目录**
			<br/> mkdir -p /dm/dmdbms
			<br/> mkdir -p /dm/dmarch
			<br/> mkdir -p /dm/dmbak
			<br/> chown -R dmdba:dinstall /dm/
			<br/> chmod -R 775 /dm/
			<br/> **设置到.bash_profile**
			<br/> export DM_HOME="/dm/dmdbms"
			<br/> export PATH="/dm/dmdbms/bin:$PATH"
			<br/> **设置系统权限**
			<br/> cat >> /etc/security/limits.conf << EOF   
			<br/> dmdba  soft  nofile  4096
			<br/> dmdba  hard  nofile  65536
			<br/> EOF
			<br/> **挂载光盘并安装**
			<br/> mkdir -p /mnt/iso
			<br/> mount /root/dm.iso /mnt/iso
			<br/> /mnt/iso/DMInstall.bin -q /dm/dmrsp.xml(参见表格后面)
			<br/> **手工设置自动启动服务**
			<br/> mv /dm/dmdbms/bin/DmServiceDMSERVER.service /usr/lib/systemd/system/DmServiceDMSERVER.service
			<br/> systemctl enable DmServiceDMSERVER.service
			<br/> systemctl start DmServiceDMSERVER.service
		</td>
	</tr>
	<tr>
		<th>人大进仓</th>
		<td></td>
		<td>
			[参见官方文档](https://help.kingbase.com.cn/v8/install-updata/install-linux/index.html)
			<br/>安装完成后root用户执行：/opt/Kingbase/ES/V8/install/script/root.sh启动服务
			<br/>以后启停服务使用kingbase用户：sys_ctl start/stop/status/restart -D /home/kingbase/data
		</td>
	</tr>
	<tr>
		<th>Redis</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>MongoDB</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>ElasticSearch</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th></th>
		<td></td>
		<td></td>
	</tr>
</table>

* **SQLServer升级glibc-2.17到glibc-2.18**

```shell
yum install gcc
wget http://ftp.gnu.org/gnu/glibc/glibc-2.18.tar.gz
tar zxf glibc-2.18.tar.gz 
cd glibc-2.18/
mkdir build
cd build/
../configure --prefix=/usr
make -j4
make install
```

* **达梦静默安装文件dmrsp.xml**

```xml
<?xml version="1.0"?>
<DATABASE>
	<LANGUAGE>en</LANGUAGE>
	<TIME_ZONE>+08:00</TIME_ZONE>
	<INSTALL_TYPE>0</INSTALL_TYPE>
	<INSTALL_PATH>/dm/dmdbms</INSTALL_PATH>
	<INIT_DB>Y</INIT_DB>
	<DB_PARAMS>
		<PATH>/dm/dmdbms/data</PATH>
		<DB_NAME>DAMENG</DB_NAME>
		<INSTANCE_NAME>DMSERVER</INSTANCE_NAME>
		<PORT_NUM>5236</PORT_NUM>
		<CTL_PATHS>
			<CTL_PATH>/dm/dmdbms/data/DAMENG/dm.ctl</CTL_PATH>
		</CTL_PATHS>
		<LOG_PATHS>
			<LOG_PATH>/dm/dmdbms/data/DAMENG/DAMENG01.log</LOG_PATH>
			<LOG_PATH>/dm/dmdbms/data/DAMENG/DAMENG02.log</LOG_PATH>
			<LOG_PATH>/dm/dmdbms/data/DAMENG/DAMENG03.log</LOG_PATH>
		</LOG_PATHS>
		<EXTENT_SIZE>16</EXTENT_SIZE>
		<PAGE_SIZE>8</PAGE_SIZE>
		<LOG_SIZE>512</LOG_SIZE>
		<CASE_SENSITIVE>Y</CASE_SENSITIVE>
		<CHARSET>1</CHARSET>
		<LENGTH_IN_CHAR>0</LENGTH_IN_CHAR>
		<USE_NEW_HASH>1</USE_NEW_HASH>
		<SYSDBA_PWD>snjixl2020</SYSDBA_PWD>
		<SYSAUDITOR_PWD>snjixl2020</SYSAUDITOR_PWD>
		<SYSSSO_PWD>snjixl2020</SYSSSO_PWD>
		<SYSDBO_PWD>snjixl2020</SYSDBO_PWD>
		<TIME_ZONE>+08:00</TIME_ZONE>
		<PAGE_CHECK>0</PAGE_CHECK>
		<EXTERNAL_CIPHER_NAME></EXTERNAL_CIPHER_NAME>
		<EXTERNAL_HASH_NAME></EXTERNAL_HASH_NAME>
		<EXTERNAL_CRYPTO_NAME></EXTERNAL_CRYPTO_NAME>
		<SVR_KEY></SVR_KEY>
		<DB_KEY></DB_KEY>
		<ENCRYPT_PWD></ENCRYPT_PWD>
		<ENCRYPT_NAME></ENCRYPT_NAME>
		<RLOG_ENC_FLAG>N</RLOG_ENC_FLAG>
		<USBKEY_PIN></USBKEY_PIN>
		<BLANK_PAD_MODE>0</BLANK_PAD_MODE>
		<SYSTEM_MIRROR_PATH></SYSTEM_MIRROR_PATH>
		<MAIN_MIRROR_PATH></MAIN_MIRROR_PATH>
		<ROLL_MIRROR_PATH></ROLL_MIRROR_PATH>
		<PRIV_FLAG>0</PRIV_FLAG>
	</DB_PARAMS>
	<CREATE_DB_SERVICE>N</CREATE_DB_SERVICE>
	<STARTUP_DB_SERVICE>Y</STARTUP_DB_SERVICE>
</DATABASE>
```

#### 安全[Security]

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th align="center" rowspan='2'>分类</th>
		<th align="center" colspan='2'>安全[Security]</th>
	</tr>
	<tr>
		<th align="center">认证[Authentication]</th>
		<th align="center">授权[Authorization]</th>
	</tr>
	<tr>
		<th rowspan='2'>SQLServer</th>
		<td>
			use master;<br/>
			create login [user_login] with password=N'[pwd]'<br/>
			  &nbsp;&nbsp;,default_database=master<br/>
			  &nbsp;&nbsp;,check_expiration=off<br/>
			  &nbsp;&nbsp;,check_policy=off;<br/>
			  [create login user](https://docs.microsoft.com/en-us/sql/relational-databases/security/authentication-access/create-a-login?view=sql-server-ver15)
			  <br/>[create db user](https://docs.microsoft.com/en-us/sql/relational-databases/security/authentication-access/create-a-database-user?view=sql-server-ver15)
			  <br/>[create schema](https://docs.microsoft.com/en-us/sql/relational-databases/security/authentication-access/create-a-database-schema?view=sql-server-ver15)
		</td>
		<td>
			create database [dbname] collate Chinese_PRC_BIN;<br/>
			use [dbname];<br/>
			create schema [schema_name];<br/>
			create user [user_db] for login [user_login];<br/>
			alter user [user_db] with default_schema=[schema_name];<br/>
			grant create table,create view,create function to [user_db];<br/>
			grant control on schema::[schema_name] to [user_db];<br/>
			[grant db permissions](https://docs.microsoft.com/en-us/sql/t-sql/statements/grant-database-permissions-transact-sql?view=sql-server-ver15)
			<br/>[grant schema permissions](https://docs.microsoft.com/en-us/sql/t-sql/statements/grant-schema-permissions-transact-sql?view=sql-server-ver15)
		</td>
	</tr>
	<tr>
		<td colspan='2'>
			jdbc:sqlserver://[host]:[port?1433]?DatabaseName=[dbname]&user=[user_login]&password=[pwd]
			<br/>&gt; sqlcmd -S host[,port] -U sa -P 'passwd' [-d dbname]
			<br/>&gt; select name from sys.databases;
			<br/>&gt; go
			<br/>&gt; use database
		</td>
	</tr>
	<tr>
		<th rowspan='2'>Oracle</th>
		<td>
			create user [user] identified by [pwd]<br/>
			  &nbsp;&nbsp;default tablespace SNSYSDATA_TBS;<br/>
			grant connect to [user];
		</td>
		<td>
			grant resource,create view,unlimited tablespace to [user];
		</td>
	</tr>
	<tr>
		<td colspan='2'>
			jdbc:oracle:thin:@//[host]:[port?1521]/[dbname]?user=[user]&password=[pwd]
			<br/>&gt; su - oracle
			<br/>&gt; sqlplus / as sydba
			<br/>&gt; sqlplus user/passwd@//host:port/dbname [as role]
		</td>
	</tr>
	<tr>
		<th rowspan='2'>MySQL</th>
		<td>create user '[user]'@'%' identified by '[pwd]';</td>
		<td>
			create database [dbname] default character set utf8mb4 collate utf8mb4_bin;<br/>
			grant all privileges on [dbname].* to '[user]'@'%';
		</td>
	</tr>
	<tr>
		<td colspan='2'>jdbc:mysql://[host]:[port?3306]/[dbname]?user=[user]&password=[pwd]&characterEncoding=UTF8&serverTimezone=Asia/Shanghai
		<br/>&gt; mysql -h localhost [-P 3306] -u root -p
		</td>
	</tr>
	<tr>
		<th rowspan='2'>DB2</th>
		<td>
			OS(Default)/Kerberos/LDAP
			<br/>[DB2 Security Model](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_9.7.0/com.ibm.db2.luw.admin.sec.doc/doc/c0021804.html)
			(Too TM's complated)
		</td>
		<td>
			create database [dbname] pagesize 32768;
			<br/>create schema [schema_name] authorization [db2user];
			<br/>级别：System-Level->Database-Level->Object-Level->Content-based.
		</td>
	</tr>
	<tr>
		<td colspan='2'>
			jdbc:db2://[host]:[port?50000]/[dbname]?user=[user]&password=[pwd]&driverType=4&currentSchema=[schema_name]
			<br/>&gt; su - db2user
			<br/>&gt; db2start
			<br/>&gt; db2
			<br/>&gt; list database directory
			<br/>&gt; connect to [dbname]
			<br/>&gt; list active databases
			<br/>&gt; list tablespaces
			<br/>&gt; list tables
		</td>
	</tr>
	<tr>
		<th rowspan='2'>达梦</th>
		<td>create user [user] identified by [pwd] default tablespace snsysdata_tbs;</td>
		<td>grant resource to [user];</td>
	</tr>
	<tr>
		<td colspan='2'>
			jdbc:dm://[host]:[port]?user=[user]&password=[pwd]
			<br/>&gt; su - dmdba
			<br/>&gt; disql user/passwd@host:5236
		</td>
	</tr>
	<tr>
		<th rowspan='2'>Postgres</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<td colspan='2'>
			jdbc:postgresql://[host]:[port]/[dbname]?user=[user]&password=[pwd]
			<br/>&gt; su - postgres
			<br/>&gt; psql -h host -p port -d dbname -U user -W
			<br/>&gt; select current_database(),current_schema(),current_user;
		</td>
	</tr>
	<tr>
		<th>Redis</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>MongoDB</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>ElasticSearch</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th></th>
		<td></td>
		<td></td>
	</tr>
</table>

#### 存储[Storage]

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th align="center" rowspan='2'>分类</th>
		<th align="center" colspan='2'>存储[Storage]</th>
	</tr>
	<tr>
		<th align="center">物理存储[Physical Storage]</th>
		<th align="center">逻辑存储[Logical Storage]</th>
	</tr>
	<tr>
		<th>SQLServer</th>
		<td>
			create database [dbname] collate Chinese_PRC_CS_AS;
			<br/>select \* from sys.database_files;
			<br/>select \* from sys.master_files;
			<br/>===设置默认位置====
			<br/>mkdir /data/sqlserver/data
			<br/>mkdir /data/sqlserver/log
			<br/>chown -R mssql:mssql /data/sqlserver
			<br/>/opt/mssql/bin/mssql-conf set filelocation.defaultdatadir /data/sqlserver/data
			<br/>/opt/mssql/bin/mssql-conf set filelocation.defaultlogdir  /data/sqlserver/log
			<br/>===查询文件位置====
			<br/>cat /var/opt/mssql/mssql.conf
			<br/>默认位置：(/var/opt/mssql/data)
		</td>
		<td>
			db=>extent(8 pages)=>page(8k)
			<br/>[Extend & Page Architecture](https://docs.microsoft.com/en-us/sql/relational-databases/pages-and-extents-architecture-guide?view=sql-server-2017)
		</td>
	</tr>
	<tr>
		<th>Oracle</th>
		<td>
			create tablespace [tbs_name];
			<br/>select \* from dba_tablespaces;
			<br/>select \* from dba_data_files;
			<br/>alter system set db_create_file_dest='/data/oracle';
			<br/>show parameter db_create_file_dest;
			<br/>默认位置(启用OMF)：$ORACLE_BASE/oradata
			<br/>查看其它文件路径：show parameter dest
			<br/>[Physical Storage Structures](https://docs.oracle.com/en/database/oracle/oracle-database/18/cncpt/physical-storage-structures.html#GUID-FFA872E1-7F63-4DC5-8A35-F21394AB4595)
		</td>
		<td>
			tablespace->segment->extent->data block
			<br>[Logical Storage Structures](https://docs.oracle.com/en/database/oracle/oracle-database/18/cncpt/logical-storage-structures.html#GUID-13CE5EDA-8C66-4CA0-87B5-4069215A368D)
		</td>
	</tr>
	<tr>
		<th>MySQL</th>
		<td>
			create database [dbname];
			<br/>查看数据库文件位置：show variables like '%datadir%';
		</td>
		<td></td>
	</tr>
	<tr>
		<th>DB2</th>
		<td>
			create database [dbname] pagesize 32768;
			<br/>create schema [schema_name] authorization [db2user];
		</td>
		<td></td>
	</tr>
	<tr>
		<th>达梦</th>
		<td>
			create tablespace [tbsname] datafile 'name.dbf' size 150;
		</td>
		<td></td>
	</tr>
	<tr>
		<th>Postgres</th>
		<td>
		</td>
		<td></td>
	</tr>
	<tr>
		<th>Redis</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>MongoDB</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>ElasticSearch</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th></th>
		<td></td>
		<td></td>
	</tr>
</table>

#### 内存[Memory]

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th align="center" rowspan='2'>分类</th>
		<th align="center" colspan='2'>内存[Memory]</th>
	</tr>
	<tr>
		<th align="center">内存管理[Management]</th>
		<th align="center">内存调优[Tuning]</th>
	</tr>
	<tr>
		<th>SQLServer</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>Oracle</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>MySQL</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>DB2</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>Redis</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>MongoDB</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>ElasticSearch</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th></th>
		<td></td>
		<td></td>
	</tr>
</table>

#### 迁移[Migration]

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th align="center" rowspan='2'>分类</th>
		<th align="center" colspan='2'>迁移[Migration]</th>
	</tr>
	<tr>
		<th align="center">导出[export]</th>
		<th align="center">导入[import]</th>
	</tr>
	<tr>
		<th>SQLServer</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>Oracle</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>MySQL</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>DB2</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>Redis</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>MongoDB</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>ElasticSearch</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th></th>
		<td></td>
		<td></td>
	</tr>
</table>

#### 可用性[Availability]

1. 全量/增量备份;
2. 一致/非一致备份;
3. 热备/冷备;

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th align="center" rowspan='2'>分类</th>
		<th align="center" colspan='2'>可用性[Availability]</th>
	</tr>
	<tr>
		<th align="center">备份[Backup]</th>
		<th align="center">还原(恢复/修复)[Restore/Recovery]</th>
	</tr>
	<tr>
		<th>SQLServer</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>Oracle</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>MySQL</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>DB2</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>Redis</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>MongoDB</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>ElasticSearch</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th></th>
		<td></td>
		<td></td>
	</tr>
</table>

#### 性能调优[Performance Tuning]

1. 数据库调优：
	* 内存(memory);
	* IO(IO);
	* 存储(Storage);
	* 并行度(parallel);
2. SQL调优：
	* 结构设计;
	* SQL结构;
	* 索引(索引类型);
	* 分区;

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th align="center" rowspan='2'>分类</th>
		<th align="center" colspan='2'>安装[Install]</th>
	</tr>
	<tr>
		<th align="center">数据库调优[DB/Instance Tuning]</th>
		<th align="center">SQL调优[SQL Tuning]</th>
	</tr>
	<tr>
		<th>SQLServer</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>Oracle</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>MySQL</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>DB2</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>Redis</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>MongoDB</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>ElasticSearch</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th></th>
		<td></td>
		<td></td>
	</tr>
</table>

#### 高可用(HA)[High Availability]

* 主从;
* 集群;

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th align="center" rowspan='2'>分类</th>
		<th align="center" colspan='2'>安装[Install]</th>
	</tr>
	<tr>
		<th align="center">图形[GUI]</th>
		<th align="center">静默[Silent]</th>
	</tr>
	<tr>
		<th>SQLServer</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>Oracle</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>MySQL</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>DB2</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>Redis</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>MongoDB</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>ElasticSearch</th>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th></th>
		<td></td>
		<td></td>
	</tr>
</table>



<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th align="center">分类</th>
		<th align="center">二级分类</th>
		<th align="center">简要说明</th>
		<th align="center">SQLServer</th>
		<th align="center">Oracle</th>
		<th align="center">MySQL</th>
		<th align="center">DB2</th>
		<th align="center">Redis</th>
		<th align="center">MongoDB</th>
		<th align="center">ElasticSearch</th>
	</tr>
	<tr>
		<th align="center" rowspan='2'>安装[Install]</th>
		<th align="center">图形[GUI]</th>
		<td rowspan='2'>
			1. PreInstall:安装前准备;<br/>
			2. Install：安装并设置选项;<br/>
			3. PostIntall：安装后处理;
		</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th align="center">静默[Silent]</th>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th align="center" rowspan='2'>安全[Security]</th>
		<th align="center">认证<br/>[Authentication]</th>
		<td>谁可以访问/登录<br/>(Access/Logon)</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th align="center">授权<br/>[Authorization]</th>
		<td>登录后能做什么<br/>(Privileges)</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th align="center" rowspan='2'>存储[Storage]</th>
		<th align="center">物理存储<br/>[Physical Storage]</th>
		<td>一般对应OS文件</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th align="center">逻辑存储<br/>[Logical Storage]</th>
		<td>逻辑分配单元</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th align="center">内存<br/>[Memory]</th>
		<td></td>
		<td>
			1. 内存管理;<br/>
			2. 内存调优;
		</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th align="center">迁移<br/>[Migration]</th>
		<td></td>
		<td>
			1. 导出[export];<br/>
			2. 导入[import];</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th align="center" rowspan='2'>可用性<br/>[Availability]</th>
		<th align="center">备份<br/>[Backup]</th>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th align="center">还原(恢复/修复)<br/>[Restore/Recovery]</th>
		<td>
			1. 全量/增量备份;<br/>
			2. 一致/非一致备份;<br/>
			3. 热备/冷备;
		</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th align="center" rowspan='2'>性能调优<br/>[Performance Tuning]</th>
		<th align="center">数据库调优<br/>[DB/Instance Tuning]</th>
		<td>
			1. 内存(memory);<br/>
			2. IO(IO);<br/>
			3. 存储(Storage);<br/>
			4. 并行度(parallel);<br/>
		</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th align="center">SQL调优<br/>[SQL Tuning]</th>
		<td>
			0. 结构设计;<br/>
			1. SQL结构;<br/>
			2. 索引(索引类型);<br/>
			3. 分区;<br/>
		</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th align="center">高可用(HA)<br/>[High Availability]</th>
		<td></td>
		<td>
			1. 主从;<br/>
			2. 集群;
		</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>
</table>

#### 常用命令[Usual Commands]

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th align="center" rowspan='2'>分类</th>
		<th align="center" colspan='3'>可用性[Availability]</th>
	</tr>
	<tr>
		<th align="center">Script</th>
		<th align="center">SQL</th>
		<th align="center">Special</th>
	</tr>
	<tr>
		<th>SQLServer</th>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>Oracle</th>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>MySQL</th>
		<td>show databases;
			<br/>use  [dbname];
			<br/>show tables;
			<br/>desc [tblname];
			<br/>show variables[ like 'name_pattern'];
		</td>
		<td>select user();--查询当前连接用户
			<br/>select database();--查询当前使用的数据库
			<br/>select version(),current_date,now(),sysdate();
		</td>
		<td>正则匹配：regexp_like(colname,'regexp')
			<br/>[Regular Expression](https://dev.mysql.com/doc/refman/8.0/en/regexp.html)
		</td>
	</tr>
	<tr>
		<th>DB2</th>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>达梦</th>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>Postgres</th>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>Redis</th>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>MongoDB</th>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>ElasticSearch</th>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th></th>
		<td></td>
		<td></td>
		<td></td>
	</tr>
</table>

#### 层级架构[Hierarchy]

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th align="center">分类</th>
		<th align="center">说明</th>
	</tr>
	<tr>
		<th>SQLServer</th>
		<td>DB Server-(1:n)->Instance-(1:n)->Database-(1:n)->Schema
		</td>
	</tr>
	<tr>
		<th>Oracle</th>
		<td>DB Server-(1:n)->Instance-(1:1)->Database-(1:n)->Schema</td>
	</tr>
	<tr>
		<th>MySQL</th>
		<td>DB Server-(1:n)->Instance-(1:n)->Database</td>
	</tr>
	<tr>
		<th>DB2</th>
		<td>DB Server-(1:n)->Instance-(1:n)->Database-(1:n)->Schema</td>
	</tr>
	<tr>
		<th>达梦</th>
		<td>DB Server-(1:n)->Instance-(1:1)->Database-(1:n)->Schema</td>
	</tr>
	<tr>
		<th>Postgres</th>
		<td>DB Server-(1:n)->Instance-(1:n)->Database-(1:n)->Schema</td>
	</tr>
	<tr>
		<th>Redis</th>
		<td></td>
	</tr>
	<tr>
		<th>MongoDB</th>
		<td></td>
	</tr>
	<tr>
		<th>ElasticSearch</th>
		<td></td>
	</tr>
	<tr>
		<th></th>
		<td></td>
	</tr>
</table>