---
layout: post
title: 视图窗口化(Views Windowing)
---

Views 提供了通过 `Widget` 对象创建对话框和其他类型窗口的支持。开发者通过实现一个 `WidgetDelegate`（或其子接口），为窗口提供显示所需的信息，并提供窗口事件的回调通知。

### 简单示例

以下是创建一个简单窗口的示例代码：

```cpp
#include "base/basictypes.h"
#include "base/compiler_specific.h"
#include "base/utf_string_conversions.h"
#include "ui/gfx/canvas.h"
#include "ui/views/controls/label.h"
#include "ui/views/view.h"
#include "ui/views/widget/widget.h"
#include "ui/views/widget/widget_delegate.h"

class WindowView : public views::WidgetDelegateView {
 public:
  WindowView() : label_(NULL) { Init(); }
  virtual ~WindowView() {}

 private:
  // Overridden from views::View:
  virtual void OnPaint(gfx::Canvas* canvas) OVERRIDE {
    canvas->FillRect(GetLocalBounds(), SK_ColorWHITE);
  }

  virtual void Layout() OVERRIDE {
    gfx::Size ps = label_->GetPreferredSize();
    label_->SetBounds((width() - ps.width()) / 2, (height() - ps.height()) / 2,
                      ps.width(), ps.height());
  }

  virtual gfx::Size GetPreferredSize() OVERRIDE {
    gfx::Size ps = label_->GetPreferredSize();
    ps.set_width(ps.width() + 200);
    ps.set_height(ps.height() + 200);
    return ps;
  }

  // Overridden from views::WidgetDelegate:
  virtual string16 GetWindowTitle() const OVERRIDE {
    return ASCIIToUTF16("Hello World Window");
  }

  virtual bool CanResize() const OVERRIDE { return true; }

  virtual bool CanMaximize() const OVERRIDE { return true; }

  virtual views::View* GetContentsView() OVERRIDE { return this; }

  void Init() {
    label_ = new views::Label(ASCIIToUTF16("Hello, World!"));
    AddChildView(label_);
  }

  views::Label* label_;

  DISALLOW_COPY_AND_ASSIGN(WindowView);
};

...

views::Widget::CreateWindow(new WindowView)->Show();
```

这个窗口在用户关闭时会自行删除，这将导致其内的 `RootView` 被销毁，包括 `WindowView`。

### WidgetDelegate

`WidgetDelegate` 是一个接口，为 `Widget` 类提供显示窗口时所需的信息，如标题和图标，以及窗口是否可调整大小等属性。它还提供了如关闭等事件的回调函数。`WidgetDelegate` 有一个访问器 `window()`，它提供对关联窗口对象的访问。一个 `Widget` 有一个 `ContentsView`，由 `WidgetDelegate` 提供，它是一个插入到窗口客户区的 `View`。

### DialogDelegate

`DialogDelegate` 是一种专门用于对话框的 `WidgetDelegate`，通常带有 OK/Cancel 按钮。`DialogDelegate` 及其关联的 `ClientView`（见下文）提供了内置的 OK/Cancel 按钮、Esc/Enter 的键盘处理程序及这些功能的启用。作为用户，你编写的 `View` 将插入到 `DialogClientView` 中提供对话框的内容，并实现 `DialogDelegate` 以代替 `WidgetDelegate`，后者提供按钮点击时的回调函数以及提供按钮文本等方法。

### Client 和 Non-Client Views

![arch.png]({{ "/assets/img/NonClientView.png" | absolute_url }})

由于 Chrome 的非标准窗口设计，Views 支持自定义渲染的非客户区。这是通过 `Window` 类和 `NonClientFrameView` 子类来支持的。要提供自定义窗口框架，`View` 子类化 `NonClientFrameView`，允许重写的 `View` 来渲染和响应窗口非客户区的事件。Views 包含两个内置类型来实现这一点——`CustomFrameView` 和 `NativeFrameView`。它们用于标准对话框和顶级窗口。

对于浏览器窗口，使用了不同的 `NonClientFrameView` 子类（如 `GlassBrowserFrameView` 和 `OpaqueBrowserFrameView`）。为允许使用这些子类，浏览器重写了 `Window` 的 `CreateFrameViewForWindow` 方法，以构造适当的框架视图。

除了 `RootView` 之外，窗口 `View` 层次结构中的顶层 `View` 是 `NonClientView`。这个 `View` 是一个容器，包含上面描述的 `NonClientFrameView` 和 `ClientView` 或其子类。`ClientView` 子类包含窗口客户区的内容（标题栏/框架内的内容）。一个常见的 `ClientView` 子类是 `DialogClientView`，它提供了内置的 OK/Cancel 按钮处理和对话框主题背景。要使用的具体 `ClientView` 由 `WidgetDelegate` 在其 `CreateClientView` 实现中指定。`DialogDelegate` 的默认实现会自动创建一个 `DialogClientView`。自定义 `WidgetDelegate` 可以实现此方法以返回其自己的 `ClientView`，例如 `BrowserView`（浏览器窗口的 `WidgetDelegate` 实现者）返回它自己。`ClientView` API 相对简单，但它可以在进行非客户区命中测试时执行操作，这是在 `TabStrip` 内的可拖动标题栏和窗口调整大小角的实现方式。

`ClientView` 和 `NonClientFrameView` 是兄弟视图，因为 Views 通常会在它们被插入 `View` 层次结构时进行一次性初始化，而 Windows Vista 及更新版本的 DWM 切换意味着需要在启用或禁用 DWM 时更换 `NonClientFrameView`。因此，如果 `ClientView` 是 `NonClientFrameView` 的子视图，它将被重新父化，这意味着它的子视图可能会重新初始化，导致负面影响。

### 创建一个窗口

使用上面定义的 `WindowView` 创建窗口的简单代码如下：

```cpp
views::Widget* window = views::Widget::CreateWindow(new WindowView);
window->Show();
```

这段代码展示了如何使用 Views 创建和管理自定义窗口。
