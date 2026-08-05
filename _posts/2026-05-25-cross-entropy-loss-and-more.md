---
layout: post
title: Cross-Entropy、KL、NLL 与 MLE：同一个目标的四种写法
date: 2026-05-25 12:00:00 +0800
description: 从一个统一目标出发，推导 cross-entropy、forward KL divergence、negative log-likelihood 与 maximum likelihood estimation 的关系及成立条件。
tags: machine-learning probability chinese
categories: notes
---

Cross-entropy（CE）、KL divergence、negative log-likelihood（NLL）和 maximum likelihood estimation（MLE）经常被当成独立的几个概念，本文将从CE讲起，揭示四者的同一性。

## 1. 从最小情形开始：One-hot 分类问题

考虑一个 $C$ 类分类问题。对输入 $x$，模型输出各类别的概率

$$
p_c=p_\theta(y=c\mid x),
\qquad
p_c\ge 0,
\qquad
\sum_{c=1}^{C}p_c=1.
$$

假设正确标签为 $j$。我们的目标是让输出正确标签的概率 $p_j$ 越大越好。于是想到可以最小化它的负对数损失，因为 $p_j$ 的负对数在 $p_j=0$ 时取正无穷，在 $p_j=1$ 时取 0：

$$
\mathcal L(x,j) = -\log p_j
$$

这个标签的分布是一个很简单的分布，只在标签正确的时候是 1，其他都是 0。我们把标签写成 one-hot 分布：

$$
q_c=\mathbb 1\{c=j\},
$$

改写一下原来的负对数损失：

$$
\begin{aligned}
\mathcal L(x,j)
&=-\log p_j\\
&=-\sum_{c=1}^{C}\mathbb 1\{c=j\}\log p_c\\
&=-\sum_{c=1}^{C}q_c\log p_c\\
&=H(q,p_\theta).
\end{aligned}
$$

这样就得到了我们熟悉的 cross-entropy loss 形式。

## 2. 统一目标：目标分布下的 expected log loss

更一般地，$q(y\mid x)$ 可以是任意分布对固定输入 $x$，定义

$$
H\!\left(q(\cdot\mid x),p_\theta(\cdot\mid x)\right)
=-\sum_y q(y\mid x)\log p_\theta(y\mid x).
$$

再对输入分布取期望，得到总体目标

$$
\begin{aligned}
\mathcal L_{\mathrm{CE}}(\theta)
&=\mathbb E_{x\sim q(x)}
\left[H\!\left(q(\cdot\mid x),p_\theta(\cdot\mid x)\right)\right]\\
&=\mathbb E_{(x,y)\sim q}
\left[-\log p_\theta(y\mid x)\right].
\end{aligned}
$$

这就是贯穿全文的目标：在目标数据分布下，最小化模型赋给实际标签的 negative log-probability。CE、KL 和 NLL 的联系都可以从这个式子得到。

### 为什么它的最优解是 $p=q$？

对固定的 $x$，考虑带概率归一化约束的优化问题：

$$
\min_{p_1,\ldots,p_C}
-\sum_{c=1}^{C}q_c\log p_c,
\qquad
\text{s.t.}\quad \sum_{c=1}^{C}p_c=1.
$$

暂设所有 $q_c>0$。引入 Lagrange multiplier $\lambda$：

$$
\mathcal J(p,\lambda)
=-\sum_{c=1}^{C}q_c\log p_c
+\lambda\left(\sum_{c=1}^{C}p_c-1\right).
$$

驻点满足

$$
\frac{\partial\mathcal J}{\partial p_c}
=-\frac{q_c}{p_c}+\lambda=0,
$$

因此 $p_c=q_c/\lambda$。结合 $\sum_c p_c=\sum_c q_c=1$，可得 $\lambda=1$，从而

$$
p_c=q_c.
$$

当某些 $q_c=0$ 时，可以通过边界条件或连续性得到相同结论。这个推导说明：cross-entropy 的最优预测正是目标分布本身；它不只适用于 one-hot 标签。

## 3. 分布视角：CE 与 KL 散度

先来看经典的 KL divergence 定义

$$
D_{\mathrm{KL}}(q\|p_\theta)
=\sum_y q(y\mid x)
\log\frac{q(y\mid x)}{p_\theta(y\mid x)}.
$$

展开 log 后，并用信息熵改写一下，则有

$$
\begin{aligned}
D_{\mathrm{KL}}(q\|p_\theta)
&=\sum_y q(y\mid x)\log q(y\mid x)
-\sum_y q(y\mid x)\log p_\theta(y\mid x)\\
&=-H(q)+H(q,p_\theta).
\end{aligned}
$$

细心的读者已经注意到了，式中的第二项就是我们的交叉熵，将其移项到等式左边，有

$$
H(q,p_\theta)
=H(q)+D_{\mathrm{KL}}(q\|p_\theta).
$$

这里目标分布 $q$ 是固定的，所以 $H(q)$ 对 $\theta$ 求导后是常数。因此

$$
\arg\min_\theta H(q,p_\theta)
=\arg\min_\theta D_{\mathrm{KL}}(q\|p_\theta).
$$

所以说，最小化 CE loss 和最小化 KL 散度是同一个优化目标。

## 4. 数据视角：经验 CE 与 NLL

现实中我们不知道总体分布 $q(x,y)$，只有数据集

$$
\mathcal D=\{(x_i,y_i)\}_{i=1}^{n}.
$$

我们用数据集的经验分布近似总体分布，并把每个观测标签视为 one-hot target，得到 empirical cross-entropy：

$$
\widehat{\mathcal L}_{\mathrm{CE}}(\theta)
=-\frac{1}{n}\sum_{i=1}^{n}
\log p_\theta(y_i\mid x_i).
$$

另一方面，在给定各自输入后标签条件独立的假设下，conditional likelihood 是所有样本条件概率的乘积

$$
p_\theta(y_{1:n}\mid x_{1:n})
=\prod_{i=1}^{n}p_\theta(y_i\mid x_i).
$$

把它改写成 negative log-likelihood，首先套对数符号不改变单调性，然后再加负数变成 loss 的形式

$$
\mathcal L_{\mathrm{NLL}}(\theta)
=-\log p_\theta(y_{1:n}\mid x_{1:n})
=-\sum_{i=1}^{n}\log p_\theta(y_i\mid x_i).
$$

不难看出

$$
\widehat{\mathcal L}_{\mathrm{CE}}(\theta)
=\frac{1}{n}\mathcal L_{\mathrm{NLL}}(\theta).
$$

不少实现把 CE 取 batch mean，把 NLL 写成全数据 sum，所以二者数值一般不想等；但依然是相同的优化目标。

## 5. 参数估计视角：最小化 NLL 就是 MLE

maximum likelihood estimation 定义为

$$
\hat\theta_{\mathrm{MLE}}
=\arg\max_\theta p_\theta(y_{1:n}\mid x_{1:n})
=\arg\max_\theta
\sum_{i=1}^{n}\log p_\theta(y_i\mid x_i).
$$

乘以 $-1$ 后，最大化变成最小化：

$$
\hat\theta_{\mathrm{MLE}}
=\arg\min_\theta \mathcal L_{\mathrm{NLL}}(\theta)
=\arg\min_\theta \widehat{\mathcal L}_{\mathrm{CE}}(\theta).
$$

因此在有限数据集上，有

$$
\boxed{
\text{MLE}
\Longleftrightarrow
\min \text{NLL}
\Longleftrightarrow
\min \text{empirical CE}
}
$$

而在固定总体分布 $q$ 的意义下，有另一组等价关系：

$$
\boxed{
\min \text{expected CE}
\Longleftrightarrow
\min D_{\mathrm{KL}}(q\|p_\theta)
}
$$

empirical CE 是 expected CE 的有限样本估计。这一步连接的是经验目标与总体目标，而不是两个有限样本量之间的恒等式。

## 6. 从 logits 计算 CE：为什么要用 log-sum-exp？

考虑 softmax 分类器。若模型输出 logits $z_1,\ldots,z_C$，softmax 概率为

$$
p_c=\frac{e^{z_c}}{\sum_{k=1}^{C}e^{z_k}}.
$$

对正确类别 $j$，one-hot cross-entropy 可以改写为

$$
\begin{aligned}
\mathcal L_{\mathrm{CE}}
&=-\log p_j\\
&=-z_j+\log\sum_{k=1}^{C}e^{z_k}.
\end{aligned}
$$

在实现中，如果直接计算 $e^{z_k}$ 数值特别大，可能 overflow。利用 softmax 对所有 logits 同加减一个常数保持不变，令 $m=\max_k z_k$，可得稳定形式

$$
\mathcal L_{\mathrm{CE}}
=-z_j+m+\log\sum_{k=1}^{C}e^{z_k-m}.
$$

这也是实践中应直接使用 `cross_entropy` 或 `log_softmax`，而不是先算 softmax、再手动取 logarithm 的原因。

## 7. 结论

| 视角     | 对象                           | 与统一目标的关系                               |
| -------- | ------------------------------ | ---------------------------------------------- |
| 分布匹配 | $D_{\mathrm{KL}}(q\|p_\theta)$ | 与 CE 相差固定的 $H(q)$                        |
| 损失函数 | $H(q,p_\theta)$                | 目标分布下的 expected negative log-probability |
| 有限数据 | NLL                            | one-hot empirical CE 的总和形式                |
| 参数估计 | MLE                            | 与最小化 NLL 完全等价                          |

---

_Migrated from the [original Notion post](https://quilted-lift-9d9.notion.site/Cross-Entropy-Loss-and-More-36b7954e6e4480a490b3dea674c50774)._
