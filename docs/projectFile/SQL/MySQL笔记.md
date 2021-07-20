### 1. MySQL 批量更新

mysql的批量更新有如下四种办法

1、replace into 批量更新

```mysql
replace into test_tbl (id,dr) values (1,'2'),(2,'3'),...(x,'y');
```

2、insert into ...on duplicate key update批量更新

```mysql
insert into test_tbl (id,dr) values (1,'2'),(2,'3'),...(x,'y') on duplicate key update dr=values(dr);
```

3.创建临时表，先更新临时表，然后从临时表中update

```mysql
create temporary table tmp(id int(4) primary key,dr varchar(50));
insert into tmp values (0,'gone'), (1,'xx'),...(m,'yy');
update test_tbl, tmp set test_tbl.dr=tmp.dr where test_tbl.id=tmp.id;
```

注意：这种方法需要用户有temporary 表的create 权限。

参考文档[https://cloud.tencent.com/developer/article/1117183](https://cloud.tencent.com/developer/article/1117183)

4、使用mysql 自带的语句构建批量更新

mysql 实现批量 可以用点小技巧来实现:

```mysql
UPDATE yoiurtable
 SET dingdan = CASE id 
   WHEN 1 THEN 3 
   WHEN 2 THEN 4 
   WHEN 3 THEN 5 
  END
WHERE id IN (1,2,3)
```

这句sql 的意思是，更新dingdan 字段，如果id=1 则dingdan 的值为3，如果id=2 则dingdan 的值为4……
where部分不影响代码的执行，但是会提高sql执行的效率。确保sql语句仅执行需要修改的行数，这里只有3条数据进行更新，而where子句确保只有3行数据执行。

如果更新多个值的话，只需要稍加修改：

```mysql
UPDATE categories 
  SET dingdan = CASE id 
    WHEN 1 THEN 3 
    WHEN 2 THEN 4 
    WHEN 3 THEN 5 
  END, 
  title = CASE id 
    WHEN 1 THEN 'New Title 1'
    WHEN 2 THEN 'New Title 2'
    WHEN 3 THEN 'New Title 3'
   END
WHERE id IN (1,2,3)
```

到这里，已经完成一条mysql语句更新多条记录了