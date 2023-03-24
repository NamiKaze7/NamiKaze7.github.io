## code

```python
def read_sql_file(sql_conf):
    sql = ""
    for line in open(sql_conf).readlines():
        sql += line.replace("\n", " ")
    return sql
```

 spark 取数&建表

```python
spark_conf = SparkConf()
spark = SparkSession.builder.config(conf=spark_conf).appName('extract_longcomment')
.enableHiveSupport().getOrCreate()

sentiment_df = spark.sql(senti_sql)
spark_df = spark.createDataFrame(final_df)
spark_df.createOrReplaceTempView("df_tmp_view")

spark.sql(create_table_sql)
spark.sql(insert_sql)
spark.stop()
```

sql

```sql
create table IF NOT EXISTS mart_grocerygoodsalgo.topic_op_service_longcomment_extract
(
    base_sku_id bigint comment 'sku id',
    version double comment '版本名',
    type string comment '数据类型',
    content ARRAY<map<string, string>> comment 'top5精选长评及分数'
)comment '45天内有销量精选长文本评论表'
partitioned by (dt string comment '日期分区字段，格式为datekey(yyyymmdd)')  STORED AS ORC
```

```sql
insert overwrite table mart_grocerygoodsalgo.longcomment_extract_fresh_buffer partition(dt='{0}')
   select
   review_id,
   base_sku_id,
   base_sku_name,
   review_body,
   sentiment_score
   from df_tmp_view
```

## 托管平台使用

```sh
--archives viewfs:///user/hadoop-grocerygoodsalgo/zhanghaozhou/py36_envs.zip
--conf spark.yarn.appMasterEnv.PYSPARK_PYTHON=./py36_envs.zip/kaze/bin/python
--files com2hive.py
--py-files extract_hive.py, string_utils.py, split_tools.py
--files create_table_final.sql, insert_final.sql, buff_data.sql, sku.sql
```

