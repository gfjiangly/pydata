====================================
4.1 NumPy ndarray：一个多维数组对象
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


----------------------------------
NumPy ndarray，一个多维数组对象
----------------------------------

NumPy最关键的特点之一是它的N维数组对象，或者叫ndarray，在Python中是一个快速、灵活的大数据集容器。
数组使你能够使用相似的语法在整个数据块上执行数学操作，与在标量元素上有相同效果的操作。

为了让您了解NumPy如何使用类似语法对于内置Python对象的标量值启用批量计算，我首先导入NumPy并生成一个小的随机数据数组::

	In [12]: import numpy as np

	# Generate some random data
	In [13]: data = np.random.randn(2, 3)

	In [14]: data
	Out[14]:
	array([[-0.2047, 0.4789, -0.5194],
	[-0.5557, 1.9658, 1.3934]])

然后我对数据施加数学操作::

	In [15]: data * 10
	Out[15]:
	array([[ -2.0471, 4.7894, -5.1944],
	[ -5.5573, 19.6578, 13.9341]])

	In [16]: data + data
	Out[16]:
	array([[-0.4094, 0.9579, -1.0389],
	[-1.1115, 3.9316, 2.7868]])

在第一个例子中，全部元素都被乘上了10。第二个例子，在数组每个"格子"相应位置值和每个自己相加。

注：
在本章和全书，我使用标准NumPy简写import numpy as np。当然，欢迎放from numpy import \*在你的代码中，避免一直写np.，但是我不建议这样的习惯。
numpy名字空间是很大的，包含许多名字和内置Python函数冲突的函数（像min和max）。

ndarray是用于同类(**homogeneous**)数据的通用多维容器;
这是说，所有的元素必须是相同类型。每个数组有一个shape。一个表示每个维度大小的元组，dtype，描述数组数据类型的对象::

	In [17]: data.shape
	Out[17]: (2, 3)
	
	In [18]: data.dtype
	Out[18]: dtype('float64')

这章讲给你介绍基本的NumPy数组使用，会大量出现于本书的剩余部分。
虽然不必要深入理解NumPy的许多数据分析应用程序，但是精通(**proficient**)面向数组的编程和思考是成为科学计算Python专家(**guru**)的关键一步。

注：
不管在文中你是见到array、NumPy array还是ndarray，除少数例外外，它们都是指相同的东西：ndarray对象。

---------------
创建ndarray
---------------

创建一个数组的最简单方式是使用array函数。
它接受任何序列对象（包括其它数组），产生一个新的包含传入数据的NumPy数组。举例，列表是转换的好选择::

	In [19]: data1 = [6, 7.5, 8, 0, 1]

	In [20]: arr1 = np.array(data1)

	In [21]: arr1
	Out[21]: array([ 6. , 7.5, 8. , 0. , 1. ])

嵌套序列，如等长度列表的列表。将转换成一个多维数组::

	In [22]: data2 = [[1, 2, 3, 4], [5, 6, 7, 8]]

	In [23]: arr2 = np.array(data2)

	In [24]: arr2
	Out[24]:
	array([[1, 2, 3, 4],
	[5, 6, 7, 8]])

由于data2是一个列表的列表，因此从数据推断NumPy数组arr2有有两个维度的shape。
我们可以通过检查ndim和shape属性来验证这个::

	In [25]: arr2.ndim
	Out[25]: 2

	In [26]: arr2.shape
	Out[26]: (2, 4)

除非明确指定，否则np.array尝试为它创建的数组推断一个合适的数据类型。
数据类型是被储存在特殊的元数据对象dtype中；
举例，在之前的两个例子中我们有::

	In [27]: arr1.dtype
	Out[27]: dtype('float64')

	In [28]: arr2.dtype
	Out[28]: dtype('int64')



