
<div align="center">

<h1><img src="docs/_static/logo.png" height="28px" /> BMCook</h1>

**Model Compression for Big Models**
    
</div>


<p align="center">
  <a href="#overview">Overview</a> • <a href="#documentation">Documentation</a> • <a href="#install">Installation</a> • <a href="#quick-start">Quick Start</a> • <a href="./README-ZH.md" target="_blank">简体中文</a>
<br>
</p>

<p align="center">
	<a href='https://bmcook.readthedocs.io/en/main/'>
	    <img src='https://readthedocs.org/projects/bmcook/badge/?version=main' alt='doc' />
	</a>
	<a href="https://github.com/OpenBMB/BMCook/blob/main/LICENSE">
	    <img alt="github" src="https://img.shields.io/github/license/OpenBMB/BMCook">
	</a>
	<a>
		 <img alt="version" src="https://img.shields.io/badge/version-0.1.0-blue">
	</a>
</p>    

## What's New

- 2022/3/29 (**BMCook 0.1.0**) Now we publicly release the first version of BMCook.

<div id="overview"></div>

## Overview

BMCook is a model compression toolkit for large-scale pre-trained language models (PLMs), which integrates multiple model compression methods. You can combine them in any way to achieve the desired speedup. Specifically, we implement the following four model compression methods, knowledge distillation, model pruning, model quantization, and model MoEfication. It has following features:

- **Various Supported Methods.** Compared to existing compression toolkits, BMCook supports all mainstream acceleration methods for pre-trained language models.
- **User Friendly.** Based on BMCook, we can implement different compression methods with just a few lines of codes.
- **Combination in Any Way.** Due to  the decoupled implications, the compression methods can be combined in any way towards extreme acceleration.

<div id="documentation"></div>

## Documentation
Our [documentation](https://bmcook.readthedocs.io/en/main/) provides more information about the package.

<div id="install"></div>

## Installation

To use BMCook, first install BMTrain.

**From PyPI (Recommend)**

```shell
$ pip install bmtrain
```

**From Source**

```shell
$ git clone https://github.com/OpenBMB/BMTrain.git
$ cd BMTrain
$ python3 setup.py install
```

Please refer to [the installation guide](https://bmtrain.readthedocs.io/en/latest/) of BMTrain for more details.

Then, clone the repository.


```shell
$ git clone git@github.com:OpenBMB/BMCook.git
```

<div id="quick-start"></div>

## Quick Start

The `gpt-example` folder provides example codes based on GPT2-Base.

Quantization-aware training：

```
    torchrun --nnodes=1 --nproc_per_node=1 --rdzv_id=1 --rdzv_backend=c10d --rdzv_endpoint=localhost train.py \
     --save-dir results/gpt2-int8 \
     --model gpt2-base \
     --start-lr 1e-4 \
     --cook-config configs/gpt2-int8.json \
```

Quantization-aware training with knowledge distillation：
```
    torchrun --nnodes=1 --nproc_per_node=1 --rdzv_id=1 --rdzv_backend=c10d --rdzv_endpoint=localhost train.py \
     --save-dir results/gpt2-int8-kd \
     --model gpt2-base \
     --start-lr 1e-4 \
     --cook-config configs/gpt2-int8-kd.json \
```

Model pruning：
```
    torchrun --nnodes=1 --nproc_per_node=1 --rdzv_id=1 --rdzv_backend=c10d --rdzv_endpoint=localhost train.py \
     --save-dir results/gpt2-prune \
     --model gpt2-base \
     --start-lr 1e-4 \
     --cook-config configs/gpt2-prune.json \
```
In this case, we only prune the input embedding layer. You can include more modules by changing the `pruned_module` field in the config file.

MoEfication (save the hidden states and then use the MoEfication toolkit)：
```
    torchrun --nnodes=1 --nproc_per_node=1 --rdzv_id=1 --rdzv_backend=c10d --rdzv_endpoint=localhost train.py \
     --save-dir results/gpt2-moe \
     --model gpt2-base \
     --start-lr 1e-4 \
     --cook-config configs/gpt2-moe.json \
```

Combine quantization, pruning and knowledge distillation：
```
    torchrun --nnodes=1 --nproc_per_node=1 --rdzv_id=1 --rdzv_backend=c10d --rdzv_endpoint=localhost train.py \
     --save-dir results/gpt2-combine \
     --model gpt2-base \
     --start-lr 1e-4 \
     --cook-config configs/gpt2-combine.json \
```

## Performances

Based on GPT-J, we evaluate different combinations of compression techniques. The corpus is OpenWebText. We also train a small GPT-J with 0.7B parameters based on this corpus from scratch, GPT-J (0.7B).

|                        |     LM Loss    |     Relative Performance    |     Speedup    |
|------------------------|----------------|-----------------------------|----------------|
|     GPT-J              |           3.37 |                        -    |          1x    |
|     GPT-J (0.7B)       |           4.06 |                       83.0% |         ~10x   |
|     GPT-J (P+D)        |           3.57 |                       94.4% |          2x    |
|     GPT-J (P+D+Q)      |           3.58 |                       94.1% |          8x    |
|     GPT-J (P+D+Q+M)    |           3.69 |                       91.3% |          10x   |

D denotes knowledge distillation. P denotes pruning. Q denotes quantization. M denotes MoEfication.

## Comparisons

|                 | Model Quantization | Model Pruning | Knowledge Distillation | Model MoEfication |
|-----------------|--------------------|---------------|------------------------|-------------------|
| [TextPruner](https://github.com/airaria/TextPruner)      |       -             | ✅             |          -              |      -             |
| [TensorFlow Lite](https://www.tensorflow.org/lite) | ✅                  | ✅             |          -              |           -        |
| [PyTorch](https://pytorch.org/)         | ✅                  | ✅             |            -            |          -         |
| [TextBrewer](https://github.com/airaria/TextBrewer)      |           -         | ✅             | ✅                      |         -          |
| BMCook          | ✅                  | ✅             | ✅                      | ✅                 |

## Community
We welcome everyone to contribute codes following our [contributing guidelines](https://github.com/OpenBMB/BMCook/blob/main/CONTRIBUTING.md).

You can also find us on other platforms:
- QQ Group: 735930538
- WeChat Official Account: OpenBMB
- Website: https://www.openbmb.org
- Weibo: http://weibo.cn/OpenBMB
- Twitter: https://twitter.com/OpenBMB

## License

The package is released under the [Apache 2.0](https://github.com/OpenBMB/BMCook/blob/main/LICENSE) License.

## Contributors

We thank Zhengyan Zhang, Baitao Gong, Yingfa Chen, Guoyang Zeng, Jie Zhou, and Zhi Zheng for the contribution. More contributors are welcome!
