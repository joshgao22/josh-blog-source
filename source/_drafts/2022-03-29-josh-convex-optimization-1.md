---
title: Josh 的学习笔记之凸优化 (Part 1 —— 凸集)
mathjax: true
comments: true
copyright: true
sticky: false
toc:
  enable: true
  number: false
  wrap: true
  expand_all: true
  max_depth: 4
categories:
  - - Josh 的学习笔记
    - 凸优化
tags:
  - 凸优化
abbrlink: 43146ee9
date: 2022-03-29 11:07:04
---

# 仿射集合和凸集

&emsp;&emsp;设 $x_1 \ne x_2$ 是 $\mathbf{R}^n$ 空间中的两个点，则具有下列形式的点

$$
y = \theta x_1 + (1-\theta)x_2,\theta \in \mathbf{R}
$$

组成一条穿过 $x_1$ 和 $x_2$ 的**直线**(*line*). 参数 $\theta = 0$ 对应 $y=x_2$，$\theta = 1$ 表明 $y = x_2$. 参数 $\theta$ 的值在

<!-- more -->