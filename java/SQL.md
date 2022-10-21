### SQL查询

1，数据去重：distinct

2，根据集合来查询：

​	in(a,b,c,,,)：查询出来的结果符合该集合，返回true。

​	not in(a,b,c,,,)：查询出来的结果符合该结果，返回false。

3，过滤值不为空的结果

​	通过is not null。

4，模糊查询

5，保留小数位数：round（a，b）,a表示数值，表示范围。

6，having：将分组后的结果根据条件进行筛选。

7，排序，order by  ”字段“ desc（asc）



**嵌套查询**：查询语句中嵌套其他查询语句

```sql
SELECT * from user WHERE id IN(SELECT id FROM msg) 在where中嵌套查询语句

```

