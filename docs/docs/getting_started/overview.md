# 包概述

**Pandas** 是一个 [Python](https://www.python.org/) 的包，提供快速、灵活和富有表现力的数据结构，旨在使“关系”或“标记”数据的使用既简单又直观。它旨在成为在Python中进行实际，**真实世界**数据分析的基础高级构建模块。此外，**它还有更宏远的目标，即成为超过任何语言的最强大，最灵活的开源数据分析/操作工具**。它已朝着这个目标迈进。

pandas非常适合许多不同类型的数据：

 - 具有异构类型列的表格数据，如SQL表或Excel电子表格。
 - 有序和无序（不一定是固定频率）时间序列数据。
 - 具有行和列标签的任意矩阵数据（均匀类型或异构）。
 - 任何其他形式的观察/统计数据集。 实际上不需要将数据标记为放置在Pandas数据结构中。

Pandas 的两个主要数据结构，[Series](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.html#pandas.Series)（1维）和[DataFrame](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame)（2维），处理金融，统计，社会科学和许多工程领域中的绝大多数典型用例。 对于R用户，DataFrame提供R的data.frame提供的所有内容以及更多内容。 Pandas建立在[NumPy](https://www.numpy.org/)之上，旨在与许多其他第三方库完美地集成在科学计算环境中。

以下是Pandas做够胜任的一些事情：

 - 在浮点和非浮点数据中轻松处理**缺失数据**（表示为NaN）。
 - 大小可变性：可以从DataFrame和更高维度的对象中**插入和删除**。
 - 自动和显式**数据对齐**：对象可以明确地与一组标签对齐，或者用户可以简单地忽略标签，让Series，DataFrame等在计算中自动对齐数据
 - 强大，灵活的**组（group by）**功能，可对数据集执行拆分应用组合操作，用于聚合和转换数据。
 - **轻松**将其他Python和NumPy数据结构中的不规则，不同索引数据转换为DataFrame对象。
 - 基于智能标签的**切片**，**花式索引**和**子集**大数据集。
 - 直观**合并**和**加入**数据集。
 - 灵活的**重塑**和数据集的旋转。 
 -  **轴的分层**标记（每个刻度可能有多个标签）。
 - 强大的IO工具，用于从**平面文件**（CSV和分隔）、Excel文件、数据库以及能从超快的**HDF5格式**中保存或加载数据。
 -  **特定时间序列**功能：日期范围生成和频率转换、移动窗口统计、移动窗口线性回归、日期转换和滞后等。

其中许多技术都是为了解决使用其他语言/科研环境时经常遇到的缺点。对于数据科学家来说，处理数据通常分为多个阶段：整理和清理数据，分析/建模数据，然后将分析结果组织成适合绘图或表格显示的形式。Pandas 是完成所有这些任务的理想工具。

其他一些说明

- Pandas 的开发速度**很快**。许多低级算法位已经在[Cython](https://cython.org/)代码中进行了大量优化。然而，与其他任何事物一样，这样做通常会牺牲性能。 因此，如果您专注于应用程序的一个功能，您可以更快创建一个专用的工具。
- Pandas 是[statsmodels](https://www.statsmodels.org/stable/index.html)的依赖，使其成为Python中统计计算生态系统的重要组成部分。
- Pandas 已广泛用于金融领域的应用和生产。

## 数据结构

维数 | 名称 | 描述
---|---|---
1 | Series | 可以看做有标签（默认是整数序列RangeIndex；可以重复）的一维数组（同类型）。是scalars的集合，同时也是DataFrame的元素。
2 | DataFrame | 一般是二维标签，尺寸可变的表格结构，具有潜在的异质型列。

### 为什么有多个数据结构？

考虑Pandas数据结构的最佳方式是作为低维数据的灵活容器。例如，DataFrame是Series的容器，Series是scalars的容器。我们希望能够以类似字典的方式从这些容器中插入和删除对象。

此外，我们希望通用API函数的合理默认行为考虑到时间序列和横截面数据集的典型方向。当使用ndarrays存储2维和3维数据时，在编写函数时会给用户带来负担以考虑数据集的方向; 轴被认为或多或少相等（除非C-或Fortran-连续性对性能有影响）。在Pandas中，轴旨在为数据提供更多的语义含义; 即，对于特定数据集，可能存在定向数据的“正确”方式。因此，目标是减少在下游功能中编码数据转换所需的心理努力量。

此外，我们希望公共API函数的合理默认行为考虑到时间序列和横截面数据集的典型方向。当使用ndarray存储2维和3维数据时，用户在编写函数时需要考虑数据集的方向；轴被认为或多或少是等价的(除非C或Fortran邻接关系到性能)。在Pandas中，轴旨在为数据提供更多的语义含义；即，对于特定的数据集，可能会有一种“正确”的方式来确定数据的方向。因此，目标是减少在下游函数中编写数据转换代码所需的脑力劳动。

举个例子，对于表格数据(DataFrame)，考虑***索引**(行)和**列**(而不是轴0和轴1)在语义上更有帮助。因此，迭代DataFrame的列会产生更具可读性的代码：

``` python
for col in df.columns:
    series = df[col]
    # do something with series
```

## 数据的可变性和拷贝

All Pandas data structures are value-mutable (the values they contain can be altered) but not always size-mutable. The length of a Series cannot be changed, but, for example, columns can be inserted into a DataFrame. However, the vast majority of methods produce new objects and leave the input data untouched. In general we like to **favor immutability** where sensible.

## 获得支持

The first stop for Pandas issues and ideas is the [Github Issue Tracker](https://github.com/Pandas-dev/Pandas/issues). If you have a general question, Pandas community experts can answer through [Stack Overflow](https://stackoverflow.com/questions/tagged/Pandas).

## 社区

Pandas is actively supported today by a community of like-minded individuals around the world who contribute their valuable time and energy to help make open source Pandas possible. Thanks to [all of our contributors](https://github.com/Pandas-dev/Pandas/graphs/contributors).

If you’re interested in contributing, please visit the [contributing guide](https://Pandas.pydata.org/Pandas-docs/stable/development/contributing.html#contributing).

Pandas is a [NumFOCUS](https://www.numfocus.org/open-source-projects/) sponsored project. This will help ensure the success of development of Pandas as a world-class open-source project, and makes it possible to [donate](https://Pandas.pydata.org/donate.html) to the project.

## 项目治理

The governance process that Pandas project has used informally since its inception in 2008 is formalized in [Project Governance documents](https://github.com/Pandas-dev/Pandas-governance). The documents clarify how decisions are made and how the various elements of our community interact, including the relationship between open source collaborative development and work that may be funded by for-profit or non-profit entities.

Wes McKinney is the Benevolent Dictator for Life (BDFL).

## 开发团队

The list of the Core Team members and more detailed information can be found on the [people’s page](https://github.com/Pandas-dev/Pandas-governance/blob/master/people.md) of the governance repo.

## 机构合作伙伴

The information about current institutional partners can be found on [Pandas website page](https://Pandas.pydata.org/about.html).

## 许可协议

```
BSD 3-Clause License

Copyright (c) 2008-2012, AQR Capital Management, LLC, Lambda Foundry, Inc. and PyData Development Team
All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are met:

* Redistributions of source code must retain the above copyright notice, this
  list of conditions and the following disclaimer.

* Redistributions in binary form must reproduce the above copyright notice,
  this list of conditions and the following disclaimer in the documentation
  and/or other materials provided with the distribution.

* Neither the name of the copyright holder nor the names of its
  contributors may be used to endorse or promote products derived from
  this software without specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE
FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER
CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY,
OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
```
