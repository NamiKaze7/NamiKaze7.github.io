# Hive sql

[TOC]

### JAVA UDF编写与应用

首先编写 java udf：

```java
import java.util.HashSet;

import org.apache.hadoop.hive.ql.exec.UDF;
import org.apache.hadoop.io.Text;

public class UniqueCharRatioUDF extends UDF {

    public Double evaluate(Text str) {
        if (str == null) {
            return null;
        }

        String s = str.toString();
        if (s.isEmpty()) {
            return 0.0;
        }

        HashSet<Character> set = new HashSet<Character>();
        for (char c : s.toCharArray()) {
            set.add(c);
        }

        return (double) set.size() / (double) s.length();
    }
}
```

将其上传至仓库：ssh://git@git.sankuai.com/~zhanghaozhou/mthdp-sample.git

在万象中上传![image-20230302192808222](/Users/zhz/Library/Application Support/typora-user-images/image-20230302192808222.png)

使用：

```hive
ADD jar viewfs:///user/hadoop-udf-hub/etl-grocerygoodsalgo_review_process/grocerygoodsalgo_review_process-online.jar;
CREATE TEMPORARY FUNCTION UniqueCharRatioUDF AS 'UniqueCharRatioUDF';
CREATE TEMPORARY FUNCTION UDFCleanReviw AS 'com.sankuai.meituan.hive.udf.UDFCleanReviw';
```

### 拆分explode

 https://zhuanlan.zhihu.com/p/115918587右表为key，hive中in的用法

```sql
select 
    /*+ MAPJOIN(sch) */ 
    t.review_id,
    t.base_sku_id,
    t.base_sku_name,
    t.review_body
  from mart_grocery.topic_op_service_comment t left semi
  join (
        SELECT DISTINCT base_sku_id
          from mart_grocery.fact_pdt_sku_schedule_snapshot
         WHERE dt='{0}'
           and schedule_date>'{1}'
       )sch
    on t.base_sku_id=sch.base_sku_id
    where t.category1_name='生鲜'
   and t.tag_id_cnt IS NULL
   AND t.review_body_length>3
   AND t.star_name='好评'
```

### row_number()排序

row_number() over(partition by company order by vst_times desc) 

```sql
select base_sku_id,
       review_body,
       sentiment_score
  from (
        select base_sku_id,
               review_body,
               sentiment_score,
               row_number() over(partition by base_sku_id order by sentiment_score desc)as rn
          from temp
       ) as t
 where t.rn <= 5
```

### 拼接与转换

CONCAT_WS 多个字端拼接

collect_set, collect_list 列转行

```sql
select username, collect_set(video_name) from t_visit_video group by username;
```

![image](https://images2018.cnblogs.com/blog/784924/201805/784924-20180516011140823-794187986.png)

### rollup、cube、grouping

GROUP BY语句除了最基本的语法外，还支持ROLLUP和CUBE语句。

#### rollup

如果是GROUP BY ROLLUP(A, B, C)的话，首先会对(A、B、C)进行GROUP BY，然后对(A、B)进行GROUP BY，然后是(A)进行GROUP BY，最后对全表进行GROUP BY操作。

```sql
select grouping(category2_id),grouping(poi_id),category2_id,poi_id,count(*)
  from nrt_xiaoxiang.app_sc_wis_reserve_process_dt_dc
 where dt=20200901
 group by
   rollup(category2_id,poi_id)
```

结果：

![image-20230129195410638](/Users/zhz/Library/Application Support/typora-user-images/image-20230129195410638.png)

分析：首先（category2_id,poi_id）进行group by操作生成8条数据，然后对category2_id进行group by操作生成2条数据，最后扫描全表

#### grouping

如果是GROUP BY CUBE(A, B, C)，则首先会对(A、B、C)进行GROUP BY，然后依次是(A、B)，(A、C)，(A)，(B、C)，(B)，(C)，最后对全表进行GROUP BY操作。 grouping_id()可以美化效果。

```sql
select grouping(category2_id),grouping(poi_id),category2_id,poi_id,count(*)
  from nrt_xiaoxiang.app_sc_wis_reserve_process_dt_dc
 where dt=20200901
 group by
   cube(category2_id,poi_id)
```

![img](/Users/zhz/Library/Application Support/typora-user-images/image-20230129195302805.png)

分析：首先（category2_id,poi_id）进行group by操作生成8条数据，然后对category2_id进行group by操作生成2条数据，对poi_id进行group by操作生成4数据，最后扫描全表
