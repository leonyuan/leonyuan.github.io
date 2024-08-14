---
layout: post
title: Views 概述(UI Views Overview)
---

本文档概述了 Views 的概念、术语和架构。目标受众是使用或开发 Views 的工程师。

## 基本事项

本文档中的点用 `(x, y)` 表示，矩形用 `[(x, y) w×h]` 表示。例如，矩形 `[(100, 100) 50×20]` 包含点 `(130, 110)`。

Views 使用的坐标系以左上角 `(0, 0)` 为原点，x 坐标向右增加，y 坐标向下增加。这与 Windows 和 GTK 的坐标系统相同，但与 Cocoa 坐标系统不同，后者的原点在左下角。本文档中的坐标使用 Views 坐标系。

Views 通常接管通过原始指针传递给它们的对象的所有权，但也有一些例外情况，比如代理（delegates）。

## Views

View 是一个 UI 元素，类似于 HTML DOM 元素。每个 View 占据一个矩形区域，称为其边界，该区域在其父 View 的坐标系中表示。Views 可以有子 View，这些子 View 根据父 View 的布局管理器进行布局，尽管单个 View 也可以重写 `View::Layout` 来实现自己的布局逻辑。每个 View 还可以有边框和/或背景。

每个 View 可以计算不同的尺寸，这些尺寸由父 View 用来决定如何定位和调整 View 的大小。View 可以具有首选大小、最小大小和最大大小中的任意一个或多个。这些大小也可能由 View 的布局管理器计算，并可能被父 View 的布局管理器使用。

通常，不建议显式更改 View 的边界。通常，边界由父 View 的 `Layout` 方法或父 View 的布局管理器计算得出。最好构建一个符合需求的布局管理器，而不是通过更改边界来手动布局 Views。

有关 Views 的详细信息，请参见 `view.h`。

## 边框

边框通常绘制在 View 的边界矩形的边缘上，并定义 View 的内容边界，即绘制 View 内容的区域。例如，一个位置为 `[(0, 0) 100×100]` 的 View，如果有一个厚度为 2 的实心边框，则其内容边界为 `[(2, 2) 96×96]`。

有关详细信息，请参见 `border.h`。

## 背景

背景绘制在 View 的其他部分（包括边框）之下。任何 View 都可以有背景，但大多数 View 没有。背景通常负责填充 View 的整个边界。背景通常是一个颜色，但也可以是渐变或其他内容。

有关详细信息，请参见 `background.h`。

## 内容

内容是 View 的内容边界内的区域。如果 View 有子 View，这些子 View 也将在 View 的内容边界内定位和绘制。没有表示 View 内容区域的类；它仅作为由 View 的边框包围的空间存在，其形状由边框的内边距定义。

## 布局与布局管理器

View 的布局管理器定义了如何在 View 的内容边界内布局其子 View。Views 提供了一些布局管理器。最简单的是 `FillLayout`，它将 View 的所有子 View 都布局在 View 的整个内容边界内。`FlexLayout` 提供了类似 CSS 的布局，用于水平和垂直排列视图。

其他常用的布局管理器包括 `BoxLayout`（`FlexLayout` 的前身）和 `TableLayout`，它提供了一个灵活的行列系统。

## 绘制

Views 通过对 View 树进行先序遍历（即，父 View 在子 View 之前绘制）来绘制。每个 View 按 z 顺序绘制其所有子 View，顺序由 `View::GetChildrenInZOrder()` 确定，因此 z 顺序中的最后一个子 View 最后绘制，因此覆盖前面的子 View。默认的 z 顺序是添加子 View 到父 View 的顺序。

不同的 View 子类在 `View::OnPaint` 中实现自己的绘制逻辑，默认情况下它仅调用 `View::OnPaintBackground` 和 `View::OnPaintBorder`。通常，View 子类在实现 `View::OnPaint` 时首先调用父类的 `View::OnPaint`。

如果你需要为 View 子类创建特殊的背景或边框，最好创建一个 `Background` 或 `Border` 子类并安装它，而不是重写 `::OnPaintBackground` 或 `::OnPaintBorder`。这样做有助于保持 Views 分为上述三部分的结构，并使绘制代码更易于理解。

## 调试

请参见相关页面获取详细信息。

## Widgets

Views 需要一个底层画布来绘制。这必须由操作系统提供，通常是通过创建某种原生绘图表面。Views 称这些为 widgets。Widget 是 Views 树与某种原生窗口（Views 称之为原生 widget）之间的桥梁。每个 Widget 都有一个根 View，它是一个特殊的 View 子类，有助于建立这种桥梁；根 View 反过来有一个单一的子 View，称为 Widget 的内容 View，它填充整个根 View。在给定 Widget 中的所有其他 View 都是该 Widget 的内容 View 的子 View。

Widgets 有许多职责，包括但不限于：

- 键盘焦点管理，通过 `FocusManager`
- 窗口调整大小/最小化/最大化
- 窗口形状处理，用于非矩形窗口
- 输入事件路由

有关详细信息，请参见 `widget.h`。

## 客户区与非客户区视图

大多数 Widget 的内容 View 是一个非客户区视图，通常是 `NonClientView` 或其派生类之一。非客户区视图有两个子 View，分别是非客户区框架视图（`NonClientFrameView`）和客户区视图（`ClientView`）。非客户区框架视图负责绘制窗口装饰、Widget 的边框、阴影等；客户区视图负责绘制 Widget 的内容。客户区视图所占据的区域有时被称为 Widget 的“客户区”。随着系统主题的变化，非客户区框架视图可以被替换，而不会影响客户区视图。

一个 Widget 及其辅助视图的整体结构如下图所示：

**[Views 结构图]**

## 对话框

一种常用的客户区视图是对话框客户区视图，它具有一个内容 View、右下角的可选按钮和左下角的可选额外 View。对话框通常通过子类化 `DialogDelegate` 或 `DialogDelegateView` 并调用 `DialogDelegate::CreateDialogWidget` 来创建。对话框的内容 View 填充了 Widget 客户区视图的整个顶部部分，而底部部分由对话框的按钮和额外 View 占据。

## 气泡

一种常见的对话框类型是气泡，即锚定在父 View 上并随着父 View 移动的对话框。气泡通常通过子类化 `BubbleDialogDelegateView` 并调用 `BubbleDialogDelegateView::CreateBubble` 来创建。气泡可以有一个标题，作为气泡 Widget 的 `NonClientFrameView` 的一部分与窗口控件一起绘制。
