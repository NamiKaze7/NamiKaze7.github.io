```sh
%%spark
--conf spark.yarn.queue=root.zw03.hadoop-grocerygoodsalgo.etl
--conf spark.network.timeout=6000
--conf spark.sql.shuffle.partitions=100
--conf hive.exec.dynamic.partition=true
--conf spark.executor.heartbeatInterval=30
--conf spark.driver.memory=8g
```

```sql
%%sql mydf --preview --quiet
select  distinct a.base_sku_id,a.base_sku_name,b.pic_url,b.pic_type
from
(
  select  base_sku_id,
 base_sku_name
  from  mart_grocery.dim_pdt_sku_snapshot
where dt = 20220608
  and category1_name != '生鲜'
) a
join
(
  select  base_sku_id,
  pic_url,
  pic_type
  from mart_grocery.dim_op_pdt_sku_picture_snapshot
  where dt = 20220608
) b
on a.base_sku_id = b.base_sku_id
where pic_type in (9, 11, 12)

mydf=mydf.toPandas()
```

```shell
#mt-jupyter
hadoop fs -get  viewfs://hadoop-meituan/user/hadoop-grocerygoodsalgo/zhanghaozhou/生鲜好评20221017.txt
#docker
hadoop fs -ls viewfs://hadoop-meituan/mnt/dolphinfs/hdd_pool/docker/user/hadoop-grocerygoodsalgo/zhanghaozhou/projects/long_comment/expriement/raw_file/
```

### git key 设置

```python
# 替换为自己的ssh secret key， 通过cat /home/sankuai/.ssh/id_rsa 查看
id_rsa = """
-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEA2ls5o/jnoqMlsfpfqUC4q8UoCbyVOrZUFZaGG1wkEXCO7ePL
Vag6pKZT1P1B9i/Yi3GHd4Nt7L2C0ivuFcaBpiiQXbbkUT47PeNhP3vda8dE3RJ6
SUfjfOetxcM2iHlMw/exAqpV4eqRDF6PMgJCY+9R5D2abfCK8b6oLWXznrDcEEav
JkgSnmwR6iRn+rJvI9Ql8NyH4doQFCzkhgrWJ9nNPj/nPueDnAAWC4PqKJGIHBbM
aBSnWWM1LRQh80zTtt5lsUvYs2enu6WnygGhDipf2jTGdTHAtEVaVBRqNUEKg0Ej
2QoFOMUdTGbV5sKZLSP3jNlCDJPODIjzPQHfLQIDAQABAoIBAQCNp6jZK1x6MgT+
VUNB9e7X4Gt9BY56LFaYOrJ9hWUQu0i+I42B4EVLE2xHPJVSNO/yZpBPhEAH2VnJ
Jx6pmcH6HU6qvT+ZRRoBqEUqfhzDEt8l3Brg93YpTdN5/I6gMrEOYN3AsOhOS3IV
6DJVawnO8y7ZNu28bNR2yA0qhn1pcHfYxlmMeX1bgQjkoIM3b2wkya0ZsK5ZT/Kf
9J4QsexJaj6btsOUpeEa+rzN0wOrchBYNG7nAk+oXCv5jZ0jvYBf+9oNLxo1h1du
W9nF0Wy7XnP4fIOeTglMYJDmChCVoAIGyRmTLZsV/7BEaEFB/YAIp32bGAjX248O
JwLpoyGBAoGBAPnLW110DNpz8qr/Ee7E6RiSIBaIiXrPPj+vafLT+t2kcIukLWd4
o6xBmOCJo21thh88+dPrWERw466rTYN+TDZn/VAJMX4QFENCtTgKGo/R+NVnnzd/
pJlkwUmFnV5Mj1SW98hjODGRBYfMQoh3YbYieA2Xq28aV1id6hPVgUY1AoGBAN/H
7bzbkSr8Q8MhQUu2uNF/e+4/CCuf5t0J8PuJ1yXxFEi4H0S4skK4GE7E6Bz6TjkM
jpXQbJOXksqb7DMT5lAbSaZOI2nnc/lIoQDT8jo5HZQKMln6q+mdQE6FUzExyesP
12J3oYXKagUurtqPWMg42G787KygqF3FMaP7inQZAoGBAPaidv96ygYXXY7AcjBS
c+Qyw0P8Y+HCJjAzPmToMFYPOqBl5f63+sLlsdBhld9BuJ+3UOSRBoHtBKE9ujcO
yKkfW9leemOB9L/EabF/KGLAavQqb+YYPajG9qFlL9Afv5VByj0s2hr2mYNPhpG/
bdarieXBzx7P31Y79RbIQ+/RAoGAINaEw27w6X7lLrAllce1bUCo3fgv6b7oMM1x
qGe8w5uR323tYLc9qV68amqoqP/uFJ8MGV3hYPMgHk0tJv2nkV+/ugDDX5oQQadv
wOBZLkQNXBj29I89ROXHdv5Hi/ODbtP/RjrJ4Ako76O/EuFwJMl6sSa6UEavDjuO
okKaOZkCgYAhi6eqxUzk5sKP8IXvkZwnSs8yQX5zTT7CyOmrRf4fgSIGggytpqO8
+ATagBoYRDg1+sYjucQI1Fus5857SB/VjbH6znZ2N3SYfbQ8jowLOxkdNCm4JYP6
qn+iCeo8kQZOhO1AagK6dmnZVHcThVwVKv3rVqXJiMqO7nGvxLeD6w==
-----END RSA PRIVATE KEY-----
"""

# 替换为自己的ssh pub key， 通过cat /home/sankuai/.ssh/id_rsa.pub 查看，且该公钥已配置到git，确保clone有权限
id_rsa_pub = """
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDaWzmj+OeioyWx+l+pQLirxSgJvJU6tlQVloYbXCQRcI7t48tVqDqkplPU/UH2L9iLcYd3g23svYLSK+4VxoGmKJBdtuRRPjs942E/e91rx0TdEnpJR+N8563FwzaIeUzD97ECqlXh6pEMXo8yAkJj71HkPZpt8IrxvqgtZfOesNwQRq8mSBKebBHqJGf6sm8j1CXw3Ifh2hAULOSGCtYn2c0+P+c+54OcABYLg+ookYgcFsxoFKdZYzUtFCHzTNO23mWxS9izZ6e7pafKAaEOKl/aNMZ1McC0RVpUFGo1QQqDQSPZCgU4xR1MZtXmwpktI/eM2UIMk84MiPM9Ad8t zhanghaozhou@meituan.com
"""

id_rsa_path = '/home/sankuai/.ssh/id_rsa'
id_rsa_pub_path = '/home/sankuai/.ssh/id_rsa.pub'
!rm -f {id_rsa_path}
!rm -f {id_rsa_pub_path}
with open(id_rsa_path, 'w') as f:
    f.write(id_rsa)
with open(id_rsa_pub_path, 'w') as f:
    f.write(id_rsa_pub)
!chmod 400 {id_rsa_path}
!chmod 400 {id_rsa_pub_path}

!git clone ssh://git@git.sankuai.com/~zhanghaozhou/long_comment_extract.git
```

