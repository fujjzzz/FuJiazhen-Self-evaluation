**3.20**
课堂上被提问上节课学的基本类型（数，字符串），我忘记了没回答出来，重新学习这部分知识总结如下：
**基本数据类型（数值）**
int:整数类型（与机器相关，等价于integer）
smallint:小整数整形（int的子集）
numeric(p,d):定点数，（最多）有p位数字，小数点右边有d位（在PG中等价于decimal）
float(n)：精度至少为n（binary）位的浮点数
real,double precision:浮点数与双精度浮点数（与机器相关）
**基本数据类型（字符串）**
char(n):固定长度的字符串，长度为n(等价于character)
varchar(n):可变长度的字符串，最大长度为n(等价于character varying)
text：非SQL标准，表示任意长度的字符串
**基本数据类型（null）**
表示一个缺失的值：可能存在但未知or可能不存在

**3.23**
