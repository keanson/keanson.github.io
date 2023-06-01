[TOC]

# Overview

This is my note for Chapter 1 of [Reinforcement Learning Theory](https://rltheorybook.github.io/rltheorybook_AJKS.pdf).

# Markov Decision Process

## Discounted (Infinite-Horizon) MDP

$$
M=(\mathcal{S},\mathcal{A},P,r,\gamma,\mu)
$$

- $\mathcal{S}$ finite or countably infinite
- $\mathcal{A}$ finite
- $P$ transition function (conditional distribution for state), where $P(s' | s, a)$ is the probability of transitioning into $s'$ given $s$ and $a$. Note that $P_{s,a}$ denotes $P(\cdot| s, a)$.
- $r$ bounded in $[0,1]$. Deterministic unless specified.
- $\gamma$ bounded in $[0, 1)$, which defines a horizon. 
  - ==Remarks==: 1. This is why it is called **discounted** MDP. 2. I assume it works like how eigenvalue defines the smoothness for an RKHS?
- $\mu$ is an initial state distribution.

### objective, policies, and values

- trajectory $\tau_t=(s_0,a_0,r_0,s_1,\dots,s_t,a_t,r_t)$ 

  - ==Remarks==: 1. It goes like state->action->reward->state->..., and each state is a sample. 2. Sort of like a filtration for a stochastic process

- policy $\pi$ is a mapping: $\mathcal{H}\to\Delta(\mathcal{A})$. A **stationary** policy mapping $\pi:\mathcal{S}\to\Delta(\mathcal{A})$. A **deterministic, stationary** policy mapping $\pi:\mathcal{S}\to\mathcal{A}$. 

  - ==Remarks==: 1. $a_t\sim \pi(\cdot|\tau_{t-1}, s_t)$? sort of weird

- value $V_M^\pi:\mathcal{S} \to \mathbb{R}$ is defined as:
  $$
  V_M^\pi(s)=\mathbb{E}\Big[\sum_{t=0}^\infty\gamma^tr(s_t,a_t)\bigm|\pi,s_0=s\Big],
  $$
  which is bounded by $1 / (1- \gamma)$.

- Similarly, action-value (Q-value) $Q_M^\pi:\mathcal{S}\times\mathcal{A}\to\mathbb{R}$​ is defined as
  $$
  Q_M^\pi(s,a)=\mathbb{E}\Big[\sum\limits_{t=0}^\infty\gamma^tr(s_t,a_t)\bigm|\pi,s_0=s,a_0=a\Big],
  $$
  which is also bounded by $1 / (1- \gamma)$.

- Goal: find optimal policy given starting state $s$:
  $$
  \max_\pi V_M^{\pi}(s)
  $$

### Bellman Consistency Equations for Stationary Policies

**Lemma 1.4.** Suppose that $\pi$ is a stationary policy. Then $V^\pi$ and $Q^\pi$ satisfy the following Bellman consistency equations: for all $s\in\mathcal{S},a\in\mathcal{A},$
$$
V^{\pi}(s)=Q^{\pi}(s,\pi(s)).\\ Q^{\pi}(s,a)=r(s,a)+\gamma\mathbb{E}_{a\sim\pi(\cdot|s),s'\sim P(\cdot|s,a)}\left[V^{\pi}(s')\right].
$$
---

Proof:
$$
V^{\pi}(s) = \mathbb{E}_{a\sim\pi(\cdot|s)}[Q^\pi(s, a)] = Q^{\pi}(s,\pi(s))
$$

$$
\begin{aligned}
Q^\pi(s,a)=&~\mathbb{E}\Big[\sum\limits_{t=0}^\infty\gamma^tr(s_t,a_t)\bigm|\pi,s_0=s,a_0=a\Big] \\
=&~ r(s, a )+\gamma \mathbb{E}\Big[\sum\limits_{t=1}^\infty\gamma^{t-1}r(s_t,a_t)\bigm|\pi,s_0=s,a_0=a\Big] \\
=&~ r(s,a)+\gamma\mathbb{E}_{a\sim\pi(\cdot|s),s'\sim P(\cdot|s,a)}\left[V^{\pi}(s')\right].
\end{aligned}
$$

---

It is helpful to view $V^\pi$ as vector of length $|\mathcal S|$ and $Q^\pi$ and $r$ as vectors of length $|\mathcal S|·|\mathcal A|$. We overload notation and let $P$ also refer to a matrix of size $(|\mathcal S|\cdot|\mathcal A|)\times|\mathcal S|$ where the entry $P_{(s,a),s'}$ is equal to $P(s'|s,a)$.

==Remarks==: This is straightforward since a function is in some sense an infinite dimensional vector where each point is a coordinate.

We also will define $P^{\pi}$ to be the transition matrix on state-action pairs induced by a **stationary** policy $\pi$, specifically
$$
P_{(s,a),(s',a')}^{\pi}:=P(s'|s,a)\pi(a'|s’).
$$

==Remarks==: $P^\pi \in \mathbb R^{(|\mathcal S|\cdot|\mathcal A|)\times(|\mathcal S|\cdot|\mathcal A|)}$

In particular, for **deterministic** policies, we have:
$$
P_{(s,a),(s',a')}^{\pi}:=\left\{\begin{array}{cl}P(s'|s,a)&\textrm{if~} a'=\pi(s')\\ 0&\textrm{if~} a'\neq\pi(s')\end{array}\right.
$$

With this notation, it is straightforward to verify

$$
Q^{\pi}=r+\gamma PV^{\pi}\\ Q^{\pi}=r+\gamma P^{\pi}Q^{\pi}.
$$

---

Proof: Notice that 
$$
[PV^\pi]_{(s,a)} = \sum_{s' \in \mathcal{S}} P(s'|s, a) V^\pi(s') = \mathbb{E}_{s'\sim P(\cdot|s,a)}\left[V^{\pi}(s')\right].
$$
Thus, we have
$$
[Q^\pi]_{(s, a)} = r(s, a) + \gamma \mathbb{E}_{s'\sim P(\cdot|s,a)}\left[V^{\pi}(s')\right].
$$
Also, note that
$$
\begin{aligned}
~[P^\pi Q^\pi]_{(s, a)} = &~ \sum_{s', a'} P(s'|s, a) \pi(a'|s')Q^\pi(s', a') \\ 
= &~ \sum_{s'} P(s'|s, a) \mathbb{E}_{a' \sim \pi(\cdot|s')} Q^\pi (s', a') \\ 
= &~ \sum_{s'} P(s'|s, a) V^\pi(s').
\end{aligned}
$$

---

**Corollary 1.5.** Suppose that $\pi$ is a stationary policy. We have that.
$$
Q^{\pi}=(I-\gamma P^{\pi})^{-1}r
$$

where $I$ is the identity matrix.

---

Proof: we need to show that $I - \gamma P^\pi$ is invertible, which is to show that for all non-zero vector $x$, $(I - \gamma P^\pi)x \ne 0$.

To see that the $I-\gamma P^\pi$ is invertible, observe that for any non-zero vector $x\in\mathbb{R}^{|\mathcal{S}||\mathcal{A}|}$

$$
\begin{aligned}
\|(I-\gamma P^\pi)x\|_\infty&=\|x-\gamma P^\pi x\|_\infty \\
&\ge\|x\|_\infty-\gamma\|P^\pi x\|_\infty\\ &\ge\|x\|_\infty-\gamma\|x\|_\infty \text{\qquad(each element of $P^\pi x$ is an average of $x$)}\\
&=(1-\gamma)\|x\|_\infty>0
\end{aligned}
$$
which implies $I-\gamma P^\pi$ is full rank.

---

**Lemma 1.6.** We have that
$$
[(1-\gamma)(I-\gamma P^\pi)^{-1}]_{(s,a),(s',a')}=(1-\gamma)\sum\limits_{t=0}^\infty\gamma^t\mathbb P^\pi(s_t=s',a_t=a'|s_0=s,a_0=a)
$$

so we can view the $(s,a)$-th row of this matrix as an induced distribution over states and actions when following $\pi$ after starting with $s_0=s$ and $a_0=a$

---

Proof: we need to show $\sum_{t=0}^{\infty} \gamma^t \mathbb{P}_t^\pi (I-\gamma P^\pi) = I$, where we use $\mathbb{P}_t^\pi$ to denote the transition matrix where $[\mathbb{P}_t^\pi]_{(s, a), (s', a')} = \mathbb P^\pi(s_t=s',a_t=a'|s_0=s,a_0=a)$.

Thus, for the element at $(s, a), (s'', a'')$, we need to show
$$
\sum_{s', a'}\sum\limits_{t=0}^\infty\gamma^t\mathbb P^\pi(s_t=s',a_t=a'|s_0=s,a_0=a) \cdot(\mathbb{I}\{(s', a')=(s'',a'')\} - \gamma P(s''|s', a')\pi(a''|s'')) = \mathbb{I}\{(s, a)=(s'',a'')\}
$$
For the LHS, we have
$$
\begin{aligned}
LHS = &~ \sum\limits_{t=0}^\infty\gamma^t\mathbb P^\pi(s_t=s'',a_t=a''|s_0=s,a_0=a) - \sum\limits_{t=0}^\infty\gamma^{t+1} \sum_{s', a'}\mathbb P^\pi(s_t=s',a_t=a'|s_0=s,a_0=a) P(s''|s', a')\pi(a''|s'')) \\
=&~ \sum\limits_{t=0}^\infty\gamma^t\mathbb P^\pi(s_t=s'',a_t=a''|s_0=s,a_0=a) - \sum\limits_{t=0}^\infty\gamma^{t+1} \mathbb P^\pi(s_{t+1}=s'',a_{t+1}=a''|s_0=s,a_0=a) \\
=&~ \mathbb P^\pi(s_0=s'',a_{0}=a''|s_0=s,a_0=a)
\end{aligned}
$$
where in the second equation we use the Chapman-Kolmogorov equation. Thus the proof is completed by that $\mathbb P^\pi(s_0=s'',a_{0}=a''|s_0=s,a_0=a) = \mathbb{I}\{(s, a)=(s'',a'')\}$.

### Bellman Optimality Equations

==There exists a **stationary** and **deterministic** policy that simultaneously maximizes $V^\pi(s)$ for all $s$.==

**Theorem 1.7.** Let $\Pi$ be the set of all non-stationary and randomized policies. Define
$$
\begin{matrix}V^{\star}(s):=\sup_{\pi\in\Pi}V^{\pi}(s)\\ Q^{\star}(s,a):=\sup_{\pi\in\Pi}Q^{\pi}(s,a).\end{matrix}
$$

which is finite since $V^{\pi}(s)$ and $Q^{\pi}(s,a)$ are bounded between 0 and $1/(1-\gamma)$.

There exists a stationary and deterministic policy $\pi$ such that for all $s\in\mathcal{S}$ and $a\in\mathcal{A},$
$$
\begin{matrix}V^\pi(s)=V^\star(s)\\ Q^\pi(s,a)=Q^\star(s,a).\end{matrix}
$$

We refer to such a $\pi$ as an optimal policy.

---

Proof at P9 of the book.

---

==Remarks==: The optimal policy used in the proof is $\widetilde{\pi}(s)=\arg \sup\limits_{a\in \mathcal A}\mathbb{E}[r(s,a)+\gamma V^{\star}(s_1)|(S_0,A_0)=(s,a)]$. 

**Theorem 1.8 (Bellman optimality equations).** We say that a vector $Q \in \mathbb{R}^{|\mathcal{S}||\mathcal{A}|}$ satisfies the Bellman optimality equations if:
$$
Q(s,a)=r(s,a)+\gamma\mathbb{E}_{s'\sim P(\cdot|s,a)}\left[\max\limits_{a'\in\mathcal{A}}Q(s',a')\right].
$$

For any $Q\in\mathbb{R}^{|\mathcal{S}||\mathcal{A}|},$ we have that $Q=Q^\star$ if and only if $Q$ satisfies the Bellman optimality equations. Furthermore, the deterministic policy defined by $\pi(s)\in\operatorname{argmax}_{a\in\mathcal{A}}Q^{\star}(s,a)$ is an optimal policy (where ties are broken in some arbitrary manner).

---

Proof at P10 of the book.

Notice that $V^*(s) = V^{\pi^*}(s) = Q^{\pi^*}(s, \pi^*(s)) \ge Q(s, a) \quad(\forall a)$, when $\pi^*$ is the optimal stationary and deterministic policy.

---

==Remarks==: Here more notations are introduced:

- greedy policy: $\pi_Q(s):=\operatorname{argmax}_{a\in\mathcal{A}}Q(s,a)$ with respect to a **vector**
  $Q\in\mathbb{R}^{|\mathcal{S}||{\mathcal{A}}|}$. (Note that this $Q$ is different from $Q^\pi$ , the latter one is the q-value vector corresponding to a policy $\pi$.) And the optimal policy given by the above theorem can be denoted as $\pi^\star = \pi_{Q^\star}$.
- The Bellman optimality operator $\mathcal{T}_{M}:\mathbb{R}^{|\mathcal{S}||\mathcal{A}|}\to\mathbb{R}^{|\mathcal{S}||\mathcal{A}|}$ is defined as: $\mathcal{T}Q:=r+\gamma PV_Q$, where $V_Q(s):=\max\limits_{a\in\mathcal{A}}Q(s,a)$ for $V_Q: \mathbb{R}^{|\mathcal{S}||\mathcal{A}|}\to\mathbb{R}^{|\mathcal{S}|}$.

This allows us to rewrite the Bellman optimality equation in the concise form:

$$
Q=\mathcal{T}Q,
$$

and, so, the previous theorem states that $Q=Q^\star$ if and only if $Q$ is a fixed point of the operator $\mathcal{T}$.

==Remarks==: The equivalent form in V can be written as:
$$
V(s) = \max_a \Big\{r(s, a) + \gamma\mathbb{E}_{s'\sim P(\cdot|s,a)}\left[V(s')\right]\Big\}
$$
where V is a vector (It is straightforward to verify since $V(s) = \max_a Q(s, a)$), and we the equation is given by $V = \mathcal T V$.

The two theorems basically proves that

- finding an optimal policy = finding an optimal deterministic and stationary policy
- an optimal deterministic and stationary policy is a greedy policy $\pi_Q$

## Finite-Horizon MDP

$$
M=(\mathcal{S},\mathcal{A},\{P\}_h,\{r\}_h,H,\mu)
$$

Changes:

- $P_h(s'|s,a)$is the probability of transitioning into state $s'$ upon taking action $a$ in state $s$ at time step $h$. Note that the time-dependent setting generalizes the stationary setting where all steps share the same transition

- A time-dependent reward function $r_h:\mathcal{S}\times\mathcal{A} \to [0,1].r_h(s,a)$ is the immediate reward associated with taking action $a$ in state $s$ at time step $h$
- A integer $H$ which defines the horizon of the problem

- $V_h^{\pi}(s)=\mathbb{E}\Big[\sum_{t=h}^{H-1}r_h(s_t,a_t)\bigm|\pi,s_h=s\Big]$ and  $V^\pi(s) := V^\pi_0(s)$
- $Q_h^{\pi}(s,a)=\mathbb{E}\Big[\sum_{t=h}^{H-1}r_h(s_t,a_t)\Big|\pi,s_h=s,a_h=a\Big]$

**Theorem 1.9.**（Bellman optimality equations) Define
$$
Q_h^{\star}(s,a)=\sup\limits_{\pi\in\Pi}Q_h^{\pi}(s,a)
$$

where the sup is over all non-stationary and randomized policies. Suppose that $Q_H=0.$ We have that $Q_h=Q_h^\star$ for all $h \in [H]$ if and only if for all $h \in [H]$,

$$
Q_h(s,a)=r_h(s,a)+\mathbb{E}_{s'\sim P_h(\cdot|s,a)}\left[\max\limits_{a'\in\mathcal{A}}Q_{h+1}(s',a')\right].
$$

Furthermore, $\pi(s,h)=\operatorname{argmax}_{a\in\mathcal{A}}Q_h^\star(s,a)$ is an optimal policy.

---

Proof: The flavor of this proof is similar to the infinite-horizon case.

First note that theorem 1.7 also applies to the finite horizon, though the stationary and deterministic $\pi$ now depends on $h$: $\pi(\cdot, h)$. Proof is straightforward.
$$
\begin{aligned}
Q_h^{\star}(s,a)=\sup\limits_{\pi\in\Pi}Q_h^{\pi}(s,a) = &~\sup\limits_{\pi\in\Pi} r_h(s, a) + \mathbb{E}\Big[\sum_{t=h+1}^{H-1}r_h(s_t,a_t)\Big|\pi,s_h=s,a_h=a\Big] \\
= &~\sup\limits_{\pi\in\Pi} r_h(s, a) + \mathbb{E}\Big[\mathbb{E}_{s' \sim P_h(\cdot|s, a)}\Big[\sum_{t=h+1}^{H-1}r_h(s_t,a_t)\Big|\pi,s_{h+1}=s'\Big]\Big] \\
= &~ r_h(s, a) + \sup\limits_{\pi\in\Pi}  \mathbb{E}_{s' \sim P_h(\cdot|s, a)}\Big[V^\pi_{h+1}(s')\Big] \\
\end{aligned}.
$$
Likewise, we define $V^*_{h+1}(s') = \sup_{\pi \in \Pi} V^\pi_{h+1}(s')$ for all $s'$, thus we have
$$
Q_h^{\star}(s,a) = r_h(s, a) + \mathbb{E}_{s' \sim P_h(\cdot|s, a)}\Big[V^*_{h+1}(s')\Big].
$$
So we only need to show that
$$
V_h^*(s) = \max_a Q_h^*(s, a),
$$
which follows the exact same proof as the infinite-horizon MDP with the use of an optimal deterministic policy $\pi(\cdot, h)$.

The reverse is highly similar as well.

---

Overall, the finite MDP is O(H) larger than the infinite one.

## Computational Complexity

Suppose that $(P,r,\gamma)$ in our MDP $M$ is specified with rational entries. Let $L(P,r,\gamma)$ denote the total bit-size required to specify $M$, and assume that basic arithmetic operations $+,-,\times, \div$ take unit time. Here, we may hope for an algorithm which (exactly) returns an optimal policy whose runtime is polynomial in **$L(P,r,\gamma)$ and the number of states and actions**.
More generally, it may also be helpful to understand which algorithms are strongly polynomial. Here, we do not want to explicitly restrict $(P,r,\gamma)$ to be specified by rationals. An algorithm is said to be strongly polynomial if it returns an optimal policy with runtime that is polynomial in only the **number of states and actions** (with no dependence on $L(P,r,\gamma))$.

==Remarks==: For computational time, there are some ''standards'' in optimization  that are not mentioned here, such as what exactly is $L(P, r, \gamma)$ and its relation to $\epsilon$ and more... refer to [slides](https://wensun.github.io/CS6789_data_fall_2021/comp_limits_annotated.pdf) for a detailed introduction.

### Value Iteration

Q-value iteration: starting at some $Q$, we iteratively apply $\mathcal{T}$.

**Lemma 1.10.** (contraction) For any two vectors $Q,Q'\in\mathbb{R}^{|\mathcal{S}||\mathcal{A}|},$
$$
\|\mathcal{T}Q-\mathcal{T}Q'\|_\infty\leq\gamma\|Q-Q'\|_\infty
$$

---

Proof at P13 of the book.

---

**Lemma 1.11.** (Q-Error Amplification)） For any vector $Q\in\mathbb{R}^{|\mathcal{S}||\mathcal{A}|},$
$$
V^{\pi_Q}\ge V^\star-\frac{2\|Q-Q^\star\|_\infty}{1-\gamma}\mathbb{1},
$$

where $\mathbb 1$ denotes the vector of all ones.

---

Proof at P14 of the book.

---

**Theorem 1.12.**（Q-value iteration convergence). Set $Q^{(0)}=0.$ For $k=0,1,\ldots,$ suppose:
$$
Q^{(k+1)}=\mathcal{T}Q^{(k)}
$$

Let $\pi^{(k)}=\pi_{Q^{(k)}}.$ For $k \geq \frac{\log \frac{2}{(1-\gamma)^2 \epsilon}}{1-\gamma},$
$$
{V^{\pi^{(k)}}}\geq V^{\star}-\epsilon \mathbb 1.
$$

---

It is actually a corollary of lemma 1.10 and 1.11 and that $(1-x)^k \le \exp(-xk)$.

---

==Remarks==: Setting $\epsilon$ as $2^{-L(P, r, \gamma)}$, and notice that $\mathcal{T}Q$ gives a complexity of $O(|\mathcal S|^2 |\mathcal A|)$, we obtain the result in the table.

### Policy Iteration

The policy iteration algorithm, for discounted MDPs, starts from an arbitrary policy $\pi_0$, and repeats the following iterative procedure: for $k=0,1,2,..$

1. Policy evaluation. Compute $Q^{\pi_k}$
2. Policy improvement. Update the policy:

$$
\pi_{k+1}=\pi_{Q^{\pi_k}}
$$

In each iteration, we compute the Q-value function of $\pi_k$, using the analytical form given in Equation 0.2, and update the policy to be greedy with respect to this new Q-value. The first step is often called policy evaluation, and the second step is often called policy improvement.

==Remarks==: 0.2 is that $Q^\pi = (I+\gamma P^\pi)^{-1} r$

 **Lemma 1.13.** We have that.

$$
Q^{\pi_{k+1}}\geq\mathcal{T}Q^{\pi_k}\geq Q^{\pi_k}
$$

$$
\|Q^{\pi_{k+1}}-Q^{\star}\|_{\infty}\leq\gamma\|Q^{\pi_{k}}-Q^{\star}\|_{\infty}
$$

---

Proof at P15 of the book.

==Remarks==: $[P^\pi Q^\pi]_{(s, a)} = \sum_{s', a'} P(s'|s, a) \pi(a'|s')Q^\pi(s', a')$,  when deterministic, we have $[P^\pi Q^\pi]_{(s, a)} = \sum_{s'} P(s'|s, a) Q^\pi(s', \pi(s'))$. since $\pi_{k+1}$ is greedy policy with regards to $Q^{\pi_k}$, thus we have $P^{\pi_{k+1}} Q^\pi_k \ge P^{\pi_{k}} Q^\pi_k$. Recursion means that $Q^{\pi_k}=r+\gamma P^{\pi_k}Q^{\pi_k}\le r+\gamma P^{\pi_{k+1}}Q^{\pi_k} \le r+\gamma P^{\pi_{k+1}}(r+\gamma P^{\pi_{k+1}}Q^{\pi_k}) \le \ldots \le \sum_{t=0}^{\infty}\gamma^{t}(P^{\pi_{k+1}})^{t}r$

---

**Theorem 1.14.**（Policy iteration convergence). Let $\pi_0$ be any initial policy. For $k\geq\frac{\log\frac{1}{(1-\gamma)\epsilon}}{1-\gamma},$ the $k$-th policy in policy iteration has the following performance bound.
$$
Q^{\pi_k}\ge Q^\star-\epsilon \mathbb 1.
$$

---

Proof: Notice that 
$$
\|Q^{\pi_k}-Q^{\star}\|_{\infty}\leq\gamma^{k}\|Q^{\pi_0}-Q^{\star}\|_{\infty}\leq(1-(1-\gamma))^{k}\|Q^{\star}\|_{\infty}\leq\frac{\exp({-(1-\gamma)k})}{1-\gamma}
$$
==Remarks==: Note that PI and VI have the same convergence rate for $Q$. However, every iteration of PI is much more expensive than VI, as PI costs $O(|\mathcal S|^3 + |\mathcal S|^2|\mathcal A|)$ (Since if we use a greedy policy, the $P^{\pi_k}$ matrix can be viewed as a $|\mathcal S| \times |\mathcal S|$ instead of $|\mathcal S||\mathcal A| \times |\mathcal S||\mathcal A|$ since $P^{\pi_k}(s, a|s_0, a_0) = P^{\pi_k}(s, \pi_k(s_0)|s_0)$. Thus the inverse contributes $|\mathcal S|^3$ and the multiplication with $r$ contributes $|\mathcal S|^2|\mathcal A|$ if we again view $r$ as a $|S| \times |\mathcal A|$ matrix.

PI can be strongly poly due to some analysis. (The intuition is that police is finite ($|\mathcal A|^{|\mathcal S|}$) and PI is monotonic)

### Value Iteration for Finite Horizon MDPs

Let us now specify the value iteration algorithm for finite-horizon MDPs. For the finize-horizon setting, it turns out that the analogues of value iteration and policy iteration lead to identical algorithms. The value iteration algorithm is specified as follows:

1. Set $Q_{H-1}(s,a)=r_{H-1}(s,a)$
2. For $h=H-2,\dots0$ , set:

$$
Q_h(s,a)=r_h(s,a)+\mathbb{E}_{s'\sim P_h(\cdot|s,a)}\left[\max\limits_{a'\in\mathcal{A}}Q_{h+1}(s',a')\right].
$$

By Theorem 1.9, it follows that $Q_h(s,a) = Q_h^\star(s,a)$ and that $\pi(s,h)=\operatorname{argmax}_{a\in\mathcal{A}}Q_h^\star(s,a)$ is an optimal policy.

==Remarks==: 1 is just a notation for simplicity, and there is a typo in 2 in the book.

### The Linear Programming Approach

VI and PI are not fully polynomial time algos due to dependence on $\gamma$. But with linear programming, we can have fully poly algo!

By the Bellman optimality equation (with the V form), we have 
$$
V(s) = \max_a \Big\{r(s, a) + \gamma\mathbb{E}_{s'\sim P(\cdot|s,a)}\left[V(s')\right]\Big\},
$$
which gives
$$
V(s) \ge r(s, a) + \gamma\mathbb{E}_{s'\sim P(\cdot|s,a)}\left[V(s')\right] \quad \forall a,s.
$$
Therefore the LP is 
$$
\begin{aligned}
\min \qquad & \sum_s \mu(s) V(s)  \\
\text{subject to\qquad} & V(s)\geq r(s,a)+\gamma\sum_{s^{\prime}}P(s^{\prime}|s,a)V(s^{\prime})\quad\forall a\in\mathcal{A},s\in\mathcal{S} 
\end{aligned}
$$
or equivalently
$$
\begin{aligned}
\min \qquad & \mathbb E_{s\sim\mu(\cdot)} [V(s)]  \\
\text{subject to\qquad} & V(s)\geq r(s, a) + \gamma\mathbb{E}_{s'\sim P(\cdot|s,a)}\left[V(s')\right]\quad \forall a\in\mathcal{A},s\in\mathcal{S} 
\end{aligned}
$$
Conceptually, LP provides a cool poly time algo.

Comments from the slides:

- VI is best thought of as a fixed point algorithm
- Pl is equivalent to a (block) simplex algorithm

#### Dual LP

For a **fixed** (possibly stochastic) policy $\pi$, let us define a visitation measure over states and actions induced by following $\pi$ after starting at $s_0$. Precisely, define this distribution, $d^{\pi}_{s_0},$ as follows:

$$
d_{s_0}^{\pi}(s,a):=(1-\gamma)\sum\limits_{t=0}^{\infty}\gamma^t\Pr^{\pi}(s_t=s,a_t=a|s_0)
$$

where $\Pr^{\pi}(s_t=s,a_t=a|s_0)$ is the probability that $s_t=s$ and $a_t=a,$ after starting at state $s_0$ and following $\pi$ thereafter. It is straightforward to verify that $d^\pi_{s_0}$ is a distribution over $\mathcal{S}\times\mathcal{A}.$ We also overload notation and write:
$$
d_\mu^\pi(s,a)=\mathbb{E}_{s_0\sim\mu}\left[d_{s_0}^\pi(s,a)\right]
$$
for a distribution $\mu$ over $\mathcal S$. Recall Lemma 1.6 provides a way to easily compute $d_\mu^\pi(s,a)$ through an appropriate vector-matrix multiplication.

==Remarks==: Lemma 1.6 gives
$$
[(1-\gamma)(I-\gamma P^\pi)^{-1}]_{(s,a),(s',a')}=(1-\gamma)\sum\limits_{t=0}^\infty\gamma^t\mathbb P^\pi(s_t=s',a_t=a'|s_0=s,a_0=a)
$$
Thus, actually we have
$$
d_{s_0}^{\pi}(s,a) = [(1-\gamma)(I-\gamma P^\pi)^{-1}]_{(s_0,\pi(s_0)),(s, a)}
$$
and
$$
d_\mu^\pi(s,a) = \sum_{s_0} \mu(s_0) [(1-\gamma)(I-\gamma P^\pi)^{-1}]_{(s_0,\pi(s_0)),(s, a)}.
$$


It is straightforward to verify that $d_\mu^\pi$ satisfies, for all states $s\in\mathcal{S}$
$$
\sum_a d_\mu^\pi(s,a)=(1-\gamma)\mu(s)+\gamma\sum_{s',a'} P(s|s',a')d_\mu^\pi(s',a').
$$
==Remarks==: Simply we have

<img src="C:\Users\BLOMKV~1\AppData\Local\Temp\WeChat Files\53d5636f2055b5b0081be0efc07194d.jpg" alt="53d5636f2055b5b0081be0efc07194d" style="zoom: 33%;" />

Let us define the state-action polytope as follows:

$$
{\mathcal K}_{\mu}:=\{d|d\geq0\mathrm{~and~}\sum_{a}d(s,a)=(1-\gamma)\mu(s)+\gamma\sum_{s^{\prime},a^{\prime}}P(s|s^{\prime},a^{\prime})d(s^{\prime},a^{\prime})\}
$$

We now see that this set precisely characterizes all state-action visitation distributions.

**Proposition 1.15.** We have that $\mathcal{K}_\mu$ is equal to the set of all feasible state-action distributions, i.e. $d \in \mathcal K_\mu$, if and only if there exists a stationary (and possibly randomized) policy $\pi$ such that $d_\mu^\pi=d$.

With respect the variables $d\in\mathbb{R}^{|\mathcal{S}|\cdot|\mathcal{A}|}$ , the dual LP formulation is as follows
$$
\begin{aligned}
\max \qquad & \frac{1}{1-\gamma}\sum_{s,a}d_\mu(s,a)r(s,a)  \\
\text{subject to\qquad} & d\in{\cal K}_\mu
\end{aligned}
$$
If $d^\star$ is the solution to this LP, and provided that $\mu$ has full support, then we have that:
$$
\pi^\star(a|s)=\frac{d^\star(s,a)}{\sum_{a'}d^\star(s,a')}
$$
is an optimal policy. An alternative optimal policy is  $\arg\max d^{\star}(s,a)$ (and these policies are identical if the optimal policy is unique).

## Sample Complexity and Sampling Models

we are interested understanding **the number of samples required to find a near optimal policy**, i.e. the sample complexity.

A **generative model** provides us with a sample $s'\sim P(·|s,a)$ and the reward $r(s, a)$ upon input of a state action pair $(s, a)$.

The **offline RL setting**. The offline RL setting is where the agent has access to an offline dataset, say generated under some policy (or a collection of policies). In the simplest of these settings, we may assume our dataset is of the form $\{(s,a,s',r)\}$ where $r$ is the reward (corresponding to $r(s,a)$ if the reward is deterministic) and $s'\sim P(\cdot|s,a).$ Furthermore, for simplicity it can be helpful to assume that the $(s,a)$ pairs in this dataset were sampled i.i.d. from some fixed distribution $\nu$ over $\mathcal{S}\times\mathcal{A}$.

## Bonus: Advantages and The Performance Difference Lemma

$$
V^{\pi}(\mu)=\mathbb{E}_{s\sim\mu}[V^{\pi}(s)]
$$

The advantage $A^{\pi}(s,a)$ of a policy $\pi$ is defined as

$$
A^{\pi}(s,a):=Q^{\pi}(s,a)-V^{\pi}(s).
$$

Note that:

$$
A^*(s,a):=A^{\pi^*}(s,a)\le0
$$

for all state-action pairs.

Analogous to the state-action visitation distribution (see Equation 0.8), we can define a visitation measure over jus the states. When clear from context, we will overload notation and also denote this distribution by $d^\pi_{s_0},$ where:

$$
d_{s_0}^\pi(s)=(1-\gamma)\sum\limits_{t=0}^\infty\gamma^t\Pr^\pi(s_t=s|s_0).
$$

Here, $\Pr^{\pi}(s_t=s|s_0)$is the state visitation probability, under r starting at state so. Again, we write:

$$
d_\mu^\pi(s)=\mathbb{E}_{s_0{\sim} \mu}\left[d_{s_0}^\pi(s)\right].
$$

for a distribution $\mu$ over $\mathcal S$.

==Remarks==: In Equation 0.8., we define $d_{s_0}^{\pi}(s,a):=(1-\gamma)\sum\limits_{t=0}^{\infty}\gamma^t\Pr^{\pi}(s_t=s,a_t=a|s_0)$, so the only difference is in $a$, such that $d_{s_0}^{\pi}(s) = \sum_a d_{s_0}^{\pi}(s,a)$.

**Lemma 1.16.**（The performance difference lemma) For all policies $\pi, \pi'$ and distributions $\mu$ over $\mathcal S$,
$$
V^{\pi}(\mu)-V^{\pi'}(\mu)=\frac{1}{1-\gamma}\mathbb{E}_{s'\sim d^\pi_\mu}\mathbb{E}_{a'\sim\pi(\cdot|s')}\left[A^{\pi'}(s',a')\right].
$$

---

Proof at P19 of the book

---

