====================================
第四章：numpy基础：数组和向量化计算
====================================

NumPy是Numerical Python的缩写(**short for**)，是Python中数值计算最重要的基础包之一。 
大多数提供科学计算功能的包使用NumPy的数组对象作为数据交换的通用语言。

有几样你将在NumPy中发现:

ndarray，一个高效的多维数组，提供快速的面向数组的算法操作和灵活的broadcasting能力。

在整个数组数据上不需要写循环就可以快速操作的数学函数。

读写数组数据到磁盘的工具，和内存映射文件工作。

线性代数、随机数生成和傅里叶变换等能力。

连接NumPy到用C、C++或FORTRAN写的库的C API。

因为NumPy提供易于使用的C API，很容易传递数据到用更低语言写的外部库和从外部库返回数据到Python的NumPy数组中。
此功能使Python成为包装(**wrap**)旧(**legacy**)C / C ++ / Fortran代码库并为其提供动态和易于使用的接口。

NumPy本身不提供建模或科学计算功能，理解NumPy数组和面向数组的计算将帮助你使用面向数组语义十分有效的工具，如pandas。
因为NumPy是一个很大的主题，我将更深入讨论许多如broadcasting等先进的NumPy特性（见附录A）。

对于大多数数据分析应用，我们将关注主要功能领域是：

快速向量化数组操作，用于数据整理和清理、子集和过滤、转换和任何其他类型的计算

常见的数组算法，如排序，唯一和集合操作

高效的描述性统计和聚合(**aggregating**)/汇总数据

数据对齐和关系数据操作对于合并和连接到一个异构(**heterogeneous**)数据集中

将条件逻辑表示为数组表达式而不是使用if-elif-else的循环分支机构

分组数据操作（聚合，转换，功能应用）

虽然Numpy提供关于通用数值数据处理的计算功能，但大量用户想要使用pandas作为基础对于许多统计和分析，尤其关于表格数据。
pandas也提供一些特殊领域，如时间序列操作，NumPy中没有呈现的。

注：
Python中面向数组的计算根源可追溯到1995年，在Jim Hugunin创建了数值计算库时。
后面的10年，大量科学计算程序社区开始在Python中做数组编程，但是库的生态系统在2000年初支离破碎(**fragmented**)。
在2005年，Travis Oliphant通过当时的Numeric和Numarray项目打造(**forge**)NumPy项目，将社区整合到单个数组计算框架中。


在Python数值计算，NumPy如此重要的原因之一是因为它被设计成对大型数组数据高效。有以下一点原因：

NumPy内部储存数据在连续的内存块，独立于其它内建Python对象。用C语言实现的NumPy算法库能够操纵内存无须类型检查或其它开销(**overhead**)。

NumPy在整个数组上执行复杂计算无须Python for循环。

为了给你性能差异的直观概念，考虑百万整型的Numpy数据以及等效的Python列表::

	In [7]: import numpy as np
	
	In [8]: my_arr = np.arange(1000000)
	
	In [9]: my_list = list(range(1000000))

现在让我们对序列中每个元素乘以2::

	In [10]: %time for _ in range(10): my_arr2 = my_arr * 2
	CPU times: user 20 ms, sys: 50 ms, total: 70 ms
	Wall time: 72.4 ms
	
	In [11]: %time for _ in range(10): my_list2 = [x * 2 for x in my_list]
	CPU times: user 760 ms, sys: 290 ms, total: 1.05 s
	Wall time: 1.05 s

基于NumPy的算法通常比纯Python实现(**counterparts**)快10到100倍（或者更多）并使用明显更少的内存。

.. toctree::
   :maxdepth: 2
   :glob:

   ../ch04/*