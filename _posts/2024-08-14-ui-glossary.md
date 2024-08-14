---
layout: post
title: UI术语(UI Glossary)
---

- **Ash:** Chrome OS 的窗口环境，负责非浏览器 UI 例如登录屏幕、系统托盘和各种内置控制界面。与 Chrome 一样，Ash UI 使用 Views 工具包构建。

- **Aura:** 跨平台窗口管理抽象层，应用于 macOS 以外的桌面平台。Aura 处理窗口显示和原生输入事件等任务。由于 Aura 不是 Views 唯一的“平台”，因此 Views 中的一些复杂性在 macOS 上可能是不必要的；然而，macOS 的平台惯例与 Aura 当前支持的平台差异较大，因此在 macOS 上使用 Aura 可能需要重大重新设计或施加不理想的 API 约束。截至目前，macOS 是否应使用 Aura 仍然是一个悬而未决的问题。

- **AX:** “无障碍” 的缩写。许多以 “AX” 开头的类涉及暴露 Views 的无障碍信息，但最佳的起点可能是缺少它的类：ViewAccessibility。

- **Cocoa:** macOS 上的应用程序环境。最初，Chrome for Mac 的 UI 完全使用 Cocoa；现在，大多数原生 UI 使用 Views，但仍有部分使用 Cocoa。与 Cocoa 交互的代码通常用 Objective-C++ 编写，其源文件通常以 .mm 结尾。有关 Cocoa 的更多背景信息。

- **Combobox:** 显示从选项列表中选择的选项的控件。也称为 “select” 或 “dropdown” 控件。在 Views 中，特定控件默认具有焦点环和下拉标记，可以以各种方式自定义。views_examples 中的 “Combo Box” 示例创建了几个简单的组合框。

- **Compositor:** 负责根据 View 层次结构生成最终可显示图形的系统。合成器管理一个图层树，以便缓存、变换和混合各种图形层，从而最小化重绘，加速滚动，并将动画同步到底层显示的刷新率。

- **Focus Ring:** 在对象获得焦点时可见的彩色轮廓，当焦点消失时隐藏。通常由 FocusRing 对象绘制，这是一个处理沿路径绘制彩色边框的 View 子类。在创建焦点环时，重要的是要使其颜色与环内外的颜色形成对比；参见 PickGoogleColorTwoBackgrounds 函数的用法。

- **HWND:** Windows 特有的。一个不透明的系统原生窗口句柄。最终类型别名为 void*，但不应解引用或检查实际值；它仅用于传递给各种 Windows 原生 API。用作 Windows 上 AcceleratedWidget 的类型。

- **Ink drop:** 实现 hover 和点击效果的类集合。这些效果源自原始的 Material Design，使用动态创建的图层绘制半透明的覆盖层，可能被限制在 View 的边界内或扩展到边界外。名称源于墨水在水中扩散的外观，最显著的效果是 “涟漪”，从单一点（通常是光标位置）快速扩散。基础 InkDrop 类还可以管理即时开/关的 “悬停高亮” 效果，并包含用于不同用途的多种挂钩。

- **LaCrOS:** “Linux And Chrome OS” 的缩写。一个架构项目，旨在将 Chrome 浏览器从 Chrome OS 的操作系统栈中解耦；历史上这些组件是紧密耦合并同时更新的。有关 LaCrOS 的更多背景信息。

- **Layer:** 合成器用来管理纹理的对象树中的节点。调用 View::SetPaintToLayer() 会使 View 绘制到图层，这对于执行一些操作（例如在 View 自身的边界外绘制或在硬件中执行某些操作）是必要的；然而，图层有内存和性能成本，并且会影响兄弟绘制顺序，应谨慎使用。

- **Layout:** 一个操作，视图设置其所有子视图的边界。通常，对 View 调用 InvalidateLayout() 会递归标记该 View 及所有祖先为脏；然后，某个时刻，Widget::LayoutRootViewIfNecessary() 会调用 RootView 上的 LayoutImmediately()，它会调用其内容上的 Layout()，依此类推。Views 目前可以通过重写虚拟的 Layout() 方法来实现自定义布局，但应使用现有的 LayoutManagers 来描述子视图的排列方式；然后 LayoutManager 将负责计算 View 的首选大小和根据需要更新子视图边界。

- **NativeWidget:** 支持跨平台 Widget 对象的平台特定对象；Widget 通过调用 NativeWidget 上的方法来实现其 API。这在 Aura 和 Mac 中分别实现，至少在 Aura 上仍然是对真实底层操作系统窗口对象的抽象。历史上，Widget 和 NativeWidget 实例之间的生命周期和所有权可能有所不同且易出错；截至目前，这一问题正在解决中。

- **NS\***: “NeXTSTEP” 的缩写。在类型上看到这个前缀通常意味着该类型是 macOS 中长期存在的基本类型，其起源可以追溯到 1990 年代和苹果收购 NeXT。

- **Omnibox:** 浏览器窗口工具栏中的组合搜索和地址栏。由早期的 PM Brian Rakowski （作为“psychic omnibox”）创造，因为它知道和做一切。在代码中，术语 “omnibox” 通常专指文本编辑控件及其相关弹出窗口；它所出现的更大的视觉 “栏”，还包含指示安全状态的提示、允许书签和显示页面操作图标的功能，称为 “location bar”。

- **Progressive Web App (PWA):** 一个网站，可能提供各种原生应用程序类似的功能，如可安装性、系统硬件集成和/或持久性。Chrome 中的 PWAs 给予了特殊的 UI 处理。有关 PWAs 的更多背景信息。

- **SkColor:** 32bpp（每通道 8 位）的非预乘 ARGB 颜色值，存储为 uint32_t。这是 Views 在与绘图和绘画 API 交互时最常用的颜色类型。由于这是固定的（物理）颜色值，而不是主题（逻辑）颜色标识符，计算出的 SkColors 可能需要在主题更改时重新计算；通常，建议尽可能传递 ColorIds 而不是颜色值。

- **Skia:** Chrome 使用的 2D 图形库。Skia 由 Google 员工维护，提供硬件加速的绘图原语。有关 Skia 的更多背景信息。

- **Skia Gold:** 用于将像素测试结果图像与经过验证的基准进行比较的 Web 应用程序。在 browser_tests 或 interactive_ui_tests 中，可以选择进行像素测试，然后生成的结果图像由 Skia Gold 实例管理。截至目前，这些像素测试仅适用于 Windows。要运行这些测试，在调用测试二进制文件时添加 --browser-ui-tests-verify-pixels、--enable-pixel-output-in-tests 和 --test-launcher-retry-limit=0 标志。

- **Throbber:** 用于向用户指示明确或不确定进度的动画对象。Views 中的特定控件是一个 16 DIP 的旋转圆圈，可以在操作完成时选择性地显示一个复选标记。views_examples 中的 “Throbber” 示例创建了一个 throbber。

- **Views (库):** 一个跨平台 UI 工具包，用于创建“原生” UI（与使用 Web 技术构建的 UI 相对）。Views 对 Chrome 浏览器和 Chrome OS 在 Ash 中的原生 UI 都很重要。Views 在 Chrome 早期创建，用于抽象化对平台特定函数的调用，使得可以为多个操作系统构建 Chrome UI；与当时的其他跨平台工具包如 wxWidgets 或 GTK 不同，它旨在提供平台原生的感觉（尤其是在 Windows 上）和一个仅包含 Chrome 所需功能的最小 API。随着时间的推移，Views 的外观和感觉从平台原生的逼真度转向了一种名为 “Harmony” 的风格，这种风格受到所有桌面平台以及 Google 的 Material Design 的影响。然而，最小的 API 表面仍然存在；Views 缺少其他 UI 工具包中常见的许多控件和功能，并且不支持移动平台。

- **View (类):** 用于表示 UI 的对象树中的节点。一个 View 具有矩形边界、一组（可能为空的）子视图和大量 API 以允许交互和扩展。它的主要目标是抽象用户交互，并将数据呈现给浏览器以供显示。历史上，实施者构建了作为重型对象的子类，包含所有处理显示、业务逻辑和底层数据模型的代码；这减少了样板代码，但使类难以测试、重用或移植到其他平台。通过字面理解名字，并使 View 子类仅处理 MVC 范式中的 “视图” 角色，实施者可以生成更健壮的代码。

- **Widget:** 在 Views 工具包中表示 “窗口” 的跨平台抽

象。它是 Views 库与操作系统平台之间的桥梁。每个 Widget 包含一个 RootView 对象，该对象再包含一个视图层次结构，表示窗口中的 UI。Widget 是通过平台特定的 NativeWidget 实现的，NativeWidget 可能由其他抽象支持（至少在 Aura 上）。可以在小窗口（例如锚定到（并被大窗口裁剪的）更大窗口的浮动气泡）之间具有父/子关系。
