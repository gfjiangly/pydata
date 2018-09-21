===============================
4.7 示例：随机游走
===============================

随机游走仿真提供一个说明性使用(**utilize**)数组操作应用。
让我们考虑一个简单的随机游走，从0开始，步长为1和-1，等概率发生。

这儿有一个纯Python方式实现的一个简单随机游走，1000个步长，使用内建的random模块::

	In [247]: import random
	.....: position = 0
	.....: walk = [position]
	.....: steps = 1000
	.....: for i in range(steps):
	.....: step = 1 if random.randint(0, 1) else -1
	.....: position += step
	.....: walk.append(position)
	.....:

见图4-4 在一次随机中前100个值的绘图例子::

	In [249]: plt.plot(walk[:100])

.. image:: images/Figure_4-4._A_simple_random_walk.png
	:width: 800

你可能观察到游走是简单地随机步长累加并且可以使用一个数组表达式产生。因此，我使用np.random模块一次画1000个抛硬币，结果置为1和-1，并且计算累(**cumulative**)加值::

	In [251]: nsteps = 1000

	In [252]: draws = np.random.randint(0, 2, size=nsteps)

	In [253]: steps = np.where(draws > 0, 1, -1)

	In [254]: walk = steps.cumsum()

我们能从中提取统计量如沿游走轨线最大最小值::

	In [255]: walk.min()
	Out[255]: -3

	In [256]: walk.max()
	Out[256]: 31

一个更复杂的统计是第一次交叉时间，随机游走达到某个具体值的步数。
在这里，我们可能想知道随机游走在任一方向上距起点0至少10步之间需要多长时间。
np.abs(walk) >= 10 给我们一个布尔数组表明游走在哪里到达或超过10，但是我们可能想知道第一个10或-10的位置。
结果是，我们使用argmax，返回在布尔数组中第一个最大值索引（True是最大值）::

	In [257]: (np.abs(walk) >= 10).argmax()
	Out[257]: 37

注意这里使用argmax不是一直高效的，因为它一直对数组做完整的扫描。
在这个具体例子中，一旦True被观察到我们就已经知道它是最大值，无须再对后面值进行扫描。