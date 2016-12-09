# 章5. 机器学习基础

## 一、基本概念

1. 为了评估机器学习算法的能力，我们必须设计其性能的衡量指标。但是有些情况下，很难决定应该衡量什么。
	- 在执行翻译任务时，我们是应该衡量整个翻译结果的准确率，还是衡量每个单词翻译的准确率？
	- 在密度估计任务中，很多模型都是隐式地表示概率分布。此时计算样本空间某个点的真实概率是不可行的，因此也就无法判断该点的概率估计的准确率。

2. 无监督学习和监督学习之间的界线通常是模糊的。对于求解监督学习问题 \\(p(y\mid \mathbf{\vec x})\\)，我们可以先求解无监督学习问题来学习联合概率分布 \\(p(\mathbf{\vec x},y)\\)，然后计算：
	$$p(y\mid \mathbf{\vec x})=\frac{p(\mathbf{\vec x},y)}{\sum\_{y^{\prime}}p(\mathbf{\vec x},y^{\prime})} $$

3. 通常我们利用最小化训练误差来训练模型，但是我们真正关心的是测试误差。因为我们通过测试误差来评估模型的泛化能力。
	- 统计理论表明：如果训练集和测试集中的样本都是独立同分布产生的，则有：模型的训练误差的期望等于模型的测试误差的期望
	- 我们将训练集和测试集共同的、潜在的样本分布称作：数据生成分布，记作 \\(p\_{data}\\)

4. 当我们使用机器学习算法时，决定机器学习算法效果的两个因素：
	- 降低训练误差
	- 缩小训练误差和测试误差的差距

	这两个因素对应着机器学习中的两个主要挑战：欠拟合和过拟合。
	- 欠拟合是由于模型不能在训练集上获取足够小的训练误差
	- 过拟合是由于模型的训练误差和测试误差之间的差距太大

	通过调整模型的容量`capacity`可以缓解该问题。模型的容量是指其拟合各种函数的能力。
	- 容量低的模型容易发生欠拟合
	- 容量高的模型容易发生过拟合	 

5. 有多种方法来改变模型的容量，如选择模型的假设空间：即代表模型的函数集合（这也称作模型的表示容量`representational capacity`）。通常在这些函数中挑选出最佳的函数是非常困难的优化问题，实际应用中只是挑选一个使得训练误差足够低的函数即可。这些额外的限制因素（比如优化算法的不完善）意味着学习算法的有效容量`effective capacity`可能小于模型的表示容量。

6. 统计学习理论提供了量化模型容量的方法。其中最出名的是`VC`维理论。统计学习理论中最重要的结论表明：训练误差与泛化误差之间差异的上界随着模型容量增长而增长，随着训练样本增多而下降。
	
	虽然上述结论对于机器学习算法有很好的指导作用，但是深度学习很难应用。原因有二：
	- 边界太宽泛
	- 难以确定深度学习的容量。由于深度学习模型的有效容量受限于优化算法，因此确定深度学习模型的容量特别困难。

7. 通常泛化误差是关于模型容量的 `U`形函数。随着模型容量增大，训练误差会下降直到逼近其最小值；泛化误差先减小后增大；泛化误差与训练误差的差值会增大。
	![capacity](../imgs/5/capacity.jpg)

8.  机器学习的“没有免费的午餐定理”表明：在所有可能的数据生成分布上，没有一个机器学习算法总是比其他的要好。

	该结论仅在考虑所有可能的数据分布时才成立。事实上现实任务中，特定任务的数据分布往往满足某类假设，从而我们可以设计在这类分布上效果更好的学习算法。

	这意味着机器学习并不需要寻找一个通用的学习算法，而是寻找一个在我们关心的数据分布上效果最好的算法。

9. 正则化是我们对学习算法做的一个修改，这种修改趋向于降低泛化误差（而不是降低训练误差）。正则化是机器学习领域的中心问题之一
	- 没有免费的午餐定理说明了没有最优的学习算法，因此也没有最优的正则化形式。我们必须挑选一个适合我们任务的正则化形式。

10. 大多数机器学习算法具有超参数。超参数的值无法通过学习算法拟合出来（比如正则化项的系数、控制模型容量的参数 ），为了解决这个问题我们引入验证集。
	- 我们将训练数据分成两个不相交的子集：训练集用于学习模型，验证集用于更新超参数。
	- 验证集通常会低估泛化误差。因此当超参数优化完成后，需要通过测试集来估计泛化误差。

## 二、估计、偏差、方差
1. 点估计：对参数 \\(\theta\\) 的一个预测，记作 \\(\hat\theta\\)。

	假设 \\(\\{x_1,x_2,\cdots,x_m\\}\\) 为独立同分布的数据点，该分布由参数 \\(\theta\\) 决定。则参数  \\(\theta\\)  的点估计为某个函数：
	 $$\hat\theta\_m =g(x_1,x_2,\cdots,x_m) $$
	注意：点估计的定义并不要求 \\(g\\) 返回一个接近真实值 \\(\theta\\) 。 

	根据频率学派的观点，真实参值 \\(\theta\\) 是固定的，但是未知的。 \\(\hat\theta\_m\\) 是数据点的函数。由于数据是随机采样的，因此 \\(\hat\theta\_m\\) 是个随机变量。

2. 偏差定义为： \\(bias(\hat\theta\_m)=\mathbb E(\hat\theta\_m)-\theta\\)，期望作用在所有数据上。
	- 如果 \\(bias(\hat\theta\_m)=0\\) ，则称估计量 \\(\hat\theta\_m\\) 是无偏的。
	- 如果 \\(\lim\_{m\rightarrow \infty}bias(\hat\theta\_m)=0\\)，则称估计量 \\(\hat\theta\_m\\) 是渐近无偏的。

	无偏估计并不一定是最好的估计。

3. 点估计的例子例：
	- 一组服从均值为 \\(\theta\\) 的伯努利分布的独立同分布样本  \\(\\{x_1,x_2,\cdots,x_m\\}\\) ， \\(\hat\theta\_m=\frac 1m\sum\_{i=1}^{m}x_i\\) 为 \\(\theta\\) 的无偏估计。
	- 一组服从均值为 \\(\mu\\)，方差为 \\(\sigma^{2}\\) 的高斯分布的独立同分布样本 \\(\\{x_1,x_2,\cdots,x_m\\}\\)：
		- \\(\hat\mu\_m=\frac 1m\sum\_{i=1}^{m}x_i\\) 为 \\(\mu\\) 的无偏估计。
		- \\(\hat\sigma^{2}\_m=\frac 1m\sum\_{i=1}^{m}(x_i-\hat\mu_m)^{2}\\) 为 \\(\sigma^{2}\\) 的有偏估计。因为 \\(\mathbb E[\hat\sigma^{2}\_m]=\frac{m-1}{m}\sigma^{2}\\)
		- \\(\tilde\sigma^{2}\_m=\frac {1}{m-1}\sum\_{i=1}^{m}(x_i-\hat\mu_m)^{2}\\) 为 \\(\sigma^{2}\\) 的无偏估计。

4. 估计量的方差记作 \\(Var(\hat \theta)\\)，标准差记作 \\(SE(\hat\theta)\\) 。它们刻画的是：当从潜在的数据分布中独立的获取数据集时，估计量的变化程度。
	
	例：一组服从均值为 \\(\theta\\) 的伯努利分布的独立同分布样本  \\(\\{x_1,x_2,\cdots,x_m\\}\\) ， \\(\hat\theta\_m=\frac 1m\sum\_{i=1}^{m}x_i\\) 为 \\(\theta\\) 的无偏估计， \\(Var(\hat\theta_m)=\frac 1m \theta(1-\theta)\\) 。表明估计量的方差随 \\(m\\) 增加而下降。这是估计量的共性。

5. 通常我们希望的是：
	- 估计量的偏差比较小，即估计量的期望值接近真实值 
	- 估计量的方差比较小，即估计量的波动比较小

6. 均值估计：\\(\hat\mu_m=\frac 1m\sum\_{i=1}^{m}x_i\\)
	
	其标准差为：
	 $$SE(\hat\mu_m)=\sqrt{ Var\left[\frac 1m\sum\_{i=1}^{m}x_i\right]}=\frac{\sigma}{\sqrt{m}} $$
	其中 \\(\sigma^{2}\\) 是样本 \\(x_i\\) 的真实方差，但是这个量难以估计。

	\\(\sqrt{\frac 1m\sum\_{i=1}^{m}(x_i-\hat\mu_m)^{2}}\\)  和 \\(\sqrt{\frac {1}{m-1}\sum\_{i=1}^{m}(x_i-\hat\mu_m)^{2}}\\) 都不是真实标准差 \\(\sigma\\)  的无偏估计。这两种方法都倾向于低估真实的标准差。但是实际应用中，\\(\sqrt{\frac {1}{m-1}\sum\_{i=1}^{m}(x_i-\hat\mu_m)^{2}}\\) 是一种比较合理的近似估计，尤其是当 \\(m\\) 较大的时候。

7. 偏差和方差衡量的是估计量的两个不同误差来源：
	- 偏差衡量的是偏离真实值的误差的期望
	- 方差衡量的是由于任意的数据采样可能导致的估计值的波动

	考虑均方误差
	$$MSE=\mathbb E[(\hat \theta_m-\theta)^{2}] $$

	根据：
	$$bias(\hat\theta\_m)^{2}+Var(\hat\theta_m)=[\mathbb E(\hat\theta\_m)-\theta]^{2}+\mathbb E[(\hat\theta\_m-\mathbb E(\hat\theta\_m))^{2}] \\\
	= \mathbb E(\hat\theta\_m)^{2}+\theta^{2}-2\theta\mathbb E(\hat\theta\_m)+\mathbb E[\hat\theta\_m^{2}]-\mathbb E(\hat\theta\_m)^{2}\\\
	=\mathbb E[\hat\theta\_m^{2}]-2\theta\mathbb E(\hat\theta\_m)+\theta^{2}\\\
	=\mathbb E[(\hat \theta_m-\theta)^{2}]=MSE $$
	即：`MSE`包含了偏差和方差。

8. 偏差、方差与模型容量有关。用`MSE`衡量的泛化误差时，增加容量会增加方差、降低偏差，而`MSE`为`U`形曲线。

	![bias_var](../imgs/5/bias_var.jpg)

9. 我们通常希望当数据集中的点 \\(m\\) 增加时，点估计会收敛到对应参数的真实值。即：
	$$\text{plim}\_{m\rightarrow \infty}\hat\theta_m=\theta $$
	\\(\text{plim}\\) 表示依概率收敛。即对于任意的 \\(\epsilon \gt 0\\)，当 \\(m\rightarrow \infty\\) 时，有： \\(P(|\hat\theta_m -\theta|)\gt \epsilon \rightarrow 0\\)

	上述条件也称做一致性。它保证了估计偏差会随着样本数量的增加而减少。
	> 渐近无偏不一定意味着一致性。如在正态分布产生的数据集中，我们可以用 \\(\hat\mu_m=x_1\\) 作为 \\(\mu\\)  的一个估计。它是无偏的，因为 \\(\mathbb E[x_1]=\mu\\)，所以不论观测到多少个数据点，该估计都是无偏的。但是他不是一致的，因为他不满足 \\(\text{plim}\_{m\rightarrow \infty}\hat\mu_m=\mu\\)

## 三、最大似然估计

1. 假设数据集 \\(\mathbf X=\\{\mathbf{\vec x}\_1,\mathbf{\vec x}\_2,\cdots,\mathbf{\vec x}\_m\\}\\) 中的样本独立同分布地由 \\(p\_{data}(\mathbf{\vec x})\\) 产生，但是该分布是未知的。

	 \\(p\_{model}(\mathbf{\vec x};\theta)\\) 是一族由 \\(\theta\\) 参数控制的概率分布函数族。我们希望通过它来估计真实的概率分布函数  \\(p\_{data}(\mathbf{\vec x})\\) ，也就是要估计 \\(\theta\\) 参数。

	最常用的估计准确是：最大似然估计。即：
	$$ \theta\_{ML}=\arg\max\_{\theta}p\_{model}(\mathbf X;\theta)\\\
	=\arg\max\_\theta\prod\_{i=1}^{m}p\_{model}(\mathbf{\vec x}\_i;\theta)$$
	由于概率的乘积会因为很多原因不便使用（如容易出现数值下溢出），因此转换为对数的形式：
	$$\theta\_{ML}=\arg\max\_\theta\sum\_{i=1}^{m}\log p\_{model}(\mathbf{\vec x}\_i;\theta) 	$$
	因为 \\(m\\) 与 \\(\theta\\) 无关，因此它也等价于：
	$$\theta\_{ML}=\arg\max\_\theta\sum\_{i=1}^{m}\frac 1m \log p\_{model}(\mathbf{\vec x}\_i;\theta) $$ 
	由于数据集的经验分布为：
	$$
 	\hat p\_{data}(\mathbf{\vec x})=\frac 1m\sum\_{i=1}^{m}\delta(\mathbf{\vec x}-\mathbf{\vec x}\_i) 
	$$
	其中 \\(\delta(\cdot)\\) 为狄拉克函数。因此：
	$$\theta\_{ML}=\arg\max\_\theta\mathbb E\_{\mathbf{\vec x}\sim \hat p\_{data}} \log p\_{model}(\mathbf{\vec x};\theta)  $$

2. 考虑数据集的经验分布  \\(\hat p\_{data}\\) 和真实分布函数的估计量 \\(p\_{model}\\) 之间的差异，`KL`散度为：
	$$D|\_{KL}(\hat p\_{data} || p\_{model};\theta) =\mathbb E\_{\mathbf{\vec x}\sim \hat p\_{data}}[\log \hat p\_{data}(\mathbf{\vec x})-\log  p\_{model}(\mathbf{\vec x;\theta})]$$
	由于 \\(\log \hat p\_{data}(\mathbf{\vec x})\\) 与 \\(\theta\\) 无关，因此要使得 \\(D|\_{KL}(\hat p\_{data} || p\_{model};\theta)\\) 最小，则只需要最小化 
	$$\mathbb E\_{\mathbf{\vec x}\sim \hat p\_{data}}[-\log  p\_{model}(\mathbf{\vec x};\theta)]$$
	也就是最大化
	$$\mathbb E\_{\mathbf{\vec x}\sim \hat p\_{data}} \log p\_{model}(\mathbf{\vec x};\theta) $$
	因此：最大似然估计就是最小化数据集的经验分布  \\(\hat p\_{data}\\) 和真实分布函数的估计量 \\(p\_{model}\\) 之间的差异

3. 最大似然估计可以扩展到估计条件概率。假设数据集 \\(\mathbf X=\\{\mathbf{\vec x}\_1,\mathbf{\vec x}\_2,\cdots,\mathbf{\vec x}\_m\\}\\)，对应的观测值为 \\(\mathbf Y=\\{y_1,y_2,\cdots,y_m\\}\\)。

	则条件概率的最大似然估计为：
	$$ \theta\_{ML}=\arg\max\_\theta P(\mathbf Y\mid \mathbf X;\theta)$$

	如果样本是独立同分布的，则可以分解成：
	$$\theta\_{ML}=\arg\max\_\theta\sum\_{i=1}^{m}\log P(y_i\mid \mathbf{\vec x}\_i;\theta) 	$$

4. 最大似然估计有两个很好的性质：
	- 在某些条件下，最大似然估计具有一致性。这意味着当训练样本数量趋向于无穷时，参数的最大似然估计依概率收敛到参数的真实值。这些条件为：
		- 真实分布 \\(p\_{data}\\) 必须位于分布函数族 \\(p\_{model}(\cdot;\theta)\\) 中。否则没有估计量可以表示  \\(p\_{data}\\) 
		- 真实分布 \\(p\_{data}\\)  必须对应一个 \\(\theta\\) 值。否则从最大似然估计恢复出真实分布 \\(p\_{data}\\)  之后，也不能解出参数  \\(\theta\\) 
	- 最大似然估计具有很好的统计效率`statistic efficiency`。即只需要较少的样本就能达到一个良好的泛化误差。

	因此最大似然估计通常是机器学习中的首选估计准则。当样本数量太少导致过拟合时，正则化技巧是最大似然的有偏估计版本。

## 四、贝叶斯估计

1. 在最大似然估计中，频率学派的观点是：真实参数 \\(\theta\\) 是未知的固定的值，而点估计 \\(\hat\theta\\) 是随机变量（因为数据是随机生成的，所以数据集是随机的）。

	贝叶斯学派认为：数据集时能够直接观测到的，因此不是随机的。而真实参数 \\(\theta\\) 是未知的、不确定的，因此 \\(\theta\\) 是随机变量。
	- 我们将对于 \\(\theta\\) 的已知的知识表示成先验概率分布 \\(p(\theta)\\)，表示我们在观测到任何数据之前，对于参数 \\(\theta\\) 的可能取值的一个分布。
	> 在机器学习中，我们一般会选取一个相当宽泛的（熵比较高）的先验分布，如均匀分布。

	- 假设观测到一组数据  \\(\mathbf X=\\{\mathbf{\vec x}\_1,\mathbf{\vec x}\_2,\cdots,\mathbf{\vec x}\_m\\}\\) ，根据贝叶斯法则，我们有：
	$$p(\theta\mid\mathbf X ) =\frac{p(\mathbf X\mid \theta)p(\theta)}{p(\mathbf X)}$$

2. 贝叶斯估计与最大似然估计有两个重要区别：
	- 贝叶斯估计预测下一个样本的分布为：
		$$p(\mathbf{\vec x}\_{m+1}\mid \mathbf{\vec x}\_1,\mathbf{\vec x}\_2,\cdots,\mathbf{\vec x}\_m) =\int p(\mathbf{\vec x}\_{m+1}\mid\theta)p(\theta\mid \mathbf{\vec x}\_1,\mathbf{\vec x}\_2,\cdots,\mathbf{\vec x}\_m)d\theta$$

		而最大似然估计预测下一个样本的分布为： \\(p\_{model} (\mathbf{\vec x} ;\theta) \\)

	- 贝叶斯估计会使得概率密度函数向着先验概率分布的区域偏移。

3. 当训练数据有限时，贝叶斯估计通常泛化性能更好。当训练样本数量很大时，往往计算代价较高。

4. 最大后验估计：有时候我们希望获取参数 \\(\theta\\) 的一个可能的值，而不仅仅是它的一个分布。此时可以通过最大后验估计选择后验概率最大的点：
	$$ \theta\_{MAP}=\arg\max\_{\theta} p(\theta \mid \mathbf X)\\\
	=\arg\max\_{\theta} \left[ \log p(\mathbf X\mid \theta) +\log p(\theta)\right]$$

	最大后验估计具有最大似然估计没有的优势：拥有先验知识带来的信息。该信息有助于减少估计量的方差，但是增加了偏差。

5. 有一些正则化方法可以被解释为最大后验估计，正则化项就是对应于  \\(\log p(\theta)\\) 。

## 五、基本机器学习
1. 支持向量机中最常用的核函数是高斯核 \\(k(\mathbf{\vec u},\mathbf{\vec v})=\mathcal N(\mathbf{\vec u}-\mathbf{\vec v};\mathbf{\vec 0};\sigma^{2}\mathbf I)\\)

	- 这个核也称作径向基函数`radial basis function:RBF`。因为其值从 \\(\mathbf{\vec u}\\) 沿着 \\(\mathbf{\vec v}\\) 向外辐射的方向减小。
	- 高斯核对应于无穷维空间中的点积

2. \\(k\\) 近邻法是个非参数学习算法，它没有任何参数。事实上它没有一个真正的训练阶段或者学习过程。
	- \\(k\\) 近邻法具有非常高的容量。这使得它在训练样本数量较大时能获得较高的精度。
	- 它的缺点有：
		- 计算成本很高。因为我们需要构建一个 \\(m\times m\\) 的核矩阵，其计算量为 \\(O(m^{2})\\)，当数据集时几十亿个样本时，计算量是不可接受的。
		- 在训练集较小时，返回能力很差
		- 它无法判断特征的重要性。假设有 1000 个特征，且输出就等于第一个特征，\\(k\\) 近邻法无法检测到这种最简单的模式。 

3. 如果我们运行学习任意大小的决策树，那么可以将决策树算法视作非参数算法。但是实践中经常有大小限制，从而使得决策树成为有参数模型。

	决策树的问题在于：由于决策树使用平行于坐标轴的拆分，使得一些对于逻辑回归很简单的问题很费力。比如：当 \\(x^{(2)}\gt x^{(1)}\\) 时为正类；否则为反类。这种拆分边界并不平行于坐标轴，使得决策树会用许多层的拆分来逼近这个边界。

4. 一个经典的无监督学习任务是寻找数据的最佳表示。常见的有：
	- 低维表示：视图将 \\(\mathbf{\vec x}\\) 中的信息尽可能压缩在一个较低维空间中
	- 系数表示：将数据嵌入到大多数项为零的一个表示中。系数表示通常需要增加维度
	- 独立表示：使数据的各个特征相互独立

5. `PCA`不仅将数据压缩到低维，它也使得降维之后的数据各特征相互独立。
	>注意：`PCA`推导过程中，并没有要求数据中心化；但是在推导协方差矩阵时，要求数据中心化。此时：
	$$ Var[\mathbf{\vec z}]=\frac{1}{m-1}\mathbf Z^{T}\mathbf Z=\frac{1}{m-1}\Sigma^{2}$$ 

6. 聚类问题本身是病态的。即：没有某个标准来衡量聚类的效果。
	- 我们可以简单的度量聚类的性质，如每个聚类的元素到该类中心点的平均距离。但是我们不知道这个平均距离对应于真实世界的物理意义。
	- 另外，可能很多不同的聚类都很好地对应了现实世界的某些属性，它们都是合理的。比如：在图片识别中包含的图片有：红色卡车、红色汽车、灰色卡车、灰色汽车。我们聚类成：红色一类、灰色一类；也可以聚类成：卡车一类、汽车一类。
	> 这个问题说明我们分布式表示的优点：我们可以对每个车辆赋予两个属性：一个表示颜色、一个表示型号。

## 六、随机梯度下降
1. 随机梯度下降时梯度下降的一个扩展，几乎所有的深度学习算法都用到了该算法。

2. 机器学习算法中，损失函数通常可以分解成每个样本的损失函数之和。如：
	$$J(\theta)=\mathbb E\_{\mathbf{\vec x },y\sim \hat p\_{data}}L(\mathbf{\vec x},y;\theta)=\frac 1m\sum\_{i=1}^{m}L(\mathbf{\vec x}\_i,y\_i;\theta) $$
	其中 \\(L\\) 是每个样本的损失函数 \\(L(\mathbf{\vec x},y;\theta)=-\log p(y\mid \mathbf{\vec x};\theta)\\)。

	总损失函数的梯度为：
	$$\nabla\_\theta J(\theta)=\frac 1m \sum\_{i=1}^{m}\nabla\_\theta L(\mathbf{\vec x}\_i,y\_i;\theta)$$
	其时间复杂度为 \\(O(m)\\)。当训练集规模为数十亿时，计算一步梯度将消耗非常长的时间。

	随机梯度下降法的核心是：梯度就是期望，而期望可以用小规模的样本来近似估计。具体来说：在算法的每一步我们从训练集中抽取小批量样本 \\(\mathbb  B=\\{\mathbf{\vec x}\_1,\cdots,\mathbf{\vec x}\_{m^{\prime}}\\}\\)，其中 \\(m^{\prime}\\) 通常是一个相对较小的数（通常从 1到几百）。更重要的是：随着 \\(m\\) 的增长， \\(m^{\prime}\\) 通常是固定的（比如在拟合几十亿的样本时，每次更新计算只适用几百个样本）。

	梯度的估计可以表示成：
	$$ g=\frac{1}{m^{\prime}}\nabla\_\theta \sum\_{i=1}^{m^{\prime}} L(\mathbf{\vec x}\_i,y\_i;\theta)$$

	然后随机梯度下降法的迭代为（\\(\epsilon\\) 为学习率）：
	$$\theta\leftarrow \theta-\epsilon g $$

3. 随机梯度下降法是大规模数据上训练大型线性模型的主要方法。
	> 非线性模型的主要方法是：结合核技巧的线性模型，以及深度学习模型。

## 七、传统机器学习的挑战
1. 传统机器学习算法的两个困难：
	- 维数灾难：当数据的维数很高时，很多机器学习问题变得相当困难。因为许多传统机器学习算法简单地假设：一个新样本的输出应该大致与最接近的训练样本的输出相同。
	- 某些算法偏好于选择某类函数。最广泛的隐式偏好是：我们要学习的函数是平滑先验或者局部不变性先验（即先验知识表明：我们要学习的函数是平滑的、或者局部不变的）。
		- 这个先验知识表明：我们要学习的函数不会在一个小区域内发生较大的变化。
		- 很多简单算法完全依赖此先验知识来达到良好的泛化。

2. 流形：指的是连接在一起的区域。如位于三维空间中的一个曲面就是一个流形。
	- 在机器学习中，我们运行流形的维数发生变化，这通常发生在流行与自身相交的情况。下图中为二维中嵌入的一维流形。在交叉的地方，流形的维数发生变化。

	![manifold](../imgs/5/manifold.jpg)

3. 如果我们期望机器学习算法处理 \\(\mathbb R^{n}\\) 上的所有输入时，很多机器学习问题看起来都是无解的。流形学习算法假设：\\(\mathbb R^{n}\\) 上的大部分区域都是无效的输入，我们感兴趣的输入只分布在 \\(\mathbb R^{n}\\) 中的某一组流行中。

	数据位于低维流形中的假设不一定总是正确的。但是在人工智能某些场景中，如涉及到图像、声音、文本时，流形假设是对的。因为：
	- 我们观察到：现实生活中有意义的图片、文本、声音的概率分布都是高度集中的。
	- 每个样本都被其他高度相似的样本包围，可以通过应用变换来遍历该流形。如一张人脸的图片，我们逐渐移动或者旋转图中的像素变成另一张人脸图片。

4. 当数据位于低维流形中时，我们使用流形坐标，而不是 \\(\mathbb R^{n}\\) 中的坐标。
	- 日常生活中，道路就是一维流形。我们使用道路编号，而不是三维空间的坐标去定位。
	- 提取低维流形中的坐标是非常具有挑战性的