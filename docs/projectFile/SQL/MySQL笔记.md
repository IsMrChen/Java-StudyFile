## 1. MySQL 批量更新

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

## 2.mysql表为什么要加version字段

有些时候我们在参与团队中的项目或者阅读他人项目的时候，可能会发现mysql的一些表在设计的时候有一个**version**字段

这类字段并不是出于业务角度出发的必要字段，那么我们不禁会问这样一个字段设置有什么作用呢？

这里我们不得不引入两个概念：悲观锁和乐观锁。

**悲观锁：**在读数据的时候就悲观的认为我的这些数据会被修改。所以在读的时候就给数据上锁，直到整个事务结束再解锁。实现方式一般是mysql本身提供的锁机制。

**乐观锁：**相对于悲观锁而言，只有更新数据的时候才会认为数据可能被修改。一般是再更新的时候做数据正确性判断。实现方式一般是mysql程序设计角度考虑。

给mysql表添加**version**字段就是常见的**乐观锁**。从程序角度解决mysql的数据冲突问题。

具体是怎么实现：

1.表中加一个版本字段，相当于对表中的每一行数据做了一个版本标记。

2.正常读取数据，记录下当前数据的版本号。

3.更新数据的时候，版本号+1，并且对比此前记录的版本号和当前版本号是否一致作为更新条件。

