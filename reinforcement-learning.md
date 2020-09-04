# Reinforcement Learning
*This whole note is under review. The following text is only a draft.*

Reinforcement Learning (RL) is the study of agents that learn how to achieve some goal by maximizing rewards in some environment. It's inspired by the punishment and pleasure mechanisms present in animals, including humans. A living being, using its sensors to get signals from the environment, learn from experience the actions that should be done and the ones that should be avoided, all without a detailed prior model of how the environment works. RL builds upon this idea to create intelligent algorithms.

Reinforcement Learning is considered a third paradigm of [Machine Learning](machine-learning.md), besides supervised and unsupervised learning. It's not supervised because the agent doesn't have access to a previous labeled dataset where it can learn from. It's also not unsupervised because it's not purely trying to find patterns in unlabeled data.

## Markov Decision Processes
When applying RL, it's important to model the collection of rewards by the agent as the time goes by. A simple mathematical tool used to model time passage is the [Markov process](temporal-pattern-recognition). In the context of RL, an extension of the Markov process called Markov Decision Process (MDP) is often used. MDPs are Markov processes with a reward attached to each state transition. Each transition is achieved by some agent's action. These transitions may be subject to a probability distribution, which means the same action at the same state may lead the agent to different states.

More formally, a MDP is a tuple *M = (S, A, T, R, γ)* where:
* *S* is the finite set of **states** of the environment
* *A* is the finite set of **actions**
* *T: S x A x S → [0, 1]* is the **state transition function** that gives the probability of ending up in state *s'* from state *s* after performing action *a*
* *R: S x A x S → ℝ* is the **reward function** that gives the reward obtained by the agent if it is in state *s*, takes action *a* and ends up in state *s'*
* *0 < γ < 1* is the **discount factor** (see discussion below)

MDPs can represent continuing and episodic tasks. A **continuing task** is one that is supposed to run forever. As an example, imagine a vacuum robot that is always cleaning and recharging itself. Conversely, **episodic tasks** are tasks that have a well defined ending (such as a round of a game of chess). Episodic tasks have **terminal states** representing the end of an episode (e.g. the moment a player loses or win the round). We can treat episodic tasks in the same way as continuing tasks if we consider that each terminal state only transitions to itself and generates a reward of zero. In this fashion, terminal states can also be called **absorbing states**.

## Policies, Returns and Values

In the context of MDPs, a policy *π* is a function that returns an action given a state (i.e. *π: S → A*). A policy models the behavior of an agent. From a policy, we can **sample a walk** in the MDP. This can be represented as a sequence of state-actions-reward triplets. In mathematical notation:

<p align="center"><img src="https://latex.codecogs.com/svg.latex?\pi\sim s_0,a_1,r_1,s_1,a_2,r_2,..." /></p>
<p align="center"><img src="https://latex.codecogs.com/svg.latex?s_i\in S,a_i\in A,r_{i+1}=R(s_{i},a_{i+1},s_{i+1}),i\geq 0" /></p>

Notice the states *s<sub>i</sub>* in the sequence can be the same since a MDP can contain loops.

The **return** of a sample is how good it is in terms of rewards generated. More specifically, we are interested in knowing how good a sample has done from a specific sate. This metric is useful to help the agent pick its next move. Not only we want our agent to get a high reward when it transitions, but we also want it to move to a state that has the potential of generating more good rewards. Focusing solely on the next best reward would make the agent take actions that are initially good but would lead it to a very bad state. It would also make the agent loss the opportunity of taking low reward states that would ultimately take it to a "pot of gold" in the end.

Although we could define the return of a state as the sum of the rewards obtained after that state, this would bring problems with continuing tasks that never reach a terminal state, or even with episodic tasks that contain deadlocks. A simple sum of rewards would amount the return of a state to infinite and no comparisons would be possible. A better definition of return is through the use of a discount factor *γ* that makes future rewards less important and converges to a single number even in an infinite sequence. Specifically, a return *G<sub>t</sub>* of the state *t* in a sequence can be define as:

<p align="center"><img src="https://latex.codecogs.com/svg.latex?G_t=r_{t+1}+\gamma r_{t+2}+\gamma^2r_{t+3}+...=\sum_{k=0}^{\infty}\gamma^kr_{t+k+1}\qquad 0\leq\gamma\leq 1" /></p>

Notice the *γ* factor determines how much weight is given to future rewards. If *γ* is close to 0, the agent will act "myopic". As *γ* approaches 1, the agent gets more far-sighted. Nevertheless, the way *γ* is applied to future rewards forms a geometric series, and this makes the return converge to a fixed number.

Because of the stochasticity of the MDP, we are not really interested in the return, which is specific to a single walk, but in the expectation of returns. We call that expectation as the **value** of a state. The value function V is defined as follows:

<p align="center"><img src="https://latex.codecogs.com/svg.latex?\begin{align*}V_\pi(s)&=\mathbb{E}[G_t|s_t=s]\\&=\mathbb{E}[r_{t+1}+\gamma r_{t+2}+\gamma^2r_{t+3}+...|s_t=s]\\&=\mathbb{E}[r_{t+1}+\gamma(r_{t+2}+\gamma r_{t+3}+\gamma^2 r_{t+4}...)|s_t=s]\\&=\mathbb{E}[r_{t+1}+\gamma G_{t+1}|s_t=s]\\&=\sum_{s'\in{S}}T(s,\pi(s),s')[R(s,\pi(s),s')+\gamma\mathbb{E}[G_{t+1}|s_{t+1}=s']]\\&=\sum_{s'\in{S}}T(s,\pi(s),s')[R(s,\pi(s),s')+\gamma V_\pi(s')]]\end{align*}" /></p>

Where *π* is the policy being followed by the agent. In its last recursive form, V is called a **Bellman equation**.

## Optimal policy

From the *V* formula, we can devise a policy *π<sup>\*</sup>(s)* that always pick the action that generates the highest value. This *π<sup>\*</sup>* defines what we call an **optimal policy**.

The optimal policy *π<sup>\*</sup>* is initially unknown and of great interest. Having the optimal policy means we can make our agent use the best strategy possible in a given MDP. There are some algorithms to find *π<sup>\*</sup>*, most notable the **value iteration** and the **policy iteration** algorithms. Value iteration iterates over initially random values attached to each state, computing a new value for each state at every step. This method has been proven to converge to the true state values. Once we have them, it's trivial to find the optimal policy: for each state, the agent should take the action that generates the best value.

Policy iteration, in its turn, starts with a random policy and then calculate *V* for each state according to that policy (the **policy evaluation** step). In another step, the action that generates the best value in each state is compared to the one prescribed by the current policy. If a different action is better than the one in the policy, the policy is updated to prescribe this new action instead (the **policy improvement** step). The method runs until convergence. Policy iteration exploits the fact that we don't need to know exactly the true state values in order to find the optimal policy.

## Monte-Carlo Policy Evaluation
As we saw in the last section, there are solutions for the problem of finding an optimal policy when all the components of an MDP are known. But what if we don't know the transition probabilities? This is a more realistic scenario, as it would be impossible or infeasible to model the *T* function beforehand.

In such an environment, we can apply **control methods** that are akin to policy evaluation. We try some policy, evaluate it and improve it if other actions turned out to be better.

As now we don't know *T*, we can't evaluate a policy using *V*, so first we must find a new evaluation method. For episodic tasks, the simplest idea is doing **Monte-Carlo (MC) policy evaluation**, which is basically simulating many episodes and averaging out the results. An overview of the process goes like this:
1. Select a policy to be evaluated.
2. Simulate an agent that follows that policy until a terminal state is reached. In other words, sample a walk using that policy.
3. The transition to the terminal state will generate a reward. This final reward can be propagated back for the calculation of the return of all the states found in the way using  *G<sub>t</sub>*.
4. Repeat steps 2 and 3 many times.
5. For each state, consider the final estimated value the mean of the returns calculated for that state over all the episodes.

The last step involves calculating a mean. Mean calculation can be done incrementally, so an incremental algorithm can be devised. Given the sequence *x<sub>1</sub>, x<sub>2</sub>, ...*, the sequence of means *μ<sub>1</sub>, μ<sub>2</sub>, ...* can be computed as following:

<p align="center"><img src="https://latex.codecogs.com/svg.latex?\begin{align*}\mu_k&=\frac{1}{k}\sum_{j=1}^kx_j\\&=\frac{1}{k}\left(x_k+\sum_{j=1}^{k-1}x_j\right)\\&=\frac{1}{k}[x_k+(k-1)\mu_{k-1}]\\&=\mu_{k-1}+\frac{1}{k}(x_k-\mu_{k-1})\\&=\mu_{k-1}+\alpha(x_k-\mu_{k-1})&&&&\alpha=\frac{1}{k}\end{align*}" /></p>

The *α* term can be viewed as a parameter for the calculation of a different kind of mean (for example, *α* can be a constant instead of *1/k*). This type of iterative mean calculation is widespread in AI models and can be interpreted as the correction of an estimative when predicting *μ<sub>k-1</sub>* but getting *x<sub>k</sub>* instead. In this view, *α* acts as a learning rate and *(x<sub>k</sub> - μ<sub>k-1</sub>)* as an error term.

Using the same logic, a **Monte-Carlo update** that calculates the new value for a state given the result of an episode can be written as **_V(s<sub>t</sub>) ← V(s<sub>t</sub>) + α[G<sub>t</sub> - V(s<sub>t</sub>)]_**. This procedure converges to the true state values under a given policy.

## Temporal-Difference Policy Evaluation
MC policy evaluation always considers all the subsequent states' returns of a given state. Instead, **Temporal-Difference (TD) policy evaluation** builds upon the idea of using only the current estimated values of *some* of the future states.

The simplest TD algorithm is called **TD(0)**. It only considers the value of the next state. For example, if an agent performs some action in a state *s<sub>t</sub>* and transitions to a state *s<sub>t+1</sub>*, only *V(s<sub>t+1</sub>)* will be used to update the value of *V(s<sub>t</sub>)*. The update is given by **_V(s<sub>t</sub>) ← V(s<sub>t</sub>) + α[r<sub>t+1</sub> + γV(s<sub>t+1</sub>) - V(s<sub>t</sub>)]_**. Compared to the Monte-Carlo update, we can see that *G<sub>t</sub>* was switched for *r<sub>t+1</sub> + γV(s<sub>t+1</sub>)*.

Why only consider the next state instead of wait the episode to finish and get the more precise value as it's done in Monte-Carlo learning? A first advantage of TD(0) is that it works for continuing tasks. TD(0) update allows us to calculate a new value right after the state transition. We don't have to wait for the end of some episode anymore.

But an even more compelling advantage of TD(0) over Monte-Carlo is that it usually converges faster. This is a surprising fact given that Monte-Carlo always uses the true outcomes of a MDP, while TD(0) uses biased values by not looking at the end of the road.

This result is a consequence of TD(0)'s implicit consideration of the MDP structure and its Markov properties when updating the values. For example, imagine a simple MDP with two states *A* and *B*. Three episodes in this MDP generate the following sequences of states and rewards: *(B, 1)*, *(B, 1)* and *(A, 0, B, 0)*. In order to calculate *V(A)*, Monte-Carlo learning will check the final reward of all episodes that passed by *A*, propagate the reward back to A using the return function and average the results. In this case, there is only one episode with *A*. As this episode generates only one reward of 0, *V(A)* becomes 0.

In its turn, TD(0) will calculate *V(A)* based *only* on *V(B)*. Because *B* generated more positive values in the previous episodes, this means it will have a value higher than 0 and this will be reflected in the calculation of *V(A)*, that will also be more than 0. TD(0) is implicitly accounting for the fact that if an agent can reach *B* from *A*, *B* has to have a major influence on *A*'s value, even if the experience so far with *A* tells another story. This connection between A and B is ignored by Monte-Carlo learning.

TD(0) and Monte-Carlo can be combined to form a more general policy evaluation algorithm called **TD(λ)**. As its name implies, it makes use of a *λ* parameter (*0 ≤ λ ≤ 1*) to control how much the learning should consider the future states of the episode. If *λ = 0*, TD(λ) behaves the same way as TD(0). If *λ = 1*, TD(λ) becomes MC evaluation.

Again, why bother mixing TD(0) and MC approaches if the former is considered better than the latter? It turns out that intermediate values of *λ* makes learning even more efficient than TD(0) for many MDPs in practice.

## Control

The presented TD methods—which includes Monte-Carlo thanks to TD(λ)—are only good to evaluate a given policy. The greater question of finding the optimal policy still remains. This is known in literature as the **control problem**.