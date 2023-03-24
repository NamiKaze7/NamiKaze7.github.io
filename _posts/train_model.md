[TOC]

## 环境搭建

```shell
while read requirement; do conda install --yes $requirement || pip install $requirement; done < requirements.txt
```



## 进程控制

```python
cpu_num = 1
os.environ["OMP_NUM_THREADS"] = str(cpu_num)
os.environ["OPENBLAS_NUM_THREADS"] = str(cpu_num)
os.environ["MKL_NUM_THREADS"] = str(cpu_num)
os.environ["VECLIB_MAXIMUM_THREADS"] = str(cpu_num)
os.environ["NUMEXPR_NUM_THREADS"] = str(cpu_num)
torch.set_num_threads(cpu_num)
```

## tokenizer重训练

```python
from tokenizers import Tokenizer
from tokenizers.decoders import ByteLevel as ByteLevelDecoder
from tokenizers.models import BPE
from tokenizers.normalizers import Lowercase, NFKC, Sequence
from tokenizers.pre_tokenizers import ByteLeve
from tokenizers.trainers import BpeTrainer

# 1、创建一个空的字节对编码模型
tokenizer = Tokenizer(BPE())

#2、启用小写和unicode规范化，序列规范化器Sequence可以组合多个规范化器，并按顺序执行
tokenizer.normalizer = Sequence([
    NFKC(),
    Lowercase()
])
#3、标记化器需要一个预标记化器，负责将输入转换为ByteLevel表示。
tokenizer.pre_tokenizer = ByteLevel()

# 4、添加解码器，将token令牌化的输入恢复为原始的输入
tokenizer.decoder = ByteLevelDecoder()
# 5、初始化训练器，给他关于我们想要生成的词汇表的详细信息
trainer = BpeTrainer(vocab_size=858, show_progress=True, initial_alphabet=ByteLevel.alphabet())
# 6、开始训练我们的语料
tokenizer.train(files=["./tmp/all_data_txt.txt"], trainer=trainer)
# 最终得到该语料的Tonkernize，查看下词汇大小
print("Trained vocab size: {}".format(tokenizer.get_vocab_size()))
# 保存训练的tokenizer
tokenizer.model.save('./my_token/')
```

根据config 修改词表维度

```python
import tokenizers
# 创建分词器
bwpt = tokenizers.BertWordPieceTokenizer()
filepath = "../excel2txt.txt" # 语料文件
#训练分词器
bwpt.train(
    files=[filepath],
    vocab_size=50000, # 这里预设定的词语大小不是很重要
    min_frequency=1,
    limit_alphabet=1000
)
# 保存训练后的模型词表
bwpt.save_model('./pretrained_models/')
#output： ['./pretrained_models/vocab.txt']

# 加载刚刚训练的tokenizer
tokenizer=BertTokenizer(vocab_file='./pretrained_models/vocab.txt')
```

二分法搜索阈值

```python
import numpy as np

def binary_search_threshold(y_true, y_pred):
    """在两个类别的问题中二分搜索最佳的阈值"""
    # 计算y_pred中的最大和最小值
    y_min = np.min(y_pred)
    y_max = np.max(y_pred)
    # 初始化二分搜索的左右边界
    left, right = y_min, y_max
    # 循环二分搜索直到左右边界相等或非常接近
    while right - left > 1e-6:
        # 计算中间阈值
        mid = (left + right) / 2
        # 根据阈值将概率预测值转换为二元标签
        y_pred_binary = (y_pred >= mid).astype(int)
        # 计算二元标签和真实标签之间的误差
        error = np.mean(y_pred_binary != y_true)
        # 根据误差调整二分搜索的左右边界
        if error > 0.5:
            right = mid
        else:
            left = mid
    # 返回最佳的阈值
    return (left + right) / 2
```

