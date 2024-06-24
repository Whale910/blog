---
title: Pinsker Inequality
date: 2024-06-23 23:12:32
tags: 优化
mathjax: true
---
最近在做 Mirror Descent 相关的一个项目的收敛性分析，需要用到 Pinsker 不等式，这里简单记录一下它的证明，文中的 $log$ 均是以 $e$ 为底的。

## Pinsker 不等式证明
**(Pinsker’s inequality)**设 $P$ 和 $Q$ 是定义在论域 $U$ 上的两个概率分布，那么
$$D_{KL}(P||Q) \ge \frac{1}{2}||P-Q||_1^2 \tag{1}$$
由 Pinsker 不等式可以得到负熵函数关于$1$-范数是强凸的，下面先给出 Pinsker 不等式的证明。

**$Proof.$**首先我们给出 KL 散度的链式法则：

$$\begin{aligned}
D_{KL}(P(X, Y)||Q(X, Y)) &= \sum_{x,y} p(x, y) \log \frac{p(x, y)}{q(x, y)} \\\\
&= \sum_{x,y} p(x)p(y|x) \log \frac{p(x)p(y|x)}{q(x)q(y|x)} \\\\
&= \sum_{x} p(x) \log \frac{p(x)}{q(x)} \sum_{y} p(y|x) + \sum_{x} p(x) \sum_{y} p(y|x) \log \frac{p(y|x)}{q(y|x)} \\\\
&= D_{KL}(P(X)||Q(X)) + \sum_{x} p(x) D_{KL}(P||Q|X=x) \\\\
&= D_{KL}(P(X)||Q(X)) + \mathbb{E}_{X \thicksim P(X)}[D_{\text{KL}}(P(Y|X) \| Q(Y|X))]
\end{aligned}
\tag{2}$$

特别地，若$P(X,Y)=P_1(X)P_2(Y),Q(X,Y)=Q_1(X)Q_2(Y)$，那么有
$$D_{KL}(P(X, Y)||Q(X, Y)) = D_{KL}(P_1(X)||Q_1(X)) + D_{KL}(P_2(X)||Q_2(X)) $$

先考虑 Pinsker 不等式的特殊情形：

$$
P = \begin{cases}
1 & \text{w.p. } p \\
0 & \text{w.p. } 1-p
\end{cases}
$$

$$
Q = \begin{cases}
1 & \text{w.p. } q \\
0 & \text{w.p. } 1-q
\end{cases} 
$$

不妨假设 $p>>q$ (其它情况类似)，令

$$
f(p, q) = p \log \frac{p}{q} + (1 - p) \log \frac{1 - p}{1 - q} - \frac{1}{2} (2(p - q))^2
$$

由于

$$
\frac{\partial f}{\partial q} = -(p - q) \left( \frac{1}{q(1 - q)} - 4 \right) \leq 0
$$

而且当 $p=q$ 时，有 $f=0$ ，可以得到当 $q \leq p$ 时，$f(p,q) \geq 0$ ，因此对于这种特殊情形，我们有 $D_{KL}(P||Q) \ge \frac{1}{2}||P-Q||_1^2$ 

一般情形：
设 $P$ 和 $Q$ 是 $U$ 上的分布.令 $A \subset U$ 为

$$ A = \{ x \mid p(x) \geq q(x) \} $$
定义 $P_A$ 和 $Q_A$ 如下：

$$ 
P_A = \begin{cases} 
1 & \text{w.p.} \sum_{x \in A} p(x) \\
0 & \text{w.p.} \sum_{x \notin A} p(x)
\end{cases} 
$$

$$ 
Q_A = \begin{cases} 
1 & \text{w.p.} \sum_{x \in A} q(x) \\
0 & \text{w.p.} \sum_{x \notin A} q(x)
\end{cases} 
$$

那么，
$$\begin{aligned}
\|P - Q\|_1 &= \sum_x |p(x) - q(x)| \\\\
 &= \sum_{x \in A} (p(x) - q(x)) + \sum_{x \notin A} (q(x) - p(x)) \\\\\
 &= \left| \sum_{x \in A} p(x) - \sum_{x \in A} q(x) \right| + \left| \left( 1 - \sum_{x \in A} p(x) \right) - \left( 1 - \sum_{x \in A} q(x) \right) \right| \\\\
 \|P - Q\|_1 &= \|P_A - Q_A\|_1 
\end{aligned}
\tag{3}$$

定义随机变量 
$$ 
Z = \begin{cases} 
1 & \text{if } x \in A \\
0 & \text{if } x \notin A
\end{cases} 
$$
假设原来的概率分布 $P$ 和 $Q$ 的多元随机变量定义为 $X$，这里拼接新的一个维度，即将随机变量变为 $(X,Z)$，但根据 $Z$ 的定义，$Z$ 和概率分布 $P$，$Q$ 的取值都取决于$X$，新的随机变量 $(X,Z)$ 并不会影响原有的概率分布。
由公式(2) $ D_{KL}(P || Q) = D_{KL}(P(Z) || Q(Z)) + \mathbb{E}_{Z \thicksim P(Z)}[D_{\text{KL}}(P(X|Z) \| Q(X|Z))] $
由于 $ D_{KL}(P(Z) || Q(Z)) = D_{KL}(P_A || Q_A) $ 且 $ \mathbb{E}_{Z \thicksim P(Z)}[D_{\text{KL}}(P(X|Z) \| Q(X|Z))] \geq 0 $, 我们有

$$\begin{aligned}
D_{KL}(P || Q) & \geq D_{KL}(P_A || Q_A) \\\\
& \geq \frac{1}{2} \| P_A - Q_A \|_1^2 \quad \text{(特殊情形)} \\\\
&= \frac{1}{2} \| P - Q \|_1^2 \quad \text{(由公式 3)}
\end{aligned}$$

这里给出对 $ \mathbb{E}_{Z \thicksim P(Z)}[D_{\text{KL}}(P(Y|Z) \| Q(Y|Z))] \geq 0 $ 的补充证明
**$Proof.$** 只需证明 KL 散度的非负性即可，由 Jensen 不等式
$$\begin{aligned}
D_{KL}(P||Q)&=\sum_{\mathbf{x}} p(\mathbf{x}) (-\log \frac{q(\mathbf{x})}{p(\mathbf{x})}) \\\\
&\geq -log(\sum_{\mathbf{x}}p(\mathbf{x}) \frac{q(\mathbf{x})}{p(\mathbf{x})}) \\\\
&=-log(1) = 0
\end{aligned}$$

## 负熵函数的强凸性
Mirror Descent 中的 Bregman 距离定义如下：

$$
D(x,y)=f(x)-f(y)-\langle \nabla f(y), x - y \rangle 
$$

若此处的 $f(x) = \sum_{i=1}^n x_i \log x_i $，那么这里的 $D(x,y)$ 就是 $D_{KL}(x || y)$，下面给出负熵函数强凸性的证明：
**$Proof.$** 令 $f(x) = \sum_{i=1}^n x_i \log x_i $

$$\begin{aligned}
f(x) &= f(y) + \langle \nabla f(y), x - y \rangle + D_{KL}(x || y) \quad \\\\
&\geq f(y) + \langle \nabla f(y), x - y \rangle + \frac{1}{2} \|x - y\|_1^2 \quad 
\end{aligned}$$
由强凸函数的性质，可以得到负熵函数 $f$ 在 $1$ 范数的意义下是强凸的。