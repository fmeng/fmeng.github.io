---
layout: post
title: "mysql复习"
categories:
- other
---

## 数据类型 ##


- 整型
<table border=1>
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
<table border=1>
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

<table border=1>
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

- 建表语句
	
		create table student（
			id int not null default 0,
			name varchar(10) not null default '',
			gender char(1) not null default 
		）；

