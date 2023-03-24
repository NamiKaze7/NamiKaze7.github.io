## spark 建表

[TOC]

### spark hive

#### 建立临时表

```python
spark_df = spark.createDataFrame(final_df)
spark_df.createOrReplaceTempView("df_tmp_view")
```

#### creat

```sql
%%sql r --preview --quiet
create table IF NOT EXISTS mart_grocerygoodsalgo.topic_op_service_comment_test
(
    base_sku_id bigint comment 'sku id',
    version double comment '版本名',
    type string comment '数据类型',
    content ARRAY<map<string, string>> comment 'top5精选长评及分数'
)comment '45天内有销量精选长文本评论表'
partitioned by (dt string comment '日期分区字段，格式为datekey(yyyymmdd)')  STORED AS ORC
```

#### insert

```sql
%%sql r --preview --quiet
insert into table mart_grocerygoodsalgo.topic_op_service_comment_test partition(dt=yesterday)
   select 
   base_sku_id,
   version,
   type,
   content
   from df_tmp_view
```

#### drop

```sql
%%sql r --preview --quiet
drop table if exists mart_grocerygoodsalgo.topic_op_service_longcomment_fresh

%%sql r --quiet --preview
alter table mart_grocerygoodsalgo.topic_op_service_longcomment_extract drop partition(dt='2022-11-17')
```

### rdd

Spark rdd 操作，对每一行作用返回空、一行或多行

```python
from pyspark.sql.functions import col,lit
r = spark.sql("""
    select category1_name,
	   category2_name,
       category3_name,
       category4_name,
       category5_name,
       category4_id,
       category5_id,
       review_id,
       base_sku_id,
       base_sku_name,
       review_body
  from mart_grocery.topic_op_service_comment
 where category1_name='生鲜'
   AND tag_id_cnt IS NULL
   AND review_body_length>3
   AND star_name='好评'
   AND dt='20230103'
""")

# func 作用一行，数据按索引取，返回多行函数返回一个包含多个row的list
def func(row):
    l = []
    review = row[-1]
    review_list = cut_sentence(review)
    header = list(row[:-1])
    for r in review_list:
        h = header.copy()
        h.append(r)
        l.append(h)
    return l
  
r3 = r.rdd.flatMap(lambda x: func(x))
l = r.columns
l.extend(['sentiment_score', 'recall_label'])
df3 = r3.toDF(l)
df4 = df3.withColumn('version', lit(2.0))
df4.createOrReplaceTempView("df_tmp_view")
```

