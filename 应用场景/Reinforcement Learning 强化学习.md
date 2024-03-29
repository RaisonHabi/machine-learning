## 一、强化学习vs监督学习
强化学习（Reinforcement Learning，RL），也叫增强学习，是指一类从（与
环境）交互中不断学习的问题以及解决这类问题的方法．强化学习问题可以描
述为一个智能体从与环境的交互中不断学习以完成特定目标（比如取得最大奖
励值）．和深度学习类似，强化学习中的关键问题也是贡献度分配问题 [Minsky,
1961]，每一个动作并不能直接得到监督信息，需要通过整个模型的最终监督信
息（奖励）得到，并且有一定的延时性．

强化学习也是机器学习中的一个重要分支．强化学习和监督学习的不同在
于，**强化学习问题不需要给出“正确”策略作为监督信息，只需要给出策略的（延
迟）回报，并通过调整策略来取得最大化的期望回报**．

强化学习和监督学习的区别在于：  
1）强化学习的样本通过不断与环境进行
交互产生，即试错学习，而监督学习的样本由人工收集并标注；  
2）强化学习的反馈信息只有奖励，并且是延迟的，而监督学习需要明确的指导信息（每一个状态对应的动作）．

[神经网络与深度学习](https://nndl.github.io/nndl-book.pdf)

&nbsp;
### 强化学习和监督学习的区别
强化学习和有监督学习的主要区别在于：
```
1、有监督学习的训练样本是有标签的，强化学习的训练是没有标签的，它是通过环境给出的奖惩来学习

2、有监督学习的学习过程是静态的，强化学习的学习过程是动态的。
这里静态与动态的区别在于是否会与环境进行交互，有监督学习是给什么样本就学什么，而强化学习是要和环境进行交互，再通过环境给出的奖惩来学习

3、有监督学习解决的更多是感知问题，尤其是深度学习，强化学习解决的主要是决策问题。因此有监督学习更像是五官，而强化学习更像大脑。
```
[强化学习是什么？ - 海晨威的回答 - 知乎](https://www.zhihu.com/question/31140846/answer/413578783)

&nbsp;

### Q-learning:强化学习的一种离线策略算法
Q-学习是强化学习的一种方法。Q-学习就是要记录下学习过的政策，因而告诉智能体什么情况下采取什么行动会有最大的奖励值。  
Q-学习不需要对环境进行建模，即使是对带有随机因素的转移函数或者奖励函数也不需要进行特别的改动就可以进行。 


Q-learning is an off policy reinforcement learning algorithm that seeks to find the best action to take given the current state.  
It's considered off-policy because the q-learning function learns from actions that are outside the current policy, like taking random actions, and therefore a policy isn't needed.

&nbsp;
## 二、强化学习知识整理
### 0.马尔可夫决策过程（Markov Decision Processes,MDPs）
MDPs 简单说就是一个智能体（Agent）采取行动（Action）从而改变自己的状态（State）获得奖励（Reward）与环境（Environment）发生交互的循环过程。

MDP 的策略完全取决于当前状态（Only present matters），这也是它马尔可夫性质的体现。  
其可以简单表示为：M=<S, A, Ps,a, R>

<h4>基本概念</h4><ol><li><img src="https://www.zhihu.com/equation?tex=s+%5Cin+S" alt="s \in S" eeimg="1"/>: 有限状态 state 集合，s 表示某个特定状态</li><li><img src="https://www.zhihu.com/equation?tex=a+%5Cin+A" alt="a \in A" eeimg="1"/>: 有限动作 action 集合，a 表示某个特定动作</li><li>Transition Model <img src="https://www.zhihu.com/equation?tex=T%28S%2C+a%2C+S%27%29+%5Csim+P_r%28s%27%7Cs%2C+a%29" alt="T(S, a, S&#39;) \sim P_r(s&#39;|s, a)" eeimg="1"/>: Transition Model, 根据当前状态 s 和动作 a 预测下一个状态 s’，这里的 <img src="https://www.zhihu.com/equation?tex=P_r" alt="P_r" eeimg="1"/> 表示从 s 采取行动 a 转移到 s’ 的概率</li><li>Reward <img src="https://www.zhihu.com/equation?tex=R%28s%2C+a%29+%3D+E%5BR_%7Bt%2B1%7D%7Cs%2C+a%5D" alt="R(s, a) = E[R_{t+1}|s, a]" eeimg="1"/>:表示 agent 采取某个动作后的即时奖励，它还有 R(s, a, s’), R(s) 等表现形式，采用不同的形式，其意义略有不同</li><li>Policy <img src="https://www.zhihu.com/equation?tex=%5Cpi%28s%29+%5Cto+a" alt="\pi(s) \to a" eeimg="1"/>: 根据当前 state 来产生 action，可表现为 <img src="https://www.zhihu.com/equation?tex=a%3D%5Cpi%28s%29" alt="a=\pi(s)" eeimg="1"/> 或 <img src="https://www.zhihu.com/equation?tex=%5Cpi%28a%7Cs%29+%3D+P%5Ba%7Cs%5D" alt="\pi(a|s) = P[a|s]" eeimg="1"/>，后者表示某种状态下执行某个动作的概率</li></ol>

### 1.MDP 求解
我们需要找到最优的策略使未来回报最大化，求解过程大致可分为两步,具体内容会在后面展开
```
预测：给定策略，评估相应的状态价值函数和状态-动作价值函数
行动：根据价值函数得到当前状态对应的最优动作
```

### 2.Bellman 期望方程
。。。。。。   
所以我们一般采用近似的办法，很多强化学习方法一般也是研究如何近似求解 Bellman 方程，一般有下面几种（后文会做具体讲解）：
```
Value Iteration
Policy Iteration
Q-learning
Sarsa
```
### 3.Policy Iteration\Value Iteration
......

<h3>4.Model-free v.s. Model-based</h3><p>Model-based 是指学习 Transition Model <img src="https://www.zhihu.com/equation?tex=T%28S%2C+a%2C+S%27%29+%5Csim+P_r%28s%27%7Cs%2C+a%29" alt="T(S, a, S&#39;) \sim P_r(s&#39;|s, a)" eeimg="1"/> ，即在状态 s 采取行动 a 后转移到 s&#39; 的概率，然后基于这个 Model 去选择最优的策略。Transition Model 的空间复杂度为 <img src="https://www.zhihu.com/equation?tex=O%28S%5E2A%29" alt="O(S^2A)" eeimg="1"/> ，所以不太适合用于解决状态空间和动作空间过大的问题。</p><p>Model-free 未知 Transition Model，通常通过不断的尝试去直接学习最优策略。</p><p>前面的 Policy Iteration 和 Value Iteration 都是 model-based 方法，因此一定程度上受限于状态空间和动作空间的规模。于是 Q-learning 应运而生。</p>

### 5.Q-learning
公式如下，可以看出 Q-leaning 由 Value iteration 演变而来，但去除了对 Transition Model 的依赖，因此属于 Model-free 的方法。另一方面下一个动作 a 的选择，原来是根据 policy 选择最优的 action，现在是 maximum 下一个 state 的值来选择 action，所以 Q-learning 属于 off-policy 算法。

### 6.State-Action-Reward-State-Action (SARSA)
公式如下，唯一与 Q-learning 的不同是，SARSA 是 on-policy 方法，需要考虑 exporation-exploitation 问题，基本方法是 [公式] -greedy。
### 7....
### PG 和 Q learning 的问题
Policy Gradient 的问题是，1）大的策略更新使训练失败，2）有时很难将策略的变化映射到参数空间，3）不合适的学习率导致梯度消失或爆炸，4）样本效率（sample efficiency）低。

Q learning 的问题是，大部分情况下，对不同的 action 差别不会很大（方差小），且在部分任务中，Q function 的值总为正。

#### 优势函数 Advantage Function
优势函数就是为了解决 Q function 值方差小而引入的，基本形式为

&nbsp;
## 三、代码示例
[强化学习](https://wizardforcel.gitbooks.io/hands-on-ml-with-sklearn-and-tf/content/docs/16.%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0.html)

&nbsp;
## 四、从Q-Learning到DQN
### 1.学习目标
1. 复习Q-Learning；

2. 理解什么是值函数近似（Function Approximation）；

3. 理解什么是DQN，弄清它和Q-Learning的区别是什么。

### 2.用Q-Learning解决经典迷宫问题
现有一个5房间的房子，如图1所示，房间与房间之间通过门连接，编号0到4,5号是房子外边，即我们的终点。我们将agent随机放在任一房间内，每打开一个房门返回一个reward。图2为房间之间的抽象关系图，箭头表示agent可以从该房间转移到与之相连的房间，箭头上的数字代表reward值。

。。。
### 3.值函数近似与DQN
<p>值函数近似（Function Approximation）的方法就是为了解决状态空间过大，也称为“维度灾难”的问题。通过用<b>函数</b>而不是Q表来表示 <img src="https://www.zhihu.com/equation?tex=Q%5Cleft%28+s%2Ca+%5Cright%29+" alt="Q\left( s,a \right) " eeimg="1"/> ，这个函数可以是线性的也可以使非线性的。</p><p><img src="https://www.zhihu.com/equation?tex=+%5Chat%7Bv%7D%5Cleft%28+s%2C%5Cboldsymbol%7Bw%7D+%5Cright%29+%5Capprox+v_%7B%5Cpi%7D%5Cleft%28+s+%5Cright%29+%5C+or%5C+%5Chat%7Bq%7D%5Cleft%28+s%2Ca%2C%5Cboldsymbol%7Bw%7D+%5Cright%29+%5Capprox+q_%7B%5Cpi%7D%5Cleft%28+s%2Ca+%5Cright%29+" alt=" \hat{v}\left( s,\boldsymbol{w} \right) \approx v_{\pi}\left( s \right) \ or\ \hat{q}\left( s,a,\boldsymbol{w} \right) \approx q_{\pi}\left( s,a \right) " eeimg="1"/> </p><p>其中 <img src="https://www.zhihu.com/equation?tex=+%5Cboldsymbol%7Bw%7D+" alt=" \boldsymbol{w} " eeimg="1"/> 称为“权重”。那怎么把这个权重求出来，即拟合出这样一个合适的函数呢？<b>这里就要结合机器学习算法里的一些有监督学习算法，对输入的状态提取特征作为输入，通过MC/TD计算出值函数作为输出，然后对函数参数</b> <img src="https://www.zhihu.com/equation?tex=+%5Cboldsymbol%7Bw%7D+" alt=" \boldsymbol{w} " eeimg="1"/> <b>进行训练，直到收敛。</b>这里主要说的是回归算法，比如线性回归、决策树、<b>神经网络</b>等。</p><p>这里，就可以引入DQN（Deep Q-Network）了，<b>实际上它就是Q-Learning和神经网络的结合，将Q-Learning的Q表变成了Q-Network。</b></p>

### 时间差分(TD)和蒙特卡洛(MC)方法
[强化学习中时间差分(TD)和蒙特卡洛(MC)方法各自的优劣？](https://www.zhihu.com/question/62388365)


&nbsp;
## References
[强化学习（Reinforcement Learning）知识整理](https://zhuanlan.zhihu.com/p/25319023)    
[强化学习——从Q-Learning到DQN到底发生了什么？](https://zhuanlan.zhihu.com/p/35882937)

