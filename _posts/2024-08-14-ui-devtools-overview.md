---
layout: post
title: UI DevTools 概述(UI Devtools Overview)
---

UI DevTools 允许 UI 开发人员像检查网页一样使用前端 DevTools Inspector 检查 Chrome 桌面 UI 系统。目前，它支持所有桌面平台，包括 Linux、Windows、Mac 和 ChromeOS。

## 如何运行

有两种方法可以启用 UI DevTools：

1. 以默认端口 9223 运行 Chromium，并使用 UI DevTools 命令行标志启动 Chromium：

   ```
   $ out/Default/chrome --enable-ui-devtools
   ```

   如果您想使用其他端口，请在标志 `--enable-ui-devtools=<port>` 中添加端口号。

2. 从 `chrome://flags` 启用 `enable-ui-devtools` 功能标志。

启用后，转到 `chrome://inspect#native-ui` 并点击“Inspect Native UI”按钮，以在单独的标签页中启动 DevTools 前端。
![Content.png]({{ "/assets/img/InspectNativeUI.png" | absolute_url }})

## 如何使用

**元素树**

视图、窗口和小部件元素会显示在层次化的元素树中。要展开元素树，右键点击根元素，然后从上下文菜单中选择“递归展开”。然后，点击元素节点以检查该元素的属性。
![Content.png]({{ "/assets/img/Elements-Tree.png" | absolute_url }})

**视图属性检查**

当元素树中的某个元素被选中时，右侧的属性面板会显示该元素的属性。所有元素都有基本属性（高度、宽度、x、y、可见性）。对于窗口和视图元素，如果该元素具有图层属性，它们将显示在一个单独的部分中。
![Content.png]({{ "/assets/img/Views-Property.png" | absolute_url }})

对于具有元数据属性的视图元素，元数据中的每个父类的属性将显示在一个附加部分中。
![Content.png]({{ "/assets/img/Views-Property2.png" | absolute_url }})

元素的基本属性可以在属性面板中修改，变化会立即显示。
![Content.png]({{ "/assets/img/Views-Property3.gif" | absolute_url }})

**检查模式**

要进入检查模式，点击 UI DevTools 左上角的检查图标。

![InspectMode.png]({{ "/assets/img/InspectMode.png" | absolute_url }})

将鼠标悬停在 UI 元素上会用蓝色矩形边框突出显示该元素，并在元素树中显示该元素的节点。
![HoveringOverElement.gif]({{ "/assets/img/HoveringOverElement.gif" | absolute_url }})

点击高亮显示的元素将固定该元素。点击元素树中相应的节点将显示该元素的属性。
![ClickingHighlightedElement.gif]({{ "/assets/img/ClickingHighlightedElement.gif" | absolute_url }})


当元素被固定时，将鼠标悬停在其他元素上会显示两个元素之间的垂直和水平距离。
![HoveringOverOtherElements.gif]({{ "/assets/img/HoveringOverOtherElements.gif" | absolute_url }})

要退出检查模式，可以再次点击检查图标或按 Esc 键。

**源面板**

在属性面板中，每个部分的右上角都有一个源链接。右键点击该链接，会在上下文菜单中显示“在新标签页中打开”选项，这将打开 Chromium 代码搜索中的类头文件。
![InspectMode.png]({{ "/assets/img/SourcesPanel.png" | absolute_url }})

如果直接点击该链接，它将在 UI DevTools 的源面板中打开源代码，该源代码从本地文件读取。
![OpenTheSourceCode.png]({{ "/assets/img/OpenTheSourceCode.png" | absolute_url }})

**视图边界高亮**

可以使用命令 Ctrl+R（Mac 上为 Meta+R）绘制每个视图元素周围的红色边框矩形。使用相同的命令可以切换矩形的开关。
![ViewBoundsHighlighting.png]({{ "/assets/img/ViewBoundsHighlighting.png" | absolute_url }})

**气泡锁定**

为了检查气泡，可以使用命令 Ctrl+Shift+R（Mac 上为 Meta+Shift+R）锁定气泡，防止它们在失去焦点时被关闭。这允许检查气泡的内部元素。气泡锁定可以使用相同的命令切换开关。

注意：在按 Ctrl+Shift+R（Mac 上为 Meta+Shift+R）之前，UI DevTools 窗口必须是活动窗口，否则会导致页面刷新。
![BubbleLocking.png]({{ "/assets/img/BubbleLocking.gif" | absolute_url }})

**UI 元素树搜索**

在元素面板中，按 Ctrl+F 打开底部的搜索栏。搜索功能允许开发人员通过名称、标签 <> 和样式属性快速搜索 UI 元素树。搜索可以进行子串匹配或精确匹配（用引号指定）。搜索返回所有匹配项并突出显示树中匹配的特定节点。搜索栏右侧的上下箭头或 ENTER 用于浏览匹配项。

![UIElementTreeSearch.png]({{ "/assets/img/UIElementTreeSearch.gif" | absolute_url }})

搜索功能也可以在 UI 元素树中搜索元素的样式属性。必须在搜索栏中输入特殊关键字‘style:’。
![SearchTheElementStyle.png]({{ "/assets/img/SearchTheElementStyle.png" | absolute_url }})

**参考文献**

- 旧版 Ash 文档（已废弃）
- 后端源代码
- Inspector 前端源代码
