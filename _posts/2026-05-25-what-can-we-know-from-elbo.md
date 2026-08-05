---
layout: post
title: 证据下界的推导和解释 What Can We Know from ELBO?
date: 2026-05-25 11:00:00 +0800
description: 从 importance sampling 和 Jensen's inequality 出发推导 ELBO，并解释它在 VAE 中的两种形式。
tags: machine-learning variational-inference chinese
categories: notes
---

这篇笔记记录 Evidence Lower Bound（ELBO）的推导过程，以及它在 variational autoencoder（VAE）中的含义。

### 为什么需要 ELBO？

在 VAE 中，我们希望最大化模型对观测 $x$ 的 marginal likelihood：

$$
p_\theta(x)=\int p_\theta(x,z)\,dz.
$$

隐变量 $z$ 往往是连续且高维的，上述积分难以直接计算。如果能找到 $\log p_\theta(x)$ 的一个可计算下界，就可以通过最大化这个下界来近似最大化 marginal likelihood。这个下界就是 ELBO。

### Preliminaries

- **Jensen's inequality**：当 $f$ 是凹函数时，$f(\mathbb{E}[X])\geq\mathbb{E}[f(X)]$。
- **KL divergence**：$D_{\mathrm{KL}}(q(z\mid x)\|p(z))=\mathbb{E}_{q(z\mid x)}\left[\log\frac{q(z\mid x)}{p(z)}\right]$。
- **Importance sampling**：$\int f(z)P(z)\,dz=\int f(z)\frac{P(z)}{Q(z)}Q(z)\,dz$，用容易采样的 $Q$ 来改写对 $P$ 的积分。

### 推导 ELBO

引入一个可以采样的 variational posterior $q_\phi(z\mid x)$，在分子分母同时乘以它：

$$
\begin{aligned}
p_\theta(x)
&=\int p_\theta(x,z)\,dz\\
&=\int p_\theta(x,z)\frac{q_\phi(z\mid x)}{q_\phi(z\mid x)}\,dz\\
&=\mathbb{E}_{z\sim q_\phi(z\mid x)}
\left[\frac{p_\theta(x,z)}{q_\phi(z\mid x)}\right].
\end{aligned}
$$

对两边取对数，并利用 $\log$ 是凹函数以及 Jensen's inequality：

$$
\begin{aligned}
\log p_\theta(x)
&=\log\mathbb{E}_{z\sim q_\phi(z\mid x)}
\left[\frac{p_\theta(x,z)}{q_\phi(z\mid x)}\right]\\
&\geq\mathbb{E}_{z\sim q_\phi(z\mid x)}
\left[\log\frac{p_\theta(x,z)}{q_\phi(z\mid x)}\right]\\
&=: \mathcal{L}_{\mathrm{ELBO}}(x).
\end{aligned}
$$

### ELBO 的两种形式

**Joint-distribution 期望形式**

$$
\mathcal{L}_{\mathrm{ELBO}}(x)
=\mathbb{E}_{z\sim q_\phi(z\mid x)}
\left[\log\frac{p_\theta(x,z)}{q_\phi(z\mid x)}\right].
$$

**Reconstruction term + KL divergence**

利用 $p_\theta(x,z)=p_\theta(x\mid z)p(z)$ 展开：

$$
\begin{aligned}
\mathcal{L}_{\mathrm{ELBO}}(x)
&=\mathbb{E}_{q_\phi(z\mid x)}[\log p_\theta(x\mid z)]
-D_{\mathrm{KL}}\big(q_\phi(z\mid x)\|p(z)\big).
\end{aligned}
$$

### 这两种形式告诉了我们什么？

- $q_\phi(z\mid x)$ 是 encoder 给出的 approximate posterior。
- $p(z)$ 是与 $x$ 无关的 prior。
- $p_\theta(x\mid z)$ 是 decoder likelihood。

最大化 ELBO 同时意味着：

1. 最大化 reconstruction term

   $$
   \mathbb{E}_{q_\phi(z\mid x)}[\log p_\theta(x\mid z)],
   $$

   让 decoder 能够重构观测 $x$。

2. 最小化 KL divergence

   $$
   D_{\mathrm{KL}}\big(q_\phi(z\mid x)\|p(z)\big),
   $$

   让 encoder 产生的隐变量分布接近 prior。

---

_Migrated from the [original Notion post](https://quilted-lift-9d9.notion.site/What-Can-We-Know-from-ELBO-36b7954e6e44806f9956ef2b01ae2b5a)._
