---
layout: post
title: 关于交叉熵损失你需要知道的事 Cross Entropy Loss and More
date: 2026-05-25 12:00:00 +0800
description: 从 KL divergence 和 maximum likelihood estimation 出发，理解 cross-entropy loss 与 NLL 的关系。
tags: machine-learning probability chinese
categories: notes
---

这篇笔记推导 CE loss 和 KL divergence、MLE、NLL 之间的关系。核心结论是：当真实标签分布固定时，最小化 KL divergence、cross-entropy 和分类问题的 negative log-likelihood 具有相同的优化目标。

### 1. Cross-Entropy Loss 的定义

在 softmax 分类器中，我们希望最大化正确标签 $j$ 的得分（概率）：

$$
\max \sum_{c=1}^{C} \mathbb{1}\{c=j\}\log p_c.
$$

其中，$\mathbb{1}\{c=j\}$ 是指示函数：当 $c=j$ 时为 1，否则为 0；$p_c=P(y=c\mid x)$ 是模型对类别 $c$ 预测的概率。改写成要最小化的 loss：

$$
\mathcal{L}_{\mathrm{CE}}
=-\sum_{c=1}^{C}\mathbb{1}\{c=j\}\log p_c.
$$

### 2. Cross-Entropy Loss 和 KL Divergence

KL divergence 的定义是：

$$
D_{\mathrm{KL}}(q\|p)
=\sum_x q(x)\log\frac{q(x)}{p(x)}.
$$

这里 $q(x)$ 是真实分布，$p(x)$ 是模型预测的分布。将它展开：

$$
\begin{aligned}
D_{\mathrm{KL}}(q\|p)
&=\sum_x q(x)\log q(x)-\sum_x q(x)\log p(x)\\
&=-H(q)+H(q,p).
\end{aligned}
$$

对于固定的真实分布 $q$，$H(q)$ 是常数，所以：

$$
\arg\min_p D_{\mathrm{KL}}(q\|p)
=\arg\min_p H(q,p).
$$

在 one-hot 分类中，$q=[0,\ldots,1,\ldots,0]$，cross-entropy 就简化为正确标签的负对数概率。

### 3. Cross-Entropy Loss 和 MLE

给定独立同分布样本 $\{x_i\}_{i=1}^{n}$，maximum likelihood estimation 寻找：

$$
\hat\theta_{\mathrm{MLE}}
=\arg\max_\theta\prod_{i=1}^{n}p_\theta(x_i)
=\arg\max_\theta\sum_{i=1}^{n}\log p_\theta(x_i).
$$

加上负号并改为最小化，就得到 negative log-likelihood：

$$
\mathcal{L}_{\mathrm{NLL}}
=-\sum_{i=1}^{n}\log p_\theta(x_i).
$$

对于单个分类样本 $x$ 及其标签 $y$，若正确标签是 $j$，则：

$$
\begin{aligned}
\mathcal{L}_{\mathrm{NLL}}
&=-\log p_\theta(y=j\mid x)\\
&=-\sum_{c=1}^{C}\mathbb{1}\{c=j\}\log p_\theta(y=c\mid x)\\
&=\mathcal{L}_{\mathrm{CE}}.
\end{aligned}
$$

因此，对于 one-hot 标签的分类问题，最大化 likelihood、最小化 NLL 和最小化 cross-entropy 是同一件事的不同写法。

---

_Migrated from the [original Notion post](https://quilted-lift-9d9.notion.site/Cross-Entropy-Loss-and-More-36b7954e6e4480a490b3dea674c50774)._
