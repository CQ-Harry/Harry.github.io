---
title: Mysql常用函数整理
date: 2018-03-15 22:59:24
tags: [Mysql,常用函数]
categories: Mysql
---

**经常编写程序的一定体会得到函数的重要性，丰富的函数往往能让工作事半功倍。**
<!--more-->
#### 字符串函数

| 函数   | 功能  |
| ------------ | :------------ |
| concat(S1,S2,...Sn) | 连接 S1,S2,...Sn 为一个字符串 |
| insert(str,x,y,instr) | 将字符串 str 从 x 位置开始，y 个字符长的子串替换为 instr |
| lower(str) | 将字符串 str 中所有字符变为小写 |
| upper(str) | 将字符串 str 中所有字符变为大写 |
| left(str,x) | 返回字符串 str 最左边的 x 个字符 |
| right(str,x) | 返回字符串 str 最左边的 x 个字符 |
| lpad(str,n,pad) | 用字符串 pad 对 str 最左边进行填充，知道长度为n个字符串 |
| rpad(str,n,pad) | 用字符串 pad 对 str 最右边进行填充，知道长度为n个字符串 |
| ltrim(str) | 去掉字符串 str 左侧的空格 |
| rtrim(str) | 去掉字符串 str 右侧的空格 |
| repeat(str,x) | 返回 str 重复 x 次的结果 |
| replace(str,a,b) | 用字符串 b 替换字符串 str 中所有出现的字符串 a |
| strcmp(s1,s2) | 比较字符串 s1 和 s2 |
| trim(str) | 去掉字符串 str 行尾和行头的空格 |
| substring(str,x,y) | 返回从字符串str x 位置起 y 个字符长度的字串 |

#### 数值函数

| 函数 | 功能 |
| ------------ | :------------ |
| abs(x) | 返回 x 的绝对值 |
| ceil(x) | 返回大于 x 的最小正整数值 |
| floor(x) | 返回小于 x 的最大正整数值 |
| mod(x,y) | 返回 x/y 的模 |
| rand() | 返回 0~1 内的随机值 |
| round(x,y) | 返回参数 x 的四舍五入的有 y 位小数的值 |
| truncate(x,y) | 返回数字 x 截断为 y 位小数的结果 |

#### 日期和时间的函数

| 函数 | 功能 |
| ------------ | :------------ |
| curdate() | 返回当前日期 |
| curtime() | 返回当前时间 |
| now() | 返回当前的日期和时间 |
| unix_timestamp(data) | 返回日期date的UNIX时间戳 |
| from_unixtime | 返回UNIX时间戳的日期值 |
| week(date) | 返回日期date为一年中的第几周 |
| year(date) | 返回日期date的年份 |
| hour(time) | 返回time的小时值 |
| minute(time) | 返回time的分钟值 |
| monthname(date) | 返回date的月份名 |
| date_format(date,fmt) | 返回按字符串 fmt 格式化日期 date 值  |
| date_add(date,interval expr type) | 返回一个日期或时间值加上一个时间间隔的时间值 |
| datediff(expr,expr2) | 返回起始时间 expr 和结束时间 expr2 之前的天数 |

#### MySQL中的日期和时间格式

| 格式符 | 格式说明 |
| ------------ | :------------ |
| %S 和 %s | 两位数字形式的秒（00,01,...,59） |
| %i | 两位数字形式的分（00,01,...,59） |
| %H | 两位数字形式的小时，24小时（00,01,...,23） |
| %h 和 %I | 两位数字形式的小时，12小时（00,01,...,12） |
| %k | 数字形式的小时，24小时（0,1,...,23） |
| %1 | 数字形式的小时，12小时（1,2,...,12） |
| %T | 24小时的时间形式（hh : mm : ss） |
| %r | 12小时的时间形式（hh : mm : ss AM或hh : mm : ss PM） |
| %p | AM或PM |
| %W | 一周中每一天的名称（Sunday,Monday,...,Saturday） |
| %a | 一周中每一天名称的缩写（Sun,Mon,...,Sat） |
| %d | 两位数字表示月中的天数（00,01,...,31） |
| %e | 数字形式表示月中的天数（1,2,...,31） |
| %D | 英文后缀表示月中的天数（1st,2dn,3rd,...） |
| %w | 以数字形式表示周中的天数（0=Sunday,1=Monday,...,6=Saturday） |
| %j | 以3位数字表示年中的天数（001,002,...,366） |
| %U | 周（0,1,52）,其中Sunday为周中的第一天 |
| %u | 周（0,1,52）,其中Monday为周中的第一天 |
| %M | 月名（January,February,...,December） |
| %b | 缩写的月名（January,February,...,December） |
| %m | 两位数字表示的月份（01,02,...,12） |
| %c | 数字表示的月份（1,2,...,12） |
| %Y | 4位数字表示的年份 |
| %y | 两位数字表示的年份 |
| %% | 直接值“%” |

#### 流程函数

| 函数 | 功能 |
| ------------ | :------------ |
| IF(value,t f) | 如果value是真，返回t；否则返回f |
| IFNULL(value1，value2) | 如果value1不为空，返回value1；否则返回value2 |
| CASE WHEN[value1] THEN[result1]... ELSE[default]END | 如果value1是真，返回result1；否则返回default |
| CASE [expr] WHEN [value1] THEN [result1] ... ELSE[default]END | 如果expr等于value1，返回result；否则返回default |

#### 其他常用函数

| 函数 | 功能 |
| ------------ | :------------ |
| datebase() | 返回当前数据库名 |
| version() | 返回当前数据库版本 |
| user() | 返回当前登录用户名 |
| inet_aton(IP) | 返回IP地址的数字表示 |
| inet_ntoa(num) | 返回数字代表的IP地址 |
| password(str) | 返回字符串str的加密版本 |
| MD5() | 返回字符串str的MD5值 |

