路径：

```shell
# docker path
/mnt/dolphinfs/hdd_pool/docker/user/hadoop-grocerygoodsalgo/
viewfs:///hadoop-meituan/mnt/dolphinfs/hdd_pool/docker/user/hadoop-grocerygoodsalgo/

# mlp path
viewfs:///hadoop-meituan/user/hadoop-grocerygoodsalgo/zhanghaozhou/
viewfs:///user/hadoop-grocerygoodsalgo/zhanghaozhou/torch_serving/sku_comment_match/2/

hadoop fs -put zhanghaozhou/sellpoint/model_saved/comment_sku_match/checkpoint-category_name_concat_20_18_2_fresh_mix_bert_100epoch/checkpoint_best_61.pt viewfs://hadoop-meituan/user/hadoop-grocerygoodsalgo/zhanghaozhou/torch_serving/sku_comment_match/1
```

```
docker-util pip install p -i http://data-source-pip.sankuai.com/simple --trusted-host data-source-pip.sankuai.com
```

## jupyter 添加环境

```shell
conda install ipykernel
python -m ipykernel install --user --name py37kaze --display-name "kaze37"
```



## jumper 密码

```
a135781012!
```

```python
import sys
sys.path.append('/mnt/dolphinfs/hdd_pool/docker/user/hadoop-grocerygoodsalgo/zhanghaozhou/')
from tools. import 
```

```sh
ssh-copy-id -i ~/.ssh/id_rsa.pub root@192.168.235.22 
```
