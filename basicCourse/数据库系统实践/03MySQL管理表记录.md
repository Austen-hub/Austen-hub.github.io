# 数据库系统实践
## 03MySQL管理表记录
#### 1.MySQL基本数据类型
###### 整数类型
- 表中每个字段都有数据类型
    + 存储格式：整型、浮点型、字符串......
    + 约束：设计外键相关内容
    + 有效范围：整型（4字节）、定长字符串......
    + ......
- 合适的数据类型，可以
    + 节省空间，例如：
        * 存储时间：整型（4字节）vs.日期型（8字节）
    + 提升新能：例如：
        * 用户ID：整型 vs.字符串
- MySQL提供多种数据类型
    + 数值类型：整数、浮点数......
    + 字符串类型：定长字符串、变长字符串......
    + 日期时间类型：unix时间、一般日期......
    + 复合类型：枚举enum、集合set......
    + 二进制类型：二进制binary......
- 整数类型的字节数及取值范围
    + 有符号整数
        * 可正可负
    + 无符号数
        * 指定unsigned属性
        * 例如：年龄不会有负数
    + 指定宽度
        * 显示时，不足位数填充
        * 类似C语言：printf("%10d",x);
        * int(8):数值宽度小于8位时，前面填满宽度
    + 零填充
        * 位数不够用0填充
        * 指定zerofill属性
        * 类似C语言：printf("%010d",x);
- 属性：**auto_increment(AI)**
    + 自增
    + 只适用整型
    + 需产生唯一标识符或顺序值时，可以利用
    + 一个表只能有1个AI字段，且:
        * not null
        * primary key 或者 unique
    + 默认：
        * 从1开始递增
        * 插入null值或不设置该字段值
            - 则从当前偏移（offset）递增一个单位步长1（increment）
    + 可另外设置
        * 当前偏移  
        set @@auto_increment_offset=1234;  
        * 单位步长  
        set @@auto_increment_increment=2;

| 类型 | 字节数 | 有符号数范围 | 无符号数范围 |
| ---- | ---- | ---- | ---- |
| tinyint | 1 | -2<sup>7</sup>~2<sup>7</sup>-1 | 0~2<sup>8</sup>-1 |
| smallint | 2 | -2<sup>15</sup>~2<sup>15</sup>-1 | 0~2<sup>16</sup>-1 |
| mediumint | 3 | -2<sup>23</sup>~2<sup>23</sup>-1 | 0~2<sup>24</sup>-1 |
| int/integer | 4 | -2<sup>31</sup>~2<sup>31</sup>-1 | 0~2<sup>32</sup>-1 |
| bigint | 8 | -2<sup>63</sup>~2<sup>63</sup>-1 | 0~2<sup>64</sup>-1 |

###### 小数类型
- 2种类型
    + 浮点数
        * 单精度float
        * 双精度double
    + 定点数decimal（字符串形式存储，精度更高）
- 可指定格式（M，D）
    + M-总长度
    + D-小数部分长度，四舍五入
- 不指定格式时
    + 浮点数：按实际值存储
    + 定点数：（M，D）=（10，0）
###### 字符串类型
- 字符串类型
    + char(n) n=0~255
    + varchar(n) n=0~65535
    + tinytext
    + text
    + mediumtest
    + longtest
- 注：
    + char(n) -添加时删除尾部空白符
    + 其他     - 不删除
- 字符串类型及存储长度

| 类型 | 长度值字节数 | 有效长度（字节） |
| ---- | ---- | ---- |
| char(n) | 1 | 0~2<sup>8</sup>-1 |
| varchar(n) | 2 | 0~2<sup>16</sup>-1 |
| tinytext | 1 | 0~2<sup>8</sup>-1 |
| text | 2 | 0~2<sup>16</sup>-1 |
| mediumtext | 3 | 0~2<sup>8</sup>-1 |
| longtext | 4 | 0~2<sup>8</sup>-1 |

###### 日期时间类型
- date          -默认格式YYYY-MM-DD
- time          -默认格式HH：MM：SS
- datetime      -默认格式YYYY-MM-DD HH：MM：SS
- timestamp     -默认格式YYYY-MM-DD HH：MM：SS
- year          -年份
    + 4位表示（默认）
        * 1901~2155
    + 2位表示
        * 70~99表示1970~1999
        * 00~69表示2000~2069
    + 1位表示
        * 0（整数）表示0000
        * 0（字符）表示2000
        * 1~9等同01~09表示2001~2009
- **datetime vs. timestamp**
    + 都包含：日期+时间
    + datetime时区无关
    + timestamp时区相关
- 时区
    + 查看当前时区  
    show variables like 'time_zone';
    + 修改为东10时区  
    set time_zone = '+10:00';
- 若表中2个timestamp字段，则
    + 第一个默认值：CURRENT_TIMESTAMP
    + 第二个默认值：0000-00-00 00：00：00

| 类型 | 字节数 | 最小值 | 最大值 |
| ---- | ---- | ---- | ---- |
| date | 3 | 1000-01-01 | 9999-12-31 |
| time | 3+ | -838:59:59.000000 | 838:59:59.000000 |
| datetime | 5+ | 1000-01-01 00:00:00 | 9999-12-31 23:59:59 |
| timestamp | 4+ | 1970-01-01 00:00:01 | 2038-01-19 03:14:07 |
| year | 1 | 1901 | 2155 |

- 注：字节数“+”：在秒后面可以接6位小数（参考time字段）

###### 复合类型
- enum枚举类型
    + 从集合中取1个值
    + 集合元素最多为2<sup>16</sup>-1=65535个
- set集合类型
    + 从集合中取多个值
    + 集合元素最多为2<sup>6</sup>=64个
###### 二进制类型
- binary
- varbinary
- bit
    + 按位存储；其他按字节存储
- tinyblob, blob, mediumblob, longblob
    + BLOB:Binary Large data OBject

| 类型 | 长度值字节数 | 存储长度 |
| ---- | ---- | ---- |
| binary(n) | 1 | 0~2<sup>8</sup>-1字节 |
| varbinary(n) | 2 | 0~2<sup>16</sup>-1字节 |
| bit(n) | 6 | 1~2<sup>6</sup>位 |
| tinyblob | 1 | 0~2<sup>8</sup>-1字节 |
| blob | 2 | 0~2<sup>16</sup>-1字节 |
| medium | 3 | 0~2<sup>24</sup>-1字节 |
| longblob | 4 | 0~2<sup>32</sup>-1字节 |

- 注：
    + 除了bit类型，其他与字符串类型相对应
    + 即:char, varchar, tinytext, text, mediumtext, longtext
#### 2.MySQL运算符
- 运算符（主要）
    + 算术运算符
        * +
        * -
        * *
        * /
        * %
    + 比较运算符
        * 比较左右两边的操作数
        * 结果为真，返回1
        * 结果为假，返回0
        * 结果不确定，返回null  
        
        | 运算符 | 说明 |
        | ---- | ---- |
        | = | 等于 |
        | !=或<> | 不等于 |
        | **<=>** | **null安全的等于(若等式两边均为NULL，返回true)** |
        | < | 小于 |
        | <= | 小于等于 |
        | > | 大于 |
        | >= | 大于等于 |
        | is null | 为null |
        | is not null | 不为null |
        | between and | 在指定范围内 |
        | in | 在指定结合内 |
        | like | 通配符匹配 |
        | regexp | 正则表达式匹配 |

        ```sql
        select
            int_field=null,
            int_field<>null, 
            int_field<=>null -- 若等式两边均为NULL，返回true
            int_field between 10 and 20,
            int_field in(10,17,20),
            int_field is null,
            varchar_field like '%Li', -- % 表示所有字符
            varchar_field regexp '^Mr', -- ^表示行首
            varchar_field regexp 'Li$' -- $表示行尾
        from comparison_test;
        ```
    + 逻辑运算符
        * 逻辑与
            - and 或 &&
            - null运算结果均为null
        * 逻辑或
            - or 或 ||
            - null or true 结果是true
            - 其他null运算结果均为null
        * 逻辑非
            - not 或 ！
            - null运算结果均为null
        * 逻辑异或
            - xor
            - null运算结果均为null
    + 位运算符
        * 对每个二进制位操作
        * 对操作数的补码操作
        * 包括
            - 位逻辑运算
                + 按位与（&）
                + 按位或（|）
                + 按位取反（~）
                + 按位异或（^）
            - 移位运算
                + 左移（<<）：左侧移出的丢弃，右侧添0补位
                + 右移（>>）：左侧添0补位，右侧移出的丢弃
- 运算符优先级   

| 优先级 | 运算 | 优先级 | 运算 |
| ---- | ---- | ---- | ---- |
| 1 | ! | 8 | | |
| 2 | ~,-(负号) | 9 | =,<=>,<,<=,>,>=,!=,<>,is,in,like,regexp |
| 3 | ^ | 10 | between and, case when then else |
| 4 | *,/,div,%,mod | 11 | not |
| 5 | +,- | 12 | &&,and |
| 6 | >>,<< | 13 | ||,or,xor |
| 7 | & | 14 | := |

#### 3.字符集设置
- 默认字符集
    + ISO_8859_1
        * 别名：latin1
        * 单字节编码
    + 汉字是双字节编码
        * 导致不支持中文字符
        * 解决：改默认字符集
- MySQL提供多种字符集
    + latin1,utf8,gbk,big5,......
- 排序规则（collation）
    + 值：字符之间的比较规则
    + 同一字符集可包含多种排序规则
    + 每个字符集有一个默认排序规则
- 排序规则命名
    + 开头：字符集
    + 中间：国家名（或general）
    + 结尾：ci、cs或bin
        * ci:大小写不敏感
        * cs:大小写敏感
        * bin:按二进制编码比较
    +  例如：utf8_general_ci
-  字符集和排序规则信息
    +  查看支持的字符编码、默认排序规则、字符长度   
    show character set;   
    + 当前使用的字符编码信息   
    show variables like 'character%';   
    + 当前使用的排序规则   
    show variables like 'collation%';  
    + 参数说明
        * character_set_client
            - 客户机的字符集，默认latin1
        * character_set_connection
            - 数据通信链路的字符集，默认latin1
            - 客户机向服务器发送请求时用它编码
        * character_set_database
            - 数据库字符集，默认latin1
        * character_set_filesystem
            - 服务器文件系统字符集，固定binary
        * character_set_results
            - 结果集的字符集，默认latin1
            - 服务器向客户机返回结果时用它编码
        * character_set_server
            - 服务实例字符集，默认latin1
        * character_set_system
            - 元数据（字段名、表名、数据库名等）的字符集，默认utf8
- MySQL启动后字符集参数：来自my.ini的[mysqld]
    + character_set_server      < character_set_server
    + character_set_database    < character_set_server
    + character_set_client      < default_character_set
    + character_set_connection  < default_character_set
    + character_set_results     < default_character_set
- 修改字符集的方法
    + 修改my.ini配置文件
        * default_character_set
        * character_set_server
    + 使用set命令
        * 例如：set character_set_database=utf8
        * 只在当前连接内有效
    + 使用set names命令设置字符集
        * 例如：set names utf8
        * 决定
            - character_set_client,character_set_connection,character_set_results
        * 旨在当前连接内有效
    + 连接服务器时指定
        * mysql -h 127.0.0.1 -u root -p --default-character-set=utf8
        * 相当于：执行set names=utf8
#### 4.增添表记录
- insert 
``` sql
insert [into] table_name [(column_name,...)]  
    values({expr|DEFAULT},...),(...),...  
    | set column_name={expr|DEFAULT},...
```
    + 若省略字段名：插入全部字段数据
    + 若指定字段名：插入指定字段数据
        * 未指定字段：默认值，规则为：
            - 有默认值的字段：默认值
            - 无默认值的字段：若允许null，则null；否则出错
            - 自增型auto_increment字段：自动生成下一个编号
            - time stamp类型的字段：当前系统日期和时间
    + 添加来自其他表的记录   
    insert [into] table_name[(column_name,...)]  
        select (column_name,...)  
        from source_table_name  
        where conditions;
- replace
    + 和insert语句基本相同
    + 不同之处：
        * 新记录主键值或者唯一性约束的字段值已存在时，将取代旧记录
#### 5.修改表记录
```sql
update table_name 
    set column_name={expr|DEFAULT},...
    [where condition]
```
#### 6.删除表记录
- delete:根据条件删除记录。
```sql
delete from table_name
[where condition]
```
    + 若没有where子句，则删除全部
    + 删除有外键约束的记录之前，先删除约束相关记录
        * 例如：教师表与课程表c有外键约束，要删除教师表编号为1的记录，则
            - 先删除：课程表中教师编号为1的课程
            - 再删除：教师表中教师编号为1的老师
- truncate:无条件删除全部记录
```sql
truncate [table] table_name
```
    + 功能等同于  
    delete from table_name
    + 区别
        * truncate：会**重置auto_increment（自增）起始值**
        * delete：不会重置
#### 7.案例：图书管理系统中表记录的操作


