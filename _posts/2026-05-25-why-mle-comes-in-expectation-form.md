---
layout: post
title: 极大似然估计的推导和期望形式 Why MLE Comes in Expectation Form?
date: 2026-05-25 10:00:00 +0800
description: 从独立样本的联合概率出发推导 MLE，并解释 empirical average 为什么收敛到 expectation。
tags: machine-learning probability chinese
categories: notes
---

这篇笔记记录 maximum likelihood estimation（MLE）的推导过程，同时解释 MLE 的两种常见写法：finite-sample 形式和 expectation 形式。

### MLE 的推导

设真实数据分布是 $p_{\mathrm{data}}(x)$，模型分布是 $p_\theta(x)$。我们从 $p_{\mathrm{data}}$ 独立采样：

$$
\{x_1,x_2,\ldots,x_n\}\overset{\mathrm{i.i.d.}}{\sim}p_{\mathrm{data}}(x).
$$

目标是找到让这组样本在模型下出现概率最大的参数 $\theta$。由于样本相互独立，联合 likelihood 是：

$$
P_\theta(x_1,x_2,\ldots,x_n)
=\prod_{i=1}^{n}p_\theta(x_i).
$$

因此 MLE 为：

$$
\hat\theta_{\mathrm{MLE}}
=\arg\max_\theta\prod_{i=1}^{n}p_\theta(x_i).
$$

对数函数单调递增，所以取对数不改变 $\arg\max$ 的结果，同时将乘积变成求和：

$$
\hat\theta_{\mathrm{MLE}}
=\arg\max_\theta\sum_{i=1}^{n}\log p_\theta(x_i).
$$

这就是 finite-sample 形式的 MLE。

### 期望形式的 MLE

将 log-likelihood 除以样本数 $n$ 不会改变最大化的参数：

$$
\hat\theta_{\mathrm{MLE}}
=\arg\max_\theta\frac{1}{n}\sum_{i=1}^{n}\log p_\theta(x_i).
$$

根据大数定律，当 $n\to\infty$ 时，empirical average 收敛到真实数据分布下的期望：

$$
\frac{1}{n}\sum_{i=1}^{n}\log p_\theta(x_i)
\xrightarrow[n\to\infty]{}
\mathbb{E}_{x\sim p_{\mathrm{data}}}
[\log p_\theta(x)].
$$

于是 population objective 可以写成：

$$
\theta^*
=\arg\max_\theta
\mathbb{E}_{x\sim p_{\mathrm{data}}}
[\log p_\theta(x)].
$$

这说明 expectation 形式不是一个新的目标，而是 finite-sample average 在样本数足够大时的 population limit。

---

_Migrated from the [original Notion post](https://quilted-lift-9d9.notion.site/Why-MLE-Comes-in-Expectation-Form-36b7954e6e4480baa235f23745fe7b00)._
