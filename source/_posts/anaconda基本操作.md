---
title: anaconda基本操作
date: 2022-11-18 11:06:44
tags:
id: 1668740846
---
# 列出所有环境
```sh
conda env list
```

# 创建环境
```sh
conda create -n <envName>
conda create -n <envName> python=<x.x> # 指定 python 版本
conda create -n <envName> --clone <envName> # 复制另一个环境
```

# 激活环境
```sh
conda activate <envName>
```

# 删除环境
```sh
conda env remove -n <envName>
```

# pip 使用阿里云源
```sh
$ cat ~/.pip/pip.conf
[global]
index-url = https://mirrors.aliyun.com/pypi/simple/
trusted-host = mirrors.aliyun.com
```

# mac m1 使用 conda forge
很多包不支持 mac m1，需要安装[miniconda](https://docs.conda.io/en/latest/miniconda.html)，然后使用 [conda forge](https://conda-forge.org/)

```sh
conda config --add channels conda-forge
conda config --set channel_priority strict
conda install <package-name>

# 或者
conda install -c conda-forge <package-name>
```