---
title: Zynq 系列 SoC / MPSoC 创建可调试的 FSBL
mathjax: true
comments: true
copyright: true
toc:
  enable: true
  number: true
  wrap: true
  expand_all: true
  max_depth: 4
tags:
  - 调试记录
  - MPSoC
  - RFSoC
  - Zynq
  - FSBL
categories:
  - 调试记录
date: 2024-06-29 16:46:38
---

FSBL 可以初始化 SoC 设备，将所需的应用程序或数据加载到内存，并在目标 CPU 核心上启动应用程序。

在 Vitis 中创建 Platform 时，可以选择同时创建 FSBL，这样 FSBL 就会位于 Platform 下。也可以创建单独的 FSBL 程序，作为普通程序使用，以便进一步修改或调试。

本文以 Zynq UltraScale+ RFSoC 为例，对 FSBL 进行调试。

# 名词释义

- [FSBL](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/18842019/Zynq+UltraScale+FSBL#ZynqUltraScale+FSBL-WhatisFSBL?)：First Stage Bootloader，第一阶段引导加载程序
- [SoC](https://en.wikipedia.org/wiki/System_on_a_chip)：System-on-Chip，片上系统

# 参考文档

[Creating a Debuggable First Stage Boot Loader](https://xilinx.github.io/Embedded-Design-Tutorials/docs/2021.1/build/html/docs/Feature_Tutorials/debuggable-fsbl/debuggable-fsbl.html)