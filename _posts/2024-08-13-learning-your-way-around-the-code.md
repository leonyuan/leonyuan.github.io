---
layout: post
title: 学习如何理解代码
---

Chromium代码库有很多值得学习的内容，既有宏观层面的（例如，进程的布局、IPC的工作方式、URL加载的流程），也有微观层面的（例如，代码习惯，如智能指针使用指南、消息循环、常见线程、线程指南、字符串使用指南等）。

## 学习以Chromium的方式做事

编码风格：如果你在其他地方有过编码经验，可能会觉得Chrome的指南（以及代码审查者的评论）比较严格。例如，禁止在行末使用多余的空格。所有的注释都应该是完整的英文句子，包括句号。严格限制每行代码的长度为80列（但对于无法拆分的内容可能有例外）。

## 个人学习计划

最终，你将完成环境搭建并开始工作。在理想情况下，我们会有足够的时间阅读每一行代码并在编写第一行代码之前理解它。然而，实际上这是不可能的。那么，我们能做些什么呢？我们建议你制定自己的学习计划，以下是一些建议的起点。幸运的是，Chromium拥有一些高质量的设计文档。尽管这些文档可能会有些过时（例如，在阅读过程中，你可能会发现一些文件已经被移动、重命名或重构），但能够理解代码的整体结构仍然是非常有帮助的。

1. 阅读最重要的开发者设计文档

	1. 多进程架构
	1. 在Chrome中显示网页
	1. 进程间通信
	1. 线程处理
	1. 了解Chrome源代码

2. 查看你的团队是否有任何文档；可能有些文档是团队中处理相同代码的人关心的，而其他人不需要知道太多细节。

3. 学习一些代码惯用法：

	1. 重要的抽象和数据结构
	1. 智能指针指南
	1. Chromium字符串使用

4. 之后，在有时间的情况下，浏览所有设计文档，阅读相关部分。

5. 熟练使用代码搜索工具（或你选择的代码浏览工具）

6. 学习如何找到了解代码运作的合适人员（找到知道代码工作原理的人）

7. 使用调试器深入学习你需要理解的代码，如果无法使用调试器，可以使用日志语句和grep工具。

8. 查看你需要理解的内容与当前理解之间的差异。例如，如果你的团队做了大量的GUI编程，那么你可能需要花时间学习GTK+、Win32或Cocoa编程。

## Blink

有时，为了修复或添加Chromium的功能，最合适的地方是在Blink（前身为WebKit）中进行。2012年有一份名为“WebKit如何工作”的幻灯片。虽然Blink已经分叉，但其中一些内容可能仍然适用。

此外，还有一份幻灯片解释了已经熟悉Chromium开发的人员进行WebKit开发的基本工作流程。