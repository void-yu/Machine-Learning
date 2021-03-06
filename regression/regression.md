# 回归分析

> 回归是用于估计两种变量之间关系的统计过程。当用于分析变量和一个多变量之间的关系时，该算法能够提供很多建模和分析多变量的技巧。具体一点说，回国分析可以帮助我们理解任意一个自变量变化，另一个自变量不变时，因变量变化的典型值。最常见的是，回归分析能在给定自变量的条件下估计出因变量的条件期望。

### 普通最小二乘回归（Ordinary Least Squares Regression，OLSR）

> 最小二乘（又称最小平方法）是一种数学优化技术。它通过**最小化误差的平方和**寻找数据的最佳函数匹配。利用最小二乘法可以简便的求得未知数据，并使得这些求得的数据与实际数据之间的误差的平方和为最小。最小二乘法还可以用于曲线拟合。

均方误差有很好的几何意义，它对应了常用的欧式距离。

普通最小二乘回归，又称为线性二乘回归。下面是模型的推导：

![lr_1](https://raw.githubusercontent.com/clhchtcjj/Pit-for-Typora/master/lr_1.jpg)



更一般的情况是：多元线性回归，即样本由$d$个属性描述。下面是推导过程：

![lr_2](https://raw.githubusercontent.com/clhchtcjj/Pit-for-Typora/master/lr_2.jpg)

**潜在问题：**

> 在许多任务中，我们会遇到大量的变量，其数目甚至超过了样例数（$d>m$）,那么$X^TX$显然不满秩。此时可以求解出多个$\hat{\vec{w}}$，它们都可以使得均方误差最小化。选择哪一个解作为输出，将由学习算法的归纳偏好决定，常见的做法是引入正则化项。

**重点：**

1. **自变量与因变量之间必须要有线性关系；**
2. 多重共线性、自相关和异方差对多元线性回归的影响很大；
3. 线性回归对异常值非常敏感，其能严重影响回归线，最终影响预测值；
4. 在多元的自变量中，我们可以通过前进法、后退法和逐步法去选择在于显著的自变量。

**补充：**

多重共线性：多重共线性（Multicollinearity）是指线性回归模型中的解释变量之间由于存在精确相关关系或高度相关关系而使模型估计失真或难以估计准确[百度百科]。通俗的说，就是变量之间有较强的相关性，影响模型的预测能力。解决多重共线问题可以考虑一下几种方法：

1. 直接删除

2. 采用逐步回归法：逐步回归的基本思想是将变量逐个引入模型，每引入一个解释变量后都要进行F检验，并对已经选入的解释变量逐个进行t检验，当原来引入的解释变量由于后面解释变量的引入变得不再显著时，则将其删除。以确保每次引入新的变量之前回归方程中只包含显著性变量。这是一个反复的过程，直到既没有显著的解释变量选入回归方程，也没有不显著的解释变量从回归方程中剔除为止。以保证最后所得到的解释变量集是最优的。

   依据上述思想，可利用逐步回归筛选并剔除引起多重共线性的变量，其具体步骤如下：先用被解释变量对每一个所考虑的解释变量做简单回归，然后以对被解释变量贡献最大的解释变量所对应的回归方程为基础，再逐步引入其余解释变量。经过逐步回归，使得最后保留在模型中的解释变量既是重要的，又没有严重多重共线性。

3. 改变变量的表现形式：对变量进行非线性变化取log等，可以有效的降低变量之间的相关性

4. 增加样本数量

5. 引入正则化项

6. 进行主成分分析：通过主成分分析提取主要特征，从而忽略次要的成分，得到相关性很低的特征

## 局部加权线性回归（Locally weighted linear regression, LWLR）

在现实生活中，很多数据不一定能用线性模型描述，为了解决非线性建模线性模型的问题，我们在预测一个点的值的时候，选择与这个点相接近的点而不是所有的点做线性回归。基于这个思想，便产生了局部加权线性回归算法。在这个算法中，其他离一个点越近，权重越大，对回归系数的贡献就越多。

目标函数变为：$J(\vec{w})=\sum_i\lambda _i(y_i-\vec{w}^T\vec{x_i})^2$

其中，$\lambda _i = exp(-\frac{(x_i-x)^2}{2k^2})$。该函数称为指数衰减函数，其中k为波长参数，它控制了权值随距离下降的速率，该函数形式上类似高斯分布(正态分布)，但并没有任何高斯分布的意义。该算法解出回归系数如下：

模型的解为：$\vec{w}=(X^TWX)^{-1}X^TW\vec{y}$



## 岭回归（Ridge Regression）

如果数据的特征数目比样本的数目还多，那么输入的数据矩阵$X$将不是满秩矩阵（可以认为是列向量直接存在相关性）。非满秩矩阵不存在逆矩阵，即上面的方法无法应用。

简单来说，岭回归就是在矩阵$X^TX$上加一个$\lambda I从而使得矩阵非奇异。进而可以求逆。

其中$I$是$m\cdot m$d的单位矩阵，则回归系数的计算公式变为：

$(X^TX+\lambda I)^{-1}X^T\vec{y}$

岭回归是一种专用于共线性数据分析的有偏估计回归方法，实质上是一种改良的最小二乘法，通过放弃最小二乘法的无偏性，损失部分信息、降低精度为代价获得回归系数更为符合实际、更为可靠的回归方法，对共线性数据的拟合要强于最小二乘法。

目标函数可以写为：$J(\vec{w})=\sum_i(y_i-\vec{w}^T\vec{x_i})^2+\lambda \sum_j w_j^2=||\vec{y}-X\vec{w}||^2_2+\lambda ||\vec{w}||^2_2$

**重点：**

1. 除常数项以外，这种回归的假设与最小二乘回归类似；
2. 它收缩了相关系数的值，但没有达到零，这表明它没有特征选择功能
3. 这是一个正则化方法，并且使用的是L2正则化。
4. 岭回归是用来处理特征数多于样本数的情况；
5. 现也用于在估计中加入偏差，从而得到更好的估计；
6. 引入$\lambda$来限制了所有$\vec{w}$之和，用过引入该惩罚项，能够减少不重要的参数，这个技术在统计学中也叫缩减。缩减方法能够去掉不重要的参数，因此能够更好的理解数据。此外，与简单的线性回归相比，缩减法能够取得更好的预测效果。

## 套索回归（Lasso，The Least Absolute Shrinkage and Selection Operator）

Lasso中对回归参数的限制，在λ较小时，一些系数会因此被迫缩减到0，这个特性可以帮助我们更好地理解数据。但在这个新的约束条件下求解回归系数，需要使用二次规划算法，极大的增加了计算复杂度，不太适用。

目标函数可以写为：$J(\vec{w})=\sum_i(y_i-\vec{w}^T\vec{x_i})^2+\lambda \sum_j w_j^2=||\vec{y}-X\vec{w}||^2_2+\lambda ||\vec{w}||_1$

Lasso 回归与Ridge回归有一点不同，它使用的惩罚函数是绝对值，而不是平方。这导致惩罚（或等于约束估计的绝对值之和）值使一些参数估计结果等于零。使用惩罚值越大，进一步估计会使得缩小值趋近于零。这将导致我们要从给定的n个变量中选择变量。

**重点：**

1. 除常数项以外，这种回归的假设与最小二乘回归类似；
2. 它收缩系数接近零（等于零），这确实有助于特征选择；
3. 这是一个正则化方法，使用的是L1正则化；
4. 如果预测的一组变量是高度相关的，Lasso 会选出其中一个变量并且将其它的收缩为零。

L1正则项==稀疏，特征选择； L2正则项==防止过拟合

## ElasticNet回归

ElasticNet是Lasso和Ridge回归技术的混合体。它使用L1来训练并且L2优先作为正则化矩阵。当有多个相关的特征时，ElasticNet是很有用的。Lasso 会随机挑选他们其中的一个，而ElasticNet则会选择两个。

目标函数可以写为：$J(\vec{w})=\sum_i(y_i-\vec{w}^T\vec{x_i})^2+\lambda \sum_j w_j^2=||\vec{y}-X\vec{w}||^2_2+\lambda_1 ||\vec{w}||_1+\lambda _2||\vec{w}||_2^2$

**重点：**

1. 在高度相关变量的情况下，它会产生群体效应；
2. 选择变量的数目没有限制；
3. 它可以承受双重收缩。

## 逻辑回归

当因变量是二分类时（0/1、yes/no、true/false）时我们应该使用逻辑回归。

具体的可以[参考文章](http://www.52caml.com/head_first_ml/ml-chapter1-regression-family/)

**重点：**

1. 在二分类问题中使用的非常多；
2. 逻辑回归因其应用非线性log转换方法，使得其不需要自变量与因变量之间有线性关系；
3. 为了方式过拟合和欠拟合，我们应该确保每个变量都是显著的。应该使用逐步回归方法去估计逻辑回归；
4. 要求没有共线性；
5. 如果因变量是序数型的，则称为序数型逻辑回归；
6. 如果因变量有多个，则称为多项逻辑回归。



## 多项式回归

自变量的**指数**超过1，则称为多项式回归。

在这个回归技术中，最适合的不是一条直线，而是一条曲线。

**重点**

1. 在很多情况下，我们为了降低误差，会使用多项式回归，但这样会造成过拟合。
2. 解决上述问题的一个行之有效的方法是：数据可视化。即观测数据与模型的拟合程度。特别是要看曲线的结尾部分，看它的形状和趋势是否有意义。高的数据项往往会产生特别罕见的预测值。

## 逐步回归（stage wise）

当我们要处理多个自变量时，我们就需要这个回归方法。在这个方法中选择变量是通过自动过程实现的，不需要人工干预。

在这个过程中，是通过观察统计值，比如判定系数，t值和最小信息准则去筛选变量。逐步回归变量一般是基于特定的标准加入或移除变量来拟合回归模型。一些常用的逐步回归方法如下:

1. 标准逐步回归做两件事：只要是需要每一步它都会添加或移除一些变量；
2. 后退法是开始于所有变量，然后逐渐移除一些不显著的变量；
3. 前进法是开始于最显著的变量，然后在模型中逐渐增加次显著变量；
4. 这个模型技术的目的是为了用最少的变量去最大化模型的预测能力，也是一种降维技术。

下面介绍前向逐步回归算法

前向逐步回归算法属于一种贪心算法，即每一步都尽可能减少误差。一开始，所有的权重都设置为1，然后每一步所做的决策是对某个权重增加或减少一个很小的值。

```python
算法的伪代码为：
数据标准化，使其分布满足0均值和单位方差
在每轮迭代中：
	设置当前最小误差lowestError为正无穷
  	对每个特征：
    	增大或缩小：
      		改变一个系数得到一个新的w
        	计算新的w下的误差
          	如果当前误差Error小于当前最小误差loweatError：
            	设置Wbest等于当前的w
             将w设置为新的Wbest
```

