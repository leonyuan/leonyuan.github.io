---
layout: post
title: 浏览器窗口(Browser Window)
---

Chromium 浏览器窗口由多个对象表示，其中一些在下图中包括：

![arch.png]({{ "/assets/img/BrowserWindow2.png" | absolute_url }})

## Frame（框架）

框架是浏览器窗口的一部分，包括标题栏、调整大小的边框以及其他在 Windows 术语中被称为“非客户区”的区域。我们提供了一个框架（称为 `BrowserFrame`），它是 `views::Widget` 类的子类，添加了一些在 Vista 系统上用于 DWM（桌面窗口管理器）调整的处理，如标签条后面的调整等。在 Windows Vista 上，仅当启用了桌面合成时才使用玻璃模式；在经典模式或 Vista 基本模式下，我们使用 XP Luna 模式。由于用户可以通过更改 Windows 主题来开启或关闭合成，因此我们需要能够动态更改框架模式。有关在 `views` 中如何执行此操作的更多信息，请参阅 `views::Widget` 文档。浏览器窗口提供了两个 `NonClientFrameView` 子类——`GlassBrowserFrameView` 和 `OpaqueBrowserFrameView`，在 DWM 切换时，它们会在 `BrowserFrame` 的 `NonClientView` 中进行交换。

## BrowserView（浏览器视图）

`BrowserView` 对象包含浏览器窗口呈现的框架之间的所有共有元素——标签条、工具栏、书签栏及其他 UI 元素。当框架发生变化时，此视图会插入到新的 `NonClientView` 中。

`BrowserView` 实现了一个名为 `BrowserWindow` 的接口，`Browser` 对象使用该接口与视图进行交互。

## Browser（浏览器）

这是浏览器窗口的核心状态和命令执行组件。它与一个抽象的 `BrowserWindow` 接口进行交互，以更新 UI。这使我们能够编写单元测试，提供一个无窗口的“测试视图”，然后在单元测试框架内执行浏览器中的高级功能，而无需运行 UI 测试。
