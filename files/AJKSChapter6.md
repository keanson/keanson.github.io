[TOC]

# Overview

This is my note for Chapter 6 of [Reinforcement Learning Theory](https://rltheorybook.github.io/rltheorybook_AJKS.pdf).

# Multi-Armed & Linear Bandits

$$
M=\{s_0,\{a_1,...,a_K\},H=1,R\}
$$

Why it is important?

when $\gamma = 0$ or $H=1$, the problem of MDP is reduced to the multi-armed bandit problem.

Note that reward is stochastic in this chapter.

## The K-Armed Bandit Problem

The setting is where we have K decisions (the “arms'”), where when we play arm $a \in \{1,2,\ldots K\}$ we obtain a random reward $r_a\in[-1,1]$ from $R(a)\in\Delta([-1,1])$ which has mean reward:

$$
\mathbb{E}_{r_a\sim R(a)}[r_a]=\mu_a
$$

where it is easy to see that $\mu_a\in[-1,1].$

More formally, we have the following interactive learning process

For $t=0\to T-1$

1. Learner pulls arm $I_t \in \{1,...,K\}$ (# based on historical information)

2. Learner observes an i.i.d reward $r_t\sim R(I_t)$ of arm $I_t$

Objective: 
$$
R_T=T\cdot\max\limits_i\mu_i-\sum\limits_{t=0}^{T-1}\mu_{I_t}
$$
Goal: no-regret, i.e., $R_T/T \to 0$ as $T \to \infty$.

We denote $a^\star=\arg\max_i\mu_i$ as the optimal arm. We define gap $\Delta_a=\mu_{a^{\star}}-\mu_a$ for any arm $a$.

**Theorem 6.1.** There exists an algorithm such that with probability at least $1-\delta,$ we have
$$
R_T=O\left(\min\left\{\sqrt{KT\cdot\ln(TK/\delta)},\sum\limits_{a\ne a^*}\frac{\ln(TK/\delta)}{\Delta_a}\right\}+K\right).
$$

---

Proof: shown later in P65, after Lemma 6.2.

Note that $\mu_{a^\star} \le UCB(a^\star) \le UCB(I_t)$.

---

==Remarks==: This is $\tilde{O}(\sqrt{KT})$.

### Some attempts

For ease of analysis, here we assume the reward is in $[0, 1]$.

1. Greedy algo: try each arm and then pull the highest one observed. This algo brings **constant regret** ($R_T/T \to C$) if in the first round, the optimal arm did not show up.

2. Explore and Commit algo:

   For $k= 1, \ldots, K:$

   ​	Pull arm-$k$ $N$ times, observe $\left\{r_i\right\}_{i=1}^N\sim R(k)$

   ​	Calculate arm $k$'s empirical mean: $\hat{\mu}_k=\sum_{i=1}^N r_i/N$

   For $t = NK, \ldots, T-1$:

   ​	Pull the best empirical arm, $I_t = \arg \max_{i \in [K]} \hat{\mu}_i$

Some light analysis: Hoeffding inequality (not very sharp but good for a sum of bounded r.v.)

Given a distribution $\mu \in \Delta([0,1]),$ and $N$ i.i.d samples $\{r_i\}_{i=1}^N\sim\mu,$w/ probability at least $1-\delta$ we have:
$$
\left|\sum\limits_{i=1}^N r_i/N-\mu\right|\le O\left(\sqrt{\frac{\ln(1/\delta)}{N}}\right) \tag{1}
$$
==Remarks==: A sharper analysis is possible, yet Hoeffding is simple and straightforward in giving a confidence interval $(\hat{\mu} - \sqrt{\ln(1/\delta)/N}, \mu + \sqrt{\ln(1/\delta)/N})$.

After the Exploration phase, with probability at least $1-\delta$, **for all** arm $k\in[K]$, we have:
$$
|\hat{\mu}_k - \mu_k| \le O\left(\sqrt{\frac{\ln(K/\delta)}{N}}\right)
$$
==Remarks==: By Union Bound, with probability $1-K\delta$, we have for all $k$ that Equation 1. holds. Substitute $K\delta \leftarrow \delta$, we have the above statement.

Thus we have 
$$
R_T = R_{\text{explore}} + R_{\text{exploit}},
$$
where $R_{\text{explore}} \le NK$, and $R_{\text{exploit}} \le (T-NK) (\mu_{I^\star} - \mu_{\hat{I}})$, in which $I^\star$ denotes the best arm and $\hat{I}$ denotes the best empirical arm.

Thus we only need to bound the second part:
$$
\begin{aligned}
\mu_{I^{\star}}-\mu_{\hat{I}}\leq
&~\left[\hat{\mu}_{I^{\star}}+\sqrt{\ln(K/\delta)/N}\right]-\left[\hat{\mu}_{\hat{I}}-\sqrt{\ln(K/\delta)/N}\right] \\
=&~\hat{\mu}_{I^{\star}}-\hat{\mu}_{\hat{I}}+2\sqrt{\ln(K/\delta)/N} \\
\le &~ 2\sqrt{\ln(K/\delta)/N},
\end{aligned}
$$
where the second line is due to $\hat{\mu}_{\hat{I}} \ge \hat{\mu}_{i}, \forall i$.

Now we have
$$
R_T \le NK + 2T \sqrt{\frac{\ln(K/\delta)}{N}},
$$
where $N$ is a hyperparameter. Thus we minimize w.r.t. $N$ and obtain
$$
R_T \le O\left(T^{2/3}K^{1/3}\cdot\ln^{1/3}(K/\delta)\right)
$$
when $N$ is set as $\left(\frac{T\sqrt{\ln(K/\delta)}}{2K}\right)^{2/3}$.

This is not good enough, so can we get a $\sqrt{T}$ bound?

### The Upper Confidence Bound (UCB) Algorithm

We will give the algo first and then dive into analysis and why the name

Play all arms and denote received reward as $r_a$ for all $a \in \{1, 2, \ldots, K\}$.

for $t = 1, \ldots, T-K$:

​	Execute arm $I_t = \arg \max_{i \in [K]} \Big (\hat{\mu}_i^t + \sqrt{\frac{\ln(TK / \delta)}{N_i^t}} \Big)$

​	Observe $r_t := r_{I_t}$

where $N^t_i$ and $\hat{\mu}_a^t$ are random r.v. defined as
$$
N_a^t=1+\sum\limits_{i=1}^{t-1}\mathbb I\{I_i=a\}
$$

$$
\hat{\mu}_a^t=\frac{1}{N_a^t}\left(r_a+\sum\limits_{i=1}^{t-1}\mathbb{I}\{I_i=a\}r_i\right)
$$

which denotes the counts and empirical mean of each arm.

Just like the Explore and Commit algo, we also construct the confidence interval as follows:

**Lemma 6.2** (Upper Confidence Bound). For all t ∈[1,...,T| and a∈[1,2,...K|, we have that with probability a least $1-\delta,$
$$
|\hat{\mu}_a^t-\mu_a|\leq2\sqrt{\frac{\ln(TK/\delta)}{N_a^t}}.
$$

---

Proof at P64 of the book.

==Remarks==: Here we have to use the martingale version of Hoeffding's inequality, i.e., Azuma-Hoeffding's inequality. (here I use the version from [wiki](https://en.wikipedia.org/wiki/Azuma%27s_inequality))
$$
P(|X_N-X_0|\ge\epsilon)\le2\exp\left(\frac{-\epsilon^2}{2\sum_{k=1}^N c_k^2}\right)
$$
Notice that $c_k = \mathbb I\{I_k = a\}$, so $\sum_{k=0}^{t-1} c_k^2 = N_a^t$ , and we obtain the version used in P64 by some simple calculation.

Directly checking appendix A.5 is more straightforward though.

---

==Remarks==: Now it should be clear why this is called UCB, since it chooses the arm with the highest upper confidence bound every iteration.

What is the intuition behind UCB?

==Remarks==: When UCB is great, it may be 2 cases. Case 1: uncertainty high->need for exploration. Case 2: uncertainty low->simply a good arm

## Linear Bandits: Handling Large Action Spaces

Assumptions for larger action spaces: 

Let $D \subset \mathbb{R}^d$ be a compact (but otherwise arbitrary) set of decisions. On each round, we must choose a decision
$x_t \in D$. Each such choice results in a reward $r_t\in[-1,1]$.

Now we assume **linearity**:
$$
\mathbb{E}[r_t|x_t=x]=\mu^{\star}\cdot x\in[-1,1],
$$
and define the noise sequence as:
$$
\eta_t = r_t - \mu^\star \cdot x_t,
$$
which is a martingale difference sequence.

==Remarks==: Proof is simple due to $\mathbb E [\eta_{t+1} | x_t] = \mathbb E[\eta_{t+1}] = \mathbb E[r_{t+1} - \mathbb E[r_{t+1}|x_{t+1}]] = 0$.

lf $x_0, \ldots, x_{T-1}$ are the decisions made in the game, then define the **cumulative regret** by

$$
R_T=T\mu^{\star}\cdot x^{\star}-\sum_{t=0}^{T-1}\mu^{\star}\cdot x_t
$$

where $x^{\star}\in D$ is an optimal decision for $\mu^\star$, i.e

$$
x^\star\in\arg\max_{x\in D}\mu^\star\cdot x.
$$
==Remarks==: Note that $x^\star$ exists due to $D$ is compact in our assumption, which says every subset has a supremum in $D$.

==Remarks==: It might be hard to directly interpret this model as a linear model, though there exists some linearity in the conditional expectation. Personally, [this formulation](https://courses.cs.washington.edu/courses/cse599i/18wi/resources/lecture9/lecture9.pdf) in 1.2 seems more natural as it is just linear regression in some sense.



LinUCB is based on “optimism in the face of uncertainty”. At episode $t$, we use all previous experience to define an uncertainty region (an ellipse) $\text{BALL}_t$. The center of this region, $\widehat{\mu}_t$, is the solution of the following regularized least squares problem:
$$
\begin{aligned}\widehat{\mu}_t&=\arg\min\limits_{\mu}\sum_{\tau=0}^{t-1}\|\mu\cdot x_\tau-r_\tau\|_2^2+\lambda\|\mu\|_2^2\\ &=\Sigma_t^{-1}\sum_{\tau=0}^{t-1}r_\tau x_\tau,\end{aligned}
$$
where $\lambda$ is a parameter and where

$$
\Sigma_t=\lambda I+\sum_{\tau=0}^{t-1}x_\tau x_\tau^\top,\textrm{with}\Sigma_0=\lambda I.
$$

The shape of the region $\text {BALL}_t$, is defined through the feature covariance $\Sigma_t$. More precisely, we can define the uncertainty ball as
$$
\text{BALL}_t = \left\{\mu|(\widehat{\mu}_t-\mu)^{\top}\Sigma_t(\widehat{\mu}_t-\mu)\leq\beta_t\right\},
$$
where $\beta_t$ is a hyperparameter.

Now we present the Linear UCB algo:

Input: $\lambda, \beta_t$

​	for $t=0, 1, \ldots$ do

​		Execute $x_t=\arg\max_{x\in D}\max\limits_{\mu\in\mathrm{BALL}_t}\mu\cdot x$ and observe the reward $r_t$.

​		Update $BALL_{t+1}$

==Remarks==: Thought the book first give bound results, I suppose it would be more natural to see why the ball is some uncertainty region, so I move this part here.

Theoretical results: valid confidence ball

**Proposition 6.6.**（Confidence) Let $\delta>0.$ We have that
$$
\Pr(\forall t,\mu^{\star}\in\text{BALL}_t)\geq1-\delta.
$$

---

Proof see next section

---

Theoretical results: upper and lower bounds

**Theorem 6.3**. Suppose that the expected costs are bounded, in magnitude, by $1$, i.e. that  $\left|\mu^{\star}\cdot x\right|\le1$  for all $ x\in D;$ that $\|\mu^{\star}\|\le W$ and $\|x\|\leq B$ for all $x \in D;$ and that the noise $\eta_t $ is $ \sigma^2$ sub-Gaussian. Set
$$
\lambda=\sigma^2/W^2,\beta_t:=\sigma^2\Big(2+4d\log\Big(1+\frac{tB^2W^2}{d}\Big)+8\log(4/\delta)\Big).
$$

We have that with probability greater than $1 - \delta$, that (simultaneously) for all $T \geq 0,$

$$
R_T\leq c\sigma\sqrt{T}\left(d\log\left(1+\frac{TB^2W^2}{d\sigma^2}\right)+\log(4/\delta)\right)
$$

where $c$ is an absolute constant. In other words, we have that $R_T$ is $\tilde{O}(d\sqrt{T})$ with high probability.

---

Proof at P69 of the book.

---

==Remarks==: Note that there is no dependence on $|\mathcal D|$, which is a great property.

**Theorem 6.4.**（Lower bound) There exists a distribution over linear bandit problems (i.e. a distribution over $\mu$）with the rewards being bounded by 1, in magnitude, and $\sigma^2 \leq 1,$ such that for every (randomized) algorithm, we have for $n \ge\max\{256,d^2/16\},$
$$
\mathbb E_\mu\mathbb E R_T\geq\frac{1}{2500}d\sqrt T.
$$

where the inner expectation is with respect to randomness in the problem and the algorithm.

---

Proof not provided.

---

==Remarks==: This shows that LinUCB is minimax optimal.

## LinUCB Analysis

This section is to understand the growth of $(\widehat{\mu}_t-\mu)^{\top}\Sigma_t(\widehat{\mu}_t-\mu)$. The first analysis is about regret, and the second one is about confidence.

**Proposition 6.7.**（Sum of Squares Regret Bound) Suppose that $\|x\|\leq B$ for $x\in D.$ Suppose $\beta_t$ is increasing and that $\beta_t \ge 1$. For LinUCB, if $\mu^\star \in \text{BALL}_t$ for all $t$, then
$$
\sum_{k=0}^{T-1}\text{regret}_t^2\leq8\beta_T d\log\left(1+\frac{TB^2}{d\lambda}\right).
$$
Proof in this section is somewhat tedious, so I leave that to the book and only write remarks here.

==Remarks==: Recap for ellipsoid/ball
if $A=A^T>0$ the set

$$
\mathcal{E}=\{x\mid x^TAx\le1\}
$$

is an ellipsoid in $\mathbb{R}^n$ , centered at $0$.

semi-axes are given by $s_i=\lambda_i^{-1/2}q_i, i.e,...
- eigenvectors determine directions of semiaxes
- eigenvalues determine lengths of semiaxes

Define width in the direction $q$ as
$$
\sup_{z \in \mathcal E} q^\top z - \inf_{z \in \mathcal E} q^\top z = 2\|A^{1/2}q\|_2.
$$
Thus the minimum width and maximum width is given by $2 \lambda_{\min}(A)^{1/2}$ and $2 \lambda_{\max}(A)^{1/2}$.

==Remarks==: Now we can see why $w_t := \sqrt{x_t^\top \Sigma_t^{-1} x_t}$ is the "normalized width" of direction $x_t$.

==Remarks==: Typo at P69, should be maximizes

==Remarks==: Though not indicated, we only need to substitute $\beta_t$ with the upper bound obtained in 6.3.2 to verify that $\text{BALL}_t$ is indeed the uncertainty ball.

However, the exact computational method is not clear for LinUCB yet. In some cases, LinUCB can be NP-hard.