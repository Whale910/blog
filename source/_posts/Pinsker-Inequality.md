---
title: Pinsker Inequality
date: 2024-06-23 23:12:32
tags: 优化
mathjax: true
---
最近在做 Mirror Descent 相关的一个项目的收敛性分析，需要用到 Pinsker 不等式，这里简单记录一下它的证明。

(Pinsker’s inequality)设 $P$ 和 $Q$ 是两个概率分布，那么
$$D_{KL}(P||Q) \ge \frac{1}{2}||P-Q||_1^2 \tag{1}$$
由 Pinsker 不等式可以得到负熵函数关于$1$-范数是强凸的

下面先证明 Pinsker 不等式，首先我们给出 KL 散度的链式法则：

$$\begin{aligned}
D_{KL}(P(X, Y)||Q(X, Y)) &= \sum_{x,y} p(x, y) \log \frac{p(x, y)}{q(x, y)} \\\\
&= \sum_{x,y} p(x)p(y|x) \log \frac{p(x)p(y|x)}{q(x)q(y|x)} \\\\
&= \sum_{x} p(x) \log \frac{p(x)}{q(x)} \sum_{y} p(y|x) + \sum_{x} p(x) \sum_{y} p(y|x) \log \frac{p(y|x)}{q(y|x)} \\\\
&= D_{KL}(P(X)||Q(X)) + \sum_{x} p(x) D_{KL}(P||Q|X=x) \\\\
&= D_{KL}(P(X)||Q(X)) + D_{KL}(P||Q|X)
\end{aligned}
\tag{2}$$

特别地，若$P(X,Y)=P_1(X)P_2(Y),Q(X,Y)=Q_1(X)Q_2(Y)$，那么有
$$D_{KL}(P(X, Y)||Q(X, Y)) = D_{KL}(P_1(X)||Q_1(X)) + D_{KL}(P_2(X)||Q_2(X)) $$

