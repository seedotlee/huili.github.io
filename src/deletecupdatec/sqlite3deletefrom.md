# sqlite3DeleteFrom
程序段是为DELETE FROM语句生成代码
<pre>
void sqlite3DeleteFrom(
Parse *pParse,         /* 解析指针 */
SrcList *pTabList,     /* 表中指向要删去内容的指针 */
Expr *pWhere           /* WHERE 语句指针.  可以为空 */
)
</pre>
此函数有如下几部分组成：<pre>
1）找到我们想要删除的表。该表必须被放在一个SrcList结构，因为一些为多个表服务的子程序将被调用并且用Srclist参数来代替Table参数。
2）弄清楚是否有触发器，是否删除的这个表是一个视图。
3）如果pTab所指的是一个视图，确保它已经初始化。
4）为表分配游标号和所有的索引。
5）开始解析上下文。
6）开始生成代码。
7）如果我们尝试去删除一个视图，该视图必须在一个临时表中。
8）在WHERE子句中解决列名。
9）如果要统计行数，初始化要删除行的编号的计数器。
10）特殊情况:DELETE语句中没有WHERE子句将删除所有的行，这样很容易删除整个表，在之前的3.6.5版本，这个优化导致行被改变的计数器(由函数sqlite3_count_changes返回的值)。
11）通常情况下有一个WHERE子句使得我们通过扫描表并挑选其中的记录去删除。
12）收集要删除行的rowid。
13）在数据库扫描的过程中删除主键写入到列表中的每一个项目，在扫描完成后删除这些项目，因为删除的项目可以更改扫描的顺序。
14）除非这是一个视图，打开要删除表的游标和它的所有索引。如果是一个视图，唯一的影响就是要解除INSTEAD OF触发器。
15）删除行。
16）结束DELETE循环。
17）关闭表上打开的游标和它所有的索引。
18）通过存储在插入到自动增量的表记录到的最大的rowid计数器值的内容更新sqlite_sequence表。
19）返回被删除行的编号，如果这个过程正在生成代码是因为调用了函数sqlite3NestedParse(),没有调用回滚函数。</pre>
