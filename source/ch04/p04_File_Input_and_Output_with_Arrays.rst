===============================
4.4 带数组的文件输入输出
===============================

NumPy能够以文本或二进制格式从磁盘保存和加载数据。
在这个部分我仅仅讨论NumPy内置二进制格式，因为大多数用户更喜欢选择pandas和其它工具加载文本或表格数据（更多内容见第六章）。

np.save和np.load是有效保存和加载磁盘上数组数据的两个主力(**workhorse**)函数。
数组默认以未压缩的原始二进制格式文件后缀为.npy保存::

	In [213]: arr = np.arange(10)
	
	In [214]: np.save('some_array', arr)

如果文件路径不包含.npy后缀，扩展名将被追加。
磁盘上的数组使用np.load加载::

	In [215]: np.load('some_array.npy')
	
	Out[215]: array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])

使用np.savez保存多个未压缩存档数组并且传递数组作为关键字参数::

	In [216]: np.savez('array_archive.npz', a=arr, b=arr)

当加载一个.npy文件，返回一个类似于dict对象来懒加载单个数组::

	In [217]: arch = np.load('array_archive.npz')

	In [218]: arch['b']
	Out[218]: array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])

如果你的数据经过压缩，需要使用numpy.savez_compressed代替::

	In [219]: np.savez_compressed('arrays_compressed.npz', a=arr, b=arr)