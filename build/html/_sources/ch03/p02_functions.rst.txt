===============================
3.2 函数
===============================

函数是python中初级的、最重要的代码组织和重用方式。
根据经验(as a rule of thumb)，如果你有预料要重复相同或相似的代码，写一个可重复使用的函数可能值得。
通过给代码块取一个名字，函数也可以增加你的代码可读性。

函数使用def关键字声明，从return关键字处返回::

	def my_function(x, y, z=1.5):
		if z > 1:
			return z * (x + y)
		else:
			return z / (x + y)

有多个return语句没有问题。
如果到达函数的结尾处还没有遇到(**encounter**)返回语句，python自动返回None。

每个函数可以有位置参数和关键字参数。
关键字参数常用来指定默认值或作为可选参数。
在前面的(**preceding**, adj)函数中，x和y是位置参数，z是关键参数。
这意味着函数可以用以下这些方式中任一种调用::

	my_function(5, 6, z=0.7)
	my_function(3.14, 7, 3.5)
	my_function(10, 20)

关于函数参数的主要限制在于位置参数必须要在关键字参数（如果有）前。
你可以用任意顺序指定关键字参数。
这将你从记忆函数参数位置中解放出来，仅仅需要记住参数的名字。

注：
也可以使用关键字参数代替位置参数。
在前面函数例子中，我们也可以这样写::

	my_function(x=5, y=6, z=7)
	my_function(y=6, x=5, z=7)

在某些情况下这可以帮助增加代码可读性。

------------------------------
名字空间、作用域和局部函数
------------------------------

函数在两种不同的作用域内可访问变量：全局和局部。
在python中，变量作用域更具描述力的名字叫名字空间。
任何在函数内被赋值的变量默认是局部名字空间。
局部名字空间在函数被调用时创建，立即由函数参数充填(**populate**).
在函数结束时，局部名字空间被释放（在本章范围(**purview**)外有一些例外）。
考虑下面函数::

	def func():
		a = []
		for i in range(5):
			a.append(i)

在func()被调用时，空列表a被创建，5个元素被追加进列表，在函数退出时a被释放。假如我们用如下的声明a::

	a = []
	def func():
		for i in range(5):
			a.append(i)

在函数作用域外赋值变量是可能的，但是要通过global关键字声明变量::

	In [168]: a = None
	
	In [169]: def bind_a_variable():
	.....: 		global a
	.....: 		a = []
	.....: 	bind_a_variable()
	.....:
	In [170]: print(a)
	[]

注意：
我一般不鼓励使用global关键字。全局变量在储存系统有些状态时使用比较典型。
如果你发现自己使用的比较多，你可能要考虑使用面向对象的编程（使用类）。

------------
返回多个值
------------

当我使用过Java和C++后，第一次使用python编程时，我最喜爱的特性之一就是能够用一个简单的语法使函数返回多个值。这里有一个例子::

	def f():
		a = 5
		b = 6
		c = 7
		return a, b, c
		
	a, b, c = f()

在数据分析和其它的科学应用中，你经常发现自己干这件事。
函数在这里实际上返回的是一个对象，叫元组，被解包到结果变量中去了。
在之前的例子中，我们可以这样做::

	return_value = f()

在这种情况，return_value将是一个有三个返回变量的三元元组。
像以前，一个可能具有吸引力的代替方案是使用字典返回多个值::

	def f():
		a = 5
		b = 6
		c = 7
		return {'a' : a, 'b' : b, 'c' : c}

这种替代方案是有用的，取决于你想尝试做什么。

---------------
函数是对象
---------------

因为python函数是对象，所以在其他语言中难以表达的结构在Python中是比较容易表达的。
假如我们正在做数据清洗工作，需要对下列字符串列表进行一些列的变换::

	In [171]: states = [' Alabama ', 'Georgia!', 'Georgia', 'georgia', 'FlOrIda',
	.....: 	'south carolina##', 'West virginia?']

任何处理过用户提交的调查数据都会见到这些凌乱的结果。
需要做很多事情来使这个字符串列表统一为分析做准备：移除空格，删除标点(**punctuation**)符号，并标准化适当的大小写。
做这件事的一种方式是使用内置的字符串方法以及(along with)re标准库模块的常用表达式::

	import re
	
	def clean_strings(strings):
		result = []
		for value in strings:
			value = value.strip()
			value = re.sub('[!#?]', '', value)
			value = value.title()
			result.append(value)
		return result

结果像这样::

	In [173]: clean_strings(states)
	Out[173]:
	['Alabama',
	'Georgia',
	'Georgia',
	'Georgia',
	'Florida',
	'South Carolina',
	'West Virginia']

一种有用的替代方法是生成一个操作列表，应用到具体的字符串集合::

	def remove_punctuation(value):
		return re.sub('[!#?]', '', value)
		
	clean_ops = [str.strip, remove_punctuation, str.title]
	
	def clean_strings(strings, ops):
		result = []
		for value in strings:
			for function in ops:
				value = function(value)
			result.append(value)
		return result

结果如下::

	In [175]: clean_strings(states, clean_ops)
	Out[175]:
	['Alabama',
	'Georgia',
	'Georgia',
	'Georgia',
	'Florida',
	'South Carolina',
	'West Virginia']

像这样更具函数性模型使你能够在一个很高水平更简单地修改字符串变换方法。
clean_strings函数现在也更具可重用性和通用性(**generic**)。

你可以将函数作为其它函数的参数，如内置的map函数，对一个序列应用函数::

	In [176]: for x in map(remove_punctuation, states):
	.....: 	print(x)
	Alabama
	Georgia
	Georgia
	georgia
	FlOrIda
	south carolina
	West virginia

---------------------
匿名（Lambda）函数
---------------------


---------------
部分参数应用
---------------


---------------
生成器
---------------


---------------
错误和异常处理
---------------
