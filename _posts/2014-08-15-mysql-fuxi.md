---
layout: post
title: "mysql复习"
categories:
- other
---

## 数据类型 ##


- 整型
<table class="meng">
<tr><td>类型</td><td>字节</td><td>最小值</td><td>最大值</td></tr>
<tr><td>&nbsp;</td><td>&nbsp;</td><td>有/无符号</td><td>有/无符号</td></tr>
<tr><td>tinyint</td><td>1</td><td>-128</td><td>127</td></tr>
<tr><td>tinyint</td><td>1</td><td>0</td><td>255</td></tr>
<tr><td>smallint</td><td>2</td><td>-32768</td><td>32767(3万)</td></tr>
<tr><td>smallint</td><td>2</td><td>0</td><td>65535(6万)</td></tr>
<tr><td>mediumint</td><td>3</td><td>-8388608</td><td>8388607(800万)</td></tr>
<tr><td>mediumint</td><td>3</td><td>0</td><td>16777215(1600万)</td></tr>
<tr><td>int</td><td>4</td><td>-2147483648</td><td>2147483647(20亿)</td></tr>
<tr><td>int</td><td>4</td><td>0</td><td>4294967295(40亿)</td></tr>
<tr><td>bigint</td><td>8</td><td>-1000亿亿(20)</td><td>1000亿亿(20)</td></tr>
<tr><td>bigint</td><td>8</td><td>0</td><td>21474836472000亿亿(20)</td></tr>
<table>
*****
unsigned 代表无符号，zerofill代表零填充 ,smallint(2)2代表零填充的宽度。
只有在无符号位时，零填充才有效。
*****


- 浮点型/定点型
<table class="meng">
<tr><td>类型</td><td>字节</td><td>最小值</td><td>最大值</td></tr>
<tr><td>&nbsp;</td><td>?</td><td>有/无符号</td><td>有/无符号</td></tr>
<tr><td>float(m,d)</td><td>4/8</td><td>-9999.99</td><td>9999.99(1万)</td></tr>
<tr><td>float(m,d)</td><td>&nbsp;</td><td>0.00</td><td>2万</td></tr>
<tr><td>decimal(m,d)</td><td>4/8</td><td>-9999.99</td><td>9999.99(2万)</td></tr>
<tr><td>decimal(m,d)</td><td>&nbsp;</td><td>0.00</td><td>2万</td></tr>
<table>
****
float(m,d)m代表精度（总共有多少位，不含小数点），d代表小数位数。
float,decimal都能表示小数，但是decimal更精确一点。
****

- 字符型

<table class="meng">
<tr><td>类型</td><td>是否定长</td><td>字节范围</td></tr>
<tr><td>char(m)</td><td>定长</td><td>0< m <255</td></tr>
<tr><td>varchar</td><td>变长</td><td>0< m <65535</td></tr>
</table>
****
char(m),实际占用m个字符，存储的数据不够m个，要用空格补齐。在数据取出过程中，会把字符后面的空格去掉。<br/>
varchar(m),有1-2个自己来表示存储数据的真实长度。
****

- 日期和时间

	1. year, 1901-2055，如果输入00-69表示2000-2069，如果输入70-99表示1970-1999。
	2. date,YYYY-MM-DD,范围1000-01-01 > 9999-13-31
	3. time HH:II:SS,范围-838-59-59 > 838-59-59
	4. datetime YY-MM-DD HH:II:SS 范围1000-01-01 00：00:00 > 9999-12-31 23:59:59
	
****
time也可以表示时间差，所以也有负值。<br/>
datetime格式的数据一般不常用，要精确到秒，可以使用整型存储的时间戳。
****

- 类型转换

<table class="meng">
<thead>
<tr>
<td>
<p align="center">
<b>下述MySQL数据类型</b></td>
<td>
<p align="center">
<b>总能转换为下述Java类型</b></td>
</tr>
</thead>
<tr>
<td>
<p><span class="type">
<span>CHAR, VARCHAR, BLOB, TEXT, ENUM, and 
SET</span></span></td>
<td>
<p>
<span>java.lang.String, java.io.InputStream, 
java.io.Reader, java.sql.Blob, java.sql.Clob</span></td>
</tr>
<tr>
<td>
<p><span class="type">
<span>FLOAT, REAL, DOUBLE PRECISION, 
NUMERIC, DECIMAL, TINYINT, SMALLINT, MEDIUMINT, INTEGER, 
BIGINT</span></span></td>
<td>
<p>
<span>java.lang.String, java.lang.Short, 
java.lang.Integer, java.lang.Long, java.lang.Double, 
java.math.BigDecimal</span><span>
</span></p>
<h3>注释：</h3>
<p>与希望转换的MySQL数据类型相比，如果选择了精度较低的Java数值类型，可能会出现舍入、溢出或精度损失。</td>
</tr>
<tr>
<td>
<p><span class="type">
<span>DATE, TIME, DATETIME, TIMESTAMP</span></span></td>
<td>
<p>
<span>java.lang.String, java.sql.Date, 
java.sql.Timestamp</span></td>
</tr>
</table>

### 用于ResultSet.getObject()的MySQL类型和Java类型 ###

<table class="meng">
<thead>
<tr>
<td>
<p align="center">
<b><span>MySQL</span>类型名称</b></td>
<td>
<p align="center">
<b>以<span>Java</span>类返回</b></td>
</tr>
</thead>
<tr>
<td>
<p><span class="type">
<span>BIT(1)</span></span><span> 
(new in MySQL-5.0)</span></td>
<td>
<p>
<span>java.lang.Boolean</span></td>
</tr>
<tr>
<td>
<p><span class="type">
<span>BIT( &gt; 1)</span></span><span> 
(new in MySQL-5.0)</span></td>
<td>
<p>
<span>byte[]</span></td>
</tr>
<tr>
<td>
<p><span class="type">
<span>TINYINT</span></span></td>
<td>
<p><span>
<span>java.lang.Boolean</span>，如果将配置属性</span>“<span>tinyInt1isBit</span>”设为“真”（默认值），并将存储大小设为“<span>1</span>”；或<span><span>java.lang.Integer</span>，如果不是的话。</span></td>
</tr>
<tr>
<td>
<p><span class="type">
<span>BOOL , BOOLEAN</span></span></td>
<td>
<p>请参见上面的<span class="type"><span>TINYINT</span>，它们目前是<span>TINYINT(1)</span></span>的别名。</td>
</tr>
<tr>
<td>
<p><span class="type">
<span>SMALLINT[(M)] [UNSIGNED]</span></span></td>
<td>
<p>
<span>java.lang.Integer</span>（无论是否为<span>UNSIGNED</span>）</td>
</tr>
<tr>
<td>
<p><span class="type">
<span>MEDIUMINT[(M)] [UNSIGNED]</span></span></td>
<td>
<p>
<span>java.lang.Integer</span>（无论是否为<span>UNSIGNED</span>）</td>
</tr>
<tr>
<td>
<p><span class="type">
<span>INT,INTEGER[(M)] [UNSIGNED]</span></span></td>
<td>
<p>
<span>java.lang.Integer</span>，如果是<span>UNSIGNED</span>，<span>java.lang.Long</span></td>
</tr>
<tr>
<td>
<p><span class="type">
<span>BIGINT[(M)] [UNSIGNED]</span></span></td>
<td>
<p>
<span>java.lang.Long</span>，如果是<span>UNSIGNED</span>，<span>java.math.BigInteger</span></td>
</tr>
<tr>
<td>
<p><span class="type">
<span>FLOAT[(M,D)]</span></span></td>
<td>
<p>
<span>java.lang.Float</span></td>
</tr>
<tr>
<td>
<p><span class="type">
<span>DOUBLE[(M,B)]</span></span></td>
<td>
<p>
<span>java.lang.Double</span></td>
</tr>
<tr>
<td>
<p><span class="type">
<span>DECIMAL[(M[,D])]</span></span></td>
<td>
<p>
<span>java.math.BigDecimal</span></td>
</tr>
<tr>
<td>
<p><span class="type">
<span>DATE</span></span></td>
<td>
<p>
<span>java.sql.Date</span></td>
</tr>
<tr>
<td>
<p><span class="type">
<span>DATETIME</span></span></td>
<td>
<p>
<span>java.sql.Timestamp</span></td>
</tr>
<tr>
<td>
<p><span class="type">
<span>TIMESTAMP[(M)]</span></span></td>
<td>
<p>
<span>java.sql.Timestamp</span></td>
</tr>
<tr>
<td>
<p><span class="type">
<span>TIME</span></span></td>
<td>
<p>
<span>java.sql.Time</span></td>
</tr>
<tr>
<td>
<p><span class="type">
<span>YEAR[(2|4)]</span></span></td>
<td>
<p><span>
<span>java.sql.Date</span>（日期设为<span>2</span>月<span>1</span>日晚上<span>2</span>点）</span></td>
</tr>
<tr>
<td>
<p><span class="type">
<span>CHAR(M)</span></span></td>
<td>
<p>
<span>java.lang.String</span>（除非列的字符集是<span class="type"><span>BINARY</span>），然后返回字节</span><span>[]</span></td>
</tr>
<tr>
<td>
<p><span class="type">
<span>VARCHAR(M) [BINARY]</span></span></td>
<td>
<p>
<span>java.lang.String</span>（除非列的字符集是<span class="type"><span>BINARY</span>），然后返回字节</span><span>[]</span></td>
</tr>
<tr>
<td>
<p><span class="type">
<span>BINARY(M)</span></span></td>
<td>
<p>
<span>byte[]</span></td>
</tr>
<tr>
<td>
<p><span class="type">
<span>VARBINARY(M)</span></span></td>
<td>
<p>
<span>byte[]</span></td>
</tr>
<tr>
<td>
<p><span class="type">
<span>TINYBLOB</span></span></td>
<td>
<p>
<span>byte[]</span></td>
</tr>
<tr>
<td>
<p><span class="type">
<span>TINYTEXT</span></span></td>
<td>
<p>
<span>java.lang.String</span></td>
</tr>
<tr>
<td>
<p><span class="type">
<span>BLOB</span></span></td>
<td>
<p>
<span>byte[]</span></td>
</tr>
<tr>
<td>
<p><span class="type">
<span>TEXT</span></span></td>
<td>
<p>
<span>java.lang.String</span></td>
</tr>
<tr>
<td>
<p><span class="type">
<span>MEDIUMBLOB</span></span></td>
<td>
<p>
<span>byte[]</span></td>
</tr>
<tr>
<td>
<p><span class="type">
<span>MEDIUMTEXT</span></span></td>
<td>
<p>
<span>java.lang.String</span></td>
</tr>
<tr>
<td>
<p><span class="type">
<span>LONGBLOB</span></span></td>
<td>
<p>
<span>byte[]</span></td>
</tr>
<tr>
<td>
<p><span class="type">
<span>LONGTEXT</span></span></td>
<td>
<p>
<span>java.lang.String</span></td>
</tr>
<tr>
<td>
<p><span class="type">
<span>ENUM(&#39;value1&#39;,&#39;value2&#39;,...)</span></span></td>
<td>
<p>
<span>java.lang.String</span></td>
</tr>
<tr>
<td>
<p><span class="type">
<span>SET(&#39;value1&#39;,&#39;value2&#39;,...)</span></span></td>
<td>
<p>
<span>java.lang.String</span></td>
</tr>
</table>

#### JDBC和JAVA数据类型之间的转换 ####

<table class="meng">
<tbody>
<tr>
<th width="20%" style="background-color:#CDCDCD;border:1px solid #AAAAAA;">
SQL
</th>
<th width="20%" style="background-color:#CDCDCD;border:1px solid #AAAAAA;">
JDBC/Java
</th>
<th width="20%" style="background-color:#CDCDCD;border:1px solid #AAAAAA;">
setXXX
</th>
<th width="20%" style="background-color:#CDCDCD;border:1px solid #AAAAAA;">
updateXXX
</th>
</tr>
<tr>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
VARCHAR
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
java.lang.String
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
setString
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
updateString
</td>
</tr>
<tr>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
CHAR
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
java.lang.String
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
setString
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
updateString
</td>
</tr>
<tr>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
LONGVARCHAR
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
java.lang.String
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
setString
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
updateString
</td>
</tr>
<tr>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
BIT
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
boolean
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
setBoolean
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
updateBoolean
</td>
</tr>
<tr>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
NUMERIC
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
java.math.BigDecimal
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
setBigDecimal
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
updateBigDecimal
</td>
</tr>
<tr>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
TINYINT
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
byte
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
setByte
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
updateByte
</td>
</tr>
<tr>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
SMALLINT
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
short
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
setShort
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
updateShort
</td>
</tr>
<tr>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
INTEGER
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
int
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
setInt
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
updateInt
</td>
</tr>
<tr>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
BIGINT
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
long
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
setLong
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
updateLong
</td>
</tr>
<tr>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
REAL
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
float
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
setFloat
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
updateFloat
</td>
</tr>
<tr>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
FLOAT
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
float
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
setFloat
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
updateFloat
</td>
</tr>
<tr>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
DOUBLE
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
double
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
setDouble
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
updateDouble
</td>
</tr>
<tr>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
VARBINARY
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
byte[ ]
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
setBytes
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
updateBytes
</td>
</tr>
<tr>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
BINARY
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
byte[ ]
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
setBytes
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
updateBytes
</td>
</tr>
<tr>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
DATE
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
java.sql.Date
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
setDate
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
updateDate
</td>
</tr>
<tr>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
TIME
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
java.sql.Time
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
setTime
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
updateTime
</td>
</tr>
<tr>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
TIMESTAMP
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
java.sql.Timestamp
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
setTimestamp
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
updateTimestamp
</td>
</tr>
<tr>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
CLOB
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
java.sql.Clob
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
setClob
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
updateClob
</td>
</tr>
<tr>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
BLOB
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
java.sql.Blob
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
setBlob
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
updateBlob
</td>
</tr>
<tr>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
ARRAY
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
java.sql.Array
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
setARRAY
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
updateARRAY
</td>
</tr>
<tr>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
REF
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
java.sql.Ref
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
SetRef
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
updateRef
</td>
</tr>
<tr>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
STRUCT
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
java.sql.Struct
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
SetStruct
</td>
<td style="vertical-align:top;border:1px solid #AAAAAA;">
updateStruct
</td>
</tr>
</tbody>
</table>


- 建表语句
	
		create table student（
			id int not null default 0,
			name varchar(10) not null default '',
			gender char(1) not null default 
		）；

- 创建数据库，删除数据库

		create database testtest;
		drop database testtest;

- 添加列，删除列，修改列

		alter table user add nickName varchar(10);
		alter table user drop column nickName;
		