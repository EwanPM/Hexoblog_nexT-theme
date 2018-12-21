---
title: '微软开源Sketch2Code,草图变代码'
tags: microsoft
categories: 资讯
abbrlink: 61588
date: 2018-09-07 22:06:12
---
今天在看微信公众号的时候，发现一个特牛逼的东西，Sketch2Code

用户界面设计过程涉及大量创造性的迭代工作。这个过程通常从在白板或白纸上画草图开始，设计师和工程师分享他们的想法，尽力表达出潜在的客户场景或工作流程。当他们在某个设计上达成一致之后，通过照片的形式将草图拍下来，然后手动将草图翻译成 HTML 代码。翻译过程需要耗费很多时间和精力，通常会减慢设计过程。

Sketch2Code 是一个基于 Web 的解决方案，使用 AI 将手绘的用户界面草图转换为可用的 HTML 代码。Sketch2Code 由微软和 Kabel、Spike Techniques 合作开发。读者可以在 GitHub 上找到与 Sketch2Code 相关的代码、解决方案开发过程和其他详细信息。

项目地址原码：https://github.com/Microsoft/ailab/tree/master/Sketch2Code

在微软官网做一些尝试:https://sketch2code.azurewebsites.net/

sketch2Code架构，工作流程
![](https://ws1.sinaimg.cn/large/0069RVTdgy1fv1c28bj9yj30m80b6mxi.jpg)

自己尝试了一下，这货简直吊炸天，不得不佩服，减少了页面布局这样无脑行为，更加专注逻辑，现在的能力还无法读懂源码，有点遗憾目前不能支持中文转译，源文件放中文就会出乱子。
![](https://ws3.sinaimg.cn/large/0069RVTdgy1fv1ckm1k0rj31060h8dkx.jpg)