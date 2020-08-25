# Reinforcement Learning
*This whole note is under review. The following text is only a draft.*

Reinforcement Learning (RL) is the study of agents that learn how to achieve some goal by maximizing rewards in some environment. It's inspired by the punishment and pleasure mechanisms present in animals, including humans. A living being, using its sensors to get signals from the environment, learn from experience the actions that should be done and the ones that should be avoided, all without a detailed prior model of how the environment works. RL builds upon this idea to create intelligent algorithms.

Reinforcement Learning is considered a third paradigm of [Machine Learning](machine-learning.md), besides supervised and unsupervised learning. It's not supervised because the agent doesn't have access to a previous labeled dataset where it can learn from. It's also not unsupervised because it's not purely trying to find patterns in unlabeled data.

## Markov Decision Processes
When applying RL, it's important to model the collection of rewards by the agent as the time goes by. A simple mathematical tool used to model time passage is the [Markov process](temporal-pattern-recognition). In the context of RL, an extension of the Markov process called Markov Decision Process (MDP) is often used. MDPs are Markov processes with a reward attached to each state. Each transition of states in a MDP is achieved by some agent's action. These transitions may be subject to a probability distribution, which means the same action at the same state may lead the agent to different states.

More formally, a MDP is a tuple *M = (S, A, T, R, γ)* where:
* *S* is the finite set of **states** of the environment
* *A* is the finite set of **actions**
* *T: S x A x S → [0, 1]* is the **state transition function** that gives the probability of ending up in state *s'* from state *s* after performing action *a*
* *R: S → ℝ* is the **reward function** that gives the reward obtained by the agent if it's in state *s*
* *0 < γ < 1* is the **discount factor** (see discussion below)

An important concept in MDPs is the **value** of a state. While the reward gives the immediate result of a state, the value gives the long-term reward. Focusing solely on the reward of a single state would make the agent to take actions that are initially good but would lead it to a very bad state. It would also make the agent loss the opportunity of taking low reward states that would ultimately take it to a "pot of gold" in the end.

Although we could define the value function as the sum of the expected rewards from a given state, this would bring problems when dealing with infinite scenarios where the agent never stops acting in the environment. In those cases, a simple sum would amount the value of any state to infinite and no comparisons would be possible. A better definition of value is through the use of a discount factor γ that makes future rewards less important and converges to a single number even after an infinite sequence of actions. Specifically, a function *V* can be define as:

<p align="center"><img src="https://latex.codecogs.com/svg.latex?V(s)=R(s)+\gamma\sum_{s'\in{S}}T(s,\pi(s),s')V(s')" /></p>

where *π(s)* is a the action that generates the highest value if the agent is in state *s*. The *π* function defines what we call an **optimal policy**. Notice that the definition of *V* is recursive and the *γ* discount factor is multiplied more and more times in the future states. It's essentially a geometric series, and this is the reason it converges to a fixed number.

The optimal policy *π* is initially unknown and of great interest. Having the optimal policy means we can make our agent use the best strategy possible in a given MDP. There are some algorithms to find *π*, most notable the **value iteration** and the **policy iteration** algorithms. Value iteration iterates over initially random values attached to each state, computing a new value for each state at every step and keeping the best one. This method has been proven to converge to the true state values. Once we have them, it's trivial to find the optimal policy: for each state, the agent should take the action that generates the best value.

Policy iteration, in its turn, starts with a random policy and then calculate *V* for each state according to that policy. In another step, the action that generates the best value in each state is compared to the one prescribed by the current policy. If a different action is better than the one in the policy, the policy is updated to prescribe this new action instead. The methods runs until convergence. Policy iteration exploits the fact that we don't need to know exactly the true state values in order to find the optimal policy.

## Monte-Carlo Learning
As we saw in the last section, there are solutions for the problem of finding a optimal policy when all the components of a MDP are known. But what if we don't know the transition probabilities?

In order to be able to find a solution in such a environment, first we must find a way to evaluate a given policy. The simplest idea is doing **Monte-Carlo learning**, which is basically simulating many episodes and averaging out the results. An overview of the process goes like this:
1. Select a policy to be evaluated
2. Simulate an agent that follows that policy until a terminal state is reached (end of the episode)
3. The terminal state will generate a reward. This final reward can be propagated back for the calculation of the value of all the states found in the way using the *V* function.
4. Repeat steps 2 and 3 many times.
5. For each state, consider the final estimated value the mean of the values calculated for that state over all the episodes.

Mean calculation can be done incrementally. Given the sequence *x<sub>1</sub>, x<sub>2</sub>, ...*, the sequence of means *μ<sub>1</sub>, μ<sub>2</sub>, ...* can be computed as following:

<p align="center"><img src="https://latex.codecogs.com/svg.latex?\begin{align*}\mu_k&=\frac{1}{k}\sum_{j=1}^kx_j\\&=\frac{1}{k}\left(x_k+\sum_{j=1}^{k-1}x_j\right)\\&=\frac{1}{k}[x_k+(k-1)\mu_{k-1}]\\&=\mu_{k-1}+\frac{1}{k}(x_k-\mu_{k-1})\\&=\mu_{k-1}+\alpha(x_k-\mu_{k-1})&&&&\alpha=\frac{1}{k}\end{align*}" /></p>

The *α* term can be viewed as a parameter for the calculation of a different kind of mean (for example, *α* can be a constant instead of *1/k*). This type of iterative mean calculation is widespread in AI models and can be interpreted as the correction of an estimative when predicting *μ<sub>k-1</sub>* but getting *x<sub>k</sub>* instead. In this view, *α* acts as a learning rate and *(x<sub>k</sub> - μ<sub>k-1</sub>)* as an error term.

Using the same logic, a **Monte-Carlo update** that calculates the new value for a state given the result of a episode can be written as **_V(s) ← V(s) + α(G<sub>t</sub> - V(s))_**, where *G<sub>t</sub>* is the true value obtained at the end of an episode *t* that passed by the state *s*.

This procedure converges to the true state values under a given policy. We can then apply control theory methods to find the optimal policy.

## Temporal-Difference Learning
Monte-Carlo learning always considers all the subsequent states' values of a given state. Instead, **Temporal-Difference (TD) learning** builds upon the idea of using only the values of *some* of the following states.

The simplest TD algorithm is called **TD(0)**. It only considers the value of the next state. For example, if an agent performs some action in a state *s<sub>t</sub>* and transitions to a state *s<sub>t+1</sub>*, only *V(s<sub>t+1</sub>)* will be used to update the value of *V(s<sub>t</sub>)*. The update is given by **_V(s<sub>t</sub>) ← V(s<sub>t</sub>) + α(R(s<sub>t+1</sub>) + γV(s<sub>t+1</sub>) - V(s<sub>t</sub>))_**. Compared to the Monte-Carlo update, we can see that *G<sub>t</sub>* was switched for *R(s<sub>t+1</sub>) + γV(s<sub>t+1</sub>)*.

Why only consider the next state instead of wait the episode to finish and get the more precise value as it's done in Monte-Carlo learning? A first advantage of TD(0) is that it works even in MDPs that are not episodic (i.e. the ones that may never reach a terminal state). But an even more compelling advantage of TD(0) over Monte-Carlo is that it usually converges faster. This is a surprising fact given that Monte-Carlo always uses the true outcomes of a MDP, while TD(0) uses biased values by not looking at the end of the road.

This result is a consequence of TD(0)'s implicit consideration of the MDP structure and its Markov properties when updating the values. For example, imagine a simple MDP with two states *A* and *B*. Three episodes in this MDP generate the following sequences of states and rewards: *(B, 1)*, *(B, 1)* and *(A, 0, B, 0)*. In order to calculate *V(A)*, Monte-Carlo learning will check the final reward of all episodes that passed by *A*, propagate the reward back to A using the value function and average the results. In this case, there is only one episode with *A*. As this episode generates a only rewards of 0, *V(A)* becomes 0.

In its turn, TD(0) will calculate *V(A)* based *only* on *V(B)*. Because *B* generated more positive values in other episodes, this means it will have a value higher than 0 and this will be reflected in the calculation of *V(A)*, that will also be more than 0. TD(0) is implicitly accounting for the fact that if an agent can reach *B* from *A*, *B* has to have a major influence on *A*'s value, even if the experience so far with *A* tells another story. This larger connection between A and B is ignored by Monte-Carlo learning.

TD(0) and Monte-Carlo can be combined to form a more general algorithm called **TD(λ)**. As its name implies, it makes use of a *λ* (*0 ≤ λ ≤ 1*) parameter to control how much the learning should consider the future states of the episode. If *λ = 0*, TD(λ) behaves the same way as TD(0). If *λ = 1*, TD(λ) becomes Monte-Carlo learning.

Again, why bother mixing TD(0) and Monte-Carlo approaches if the former is considered better than the latter? It turns out that intermediate values of *λ* makes learning even more efficient than TD(0) for many MDPs in practice.

## Control Theory

TD Learning—which includes Monte-Carlo learning thanks to TD(λ)—is only good to evaluate a given policy. The greater question of finding an optimal policy still remains.