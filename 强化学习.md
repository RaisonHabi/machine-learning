## 一、强化学习
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
## 二、知识整理
### 0.马尔可夫决策过程（Markov Decision Processes,MDPs）
MDPs 简单说就是一个智能体（Agent）采取行动（Action）从而改变自己的状态（State）获得奖励（Reward）与环境（Environment）发生交互的循环过程。

MDP 的策略完全取决于当前状态（Only present matters），这也是它马尔可夫性质的体现。  
其可以简单表示为：M=<S, A, Ps,a, R>

<h4>基本概念</h4><ol><li><img src="https://www.zhihu.com/equation?tex=s+%5Cin+S" alt="s \in S" eeimg="1"/>: 有限状态 state 集合，s 表示某个特定状态</li><li><img src="https://www.zhihu.com/equation?tex=a+%5Cin+A" alt="a \in A" eeimg="1"/>: 有限动作 action 集合，a 表示某个特定动作</li><li>Transition Model <img src="https://www.zhihu.com/equation?tex=T%28S%2C+a%2C+S%27%29+%5Csim+P_r%28s%27%7Cs%2C+a%29" alt="T(S, a, S&#39;) \sim P_r(s&#39;|s, a)" eeimg="1"/>: Transition Model, 根据当前状态 s 和动作 a 预测下一个状态 s’，这里的 <img src="https://www.zhihu.com/equation?tex=P_r" alt="P_r" eeimg="1"/> 表示从 s 采取行动 a 转移到 s’ 的概率</li><li>Reward <img src="https://www.zhihu.com/equation?tex=R%28s%2C+a%29+%3D+E%5BR_%7Bt%2B1%7D%7Cs%2C+a%5D" alt="R(s, a) = E[R_{t+1}|s, a]" eeimg="1"/>:表示 agent 采取某个动作后的即时奖励，它还有 R(s, a, s’), R(s) 等表现形式，采用不同的形式，其意义略有不同</li><li>Policy <img src="https://www.zhihu.com/equation?tex=%5Cpi%28s%29+%5Cto+a" alt="\pi(s) \to a" eeimg="1"/>: 根据当前 state 来产生 action，可表现为 <img src="https://www.zhihu.com/equation?tex=a%3D%5Cpi%28s%29" alt="a=\pi(s)" eeimg="1"/> 或 <img src="https://www.zhihu.com/equation?tex=%5Cpi%28a%7Cs%29+%3D+P%5Ba%7Cs%5D" alt="\pi(a|s) = P[a|s]" eeimg="1"/>，后者表示某种状态下执行某个动作的概率</li></ol>

#### MDP 求解
我们需要找到最优的策略使未来回报最大化，求解过程大致可分为两步,具体内容会在后面展开
```
预测：给定策略，评估相应的状态价值函数和状态-动作价值函数
行动：根据价值函数得到当前状态对应的最优动作
```

<h3>Model-free v.s. Model-based</h3><p>Model-based 是指学习 Transition Model <img src="https://www.zhihu.com/equation?tex=T%28S%2C+a%2C+S%27%29+%5Csim+P_r%28s%27%7Cs%2C+a%29" alt="T(S, a, S&#39;) \sim P_r(s&#39;|s, a)" eeimg="1"/> ，即在状态 s 采取行动 a 后转移到 s&#39; 的概率，然后基于这个 Model 去选择最优的策略。Transition Model 的空间复杂度为 <img src="https://www.zhihu.com/equation?tex=O%28S%5E2A%29" alt="O(S^2A)" eeimg="1"/> ，所以不太适合用于解决状态空间和动作空间过大的问题。</p><p>Model-free 未知 Transition Model，通常通过不断的尝试去直接学习最优策略。</p><p>前面的 Policy Iteration 和 Value Iteration 都是 model-based 方法，因此一定程度上受限于状态空间和动作空间的规模。于是 Q-learning 应运而生。</p>

### Q-learning
公式如下，可以看出 Q-leaning 由 Value iteration 演变而来，但去除了对 Transition Model 的依赖，因此属于 Model-free 的方法。另一方面下一个动作 a 的选择，原来是根据 policy 选择最优的 action，现在是 maximum 下一个 state 的值来选择 action，所以 Q-learning 属于 off-policy 算法。

### State-Action-Reward-State-Action (SARSA)
公式如下，唯一与 Q-learning 的不同是，SARSA 是 on-policy 方法，需要考虑 exporation-exploitation 问题，基本方法是 [公式] -greedy。

### PG 和 Q learning 的问题
Policy Gradient 的问题是，1）大的策略更新使训练失败，2）有时很难将策略的变化映射到参数空间，3）不合适的学习率导致梯度消失或爆炸，4）样本效率（sample efficiency）低。

Q learning 的问题是，大部分情况下，对不同的 action 差别不会很大（方差小），且在部分任务中，Q function 的值总为正。

#### 优势函数 Advantage Function
优势函数就是为了解决 Q function 值方差小而引入的，基本形式为

&nbsp;
## 三、代码示例
[强化学习](https://wizardforcel.gitbooks.io/hands-on-ml-with-sklearn-and-tf/content/docs/16.%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0.html)

&nbsp;
## reference
[强化学习（Reinforcement Learning）知识整理](https://zhuanlan.zhihu.com/p/25319023)  
[]()  
[]()
