# MyBatis批量更新数据三种方法效率对比

实现的方式有三种：

- 用for循环通过循环传过来的参数集合，循环出N条SQL
- 用mysql的case when条件判断变相的进行批量更新
- 用ON DUPLICATE KEY UPDATE进行批量更新

第一种方法想要成功，需要在db链接url后面带一个参数 <a style="color:red"> **&allowMultiQueries=true**</a>

例如:

```java
 jdbc:mysql://localhost:3306/mysqlTest?characterEncoding=utf-8&allowMultiQueries=true
```

## 三种方法实例：

```xml
 
    <!-- 批量更新第一种方法，通过接收传进来的参数list进行循环着组装sql -->
     <update id="updateBatch" parameterType="java.util.List" >
        <foreach collection="list" item="item" index="index" open="" close="" separator=";">
            update standard_relation
            <set >
                <if test="item.standardFromUuid != null" >
                    standard_from_uuid = #{item.standardFromUuid,jdbcType=VARCHAR},
                </if>
                <if test="item.standardToUuid != null" >
                    standard_to_uuid = #{item.standardToUuid,jdbcType=VARCHAR},
                </if>
                <if test="item.gmtModified != null" >
                    gmt_modified = #{item.gmtModified,jdbcType=TIMESTAMP},
                </if>
            </set>
            where id = #{item.id,jdbcType=BIGINT}
        </foreach>
    </update>
 
    <!-- 批量更新第二种方法，通过 case when语句变相的进行批量更新 -->
    <update id="updateBatch" parameterType="java.util.List" >
        update standard_relation
        <trim prefix="set" suffixOverrides=",">
            <trim prefix="standard_from_uuid =case" suffix="end,">
                <foreach collection="list" item="i" index="index">
                    <if test="i.standardFromUuid!=null">
                        when id=#{i.id} then #{i.standardFromUuid}
                    </if>
                </foreach>
            </trim>
            <trim prefix="standard_to_uuid =case" suffix="end,">
                <foreach collection="list" item="i" index="index">
                    <if test="i.standardToUuid!=null">
                        when id=#{i.id} then #{i.standardToUuid}
                    </if>
                </foreach>
            </trim>
            <trim prefix="gmt_modified =case" suffix="end,">
                <foreach collection="list" item="i" index="index">
                    <if test="i.gmtModified!=null">
                        when id=#{i.id} then #{i.gmtModified}
                    </if>
                </foreach>
            </trim>
        </trim>
        where
        <foreach collection="list" separator="or" item="i" index="index" >
            id=#{i.id}
        </foreach>
    </update>
批量更新第三种方法，用ON DUPLICATE KEY UPDATE
 <insert id="updateBatch" parameterType="java.util.List">
        insert into standard_relation(id,relation_type, standard_from_uuid,
        standard_to_uuid, relation_score, stat,
        last_process_id, is_deleted, gmt_created,
        gmt_modified,relation_desc)VALUES
        <foreach collection="list" item="item" index="index" separator=",">
            (#{item.id,jdbcType=BIGINT},#{item.relationType,jdbcType=VARCHAR}, #{item.standardFromUuid,jdbcType=VARCHAR},
            #{item.standardToUuid,jdbcType=VARCHAR}, #{item.relationScore,jdbcType=DECIMAL}, #{item.stat,jdbcType=TINYINT},
            #{item.lastProcessId,jdbcType=BIGINT}, #{item.isDeleted,jdbcType=TINYINT}, #{item.gmtCreated,jdbcType=TIMESTAMP},
            #{item.gmtModified,jdbcType=TIMESTAMP},#{item.relationDesc,jdbcType=VARCHAR})
        </foreach>
        ON DUPLICATE KEY UPDATE
        id=VALUES(id),relation_type = VALUES(relation_type),standard_from_uuid = VALUES(standard_from_uuid),standard_to_uuid = VALUES(standard_to_uuid),
        relation_score = VALUES(relation_score),stat = VALUES(stat),last_process_id = VALUES(last_process_id),
        is_deleted = VALUES(is_deleted),gmt_created = VALUES(gmt_created),
        gmt_modified = VALUES(gmt_modified),relation_desc = VALUES(relation_desc)
 </insert>
```

```java
     @Override
        public void updateStandardRelations() {
            List<StandardRelation> list=standardRelationMapper.selectByStandardUuid("xiemingjieupdate");
            for(StandardRelation tmp:list){
                tmp.setStandardFromUuid(tmp.getStandardFromUuid()+"update");
                tmp.setStandardToUuid(tmp.getStandardToUuid()+"update");
            }
            long begin=System.currentTimeMillis();
            standardRelationManager.updateBatch(list);
            long end=System.currentTimeMillis();
            System.out.print("当前的批量更新的方法用时"+(end-begin)+"ms");
        }
```

测试情况如图所示：

![MY1](img\MY1.png)

sql语句for循环效率其实相当高的，因为它仅仅有一个循环体，只不过最后update语句比较多，<a style="color:red">量大了就有可能造成sql阻塞</a>

case when虽然最后只会有一条更新语句，但是xml中的循环体有点多，每一个case when 都要循环一遍list集合，所以<a style="color:red">大批量拼sql的时候会比较慢,所以效率问题严重。使用的时候建议分批插入</a>

duplicate key update可以看出来是最快的，但是一般大公司都禁用，**公司一般都禁止使用replace into和INSERT INTO … ON DUPLICATE KEY UPDATE，这种sql有可能会造成数据丢失和主从上表的自增id值不一致。**而且用这个更新时，记得一定要加上id，而且values（）括号里面放的是数据库字段，不是java对象的属性字段

![MY2](img\MY2.png)

根据效率，安全方面综合考虑，选择适合的解决方案

## 经验之谈：

在使用的时候，个人推荐第二种解决方案，因为第一种方案的话，跟在java代码在循环中调用sql方法一样，我们本着尽量不要在循环中写sql的原则，选择第二种sql拼接批量更新和插入比较好，看文章上面提到的耗时作为参考的话，一次5000条的效率case when比for循环要好，如果数据量比较大，我们可以拆分成每5000条拼接一次，效率就会提升很多

**拆分List可以利用一个工具类：**

```java
import java.util.AbstractList;
import java.util.List;

/**
 * List分组工具类
 */
public class ListUtils {

    public static <T> List<List<T>> partition(final List<T> list, final int size) {
        if (list == null) {
            throw new NullPointerException("List must not be null");
        }
        if (size <= 0) {
            throw new IllegalArgumentException("Size must be greater than 0");
        }
        return new Partition<>(list, size);
    }

    private static class Partition<T> extends AbstractList<List<T>> {
        private final List<T> list;
        private final int size;

        private Partition(final List<T> list, final int size) {
            this.list = list;
            this.size = size;
        }

        @Override
        public List<T> get(final int index) {
            final int listSize = size();
            if (index < 0) {
                throw new IndexOutOfBoundsException("Index " + index + " must not be negative");
            }
            if (index >= listSize) {
                throw new IndexOutOfBoundsException("Index " + index + " must be less than size " +
                        listSize);
            }
            final int start = index * size;
            final int end = Math.min(start + size, list.size());
            return list.subList(start, end);
        }

        @Override
        public int size() {
            return (int) Math.ceil((double) list.size() / (double) size);
        }

        @Override
        public boolean isEmpty() {
            return list.isEmpty();
        }
    }
}
```

拆分list    partition的参数：（list，每拆分出的一个list的最大size）

```java
//暂定每1000条一批更新
List<List<T>> splitList = ListUtils.partition(list,1000);
  for(List<T> spl:splitList){
       //这里批量更新  List：spl
  }  
}
```

并且在对数据库做更新操作要考虑到是否并发的问题，如果有并发问题，要尝试使用锁

[参考文章地址](https://blog.csdn.net/q957967519/article/details/88669552)

