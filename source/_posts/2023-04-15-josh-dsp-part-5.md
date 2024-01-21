---
title: "Josh's Review: 数字信号处理（Part 5 — 部分 FFT 蝶形图）"
mathjax: true
comments: true
copyright: true
toc:
  enable: true
  number: false
  wrap: true
  expand_all: false
  max_depth: 4
abbrlink: 14fc3f1c
date: 2023-04-15 22:20:54
tags:
  - 数字信号和处理
  - DSP
  - 蝶形图
  - FFT
  - 基2按时间抽取
  - 基2按频率抽取
  - 基4
  - 分裂基FFT
categories:
  - [Josh 的学习笔记, 数字信号处理]
---

# 1. 4 点 FFT

## 1.1. 基-2 按时间抽取 FFT（4-point radix-2 DIT-FFT）

### 1.1.1. 比特位反序输入、自然顺序输出

![4-point radix-2 DIT-FFT 比特位反序输入、自然顺序输出](../images/post/2023-04-15-josh-dsp-part-5/2023-04-15-josh-dsp-part-5-010-4PointRadix2DITFFTIRON.png){width=400px}

<!-- more -->

### 1.1.2. 自然顺序输入、比特位反序输出

![4-point radix-2 DIT-FFT 自然顺序输入、比特位反序输出](../images/post/2023-04-15-josh-dsp-part-5/2023-04-15-josh-dsp-part-5-020-4PointRadix2DITFFTINOR.png){width=400px}

## 1.2. 基-2 按频率抽取 FFT（4-point radix-2 DIF-FFT）

### 1.2.1. 比特位反序输入、自然顺序输出

![4-point radix-2 DIF-FFT 比特位反序输入、自然顺序输出](../images/post/2023-04-15-josh-dsp-part-5/2023-04-15-josh-dsp-part-5-030-4PointRadix2DIFFFTIRON.png){width=400px}

### 1.2.2. 自然顺序输入、比特位反序输出

![4-point radix-2 DIF-FFT 自然顺序输入、比特位反序输出](../images/post/2023-04-15-josh-dsp-part-5/2023-04-15-josh-dsp-part-5-040-4PointRadix2DIFFFTINOR.png){width=400px}

## 1.3. 分裂基按频率抽取 FFT（4-point split radix-2/4 DIF-FFT）

![4-point split radix-2/4 DIF-FFT](../images/post/2023-04-15-josh-dsp-part-5/2023-04-15-josh-dsp-part-5-050-4PointSplitRadix24DIFFFT.png){width=400px}

## 1.4. 基-4 按频率抽取 FFT（4-point radix-4 DIF-FFT）

![4-point radix-4 DIF-FFT](../images/post/2023-04-15-josh-dsp-part-5/2023-04-15-josh-dsp-part-5-060-4PointRadix4DIFFFT.png){width=400px}

# 2. 8 点 FFT

## 2.1. 基-2 按时间抽取 FFT（8-point radix-2 DIT-FFT）

### 2.1.1. 比特位反序输入、自然顺序输出

![8-point radix-2 DIT-FFT 比特位反序输入、自然顺序输出](../images/post/2023-04-15-josh-dsp-part-5/2023-04-15-josh-dsp-part-5-070-8PointRadix2DITFFTIRON.png){width=600px}

### 2.1.2. 自然顺序输入、比特位反序输出

![8-point radix-2 DIT-FFT 自然顺序输入、比特位反序输出](../images/post/2023-04-15-josh-dsp-part-5/2023-04-15-josh-dsp-part-5-080-8PointRadix2DITFFTINOR.png){width=600px}

## 2.2. 基-2 按频率抽取 FFT（8-point radix-2 DIF-FFT）

### 2.2.1. 比特位反序输入、自然顺序输出

![8-point radix-2 DIF-FFT 自然顺序输入、比特位反序输出](../images/post/2023-04-15-josh-dsp-part-5/2023-04-15-josh-dsp-part-5-090-8PointRadix2DIFFFTIRON.png){width=600px}

### 2.2.2. 2自然顺序输入、比特位反序输出

![8-point radix-2 DIF-FFT 自然顺序输入、比特位反序输出](../images/post/2023-04-15-josh-dsp-part-5/2023-04-15-josh-dsp-part-5-100-8PointRadix2DIFFFTINOR.png){width=600px}

## 2.3. 分裂基按频率抽取 FFT（8-point split radix-2/4 DIF-FFT）

![8-point split radix-2/4 DIF-FFT](../images/post/2023-04-15-josh-dsp-part-5/2023-04-15-josh-dsp-part-5-110-8PointSplitRadix24DIFFFT.png){width=600px}

# 3. 16 点 FFT

## 3.1. 基-2 按时间抽取 FFT（16-point radix-2 DIT-FFT）

### 3.1.1. 比特位反序输入、自然顺序输出

![16-point radix-2 DIT-FFT 比特位反序输入、自然顺序输出](../images/post/2023-04-15-josh-dsp-part-5/2023-04-15-josh-dsp-part-5-120-16PointRadix2DITFFTIRON.png){width=1000px}

### 3.1.2. 自然顺序输入、比特位反序输出

![16-point radix-2 DIT-FFT 自然顺序输入、比特位反序输出](../images/post/2023-04-15-josh-dsp-part-5/2023-04-15-josh-dsp-part-5-130-16PointRadix2DITFFTINOR.png){width=1000px}

## 3.2. 基-2 按频率抽取 FFT（16-point radix-2 DIF-FFT）

### 3.2.1. 比特位反序输入、自然顺序输出

![16-point radix-2 DIF-FFT 比特位反序输入、自然顺序输出](../images/post/2023-04-15-josh-dsp-part-5/2023-04-15-josh-dsp-part-5-140-16PointRadix2DIFFFTIRON.png){width=1000px}

### 3.2.2. 自然顺序输入、比特位反序输出

![16-point radix-2 DIF-FFT 自然顺序输入、比特位反序输出](../images/post/2023-04-15-josh-dsp-part-5/2023-04-15-josh-dsp-part-5-150-16PointRadix2DIFFFTINOR.png){width=1000px}

## 3.3. 分裂基按时间抽取 FFT（16-point split radix-2/4 DIT-FFT）

![16-point split radix-2 DIT-FFT](../images/post/2023-04-15-josh-dsp-part-5/2023-04-15-josh-dsp-part-5-160-16PointSplitRadix24DITFFT.png){width=1000px}

## 3.4. 分裂基按频率抽取 FFT（16-point split radix-2/4 DIF-FFT）

![16-point split radix-2 DIF-FFT](../images/post/2023-04-15-josh-dsp-part-5/2023-04-15-josh-dsp-part-5-170-16PointSplitRadix24DIFFFT.png){width=1000px}

## 3.5. 基-4 按频率抽取 FFT（16-point radix-4 DIF-FFT）

![16-point radix-4 DIF-FFT](../images/post/2023-04-15-josh-dsp-part-5/2023-04-15-josh-dsp-part-5-180-16PointRadix4DITFFT.png){width=1000px}

可以定义如下基-4 DIF-FFT 简化蝶形来简化流图表示的复杂程度

![基-4 DIF-FFT 简化蝶形](../images/post/2023-04-15-josh-dsp-part-5/2023-04-15-josh-dsp-part-5-190-SimplifiedRadix4Butterfly.png){width=800px}

# 参考文献

王世一. *数字信号处理, 修订版.* 北京理工大学出版社, 1997.
