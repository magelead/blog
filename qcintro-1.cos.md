Title: 量子计算入门（一）
Date: 2021-10-11 8:56
Tags: 物理, 算法  





希望这个系列能为对量子计算感兴趣的人们提供一些帮助。

我将撰写一系列量子计算入门博客，从最基本的量子比特（Qubit）讲起，一直讲到量子质因数分解。每一章都会附有Python代码，您可以运行代码，创建量子电路，验证您所学到的理论是否与实验结果一致，我相信这会大大降低量子力学的神秘性。

> IBM的开源Python量子计算框架Qiskit，让我们可以足不出户，通过网络操作量子计算机。
>
> https://quantum-computing.ibm.com/



⚠️：整个系列的前三章内容与 Nielsen 的 [Quantum Computing for the Very Curious ](https://quantum.country/qcvc)高度一致，非常建议阅读原文。第四章 Shor 算法按照 Nielsen 的风格续写，尽量做到深入浅出。第五章内容待定，如果大家有什么意见，可以留言给我。



这个系列将解释量子计算机的工作原理。它不是研究论文，也不是基于各种简单类比的普及。我们将深入挖掘，以便您了解量子计算的细节。在此过程中，我们还将学习量子力学的基本原理，因为理解量子计算需要这些原理。

学习这些材料具有挑战性。量子计算和量子力学是著名的“难”学科，通常被认为是神秘而令人生畏的。但是我相信通过阅读这一系列博客，任何有好奇心和有决心的人都可以深刻地理解量子计算。

当然，您需要一些数学知识来理解这篇文章。我假设您熟悉复数和线性代数——矢量、矩阵等。我还将假设您了解传统计算机中使用的逻辑门——诸如 AND、OR、NOT 等。

如果您缺少相关的数学知识储备，您需要获得它们。如何做到这一点取决于您之前的经验和学习偏好——没有一刀切的方法，您需要自己弄清楚。但您可能会觉得有帮助的两个资源是： (1) [3Blue1Brown](https://www.youtube.com/playlist?list=PLZHQObOWTQDPD3MizzM2xVFitgF8hE_ab) 关于线性代数的一系列YouTube 视频；(2) [Gil Strang ](https://ocw.mit.edu/courses/mathematics/18-06-linear-algebra-spring-2010/video-lectures/)更深入的线性代数讲座。尝试一下，如果您发现它们有帮助，请继续。如果没有，请探索其他资源。

人们似乎试图避免学习数学。如果您环顾网络，就会发现很多关于量子计算的华丽介绍都避开了数学。例如，YouTube 上有许多相当漂亮的视频。它们看起来很有趣，更好的那些会给您一些类比来帮助您理解量子计算。但他们都不能够令真正好奇的人满足。坦率地说，如果他们不解释实际的底层数学模型，那么您可能会花费数年时间观看和重新观看此类视频，而您永远不会真正理解它。这就像和一群篮球运动员一起出去玩，听他们谈论篮球。您可能会喜欢它，并感觉好像您正在学习篮球。但除非您真的花很多时间打球，否则您永远学不会打篮球。要理解量子计算，您绝对必须精通数学模型。

数学从来都不是障碍，它是人类发明的最好的工具。



目录

1. 入门量子计算

2. 量子搜索算法

3. 量子隐形传态

4. 量子质因数分解

5. 待定

# 1. 入门量子计算

如果人类曾经接触过外星智能，这些外星人会拥有电脑吗？在科幻小说中，外星人的计算机是司空见惯的。如果这是正确的，这意味着外星人可以通过某种方式独立于人类发现计算机。毕竟，如果外星人独立发明了可口可乐、神奇宝贝或哈利波特书籍，我们会感到非常惊讶。如果外星人也拥有计算机，我们反而会觉得很自然。那是因为**计算机**是人类和外星文明都会产生的**一个终极问题的答案**。

在地球上，计算机的主要发明者是英国数学家艾伦·图灵。在他 1936 年发表的论文中，图灵并不是要发明一个聪明的小工具或创建一个行业。相反，他是在试图攻破由德国数学家大卫希尔伯特在 1928 年提出的关于数学本质的问题。这听起来很深奥，但理解希尔伯特和图灵的思想要点是值得的，因为它阐明了计算机的来源，以及计算机将何去何从的未来。

> Alan M. Turing, On Computable Numbers, with an Application to the Entscheidungsproblem (1936).

在他的职业生涯中，希尔伯特对数学知识的终极极限很感兴趣：原则上，人类能够对数学了解到什么程度，以及数学的哪些（如果有）部分永远不为人类所知？粗略地说，希尔伯特 1928 年的问题，讨论是否存在数学家可以遵循的通用算法，该算法可以让他们弄清楚任何给定的数学陈述是否可证明。希尔伯特希望的算法有点像，用于将两数字相乘的纸笔算法。除了不是从两个数字开始，您会从一个数学猜想开始，在完成算法的步骤之后，您会知道这个猜想是否可以证明。该算法在实践中使用起来可能太耗时，但如果存在这样的算法，那么至少在原则上数学是可知的。

1928 年，算法的概念还很模糊。到那时为止，算法通常是由人类使用纸和铅笔来执行的，如刚才提到的乘法算法或长除法算法。思考希尔伯特的问题迫使图灵准确地表达算法的含义。为此，图灵描述了我们现在所说的图灵机：可以执行任何算法的单一、通用可编程计算设备。

今天，我们已经习惯了计算机可以通过编程来做许多不同的事情的想法。然而，在图灵时代，通用可编程计算机的想法是非凡的。图灵认为，只要提供了正确的程序，单个固定设备就可以模仿任何算法过程。这是想象力的惊人飞跃，也是现代计算的基础。

为了论证他的机器可以**模仿**任何算法过程，图灵考虑了人类数学家在执行算法时可以执行的所有运算。对于每一种运算，他必须证明他的机器总是可以做同样的事情。他的论点太长，无法在这里完整重现，但看看图灵推理的风格很有趣，也很有启发性：

> 计算通常是通过在纸上书写某些符号来完成的。我们可以假设这张纸像儿童算术书一样被分成正方形。在初等算术中，纸张的二维特性有时会被使用到。但是这样的使用总是可以避免的，我认为人们会同意纸张的二维特性不是计算的本质。我假设计算是在一维纸上进行的，即在分成正方形的磁带上进行。……计算者的行为 [图灵指的是执行算法的人] 任何时候都取决于他正在观察的符号，以及当时他大脑状态。我们可以假设有一个界限B，B是计算者在某一时刻可以观察到的符号或正方形的数量。如果他想观察更多，他必须使用连续的观察。我们还将假设需要考虑的大脑状态数量是有限的。

显然，这是一个非正式的启发式论证！引用孩子的算术书或某人的大脑状态不是严格的、完美无缺的论点。但图灵的论点足以令人信服，以至于后来的数学家和科学家在很大程度上都愿意接受它。图灵机成为了黄金标准：我们可以在图灵机上执行算法。从那时起，计算已经发展成为一个产业，基于图灵模型的计算机已经售出数十亿台。

尽管如此，图灵的分析还是有一些令人不安的地方。他可能在关于什么是算法的非正式推理中遗漏了一些东西？1985 年，英国物理学家 David Deutsch 提出了一种更深入的方法来定义算法的含义。

> David Deutsch, “Quantum theory, the Church-Turing principle and the universal quantum computer” (1985). 

多伊奇（Deutsch）指出，每一个算法都是由物理系统执行的，无论是拿着纸笔的数学家，还是算盘这样的机械系统，还是现代计算机。多伊奇然后考虑了以下问题：

是否有（单个）通用计算设备可以有效地**模拟**任何其他物理系统？

如果有这样的设备，您可以用它来执行任何算法，因为算法必须在某种物理系统上执行。因此，该设备将是一台真正通用的计算机。更重要的是，多伊奇指出，您不需要像图灵那样依赖非正式的、启发式的论据来证明您的算法概念是正确的。您可以使用物理定律来证明您的设备是通用的。

那么让我们回到开篇的问题：外星人会拥有电脑吗？上面多伊奇的问题是一个关于宇宙本质的简单而基本的问题。这是多伊奇的外星人同行可能也会思考的问题。他们所属的文明都将不可阻挡地去发明计算机。

**从这个意义上说，计算机不仅仅是人类的发明。它们是宇宙的基本特征，是关于宇宙如何运作的一个简单而深刻的问题的答案。它们很可能被许多外星智能一次又一次地发现。**

多伊奇是一位拥有量子力学背景的物理学家。他观察到，基于图灵模型的普通日常计算机在模拟量子力学系统方面存在很多问题。

> Yu Manin 和 Richard Feynman 等研究人员此前已经观察到了这一点，并因此推测了基于量子力学的计算机。

特别是，它们在进行此类模拟时似乎异常缓慢且效率低下。多伊奇被迫发明了一种新型的计算系统——量子计算机。量子计算机可以做传统计算机可以做的一切，但也能够有效地模拟量子力学过程。因此，它们可以说是比传统计算机更自然的计算模型。如果我们遇到外星人，我敢打赌他们会使用量子计算机（或者，也许会有量子计算大脑）。毕竟，外星人的技术很可能比当前的人类文明先进得多。



## 1.1 量子比特



比特（Bit）：数学上的 0 或者 1，物理上，一个比特可以被任何一种物理系统所表示，只要这种物理系统总是处在两个不同状态中的一个。常见的例子有：具有正反两面的银币，只有开和关的电子开关，只允许两个不同电压的电路。

计算机是由许多比特组成的，与之相似，量子计算机是由量子比特（Qubit）组成的。

> 量子比特也被称为量子位。

像比特一样，量子比特也有状态。比特的状态要么是 0，要么是 1。量子比特的状态却是一个**矢量**。

更准确地说，量子比特的状态是一个存在于二维复矢量空间的矢量。这个矢量空间被叫做状态空间。

比如，这是量子比特的一个可能状态：
$$
\left[ \begin{array}{c} 1 \\ 0 \end{array} \right]
$$

物理上，量子比特可以是任何一种**双态系统**，它拥有两个不同的基态，对应于二维矢量空间中的基矢。

比如，电子的自旋，向上或向下。又或者是光子的极化，垂直极化和水平极化。在一个经典的系统中，一个比特必须处在两个状态中的一个，这时100%确定的。然而，量子力学允许量子比特处在一种两个基态的叠加态中，即一个任意二维矢量可以被两个基矢量表示。当您观察量子比特时，结果总是是随机的从两个基态种挑选一个，您永远无法预先确定的知道它的结果。叠加态是量子力学和量子计算的基本性质。

小结：

（a) 量子比特有状态，叫做量子状态（Quantum State）（b）就像比特，这个状态是一种抽象的数学描述（c）比特的抽象状态是0或1，而量子状态是一个二维矢量（d）量子状态所生存的空间被称为状态空间，它本质上是一个二维矢量空间。

如果您规定永远只使用二维矢量空间中的基矢来存储信息，而不考虑任何叠加态。量子比特就变的与比特并无区别了。

如何用量子状态表示比特的状态0呢？在上文中其实已经提到了，为了书写方便，我们将给它一个特殊的符号$|0\rangle$
$$
|0\rangle := \left[ \begin{array}{c} 1 \\ 0 \end{array} \right]
$$
那么比特状态1呢？
$$
|1\rangle := \left[ \begin{array}{c} 0 \\ 1 \end{array} \right].
$$
虽然我们所做的只是找到了一种看起来新奇的符号，$|0\rangle$ 或 $|1\rangle$ ，来表示矢量罢了。但是您最好把 $|0\rangle$  或  $|1\rangle$ 作为整体看成一种新的符号，代表了一种新的数学对象。由于它们是矢量空间中的基矢，在量子计算领域里经常被叫做计算基础状态（Computational Basis State）。

> $|\;\rangle$ 其实是被狄拉克发明的ket符号，并在量子力学中被广泛运用。这种符号为量子状态的矢量表示增加了一层额外的抽象。 (Modern Quantum Mechanics by J. J. Sakurai)



## 1.2 量子比特的任意状态

计算基础状态$|0\rangle$ 和 $|1\rangle$ 只是一个量子比特可以处于的无穷多种状态中的两个。毕竟，一个量子状态是一个二维矢量。这是一个比特作为标量所不具备的特质。

这里有一个图示，强调了量子态的矢量本质。

> 为了方便，今后我将混用量子状态，量子态，态，它们是一个意思。



![img](https://imghub-1258224042.cos.ap-nanjing.myqcloud.com/images/qcintro-1/cbs.png)

在这个例子中，态 0.6$|0\rangle$ + 0.8$|1\rangle$代表了一种叠加态，它表示0.6倍的$|0\rangle$基矢加上0.8倍的$|1\rangle$基矢。也可以用常见的矩阵符号表示如下


$$
0.6|0\rangle + 0.8 |1\rangle = 0.6 \left[ \begin{array}{c} 1 \\ 0 \end{array} \right] + 0.8 \left[ \begin{array}{c} 0 \\ 1 \end{array} \right] = \left[ \begin{array}{c} 0.6 \\ 0.8 \end{array} \right].
$$

一般来说，量子态是复矢量，也就是说，它们可以有复数作为条目。例如，另一个量子态是矢量

$$
\frac{1+i}{2} |0\rangle + \frac{i}{\sqrt{2}} |1\rangle,
$$
它可以被写为
$$
\left[ \begin{array}{c} \frac{1+i}{2} \\ \frac{i}{\sqrt{2}} \end{array} \right].
$$

因为量子态是复矢量，上面的图示并不准确。那个平面是一个二维实数矢量存在的空间，而不是复数矢量。但是，可视化量子态任然是一种有意义的思考方式。更准确的可视化方法是[布洛赫球](https://qiskit.org/textbook/ch-states/representing-qubit-states.html#3.-The-Bloch-Sphere-)（Bloch Sphere）。

到这里您已经了解了量子态的数学表示，但您肯定有很多疑问。比如，为什么量子态是矢量，为什么是复数？

这些都是很好的问题。但他们确实需要一些时间来回答。就上下文而言，量子力学的发现不是一个单一的事件，而是发生在 1900 年至 1925 年 25 年的工作中。许多诺贝尔奖都是因为这一过程中的里程碑而获得的。这包括阿尔伯特·爱因斯坦的诺贝尔奖，主要因与量子力学（不是人们有时认为的相对论）相关的工作而获奖。

当世界上一些最聪明的人为发展一个理论而奋斗了 25 年时，这注定不是一个显而易见的理论！事实上，使用二维复矢量描述简单量子系统的想法总结了人们 25 年来人们认识到的大部分知识。从这个意义上说，这是一个非常简单而美丽的陈述。但这不是一个明显的陈述，可能需要几个小时才能理解。这比花 25 年才能理解要好！

让我们总结一些常用的关于量子态名词。

最常见的术语之一是叠加（Supperpositon）。人们会说像这样的状态$0.6|0\rangle+0.8|1\rangle$是一个叠加的$|0\rangle$ 和 $|1\rangle$. 他们的意思其实是状态是$|0\rangle$ 和 $|1\rangle$是一种线性组合。 您可能想知道为什么他们不只是说“线性组合”（有时他们会说），应为这两个词来了来自不同的文化和不同的历史。

另一个常用术语是振幅（Amplitude）。振幅是某个基矢在叠加状态下的系数。例如，在状态 $0.6|0\rangle+0.8|1\rangle$ 中， $|0\rangle$的振幅是0.6，$|1\rangle$的振幅是0.8。

对于一个一般的量子比特的状态来讲，两个振幅都可以是复数，让我们用$\alpha$ 和$\beta$ 来表示，

$$
\alpha|0\rangle+\beta|1\rangle
$$

这里有一个限制条件，振幅模的平方和必须等于一，这叫做归一化条件。
$$
|\alpha|^2+|\beta|^2 = 1
$$
稍后您将会学到，这与用振幅来解释随机物理现象的概率有关。

一句话总结：一个量子比特的状态用一个存在于二维复矢量空间的单位矢量来表述，这个空间叫做状态空间。



**例1：用Qiskit创建具有任意状态的量子比特$\alpha|0\rangle+\beta|1\rangle$**

此代码涉及到一个还未介绍的概念：量子电路（Quantum Circuit）。量子电路是一种对量子计算可视化的表达，它对于我们理解量子计算至关重要。我们会在1.3节介绍它，不用太担心。

> 以下代码需要安装Python Qiskit Library。您也可以直接在云端运行，[IBM Quantum Lab](https://quantum-computing.ibm.com/)。

```python
from math import sqrt, pi
from qiskit import QuantumCircuit, BasicAer, execute
from qiskit.visualization import plot_bloch_multivector

# change alpha beta as you want
alpha = 1/sqrt(2)
beta = 1j/sqrt(2)

# 2D complex vector representing the quantum state
vector = [alpha, beta]

# create a quantum circuit with only one qubit
qc = QuantumCircuit(1)
# initialzie the qubit as the vector we defined
# arg 0 is the index of the only qubit we created
qc.initialize(vector, 0)


# simulate the state
# note that statevector_simulator is used for calculating statevector (Quantum State)
backend = BasicAer.get_backend('statevector_simulator')
result = execute(qc, backend).result()
state = result.get_statevector(qc)

# plot the state on a Bloch sphere
plot_bloch_multivector(state)
```

**Output:**

![img](https://imghub-1258224042.cos.ap-nanjing.myqcloud.com/images/qcintro-1/bloch_positive_i.png)



## 1.3 量子逻辑门

为了进行量子计算，仅仅了解量子态是不够的。我们还需要能够利用他们做一些有有意义的事！我们使用量子逻辑门 (Quantum Logic Gates)来做到这一点。

> 门其实就是量子力学中的算子。

量子逻辑门只是一种操纵量子信息的方式，量子信息被量子状态表示。它们类似于普通日常计算机中使用的经典逻辑门——诸如 AND、OR 和 NOT 之类的门。而且，就像经典门在传统计算机中的作用一样，量子门是量子计算的基本构建模块。它们也是描述许多其他量子信息处理任务的便捷方式（例如量子隐形传态）。

特别是，就像任何经典计算都可以使用 AND、OR 和 NOT 门构建一样，我们在接下来的几节中描述的量子门足以进行任何量子计算。

我们将提到的许多量子门都是基于熟悉的经典逻辑门。但有几个是不同的。这些差异看起来几乎是微不足道的。但这些差异就是量子计算“超能力”，以及量子计算机远优于经典计算机的可能性。

坦率地说，这一节内容会有点啰嗦。





### 1.3.1 量子非门



毫无疑问，您可以猜测，量子非门（Quantum NOT Gate）是经典非门的推广。在计算基础上，量子非门完全符合您的预期，模仿经典非门。也就是说，它将 $|0\rangle$ 状态变为 $|1\rangle$，反之亦然：
$$
NOT|0\rangle = |1\rangle
$$

$$
NOT|1\rangle = |0\rangle
$$



将非门应用于一般叠加态，它是一个线性算符，
$$
NOT (\alpha|0\rangle +\beta|1\rangle) = \alpha|1\rangle+\beta |0\rangle.
$$

由于历史原因，从事量子计算的人们通常使用不同的符号，量子非门的符号是$X$而不是NOT。

到目前为止，我们表达 $X$ 门工作的方式用的是的非常代数的方法。还有另一种表示，量子电路（Quantum Circuit）表示。在量子电路中，如果我们描绘一个 $X$ 门，将如下所示：

![img](https://imghub-1258224042.cos.ap-nanjing.myqcloud.com/images/qcintro-1/X_gate.png)

从左到右的线就是所谓的量子线(Quantum Wire)。量子线代表单个量子位。术语“线”及其绘制方式看起来就像量子位在空间中移动。但是，将从左到右绘制的线视为代表时间的流逝才是正确的。所以最初的线段代表时间的流逝，量子位没有任何变化。然后$X$ 门被应用到量子位 ，量子位的状态改变。时间继续流逝，量子位的状态不再变化。



有时我们会将输入和输出的状态明确地画在量子电路中，比如：

![img](https://imghub-1258224042.cos.ap-nanjing.myqcloud.com/images/qcintro-1/X_gate_decorated.png)

事实上，这是我们的第一个量子计算实例。一个简单的计算，只涉及一个量子位和一个门，但仍然是一个真正的量子计算！您好，世界！

$X$ 门的第三种表示也值得了解，它可以表示为 $2 \times 2$ 矩阵：
$$
X = \left[ \begin{array}{cc} 0 & 1 \\ 1 & 0 \end{array} \right].
$$
**Proof**:
$$
\begin{aligned} &  X (\alpha|0\rangle + \beta|1\rangle)=  \left[ \begin{array}{cc} 0 & 1 \\ 1 & 0 \end{array} \right] (\alpha|0\rangle + \beta|1\rangle) \\ & = \left[ \begin{array}{cc} 0 & 1 \\ 1 & 0 \end{array} \right] \left[ \begin{array}{c} \alpha \\ \beta \end{array} \right] \\ & = \left[ \begin{array}{c} \beta \\ \alpha \end{array} \right] \\ & = \alpha|1\rangle+\beta|0\rangle .\end{aligned}
$$

这告诉我们 $X$ 及其矩阵表示对所有矢量的有完全相同作用，因此是相同的运算。事实上，我们稍后将看到的，所有量子门都可以被认为是矩阵，矩阵条目指定了门的确切细节。 



顺便说一下，将 $X$ 门视为矩阵可以澄清之前可能令人困惑的一个地方。我将 $X$ 门写为具有作用 $X|0\rangle = |1\rangle$,$ X|1\rangle = |0\rangle$。隐含地——我从来没有这么说过，尽管这是真的——$X$ 门是一个数学函数，将输入状态转换为输出状态。但是当我们写函数的时候，我们通常使用括号，那么为什么我不写 $X(|0\rangle)$ 和类似的$X(|1\rangle)$  呢？原因是对于线性函数，即矩阵，通常省略括号而只写 $X|0\rangle$ - 函数应用只是矩阵乘法。



>  非门：交替 $|0\rangle$ 和 $|1\rangle$。



**例2：量子计算的Hello World！**

```python
from qiskit import QuantumCircuit, BasicAer, execute

# create a quantum circuit with one qubit and one bit
qc = QuantumCircuit(1,1)  
# apply not gate to the qubit with index 0
qc.x(0)
# measue the qubit with index 0 and store the result to bit with index 0
qc.measure(0,0)
# plot the circuit
qc.draw('mpl')
```


```python
# simulation: run the quantum circuit 1000 times and print all results
backend = BasicAer.get_backend('qasm_simulator') 
result = execute(qc, backend, shots=1000).result()
counts  = result.get_counts(qc)
print(counts)
```

**Output:**

![img](https://imghub-1258224042.cos.ap-nanjing.myqcloud.com/images/qcintro-1/hello_world.png)

```pyt
{1: '1000'}
```

如何理解这个结果？Qiskit默认量子比特q最开始处于$|0\rangle$态。然后我们使用了非门，q变成了$|1\rangle$态。最后，我们测量了量子比特q，并把结果储存在比特c上。我们重复了这一实验1000次。应为量子比特q没有处于叠加态，所以测量结果是确定的。无论我们测多少次，它永远都是1。稍后我们会看更多的例子，测量的随机性会有所体现。

> 单线表示量子比特，双线表示比特。

### 1.3.2 量子线

量子线：为什么最简单的量子电路往往也是最难实现的?

最简单的量子电路什么都不做。也就是说，它是单根量子线：

![img](https://imghub-1258224042.cos.ap-nanjing.myqcloud.com/images/qcintro-1/identity_circuit.png)



这个电路只是一个不随时间变化量子比特。更明确地说，如果将某个任意量子态 $|\psi\rangle$ 输入到电路中，则输出会是完全相同的状态 $|\psi\rangle$（使用希腊字母 $\psi$表示任意量子态是一种通常的做法）：



![img](https://imghub-1258224042.cos.ap-nanjing.myqcloud.com/images/qcintro-1/identity_circuit_labelled.png)

在数学上，这个电路是微不足道的。但在物理上它远非微不足道。在许多物理系统中，量子线实际上是最难实现的量子计算之一！

原因是量子态通常非常脆弱。如果您的量子位存储在某个微小系统中——可能是单个光子或单个原子——那么很容易破坏该状态。改变一个原子或一个光子的状态真的不需要太多能量。因此，虽然量子线在数学上是微不足道的，但它们可能是在真实系统中最难构建的的元素之一。



尽管如此，有一些系统很容易实现量子线。如果您将量子位存储在中微子中，那么该状态实际上会得到很好的保存。原因是中微子几乎不与其他形式的物质相互作用——中微子可以轻松穿过一英里的铅而不受干扰。但是，虽然中微子如此稳定令人着迷，但这并不意味着它们可以制造出好的量子比特。问题是，由于没有简单的方法可以使普通物质与它们相互作用，我们无法以受控方式操纵它们的量子态，因此无法实现量子门。

这里有一种微妙的平衡，适用于对进行量子信息处理的许多设计方案，而不仅仅是中微子。如果我们想存储量子状态，如果我们的量子位仅与其他系统进行非常微弱的交互，那么这些系统就不会破坏它们，这会很有帮助。但是，如果量子位与其他系统的交互作用很小，那么也很难操纵量子位。因此，能制造出好的量子线的系统通常很难为其构建量子门。设计量子计算机的大部分艺术都是关于寻找解决这种紧张局势的方法。通常，这意味着要尝试设计大部分时间相互作用较弱的系统，但有时可能会引起强烈相互作用，因此可以实现量子门。

> 量子线只是一个不随时间变化量子比特。但很难实现，因为量子态通常很脆弱。



### 1.3.3 多门量子电路



我们来看一个简单的多门量子电路。它是一个有两个连续 $X$ 门的电路：

![img](https://imghub-1258224042.cos.ap-nanjing.myqcloud.com/images/qcintro-1/double_X_gate.png)

净效应是恢复原始量子状态，无论该状态是什么。换句话说，这个电路相当于一条量子线：

![img](https://imghub-1258224042.cos.ap-nanjing.myqcloud.com/images/qcintro-1/double_X_gate_identity.png)

理解这一点的一种方法是将 $X$ 应用于任意输入状态 $\alpha|0\rangle+\beta|1\rangle$ 两次。

第二种方式是基于我们之前为 $X$ 门找到的矩阵表示。

我们观察到 $XX$ 等价于
$$
\begin{aligned} XX & = \left[ \begin{array}{cc} 0 & 1 \\ 1 & 0 \end{array} \right] \left[ \begin{array}{cc} 0 & 1 \\ 1 & 0 \end{array} \right] \\ & = \left[ \begin{array}{cc} 1 & 0 \\ 0 & 1 \end{array} \right]. \end{aligned}
$$

> 进行矩阵乘法是分析量子电路的一种非常常见的方法

### 1.3.4 哈达玛门

当然，与经典的非门相比，$X$ 似乎并没有做太多事情。在本节中，我将介绍一个明确涉及量子效应的门，即哈达玛（Hadamard）门。

与 $X$ 门一样，我们将首先解释哈达玛门如何作用于计算基础状态。用 $H$ 表示该门，这是它的作用：

$$
H|0\rangle = \frac{|0\rangle +|1\rangle}{\sqrt 2}
$$

$$
H|1\rangle = \frac{|0\rangle -|1\rangle}{\sqrt 2}
$$

当然，$|0\rangle$ 和 $|1\rangle$ 并不是唯一的量子态。 哈达玛门如何作用于更一般的量子态？

就像量子非门一样，它是线性的：
$$
H(\alpha|0\rangle + \beta|1\rangle) = \alpha \left( \frac{|0\rangle+|1\rangle}{\sqrt 2} \right) + \beta \left( \frac{|0\rangle-|1\rangle}{\sqrt 2} \right).
$$

$$
H(\alpha|0\rangle + \beta|1\rangle) = \frac{\alpha+\beta}{\sqrt 2} |0\rangle + \frac{\alpha-\beta}{\sqrt 2}|1\rangle
$$

我们通常不会处理如此复杂的表达式。我在这里这样做的唯一原因是想要更加清晰。我们将主要使用 $H$ 门的电路和矩阵表示（见下文）。

事实上，量子计算的很多工作都是研究如何从低抽象层次到更高抽象层次的办法。到目前为止，我们的大部分工作都处于非常低的水平，似乎更像是线性代数的练习，而不是对新计算模型的讨论。这也许看起来很奇怪。毕竟，如果您要向某人解释经典计算机，您就不会从最琐碎的地方开始，比如如何使用 AND 门和 NOT 门等。您将从设计良好的高级编程语言开始，然后在不同的抽象层之间来回切换。

我希望我可以从量子计算机的高级抽象开始。然而，我们仍处于量子计算的早期，在很大程度上，人类还没有发明如此高层次的抽象。人们仍在努力探索。

这是一个令人兴奋的情况：这意味着几乎所有重大突破都在前方。从某种意义上说，我们对量子计算仍然知之甚少。这听起来可能令人惊讶：毕竟，有关于这个主题的大而厚的[教科书](https://www.amazon.com/Quantum-Computation-Information-10th-Anniversary/dp/1107002176/ref=sr_1_2?s=books&ie=UTF8&qid=1541350337&sr=1-2)。但是您可以写一本关于1940 年代后期的 ENIAC计算机的大而厚的教科书。毕竟，这是一个非常复杂的系统。那本教科书看起来很吓人，但它不会是计算领域的最终定论。在大多数情况下，我们今天理解量子计算的方式是在类似于ENIAC 的水平上，查看量子位、逻辑门和线性代数的具体细节，并想知道更高层次的理解可能是什么。这种情况可以被认为很像在产生 Lisp 和Haskell 以及 Prolog 和 Smalltalk 等语言的突破之前的编程语言设计。这使它成为一个非凡的创造机会，是未来几十年和几个世纪的挑战。

现在让我们回到正题。这是哈达玛门的电路表示。它看起来就像电路表示中的 $X$ 门，只是将门标签更改为 $H$:

![img](https://imghub-1258224042.cos.ap-nanjing.myqcloud.com/images/qcintro-1/H_gate.png)

$H$ 的矩阵表示为：
$$
H = \frac{1}{\sqrt 2} \left[ \begin{array}{cc} 1 & 1 \\ 1 & -1 \end{array} \right].
$$



为了验证这个矩阵的正确性，让我们检查矩阵作用于$|0\rangle$ 和$|1\rangle$ 的效果：
$$
\begin{aligned} \frac{1}{\sqrt 2} \left[ \begin{array}{cc} 1 & 1 \\ 1 & -1 \end{array} \right] |0\rangle & = \frac{1}{\sqrt 2} \left[ \begin{array}{cc} 1 & 1 \\ 1 & -1 \end{array} \right] \left[ \begin{array}{c} 1 \\ 0 \end{array} \right] \\ & = \frac{1}{\sqrt 2} \left[ \begin{array}{c} 1 \\ 1 \end{array} \right] \\ & = \frac{|0\rangle+|1\rangle}{\sqrt 2}. \end{aligned}
$$

$$
\begin{aligned} \frac{1}{\sqrt 2} \left[ \begin{array}{cc} 1 & 1 \\ 1 & -1 \end{array} \right] |1\rangle & = \frac{1}{\sqrt 2} \left[ \begin{array}{cc} 1 & 1 \\ 1 & -1 \end{array} \right] \left[ \begin{array}{c} 0 \\ 1 \end{array} \right] \\ & = \frac{1}{\sqrt 2} \left[ \begin{array}{c} 1 \\ -1 \end{array} \right] \\ & = \frac{|0\rangle-|1\rangle}{\sqrt 2}. \end{aligned}
$$



因此，矩阵在 $|0\rangle$ 和 $|1\rangle$ 状态上的作用与哈达玛门相同。根据矩阵乘法的线性，矩阵在所有输入状态上的也作用与哈达玛门相同，因此它们是相同的运算。

是什么让哈达玛门作为量子门而强大？我们可以用它做什么？

我们还没有（相当）足够的知识背景来对这些问题给出准确的答案。但是有一个类比可以提供一些灵感。

想象一下，在数千年之前，您生活在北非，并且出于某种原因决定前往伊比利亚半岛。如果您还没有船或其他可靠的方法来穿越大片水域，您需要一路穿越非洲，经过阿拉伯半岛，再经过欧洲，回到伊比利亚半岛：

![img](https://imghub-1258224042.cos.ap-nanjing.myqcloud.com/images/qcintro-1/Africa_Europe_by_land.jpg)

资料来源：Reto Stöckli, [NASA Earth Observatory](https://visibleearth.nasa.gov/view.php?id=73884) (2004)。

然而，假设您发明了一种新设备，即船，它扩大了您可以到达的位置范围。然后，您可以采取更直接的路线前往伊比利亚半岛，大大缩短所需的时间：

![img](https://imghub-1258224042.cos.ap-nanjing.myqcloud.com/images/qcintro-1/Africa_Europe_by_sea.jpg)

哈达玛门和类似的门的作用是扩大计算机可以执行的运算的范围。这种扩展使量子计算机可以走捷径，因为量子计算机以传统经典计算机无法实现的方式“移动”。而且，我们希望，这能让我们更快地解决一些计算问题。

另一个有用的类比是国际象棋游戏。想象一下，您正在下棋，规则发生了对您有利的变化，从而使您的车子的移动范围扩大了。这种额外的灵活性使您能够更快地将军。

类似的事情发生在哈达玛门上。通过扩展我们可以访问的状态范围，超出经典计算机可能的范围，我们可以在计算中走捷径。我们将在后续部分中看到明确的示例。

为了更熟悉哈达玛门，让我们分析一个简单的电路：

![img](https://imghub-1258224042.cos.ap-nanjing.myqcloud.com/images/qcintro-1/H_H_circuit.png)



这个电路是做什么的？

在我们计算之前，值得停下来尝试猜测结果。猜测的重点不是让它正确——而是挑战自己，学会建立启发式的思维模型来思考量子电路中发生的事情。那些心智模型一开始可能不会很好，但没关系——如果您继续这样做，它们会变得更好。

这是您可以用来考虑该电路的一种启发式方法：您可以将 $H$ 视为将 $|0\rangle$ 和 $|1\rangle$ 状态混合在一起。因此，如果您将 $H$ 两次应用于 $|0\rangle$，也许它会将 $|0\rangle$ 和 $|1\rangle$ 更彻底的混合在一起。根据这种启发式方法，您认为会产生什么样的状态？您相信结果吗？为什么或者为什么不？您能想到其他可以帮助您猜测答案的启发式方法吗？

好的，让我们计算一下实际发生的情况。我们把第一个哈达玛门作用于 $|1\rangle$ 状态，我们得到：
$$
\frac{|0\rangle-|1\rangle}{\sqrt{2}}.
$$
然后我们应用第二个哈达玛门得到：
$$
\frac{1}{\sqrt 2} \left( \frac{|0\rangle+|1\rangle}{\sqrt 2} - \frac{|0\rangle-|1\rangle}{\sqrt 2} \right).
$$
$|0\rangle$ 项相互抵消，而 $|1\rangle$ 项加强。当我们合并这些项时，我们看到输出只是 $|1\rangle$ 状态，与我们开始时相同。

该电路的净效应与量子线完全相同：

![img](https://imghub-1258224042.cos.ap-nanjing.myqcloud.com/images/qcintro-1/H_H_identity.png)

有另一种方式可以理解这一点。如果我们向电路输入一个任意量子态$|\psi\rangle$，那么输出一定是$HH|\psi\rangle$，即两个$H$矩阵应用于$|\psi\rangle$的结果. 但如果您只计算矩阵乘积 $HH$，结果是 $2 \times 2$ 单位矩阵，$H H = I$。因此电路的输出必须与输入相同，$HH|\psi\rangle = I |\psi\rangle = |\psi\rangle$，就像量子线一样。

当然，这个结果违反了我们的直觉猜测，即两个哈达玛会将 $|0\rangle$ 和 $|1\rangle$ 彻底混淆。思考我们的直觉出了什么问题是很有趣的，比如通过查看 $H$ 对 $|0\rangle$ 作用两次的计算。您会看到，在第二个门之后，$|1\rangle$ 项完全相互抵消，而 $|0\rangle$ 项相互加强。



尽管这种运算并不复杂，但我还是请您注意它。因为这种类型的相互抵消或加强在许多量子计算算法中至关重要。在不深入细节的情况下，许多此类算法的粗略工作方式是首先使用哈达玛门“扩散”量子态，即把基态变成叠加态。在算法结束时，他们使用巧妙的抵消和加强模式将叠加态重新组合成一个（或可能是少数几个）基态，包含所需的答案。这是一个有点含糊且可能诱人的描述，我们会之后详细的进行讨论。



**练习：**验证 $HH = I$，其中 $I$ 是 $2 \times 2$ 单位矩阵，
$$
I = \left[ \begin{array}{cc} 1 & 0 \\ 0 & 1 \end{array} \right].
$$

**练习：** 假设我们定义了一个矩阵 $J$ 而不是 $H$：
$$
J := \frac{1}{\sqrt 2} \left[ \begin{array}{cc} 1 & 1 \\ 1 & 1 \end{array} \right]
$$
乍一看，$J$ 似乎会形成一个有趣的量子门，与 $H$ 类似。例如，$J|0\rangle = \frac{|0\rangle+|1\rangle}{\sqrt 2}$ 和 $J|1\rangle = \frac{|0\rangle+|1\rangle}{ \sqrt 2}$。这些都是良好的、归一化的量子态。但是如果我们将 $J$ 应用于量子态 $\frac{|0\rangle-|1\rangle}{\sqrt 2}$ 会发生什么？为什么这使得 $J$ 不适合用作量子门？

**练习：** 考虑量子电路：

![img](https://imghub-1258224042.cos.ap-nanjing.myqcloud.com/images/qcintro-1/H_X_circuit.png)

解释为什么这个电路的输出是 $XH|\psi\rangle$，而不是 $HX|\psi\rangle$？您可能会天真地假设，应该按照门在电路中出现的顺序进行运算。这是一个需要注意的常见问题——它会发生是因为量子门在电路中从左到右代表了从先到后，而矩阵乘法从右到左组成代表了从先到后。

**例3：哈达玛门**

```python
from qiskit import QuantumCircuit, BasicAer, execute
from qiskit.visualization import plot_histogram

# create a quantum circuit with one qubit and one bit
qc = QuantumCircuit(1,1)
# apply hadamard gate on qubit 0
qc.h(0)
# measure qubit 0 to bit 0
qc.measure(0,0)
# plot the circuit
qc.draw('mpl')
```
**Output:**

![img](https://imghub-1258224042.cos.ap-nanjing.myqcloud.com/images/qcintro-1/image-20211016192118340.png)

---

```python
# simulation: run the quantum circuit 1000 times and print all results
# note that qasm_simulator is used for measurements
backend = BasicAer.get_backend('qasm_simulator') 
result = execute(qc, backend, shots=1000).result()
counts  = result.get_counts(qc)
print(counts)
```

**output:**

```
{'0': 499, '1': 501}
```
---
```
# visualize results
plot_histogram(counts)
```

**output:**

![img](https://imghub-1258224042.cos.ap-nanjing.myqcloud.com/images/qcintro-1/image-20211016192556740.png)

一开始量子比特q默认处于$|0\rangle$态，应用一个哈达玛门后，量子比特q处于叠加态
$$
\frac{1}{\sqrt{2}}\left(|0\rangle +|1\rangle \right)
$$

测量该量子比特q并把结果储存在比特c里，c有两种可能的结果，c=0有50%概率，或c=1有50%概率。

我们准备1000个相同的$|0\rangle$态输入电路，重复1000次测量，统计实验结果为测到0有499次，测到1有501次，并根据此数据估计频率画出了直方图。



### 1.3.5 测量一个量子比特

假设一位（假设的！）量子物理学家 Alice 在她的实验室中准备了**一个**量子位，处于量子态 $\alpha|0\rangle+\beta|1\rangle$。然后她将她的量子比特交给另一位量子物理学家 Bob，但没有告诉他 $\alpha$ 和 $\beta$ 的值。 Bob 有什么办法可以算出 $\alpha$ 和 $\beta$ 吗？也就是说，Bob 可以做一些实验来找出量子态的身份吗？

这个问题令人惊讶的答案是NO! 事实上，**如果 $\alpha$ 和 $\beta$ 一开始是未知的**，就没有办法找出它们。 **换一种方式来说，任何系统的量子态——无论是量子位还是其他系统——都不能直接观察**。



我说这很令人惊讶，因为它与我们习惯的世界如何运作的方式大不相同。如果您的汽车出现问题，机械师可以使用诊断工具来了解发动机的内部状态。诊断工具越好，他们可以知道的东西就越多。当然，引擎的某些部分可能无法访问——例如，他们可能不得不破坏某个部分，或者使用显微镜。但是，如果机械师告诉您物理定律禁止他们弄清楚发动机的内部状态，您可能会相当怀疑。

同样，当您第一次开始学习量子电路时，直觉上似乎我们应该能够随时观察量子态的振幅。但事实证明，这是物理定律所禁止的。这些振幅最好被视为一种隐藏信息。

那么，我们可以从量子态中得出什么信息？除了以某种方式测量 $\alpha$ 和 $\beta$，其实还有其他方法可以从量子位中获取有用的信息。让我描述一个特别重要的过程，称为计算基础状态中的测量（Measurement in the Computational Basis）。这是量子计算中的一个基本术语：这是我们通常从量子计算机中提取信息的方式。我现在将解释它如何适用于单个量子位，然后推广到多量子位系统。

假设一个量子位处于 $\alpha|0\rangle+\beta|1\rangle$ 状态。当您**在计算基础状态中测量这个量子位**时，它会为您提供一些经典的信息：它以概率 $|\alpha|^2$ 给出结果 0，以概率 $|\beta|^2$ 给出结果 1。

为了更具体地考虑这个过程，假设您的量子位在某个物理系统中被实例化。也许它以某种方式以原子状态存储。究竟是什么并不重要，但是您的实验室中有这个量子位。您有一些测量设备，可能是一些大而复杂的设备，可能涉及激光和微处理器以及一个用于读取测量结果的屏幕。这个测量设备以某种方式与您的量子位交互。

在测量交互之后，您的测量设备会记录一个结果。例如，您可能得到结果 0。或者您得到结果 1。关键的事实是结果是普通的经典信息——您非常熟悉的东西——然后您可以使用它做其他事情，并控制其他进程。

因此，量子计算的工作方式是我们使用一系列量子门来操纵量子态，然后在计算结束时（通常）我们进行测量以读出计算结果。如果我们的量子计算机只是一个量子位，那么结果将是一个经典位（Bit）。如果，更常见的情况是，它是多个量子位，那么测量结果将是多个经典位。

这个测量过程的一个基本事实是它会改变量子位原本的状态。测量后，如果得到结果 0，那么测量之后的量子位的状态（“后验状态”）就是 $|0\rangle$。另一方面，如果得到结果 1，则量子位的后验状态是 $|1\rangle$。

总结一下：如果我们在计算基础状态上测量一个状态为 $\alpha |0\rangle+\beta|1\rangle$ 的量子位，那么结果会是一个经典位。它要么为 0，概率为 $|\alpha|^ 2$ ；要么为 1，概率为 $|\beta|^2$。测量后量子位对应的状态为$|0\rangle$或$|1\rangle$。

需要注意的一个关键点是，对某个量子位测量后，无论结果如何，该量子位的$\alpha$ 和 $\beta$ 都没有了。无论后验状态是$|0\rangle$还是$|1\rangle$，都没有$\alpha$或$\beta$的痕迹。因此，您无法获得有关它们的更多信息。从这个意义上说，$\alpha$ 和 $\beta$ 是一种隐藏信息——测量不会告诉您它们是什么。

**这是很重要一点，因为它意味着您不能在一个量子位中存储无限量的经典信息。** 毕竟，$\alpha$ 是一个复数，您可以想象在其中存储等价于大量经典位的信息。比如 $\alpha$ 的实部的二进制展开式，$\Re(\alpha)=10\ldots1$，这个位串（Bit String）可以无限长。如果有一些实验方法可以精确测量 $\alpha$ 的值，那么您就可以提取无限量的经典信息。但是如果没有测量 $\alpha$ 的方法，那是不可能的。



> 我们一直在讨论单个物理系统，但要凭经验确定概率，我们必须事先准备出许多的完全相同的物理系统，它们构成一个集合（Ensemble）。我们对整个集合进行测量，得到许多测量结果，通过分析测量结果的频率来估计概率。（ J. J. Sakurai Modern Quantum Mechanics）



我一直在谈论对计算基础状态的测量。事实上，您还可以在量子系统中进行其他类型的测量。但从某种意义上说，计算基础状态中的测量结果是最基本的。原因是通过将计算基础状态测量与量子门（如哈达玛门和非门（以及其他门）相结合，可以模拟任意量子测量。因此，理论上来讲，您已经掌握了绝对需要了解的有关测量的全部信息。



如何在量子电路中表示测量？这是一个简单的例子：

![img](https://imghub-1258224042.cos.ap-nanjing.myqcloud.com/images/qcintro-1/X_H_measure.png)





这是一个单量子位量子电路，输入状态为 $|\psi\rangle$。应用非门，然后是哈达玛门。该电路以一个对计算基础状态的测量结束，由略微拉长的半圆表示。 $m$ 是表示测量结果的经典位 ， 0 或 1。我们使用双线表示经典位 $m$ 。



关于测量的最后一个评论是它与我们之前讨论的量子态的归一化条件有关。 假设我们有量子态：
$$
\alpha|0\rangle+\beta|1\rangle
$$
那么两个可能的测量结果 0 和 1 的概率之和必须为 1，所以我们有：
$$
|\alpha|^2+|\beta|^2 = 1.
$$
这正是量子态的归一化条件——即，量子态的长度必须为 1。该约束的起源实际上只是测量概率加起来必须为 1 的事实。



**练习：** 假设我们得到了状态 $\frac{|0\rangle+|1\rangle}{\sqrt 2}$ 或状态 $\frac{|0\rangle-|1\rangle }{\sqrt 2}$，但没有告诉我们得到了哪个状态。我们想弄清楚我们被赋予了哪种状态。如果我们只是进行计算基础状态测量，那么对于这两种状态，我们都会以 $\frac{1}{2}$ 的概率得到结果 0，以 $\frac{1}{2}$ 的概率得到结果 1。所以我们不能直接使用计算基础状态测量来区分它们。但是假设我们将状态放入以下电路中：

![img](https://imghub-1258224042.cos.ap-nanjing.myqcloud.com/images/qcintro-1/distinguish.png)

证明如果输入状态 $\frac{|0\rangle+|1\rangle}{\sqrt 2}$ 则输出为 $m = 0$，概率为 1，而如果状态 $\frac{|0\rangle-|1\rangle}{\sqrt 2}$ 是输入然后输出是 $m = 1$ 的概率为 1。因此，虽然这两种状态如果只是在计算基础状态上测量是无法区分的，但可以通过一个简单的量子电路的帮助来区分。

### 1.3.6 通用单量子比特量子门

到目前为止，我们已经了解了两个量子门，即非门和哈达玛门，以及可用于从我们的量子电路中提取经典信息的测量过程。在本节中，我将回到量子门，看看最通用的单量子位门。

让我们先复习一下非门和哈达玛门的矩阵表示：
$$
\begin{aligned} X = \left[ \begin{array}{cc} 0 & 1 \\ 1 & 0 \end{array} \right]; \,\,\,\, H = \frac{1}{\sqrt 2} \left[ \begin{array}{cc} 1 & 1 \\ 1 & -1 \end{array} \right] \end{aligned}
$$

如果这些门的输入是量子态 $|\psi\rangle$，则输出分别是 $X|\psi\rangle$ 和 $H|\psi\rangle$。

一般的单量子位门的工作原理类似。特别是，一般的单量子位门可以表示为 $2\times 2$ 幺正矩阵(Unitary Matrix) $U$。 （如果您生疏了，我会马上提醒您一个矩阵是幺正矩阵意味着什么，您现在可以把它当成一个矩阵就行。）如果门的输入是状态 $| \psi\rangle$ 那么门的输出是 $U|\psi\rangle$。因此非门和哈达玛门分别对应于 $U = X$ 和 $U = H$ 的特殊情况。



矩阵 $U$ 是幺正矩阵是什么意思？用代数方式回答这个问题最容易，它只是意味着 $U^\dagger U = I$，即$U$的共轭转置乘以$U$，等于单位矩阵。
$$
U^\dagger := (U^T)^*.
$$

所以对于一个 $2 \times 2$ 矩阵，它的共轭转置就是：
$$
\left[ \begin{array}{cc} a & b \\ c & d \end{array} \right]^\dagger = \left[ \begin{array}{cc} a^* & c^* \\ b^* & d^* \end{array} \right].
$$

> 幺正矩阵也被称为酉矩阵。

您可能会问一些基本问题：为什么单量子位门由酉矩阵描述？我们如何才能直观地了解矩阵的幺正性意味着什么？虽然方程 $U^\dagger U = I$ 很容易用代数检查，但我们希望对该方程的含义有一些直觉上的理解。

我们稍后将在下一节回答这个问题。

**练习：** 证明 $X$ 是幺正的。

**练习：** 证明单位矩阵 $I$ 是酉矩阵。

**练习：** 您能找到一个 $2 \times 2$ 矩阵的例子，它是酉矩阵，不是 $I$、$X$ 或 $H$？ 



现在，让我们看看其他几个单量子位量子门。非门 $X$是由物理学家 Wolfgang Pauli 在量子力学的早期引入的。他还引入了了另外两个矩阵，$Y$ 和 $Z$，它们也是有用的量子门。三个门，$X$、$Y$ 和$Z$ 统称为泡利矩阵。 $Y$ 和 $Z$ 门将是我们量子门工具包中有用的额外工具；就前面的类比而言，它们扩展了我们可以使用的所有运算。例如，它们在量子隐形传态和量子纠错等协议中至关重要。



$Y$ 门类似于$X$ 门，但在非对角线上不是1，而是有$-i$ 和$i$，  $Y|0\rangle=i|1\rangle$  and $Y|1\rangle=-i|0\rangle$ :
$$
Y := \left[ \begin{array}{cc} 0 & -i \\ i & 0 \end{array} \right].
$$

$Z$ 门保持 $|0\rangle$ 不变，并将 $|1\rangle$ 带到 $-|1\rangle$：
$$
Z := \left[ \begin{array}{cc} 1 & 0 \\ 0 & -1 \end{array} \right].
$$

**练习：** 证明 $Y$ 和 $Z$ 矩阵是酉矩阵，因此是合法的量子门。

另一个有用的单量子位量子门是 $P$ 门，它有一个参数 $\theta$，表示绕着布洛赫球的$Z$轴旋转的度数。
$$
P(\theta)=\left[ \begin{array}{cc} 1 & 0 \\ 0 & e^{i\theta} \end{array} \right].
$$

$$
P(\theta)(\alpha|0\rangle+\beta1\rangle)=\alpha|0\rangle+e^{i\theta}\beta|1\rangle
$$



这些就是目前我们需要掌握的单量子比特量子门。在接下来的几节中，我们将建立一些关于幺正性意味着什么的直觉，但我们不会向我们的量子计算模型添加任何额外的元素。事实上，到了现在，我们几乎知道量子计算所需的一切。只需要两个额外的元素：将我们对单个量子位的描述扩展到多个量子位，以及描述一个简单的双量子位量子门。我们很快就会讨论这些事情——毫不奇怪，它们看起来很像我们已经看到的。



**例3：X门，Y门，Z门，P门**

```python
from math import pi
from qiskit import QuantumCircuit, BasicAer, execute

# create a quantum circuit with one qubit and one bit
qc = QuantumCircuit(1)

qc.p(pi/2, 0) # try different gates: qc.x(0), qc.y(0), qc.z(0), qc.p(pi/2, 0)

# plot the circuit
qc.draw('mpl')
```

**Output:**

![img](https://imghub-1258224042.cos.ap-nanjing.myqcloud.com/images/qcintro-1/image-20211016194953643.png)

---

```python
# simulation: run the quantum circuit 1000 times and print all results
# note that unitary_simulator is used for finding unitary matrices
backend = BasicAer.get_backend('unitary_simulator') 
result = execute(qc, backend, shots=1000).result()
result.get_unitary(qc, decimals=3)
```

**Output:**

```python
array([[1.+0.j, 0.+0.j],
       [0.+0.j, 0.+1.j]])
```



### 1.3.7 为什么是酉矩阵？

对于复矢量 $z=(z_1,...,z_n)$，其长度（范数）定义为

$$
\|z\|= \sqrt{\sum_{i=1}^{n}|z_i|^2}=\sqrt{\sum_{i=1}^n z_i^* z_i}.
$$

我们可以直观地了解矩阵是酉矩阵意味着什么吗？事实证明，酉矩阵保留其输入的长度。换句话说，如果我们取任何矢量 $|\psi\rangle$ 并计算长度 $\| U|\psi\rangle \|$ 它总是等于原始矢量的长度 $\||\psi\rangle\|$。在这方面，它们很像普通（真实）空间中的旋转或反射，它们也不会改变长度。从某种意义上说，酉矩阵是真实旋转和反射的复杂推广。

到目前为止，我们主要处理的矢量是单个量子比特的状态，即归一化的二维矢量。但实际上，最后一段的陈述对于 $d \times d$ 酉矩阵也是正确的，即 $\|U|\psi\rangle\| = \||\psi\rangle\|$ 对于 $d$ 维度的任何矢量 $|\psi\rangle$ 为真。

证明实际上非常简单——大多数情况下，我们只是计算 $\|U|\psi\rangle \|$ 并使用一些代数来检查它是否等于长度 $\||\psi\rangle\|$。但在我们得到证明之前，请注意这个结果对量子门来说是个好消息。原因是在量子门中，输入状态将被归一化（长度为 1），因为这是对量子状态的要求。我们希望相应的输出状态也能被归一化，否则它就不是一个合法的量子状态。幸运的是，酉矩阵的长度保持特性确保了输出状态被正确归一化。

您应该还记得：量子态也是归一化的，因为测量概率之和必须为 1。量子门是酉矩阵和量子态是归一化的是的我们的量子态始终具有很好的概率解释。

事实上，酉矩阵是唯一以这种方式保持矢量长度的矩阵。因此，看待酉矩阵的一个好方法是，它们是能保持矢量长度的矩阵类。这就是代数条件 $U^\dagger U = I$ 的几何解释（和直观意义）。

现在让我们证明酉矩阵确实能够保持矢量的长度。

**证明：** 证明很简单。主要是计算 $\|U|\psi\rangle\|$。实际上，计算长度的平方更容易一些，$\|U|\psi\rangle\|^2$。这只是矢量 $U|\psi\rangle$ 分量模的平方和：
$$
\|U|\psi\rangle\|^2 = \sum_j (U\psi)_j^* (U\psi)_j
$$
请注意，我使用 $(U\psi)_j$ 来表示 $U|\psi\rangle$ 的第 $j$ 个分量，为了方便去掉了完整的 ket 符号，只使用了 $\psi$。下一步，我们将展开上面的等式，并寻找机会应用 $U$ 的幺正性。特别地，分量 $(U\psi)_j$ 由 $\sum_l U_{jl} \psi_l$ 给出，对于复共轭项也类似。所以我们可以将上面的等式改写为：
$$
\|U|\psi\rangle\|^2 = \sum_{jkl} U_{jk}^*\psi_k^* U_{jl}\psi_l
$$

为了利用 $U$ 的幺正性，我们将把 $U$ 项移到一起，并以 $U^\dagger$ 的形式重写。这给出
$$
\|U|\psi\rangle\|^2 = \sum_{jkl} U_{kj}^\dagger U_{jl} \psi_k^* \psi_l,
$$
我们交换了第一个 $U$ 上的 $jk$ 的顺序，以便将其改写为 $U^\dagger$。 $j$ 指标出现在此等式中的唯一位置是 $U_{kj}^\dagger U_{jl}$ 项。因此，我们可以对 $j$ 进行求和，这些项变为 $(U^\dagger U)_{kl}$，这只是单位矩阵中的第 $kl$ 项，因为$U^\dagger U = I$。所以上面的等式变成：
$$
\|U|\psi\rangle\|^2 = \sum_{kl} \delta_{kl} \psi_k^* \psi_l.
$$

当我们求和时， $\delta_{kl}=1$  当 $k=l$ 时，其他情况下它是 0。因此我们可以去一个求和指标，方程简化到：
$$
\|U|\psi\rangle\|^2 = \sum_{k} \psi_k^* \psi_k.
$$
显然，右侧等于 $|\psi\rangle$ 范数的平方。所以我们已经证明了 $\|U|\psi\rangle\|^2 = \||\psi\rangle\|^2$，因此 $\|U|\psi\rangle\| = \||\psi\rangle\|$。这就完成了酉矩阵是长度保持矩阵的证明。

事实上，正如我之前提到的，也可以证明酉矩阵是唯一以这种方式保持长度的矩阵。让我更准确地叙述一下：

**定理：** 让 $M$ 是一个矩阵。然后 $\|M|\psi\rangle \| = \||\psi\rangle\|$ 对于所有矢量 $|\psi\rangle$ 当且仅当 $M$ 是幺正的。

我们已经证明了其中的一半；我们马上将证明另一半。这个定理回答了我们之前的许多问题：我们明白为什么量子门必须是幺正的，因为它们是唯一保持归一化的矩阵。当然，它并没有完全回答这个问题，因为它首先没有告诉我们为什么量子门应该是矩阵（即线性运算）。我们虽然要接受这个事实。但事实上，我们可以更深入地理解为什么这是真的。但在本节中，我们将停留在酉矩阵能够保持长度这种程度。

**为什么幺正矩阵是唯一保留长度的矩阵？**

好的，让我们证明刚才遗漏的部分：让我们证明酉矩阵是唯一保持长度的矩阵。

证明有点长。但您会发现，这是熟悉一些新的标准的量子力学术语和符号的好方法。坦率地说：虽然这些术语在量子计算中非常有用，但我们在第一章的其他地方并不严格需要它们（尽管我们将在后面的部分中使用）。如果您想跳过证明或略读它，没关系，这确实是可以跳过的部分。但是在某些时候，您应该回来理解这一部分。无论如何，这个证明具有一定的美感。

到目前为止，我们一直在处理二维矢量，但是无论我们处理多少维，我要说的都适用。所以假设我们有一个矢量 $|\psi\rangle$ 可以是以分量的形式写成：
$$
|\psi\rangle = \left[ \begin{array}{c} a \\ b \\ \vdots \\ z \end{array} \right]
$$
我们将定义一个新对象，也用 $\psi$ 标记，但现在用另一个方向的括号：
$$
\langle \psi| := [a^* b^* \dots z^*].
$$

也就是说，$\langle\psi|$ 是一个行矢量，其条目与 $|\psi\rangle$ 相同，但复共轭。矢量 $|\psi\rangle$ 被称为 ket，并且$\langle \psi|$ 被称为 bra，使其成为 bra-ket 或 bracket符号。是的，理论物理学家也会开一些老掉牙的玩笑。这些名称由理论物理学家保罗·狄拉克 (Paul Dirac) 在 1939 年命名，通常称为狄拉克  bra-ket (Dirac bra-ket) 符号，有时也称为狄拉克 (Dirac) 符号。

关于bra $\langle \psi|$ 的一个关键事实是它通过匕首操作（共轭转置）与 ket $|\psi\rangle$ 相关：
$$
|\psi\rangle^\dagger = \langle \psi|.
$$
很容易理解为什么这个等式是真实的。取矢量
$$
|\psi\rangle = \left[ \begin{array}{c} a \\ b \\ \vdots \\ z \end{array} \right],
$$
并应用匕首操作，这意味着取转置，将其转换为包含条目 $a, b, \ldots, z$ 的行矢量，然后取复共轭，得到 $[a^* b^* \ldots z^*]$，这就是$\langle \psi|$的定义。以类似的方式，我们看到 $\langle \psi|^\dagger = |\psi\rangle$。

另一个有用的恒等式是用狄拉克符号表示 $|\psi\rangle$ 的长度：
$$
\||\psi\rangle\|^2 = \langle \psi| |\psi\rangle.
$$
也就是说，长度的平方正好等于行矢量 $\langle \psi|$ 与列矢量 $|\psi\rangle$ 的乘积。为了证明它，请注意右侧是
$$
[a^* b^* \ldots ] \left[ \begin{array}{c} a \\ b \\ \vdots \end{array} \right] = |a|^2 + |b|^2 + \ldots
$$
当然，这就是 $\||\psi\rangle\|^2$，正如我们想要的那样。

使用狄拉克符号的物理学家通常不会写出 $\langle \psi| |\psi\rangle$.他们稍微简化了一下，省略了中间的竖线之一，将其写为：
$$
\langle \psi| \psi \rangle.
$$
这只是一个轻微的简化，但事实证明，省略额外的竖线让生活变得更加轻松，而且非常值得。这只是一种省略竖线的情况，在其他情况也经常会省略垂线。在实践中，它很少引起混淆，尽管您当然需要习惯它。

另一个有用的恒等式是，如果 $M$ 是一个矩阵，而 $|\psi\rangle$ 是一个 ket，那么
$$
(M|\psi\rangle)^\dagger = \langle \psi|M^\dagger.
$$
如果您把 $\langle \psi|$ 看作是 $|\psi\rangle$ 的匕首，那么这个等式的意思是，取乘积 $M|\psi\rangle$ 的匕首 等价于 取$M$ 的匕首和 $|\psi\rangle$ 的匕首，然后相乘，但顺序颠倒。这很容易证明。作为对自己的挑战，您可能想立即停下来尝试证明。如果您遇到困难，可以参考底下的证明。

**证明：** 证明等式的方法是应用定义。我们将查看左侧的第 $j$ 个分量，$(M|\psi\rangle)^\dagger_j$，我们将证明它等于右侧的第 $j$ 个分量。
$$
\left(M|\psi\rangle\right)^\dagger_j=\left(M_{jk} \psi_k \right)^\dagger=M^*_{jk}\psi^*_k
$$

$$
\left(\langle\psi|M^\dagger\right)_j = \psi^*_k M^\dagger_{kj} =M^*_{jk}\psi^*_k
$$

其中重复指标需要[求和](https://mathworld.wolfram.com/EinsteinSummation.html)，对任意复矢量$z$，我们有$z_j^\dagger=z_j^*$。



这里有一个练习供您完成，会用到之前的一些符号。

**练习：** 证明对于任何矩阵 $M$ 和矢量 $|\psi\rangle$，以下恒等式成立，用于表示 $M|\psi\rangle$ 的长度：
$$
\|M|\psi\rangle\|^2 = \langle\psi|M^\dagger M |\psi\rangle.
$$

**证明：** 让 $|\phi\rangle = M|\psi\rangle$，
$$
\|M|\psi\rangle\|^2=\|\phi\rangle\|^2 = \langle\phi|\phi\rangle=\langle\psi|M^\dagger M |\psi\rangle,
$$

其中我们使用了
$$
\langle\phi|=|\phi\rangle^\dagger=\langle\psi|M^\dagger.
$$


您可能想知道为什么我们关心所有这些等式？当然，实际上我只是在提前学习它们——我们稍后会在证明中会用到。它们将长度与矩阵运算联系起来。这并不奇怪——我们将假设我们有一个保持长度的运算。能够将该运算与有关矩阵乘法的运算联系起来会是非常有帮助的。这是我们对上述等式有兴趣的原因。

好吧，我们一直在研究一些沉重、详细的材料。在证明**为什么幺正矩阵是唯一保留长度的矩阵**之前，我们还得学习一点背景知识，我保证只有一点点。

我将引入一个单位矢量（矢量），表示为 $|e_j\rangle$，意思是在第 $j$ 个分量为 1，其他地方为 0 的矢量。因此，例如，对于一个量子位：
$$
\begin{aligned} |e_0\rangle & = \left[ \begin{array}{c} 1 \\ 0 \end{array} \right] \\ |e_1\rangle & = \left[ \begin{array}{c} 0 \\ 1 \end{array} \right] \end{aligned}
$$

线性代数告诉我们，如果 $M$ 是一个矩阵，那么 $M|e_k\rangle$ 只是 $M$ 的第 $k$ 列。 （如果您不记得了，我鼓励您停下来弄清楚为什么它是真的。）由此您可以很容易地推断出 $\langle e_j|M|e_k\rangle$ 是 $M$ 的第 $jk$ 个条目。

好的，这些关于符号的讨论已经足够了！让我们进入主要事件。特别地，让我们回忆一下我们要完成证明的定理的陈述。还记得我们在之前证明了定理的反向意义，所以我们只需要证明正向意义：

**定理：** 让 $M$ 是一个矩阵。然后 $\|M|\psi\rangle \| = \| |\psi\rangle\|$ 适用于所有矢量 $|\psi\rangle$ 当且仅当 $M$ 是幺正的。

**证明：** 假设我们已经知道 $M$ 是长度保留的，我们需要分析矩阵 $M^\dagger M$。我们的目标是证明这是单位矩阵，因此 $M$ 是酉矩阵。为此，我们将首先分析对角元素 $(M^\dagger M)_{jj}$，并证明它们都等于 1。然后我们将注意力转向 off -diagonal 元素并表明它们都等于 0。

为了理解对角元素，我们从前面知道： $(M^\dagger M)_{jj} = \langle e_j|M^\dagger M|e_j\rangle = \|M|e_j\rangle\|^2 $.但由于 $M$ 是长度保留的，后者只是 $\||e_j\rangle\|^2$，即 1。因此我们得出结论，所有对角线元素确实都是 1。

非对角元素呢，即 $(M^\dagger M)_{jk}$ where $j \neq k$？我们能证明这些都等于 0 吗？好吧，我们想要做的是以某种方式将 $(M^\dagger M)_{jk}$ 与某个矢量 $M|\psi\rangle$ 的长度相关联，然后使用长度保留属性。一种想法是尝试使用 $|\psi\rangle = |e_j\rangle + |e_k\rangle$，因为这涉及 $j$th 和 $k$th 方向。从长度保持属性我们有：
$$
\|M|\psi\rangle\|^2 = \| |\psi\rangle\|^2 = 1^2+1^2 = 2.
$$
我们还有：
$$
\begin{aligned} \|M|\psi\rangle\|^2 & = \langle \psi| M^\dagger M |\psi\rangle \\ & = \langle e_j|M^\dagger M|e_j\rangle + \langle e_j|M^\dagger M|e_k\rangle \\ & \hphantom{ == } + \langle e_k|M^\dagger M|e_j\rangle + \langle e_k|M^\dagger M |e_k\rangle \\ & = 1 + \langle e_j|M^\dagger M|e_k\rangle + \langle e_k|M^\dagger M|e_j\rangle + 1 \end{aligned}
$$
比较最后两组方程，我们有：
$$
\langle e_j|M^\dagger M|e_k\rangle + \langle e_k|M^\dagger M|e_j\rangle = 0.
$$
这与我们想要的很接近，但并不完全正确。它告诉我们 $(M^\dagger M)_{jk} + (M^\dagger M)_{kj} = 0$。我们能做得更好吗？您也许想掉过头继续尝试找到某种方法来消除这些项中的一个或另一个。但是没有直接的方法可以做到这一点——至少，我所知道的没有直接的方法。



但是如果我们做一些稍微不同的事情，不使用 $|\psi\rangle = |e_j\rangle+|e_k\rangle$ ，我们使用 $|\psi\rangle = |e_j\rangle+i|e_k \rangle$?遵循相同的推理思路，我们将再次得到一个涉及 $(M^\dagger M)_{jk}$ 的方程，这似乎很有道理。我不会明确地完成这些步骤，您可以自己完成，您最终会得到等式：
$$
(M^\dagger M)_{jk} - (M^\dagger M)_{kj} = 0.
$$
这很棒！我们可以把它加到前面的方程中，推导出 $(M^\dagger M)_{jk} = 0$ 每当 $j \neq k$ 时，我们得出结论 $M^\dagger M = I$，即, $M$ 是幺正的。



### 1.3.8 受控非门

我们已经开发出进行通用量子计算所需的大部分想法。我们了解量子位、量子态，并拥有一系列量子门。然而，我们所有的门都只涉及一个量子位。为了计算，我们需要某种方式让量子比特相互交互。也就是说，我们需要涉及两个（或更多）量子位的量子门。

这种门的一个例子是受控非门（CNOT Gate）。在量子电路语言中，我们有两条线，代表两个量子位，以及表示 受控非门的符号：

![img](https://imghub-1258224042.cos.ap-nanjing.myqcloud.com/images/qcintro-1/cnot.png)

上面带有填充小点的导线（在本例中为顶部导线）称为 控制（control） 量子位，其原因稍后会变得清晰。上面带有较大的未填充圆圈的导线称为 目标（target） 量子位。

到目前为止，我还没有说双量子位系统的可能状态是什么，但您可能已经猜到了。我们现在有四种计算基础状态，对应于双量子位系统的四种可能状态：$|00\rangle$、$|01\rangle$、$|10\rangle$ 和 $|11\rangle$。而且，对于双量子位系统，我们不仅可以拥有这四种状态，还可以拥有它们的叠加（即线性组合）：
$$
\alpha|00\rangle+\beta|01\rangle+\gamma |10\rangle+\delta|11\rangle
$$
这里振幅$\alpha,\beta,\gamma,\delta$只是复数，模的平方和为1，即$|\alpha|^2+|\beta|^ 2+|\gamma|^2+|\delta|^2 = 1$。这与我们对单个量子位的归一化条件相同。



> n 个量子位的多量子位系统具有 $2 ^{n}$ 个计算基础状态，表示为 $\vert 00 \cdots 00 \rangle$, $\vert 00 \cdots 01 \rangle$, $\vert 00 \cdots 10 \rangle$ $\ldots$ $\vert 11 \cdots 11\rangle$。



现在，CNOT 门就像量子非门一样，直接受到经典门的启发。它的作用非常简单。如果控制量子位设置为 1，如状态 $|10\rangle$ 和 $|11\rangle$，则它翻转目标量子位。否则它什么都不做。它在我们拥有的所有四个计算基础状态上的作用分别是：
$$
\begin{aligned} |00\rangle & \rightarrow & |00\rangle \\ |01\rangle & \rightarrow & |01\rangle \\ |10\rangle & \rightarrow & |11\rangle \\ |11\rangle & \rightarrow & |10\rangle \end{aligned}
$$
如果您熟悉经典编程语言，那么您可以将 CNOT 视为一种非常简单的“if-then”语句：“if”设置了控制量子位，“then”翻转目标量子位。虽然简单，但它可以用作构建其他更复杂的条件行为。

有一种方法可以将上述所有四个等式汇总为一个等式。假设 $x$ 和 $y$ 是经典位，即 0 或 1。那么我们可以将上面的等式重写为一个等式：
$$
\begin{aligned} |x, y\rangle & \rightarrow & |x, y\oplus x\rangle. \end{aligned}
$$
请注意插入的逗号以使其更易于阅读——这在处理多量子位状态时很常见。

上面的等式清楚地表明，CNOT 不改变控制量子位 $x$，但如果 $x$ 设置为 1，则翻转目标量子位 $y$。注意 $\oplus$ 是 **addition modulo 2**，其中$1 \oplus 1 = 0$，正如我们从 CNOT 将 $|11\rangle$ 带到 $|10\rangle$ 的事实所期望的那样。

$1\oplus1$ 的Python代码是`(1+1)%2`.

这就是 CNOT 的全部内容。这真的是一个非常简单的想法和量子门。请注意，正如我们对量子门所期望的那样，它当然会线性地作用于计算基础状态的叠加。所以：
$$
\begin{aligned} & \hphantom{ \rightarrow } \alpha|00\rangle+\beta|01\rangle+\gamma |10\rangle+\delta|11\rangle \\ & \rightarrow \alpha|00\rangle+\beta|01\rangle+\gamma |11\rangle+\delta|10\rangle \end{aligned}
$$

而且，虽然我不会明确地进行验证，但 CNOT 是幺正的，因此正如我们所期望的那样，保留了量子态的长度。



当然，CNOT 不仅仅出现在两量子位计算中。它也出现在涉及更多量子位的计算中。例如，假设我们有 3 个量子比特，以及 $|000\rangle$、$|001\rangle$ 等计算基础状态。这是一个 CNOT，第二个量子位作为控制量子位，第三个量子位作为目标量子位：

![img](https://imghub-1258224042.cos.ap-nanjing.myqcloud.com/images/qcintro-1/cnot23.png)

怎么回事？好吧，我们可以写出在任意计算基础状态 $|x, y, z\rangle$ 上会发生什么，其中 $x, y$ 和 $z$ 都是经典位。当然，第一位 $x$ 根本没有改变，因为它不涉及 CNOT。第二位 $y$ 是控制位，因此不会改变。但是如果控制位 $y$ 设置为 1，则第三位 $z$ 被翻转。 因此我们可以将 CNOT 的动作写为：
$$
|x,y,z\rangle \rightarrow |x,y, z\oplus y\rangle
$$
我已经将 CNOT 描述为“经典”门，但它可以与单量子位门结合来做非经典的事情。让我给您一个明确的例子。这是另一种双量子位计算。它从 $|00\rangle$ 计算基础状态开始，我们对第一个量子位应用哈达玛门，然后做一个 CNOT：

![img](https://imghub-1258224042.cos.ap-nanjing.myqcloud.com/images/qcintro-1/generate_entanglement.png)



回想一下，对于单个量子位，哈达玛门将 $|0\rangle$ 带到相等的叠加 $(|0\rangle+|1\rangle)/\sqrt{2}$。对于这两个量子位，它根本不会影响第二个量子位，因此它把 $|00\rangle$ 带到 $(|00\rangle+|10\rangle)/\sqrt{2}$。

接下来我们应用 CNOT 门。这使得 $|00\rangle$ 状态保持不变，因为控制位是 0。并且它需要 $|10\rangle$ 到 $|11\rangle$，因为控制位是 1。所以电路的输出是：
$$
\frac{|00\rangle+|11\rangle}{\sqrt 2}.
$$

这种输出状态是一种相当非经典的状态——它实际上是一种称为纠缠状态（entangled state）的状态。这种状态没有经典的对应状态，不像 $|00\rangle$ 这样的计算基础状态。事实上，纠缠态可用于完成各种有趣的信息处理任务，包括量子隐形传态和快速量子算法。

我在上面忽略了一点，但值得一提的是：在电路中，我分别绘制了 $|0\rangle$ 和 $|0\rangle$ 作为输入量子位。按照惯例，这样做是为了表示 $|00\rangle$ 的组合输入。更一般地，人们将 $|0\rangle|0\rangle$ 与 $|00\rangle$ 互换使用，$|0\rangle|1\rangle$ 与 $|01\rangle$ 互换使用，依此类推。需要一点时间来适应，但一切都如您所愿，您只需要稍微练习一下，它就会变得很自然。

更一般地，如果我们有单量子位状态 $\alpha|0\rangle+\beta|1\rangle$ 和 $\gamma|0\rangle+\delta|1\rangle$，那么当两个量子位被放置在一起时的组合状态是：
$$
\begin{aligned} & \hphantom{ = } (\alpha|0\rangle+\beta|1\rangle)(\gamma|0\rangle+\delta|1\rangle) \\ & = \alpha\gamma|00\rangle+\alpha\delta |01\rangle+ \beta\gamma |10\rangle+ \beta\delta |11\rangle. \end{aligned}
$$


我说 CNOT 不改变控制量子位，而是修改目标量子位。这仅适用于计算基础状态。事实上，目标量子位实际上有可能改变控制量子位。至少花一两分钟来理解（并且，如果您感觉精力充沛，尝试解决）以下练习是值得的：



**练习：** 您能找到单量子位状态 $|a\rangle$ 和 $|b\rangle$ 以便将 CNOT 应用于组合状态 $|ab\rangle$ 会改变第一个量子位，即控制量子位？

让我给您一个例子来解决上面的练习。假设我们引入单量子位状态 $|+\rangle$ 和 $|-\rangle$，定义为：
$$
\begin{aligned} |+\rangle & := \frac{|0\rangle+|1\rangle}{\sqrt 2} \\ |-\rangle & := \frac{|0\rangle-|1\rangle}{\sqrt 2} \end{aligned}
$$
以下电路恒等式成立：

![img](https://imghub-1258224042.cos.ap-nanjing.myqcloud.com/images/qcintro-1/cnot_backwards.png)



也就是说，如果我们把 $|+-\rangle$ 放进去，那么 $|--\rangle$ 就出来了，即目标量子位没有改变，但控制量子位改变了！证明它并不难。提示：
$$
|+-\rangle=|+\rangle|-\rangle=\left(\frac{|0\rangle+|1\rangle}{\sqrt 2}\right)\left(\frac{|0\rangle-|1\rangle}{\sqrt 2}\right)
$$

这个例子中也许可以让我们谨慎一些。这意味着来自计算基础状态的直觉有时可能不完整或具有误导性。 CNOT 不是简单地以控制为条件对目标执行某些操作。它也在对控制量子位做一些事情，以目标量子位为条件。

**练习：** 证明 CNOT 门的逆就是 CNOT 门。



**例5：CNOT Gate**

```python
from qiskit import QuantumCircuit, BasicAer, execute

# create a quantum circuit with 2 qubits and 2 bits
qc = QuantumCircuit(2,2)
# apply x gate on qubit 0 and qubit 1
qc.x([0,1])
# apply cnot gate, qubit 0 is control, qubit 1 is target
qc.cx(0, 1) 
# measure qubits [0, 1] to bits [0, 1]
qc.measure([0, 1], [0, 1])
# plot the circuit
qc.draw('mpl')
```

**Output:**

![img](https://imghub-1258224042.cos.ap-nanjing.myqcloud.com/images/qcintro-1/image-20211016200852955.png)

---

```python
# simulation: run the quantum circuit 1000 times and print all results
backend = BasicAer.get_backend('qasm_simulator') 
result = execute(qc, backend, shots=1000).result()
result.get_counts(qc)
```

**Output:**

```
{'01': 1000}
```

这个电路值得我们花一些时间去理解。首先电路的初始状态是$|q_1q_0\rangle=|00\rangle$，经过两个非门，变成了$|q_1q_0\rangle=|11\rangle$，然后经过一个受控非门（CNOT），控制量子位是$q_0=1$，目标量子位是$q_1$将被翻转，最终得到一个基态$|q_1q_0\rangle=|01\rangle$。



> 在Qiskit中，多量子态的标记方式是从右向左增加序号的( $|q_7q_6\ldots q_0\rangle$ )，而大部分量子力学书中是从左向右的$|q_0q_1\ldots q_7\rangle$。

## 1.4 通用量子计算

> 在自然科学中，大自然给了我们一个世界，我们只是去发现它的规律。在计算机中，我们可以将规律融入其中并创造一个世界。— Alan Kay

让我们回到我们开始的问题：是否有单一的计算设备可以有效地**模拟**任何其他物理系统？目前，人类拥有的这种计算设备的最佳候选者是量子计算机。正如您可能已经猜到的那样，您通过组合我们一直在讨论的所有元素来制作这样的设备。在本节中，我们将讨论什么是量子计算机、它们为何有用，以及它们是否可用于有效模拟任何其他物理系统。

### 1.4.1 量子计算模型（量子计算机）

> 传统上，计算理论几乎完全是抽象的，作为纯数学的一个主题。这是错过了重点。计算机是物理对象，计算是物理过程。计算机可以或不可以计算什么仅由物理定律决定，而不是由纯数学决定。
>
> — David Deutsch

在一般的量子计算中，您从许多量子位开始——我将在这里画四个，但总的来说，它可能更多（或更少）。您可以应用各种量子门，特别是单量子位门和 CNOT 门。在电路的最后，您通过在计算基础状态上进行测量来读出结果。这就是它的样子：

![img](https://imghub-1258224042.cos.ap-nanjing.myqcloud.com/images/qcintro-1/general_circuit.png)

我没有明确画出单量子位门，但它们可能是各种各样的东西——哈达玛门、非门、旋转，也许还有其他。另请注意，虽然计算以计算基础状态 $|0000\rangle$ 开始，但您也可以从其他一些计算基础状态开始。

我们可以将量子计算的三个步骤总结如下：

1. 从计算基础状态开始。
2. 应用一系列 CNOT 和单量子位门。
3. 要获得结果，请在计算基础状态上进行测量。任何结果的概率，比如 $00\ldots 0$，只是相应振幅模的平方。

这就是一般量子计算的全部内容！如果您理解了这个模型，您就知道什么是量子计算机。这很简单，简单到足以让我有时听到人们说“这就是全部吗？”但是，虽然模型很简单，但它包含了非凡的深度，探索它可能会花费许多人一生的时间。

> 真正的量子计算机。触手可及。 IBM 提供对可用的最先进量子计算机的云访问。快使用我们的量子平台学习、开发和运行程序。https://quantum-computing.ibm.com/

如果您理解了最低的层次——AND 门和 NOT门，或者其他我们之前学习到的的量子电路类型——那么您就有了理解更高级别抽象的基础。如果您是一名程序员，您完全可以自己设计一种基于量子计算的新的编程语言。这种语言将引入更高级别抽象，使得人们能够轻松地开发各种量子应用程序，而不必关心底层的量子力学原理。但是，原则上，这些抽象总是可以化简至量子门的级别。

这可能会让您想知道：这是否意味着您还需要掌握所有更高级别的抽象？答案是不！

有几个原因。一个原因是，正如我们之前所讨论的，人类还不知道更高级别的抽象是什么。我们仍在试图弄清楚它们。第二个原因是，更高级别的抽象的可能性似乎是无穷无尽的。我的猜测——这只是一个猜测——是我们将继续在经典和量子计算中永远发现美丽的新抽象。

一个相关的事实：的确存在不同于量子电路模型的量子计算模型。有些只是我们已经学到的模型的微小变化。例如，我们可能会允许在电路中使用任何两个量子位的幺正门，而不是只使用 CNOT 门。或者，也许我们不使用量子位，而是使用其他类型的基本量子系统——比如说，*qutrit*，它具有三个计算基态，$|0\rangle、|1\rangle$ 和 $|2\rangle $.您可能不会感到惊讶，由此产生的计算模型本质上等同于我描述的量子电路模型。我的意思是，他们可以使用大致相当数量的门和其他物理资源来模拟量子电路模型（反之亦然）。

还有更多奇特的可能性，例如基于测量的量子计算、拓扑量子计算等。我不会在这里详细描述这些，但可以说它们在表面上与电路模型非常不同。尽管如此，它们在数学上都是等效的，包括量子电路模型。因此，任何这些模型中的量子计算都可以转化为量子电路模型中的等价物，转化成本只有很小的开销。反之亦然。

您可能想知道为什么人们要费心考虑其他模型，如果它们在数学上等同于量子电路模型。原因是，仅仅因为两个模型在数学上是等效的，并不意味着它们在思想上是等效的。不同的计算模型激发不同的思维方式，产生不同的想法。因此，拥有其他等效模型很有价值。

**练习：** 我们之前看到量子门的组成对应于矩阵乘法（以相反的顺序）。证明两个酉矩阵 $U$ 和 $V$ 的乘积也是酉矩阵。因此，任何量子电路（在测量之前）的净效应是在系统的状态空间里进行一次幺正运算。

让我用一种特别的量子门来结束本节。这些门是单位矩阵 $I$ 的倍数：
$$
\begin{aligned} \left[ \begin{array}{cc} e^{i\theta} & 0 \\ 0 & e^{i\theta} \end{array} \right] = e^{i\theta} \left[ \begin{array}{cc} 1 & 0 \\ 0 & 1 \end{array} \right] = e^{i \theta} I. \end{aligned}
$$
当 $\theta$ 是实数时，这是一个酉矩阵，因此是一个有效的量子门。门的作用只是将量子计算机的状态乘以 $e^{i\theta}$。数字 $e^{i\theta}$ 称为全局相位因子（Global Phase Factor）。但即使门是有效的，我们也很少明确地将此类门放入量子电路中。原因是这样的全局相位因子对计算结果没有影响。要了解原因，请想象一个包含许多此类量子门的量子电路，可能具有不同的值，$\theta_1、\theta_2、\ldots$。无论门在电路中的哪个位置出现，最终效果都是将电路的输出状态乘以 $e^{i \theta_1 + i\theta_2 + \ldots}$ 的总因子。这不会改变计算基础状态各个振幅模的大小，因此对计算结束时的测量概率没有影响。我们把这种门排除在外，没有任何损失。



进行量子计算人往往对这种全局相位因子漠不关心。他们会做一些事情，比如不费心区分诸如 $X$ 和 $-X$ 之类的单一门，称这些门“在全局相位因子上是相同的”。它们只是意味着 $-X$ 门与 $X$ 门加上一个 $-I$ 门相同。由于后一个门对计算的输出没有影响，因此可以安全地省略它。但也有例外，我们将在下一章中看到有关量子搜索算法的示例，其中几个地方我们会将量子态乘以全局相位因子 $-1$ ，这是是有意义的操作。

### 1.4.2 量子计算机有什么用(Toffoli门)？



现在我们知道了量子计算机是什么，但它们有什么用？

早些时候，我告诉大家量子计算机能扩展计算时可用的操作范围。这类似于船只改变了我们的出行方式，使人类能够探索新的路径。大大缩短了从A到B的时间。

为了使这个类比成立，它们至少需要与经典计算机一样强大。幸运的是，可以将任何经典电路转换为量子电路。显而易见的方法是想象您的经典电路是用一些标准的通用集来表达的——比如，与门和非门——然后将这些门转换为等效的量子门。

> 或门可以用与门和非门来表示。 （$x_1\lor x_2=!(!x_1 \land !x_2)$）

用非门很容易做到这一点——我们只是把它变成一个 $X$ 门。但并不那么容易的是与门。您可能想知道是否有一	种量子门以$|x,y\rangle$两个量子位作为输入，然后输出单个量子位 $|x \wedge y\rangle $，其中 $x \wedge y$ 表是 $x$ AND $y$ 。

不幸的是，这种“量子门”根本没有任何意义！它根本不可能是幺正的：一个以两个量子位作为输入的幺正门必然有两个量子位作为输出。我描述的“门”只有一个量子位作为输出，所以它不可能是幺正的。

您可能想知道是否有某种方法可以找到一个双量子位量子门，它的一个输出是 $ x \wedge y$，另一个输出是其他东西。我不会证明这一点，但事实证明这是不可能的。证明实际上并不那么难——如果您想要挑战，这是一个有趣的思考练习。

这一切都相当令人失望。但是有一个解决方案。它是使用称为 Toffoli 门的三量子位量子门。 Toffoli 门与 CNOT 门非常相似，但它不是只有一个控制量子位，而是有两个控制量子位 $x$ 和 $y$，以及一个目标量子位 $z$。如果设置了两个控制量子位为$1$，则翻转目标。否则，目标将不改变：

![img](https://imghub-1258224042.cos.ap-nanjing.myqcloud.com/images/qcintro-1/toffoli.png)

如果目标以 $z = 0$ 开始，那么您可以看到目标输出只是 $x \wedge y$，因此可以使用 Toffoli 门来模拟经典的与门。

现在，如果我们有任何经典的 AND 门和 NOT 门电路，那么就会有一个相应的量子电路，涉及相同数量的 Toffoli 门和 $X$ 门，可以用来计算相同的东西。

**练习：** 什么是可以计算与非门（NAND Gate）的量子电路？回想一下，给定两个比特 $x$ 和  $y$，$x \;\text{NAND}\;y = !(x \wedge y)$。

**练习：** 您能找到一种只使用一个 Toffoli 门而不使用其他量子门来实现 NAND 门的方法吗？请注意，如果您仅使用单个 Toffoli 门而不使用其他量子门来回答该练习，那么您在此处的答案可能与您对上一个练习的答案相同。 （Toffoli 门，$z=1$）



等等，这里还一个问题。Tofffoli 门不在我们已经熟悉的标准基本量子门集中。然而，可以用 CNOT 和单量子位幺正门来构建 Toffoli 门。进行细分的一种方法如下所示：

![img](https://imghub-1258224042.cos.ap-nanjing.myqcloud.com/images/qcintro-1/toffoli_circuit.png)



有各种巧妙的方式来“解释”为什么这个电路有效，但我要告诉您一个秘密：最早的大部分工作都是通过纯粹的蛮力完成的，人们只是尝试了很多不同的方式来实现Toffoli 门（有时，使用计算机来协助进行搜索）。坦率地说，我不会太担心这个电路为什么会起作用，只是想当然地认为它确实有效。如果需要，您可以深入了解原因。需要知道电路细节的时候实际上并不多，所以我不建议记住它——除非您有充分的理由。



**练习：** 证明 Toffoli 门的逆就是 Toffoli 门。 ($[z\oplus(x\wedge y)]\oplus(x\wedge y)=z$)

### 1.4.3 不，真的，量子计算机有什么用？

令人欣慰的是，我们总是可以模拟经典电路——这意味着量子计算机并不比经典计算机慢——但并没有回答上一节的问题：量子计算机适合哪些问题？我们能否系统地证明它们比经典计算机更快？事实证明，没有已知的通用方法可以做到这一点。但是在处理一些问题的时候，量子计算机的性能已经证明优于经典计算机。

从长远来看，我相信量子计算机最重要的用途将是模拟其他量子系统。这听起来可能很深奥——除了量子物理学家之外，为什么有人会关心模拟量子系统？但是未来的每个人都会（或者至少会关心后果）。世界是由量子系统组成的。制药公司雇佣了数以千计的化学家来合成分子并表征其特性。目前这是一个非常缓慢和艰苦的过程。在理想的世界中，通过进行高度准确的计算机模拟，他们能够以数百万倍的速度获得相同的信息。他们会得到更多有用的信息，回答化学家今天不可能回答的问题。不幸的是，经典计算机在模拟量子系统方面很糟糕。

经典计算机不擅长模拟量子系统的原因不难理解。假设我们有一个包含 $n$ 个原子的分子——对于一个小分子，$n$ 可能是 1-10，对于一个复杂的分子，它可能是数百、数千甚至更多。并且假设我们将每个原子视为一个量子位（不是真的，而是随它去）：为了描述系统，我们需要 $2^n$ 个不同的振幅，每个振幅对应于一个n个量子位的计算基础状态，例如， $|010011\ldots\rangle$。

> 非常粗略的估计，$O(n)$ 个量子比特问题大约需要 $O(2^n)$ 个经典比特去模拟。

当然，原子不是量子位。它们更复杂，我们需要更多的振幅来描述它们。

从长远来看，量子计算机会赢，而且很容易赢。

重点是有理由相信，如果我们可以轻松模拟量子系统，我们就可以大大加快药物发现和其他新型材料的发现。

在历史的大部分时间里，我们人类对物质是什么几乎一无所知。这在过去一个世纪左右发生了变化，因为我们已经对物质有了惊人的详细理解。但是，虽然这种理解有所增长，但我们控制物质的能力却滞后了。本质上，我们依赖于大自然意外为我们提供的东西。我们在合成新化学元素和新分子等方面有所进步，但我们的控制仍然非常原始。

我们现在正处于从几乎无法控制物质到几乎完全控制物质的过渡阶段。物质将变得可编程；它将是可设计的。这将是我们对物质理解的重大转变，就像从机械计算设备到现代计算机的转变是为了计算。我们将创造什么样新形式的物质？我不知道，但使用量子计算机来模拟量子系统的能力将成为这一新兴设计科学的重要组成部分。

“量子计算机有什么用？” 这个问题也有一些更加实际和传统回答。答案是列出各种算法问题，我们有证据表明在量子计算机上可以比在经典计算机上更快地解决这些问题。

最著名的例子是 Peter Shor 美丽的量子质因数分解算法。在经典计算机上，找到 $n$ 位整数的质因数似乎是一个非常困难的问题。最好的现有算法在计算上非常昂贵，其成本随 $n$ 呈指数增长。即使只有几百位数的数字目前在经典计算机上也不可以被分解。相比之下，如果可以建造大型量子计算机，Shor 的量子分解算法将使分解成为一项相对容易的任务。

质因数分解可能看起来不是一个非常有趣的例子。但事实证明，分解的能力可以让您破解一些最广泛使用的加密方案，Gmail 和 Amazon 等服务使用这些方案来保护您的通信隐私。这种破解加密的能力使世界情报机构对质因数分解非常感兴趣，自 1990 年代中期以来，他们已矢量子计算研究投入了大量资金。事实上，有一本很好的（尚未成文的）历史书要写，关于量子计算的兴起是如何由世界情报机构对获取人类私人思想的兴趣引起的。

### 1.4.4 量子计算机真的是通用设备吗？

> 世界的永恒奥秘在于它的可理解性…… 可理解性是一个奇迹。
>
> — 阿尔伯特·爱因斯坦

在本节开始时，我们讨论过是否有任何单一的通用计算设备可以有效地模拟任何其他物理系统？我们了解到，经典计算机在有效模拟量子系统方面似乎存在很多问题。

量子计算机呢？虽然它们当然可以模拟许多量子系统，但这是否意味着它们可以用来有效地模拟任何物理系统？

这个问题是一个悬而未决的问题！我们还不知道答案。

回答这个问题的部分困难在于，人类还没有发现物理学的最终基本定律。现代物理学基于两个非常有效的理论：爱因斯坦的广义相对论，它描述了引力的工作原理；以及粒子物理学的标准模型，它解释了几乎所有其他事物（电磁力、强核力和弱核力）的工作原理。

问题是，我们还没有一个很好的结合广义相对论和标准模型的量子引力理论。如果没有这样的量子引力理论，我们就无法回答量子计算机是否可以有效模拟任何其他物理系统的问题。也许未来需要某种比量子计算机更强大的量子引力计算机来模拟量子引力。

让我们问一个稍微不那么雄心勃勃的问题，即我们是否可以使用量子计算机来有效地模拟广义相对论和标准模型？

标准模型是一种特殊类型的量子力学理论，称为量子场论。 John Preskill 及其合作者撰写了一系列论文，有关进展回顾，请参见：John Preskill，[用量子计算机模拟量子场论](https://arxiv.org/abs/1811.10085) (2018)。解释如何使用量子计算机有效地模拟量子场论。这些论文还没有模拟完整的标准模型，但它们确实取得了相当大的进步。它仍然是一个令人兴奋的开放问题，尽管这个问题已经取得了令人鼓舞的进展。

在广义相对论的情况下，据我所知，这个问题仍然悬而未决。事实上，即使讲清楚问题是什么也不是十分简单的。广义相对论支持封闭类时曲线的存在，它可以在某种意义上用于向过去发送信息。这对计算产生了有趣的影响：有一种方法可以让计算机知道未来计算的结果。不出所料，这改变了一切可能！另一个复杂因素是，当您谈论计算中的“高效模拟”时，您的意思是时间和空间开销不会太大。但在广义相对论中，即使是空间和时间的基本单位也不是那么清楚。很难说效率意味着什么。最后，近奇点的时间和空间会以奇怪的方式扭曲，这再次使得很难准确说出进行有效计算的意义。

顺便说一下，我一直在没有解决一个重要的问题，这可能会困扰您：正如我所解释的，量子计算机根本不是一个 单一的计算设备，因为有许多可能的量子电路。不过，这没关系，因为有一个模型称为通用量子图灵机，它是一个单一的计算设备，可以模拟任何量子电路。所以您应该把上面的讨论理解为是关于通用量子图灵机的。我不会在本节中解释通用量子图灵机的细节，因为在实践中量子电路模型要常用得多。但如果您对细节感兴趣，我推荐这篇 [Bernstein 和 Vazirani 的论文](https://epubs.siam.org/doi/abs/10.1137/S0097539796300921)。

通用计算机的存在很容易被认为是理所当然的。但是没有先验的逻辑理由去相信必须有一台可以有效模拟所有其他物理系统的机器。这就像相信可以将您的汽车用作冲浪板、超市手推车或热带雨林。实际上，在某种意义上，这是可能的，在有限范围内：如果您可以任意良好地重新排列质子、中子和电子，您就可以将汽车变成冲浪板、超市手推车或热带雨林的（小）部分。所以物质确实具有有趣的普遍性。当然，这也很了不起。然而，迄今为止的证据表明，我们的宇宙确实允许使用这种通用机器。这是一个很好的问题——据我所知，在很大程度上还没有被探索过——考虑一套物理定律，在这些定律中，这样的通用机器是不可能的。这听起来可能毫无意义——为什么要想象其他可能的宇宙？然而，探索这种激进的反事实往往是更好地了解我们自己的宇宙的绝佳策略。

所以这里有一个非常奇怪的循环。物理定律决定了可以进行什么样的计算。然而，可以进行的那种计算似乎足以描述物理定律。然后可以使用该描述（有效地！）模拟任何物理系统：

![img](https://imghub-1258224042.cos.ap-nanjing.myqcloud.com/images/qcintro-1/strange_loop.png)

循环的上半部分几乎是显而易见的。循环的下半部分是非凡的。没有先验的理由去相信物理定律保证了存在一种机器可以模拟物理系统。您可能会以人择为由争论——我们人类在宇宙中，并且在物理学方面做得非常成功，因此以此为条件，它一定是真的。但这并不是一个非常令人满意的解释。这仍然是一个谜。爱因斯坦是对的：世界的可理解性是一个奇迹。





# 参考文献

[1] [https://quantum.country](https://quantum.country)

[2] J. J. Sakurai Modern Quantum Mechanics

[3] [https://en.wikipedia.org/wiki/Bra–ket_notation](https://en.wikipedia.org/wiki/Bra–ket_notation)

[4] M. A. Nielsen Quantum Computation and Quantum Information
