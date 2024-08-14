---
layout: post
title: 进程间通信 (IPC)
---

## 目录
- 概述  
  - 浏览器中的IPC  
  - 染器中的IPC  
- 消息  
  - 消息类型  
  - 声明消息  
    - 序列化值  
  - 发送消息  
  - 处理消息  
  - 安全注意事项  
- 通道  
- 同步消息  
  - 声明同步消息  
  - 发送同步消息  
  - 处理同步消息  
  - 将消息类型转换为消息名称  

## 概述  
Chromium具有多进程架构，这意味着我们有许多进程相互通信。我们主要的进程间通信机制是命名管道。在Linux和OS X上，我们使用socketpair()。为每个渲染器进程分配了一个命名管道，用于与浏览器进程通信。这些管道以异步模式使用，以确保双方不会因等待对方而被阻塞。

有关如何编写安全的IPC端点的建议，请参阅IPC的安全提示。

### 浏览器中的IPC  
在浏览器中，与渲染器的通信是在单独的I/O线程中完成的。视图之间的消息必须通过ChannelProxy代理到主线程。这种方案的优势在于资源请求（如网页等），这是最常见且性能关键的消息，可以完全在I/O线程上处理，而不会阻塞用户界面。这些通过RenderProcessHost插入到通道中的ChannelProxy::MessageFilter实现。这种过滤器在I/O线程中运行，拦截资源请求消息，并直接转发到资源调度主机。有关资源加载的更多信息，请参阅多进程资源加载。

### 渲染器中的IPC  
每个渲染器也有一个线程来管理通信（在这种情况下是主线程），而渲染和大部分处理发生在另一个线程上（请参阅多进程架构中的图示）。大多数消息是通过主渲染器线程从浏览器发送到WebKit线程，反之亦然。这个额外的线程用于支持同步的渲染器到浏览器消息（参见下面的“同步消息”）。

## 消息  
### 消息类型  
我们有两种主要的消息类型：“路由消息”和“控制消息”。控制消息由创建管道的类处理。有时该类会允许其他人通过具有MessageRouter对象来接收消息，其他监听器可以注册并接收通过它们的唯一（每个管道）ID发送的“路由消息”。

例如，在渲染时，控制消息并不特定于某个视图，因此由RenderProcess（渲染器）或RenderProcessHost（浏览器）处理。资源请求或修改剪贴板的请求不是视图特定的，因此是控制消息。路由消息的一个例子是请求视图绘制区域的消息。

历史上，路由消息用于将消息发送到特定的RenderViewHost。然而，技术上任何类都可以通过使用RenderProcessHost::GetNextRoutingID并将自身注册到RenderProcessHost::AddRoute来接收路由消息。目前，RenderViewHost和RenderFrameHost实例都有自己的路由ID。

消息类型独立于消息是从浏览器发送到渲染器，还是从渲染器发送到浏览器。与文档帧相关的消息从浏览器发送到渲染器时称为帧消息，因为它们被发送到RenderFrame。同样，从渲染器发送到浏览器的消息称为FrameHost消息，因为它们被发送到RenderFrameHost。你会注意到，在frame_messages.h中定义的消息分为两个部分，一个是Frame消息，一个是FrameHost消息。

插件也有独立的进程。与渲染消息类似，有从浏览器发送到插件进程的PluginProcess消息和从插件进程发送到浏览器的PluginProcessHost消息。这些消息都定义在plugin_process_messages.h中。自动化消息（用于从UI测试控制浏览器）以类似的方式完成。

这种组织方式也适用于浏览器和渲染器之间交换的其他消息组，例如在view_messages.h中定义的在RenderViewHost和RenderView之间交换的View和ViewHost消息。

### 声明消息  
声明消息使用特殊的宏。从渲染器到浏览器声明一个包含URL和整数作为参数的路由消息（例如特定于帧的FrameHost消息），写：

```cpp
IPC_MESSAGE_ROUTED2(FrameHostMsg_MyMessage, GURL, int)
```

要从浏览器到渲染器声明一个不特定于帧且不包含参数的控制消息，写：

```cpp
IPC_MESSAGE_CONTROL0(FrameMsg_MyMessage)
```

#### 序列化值  
参数使用ParamTraits模板序列化和反序列化到消息体中。在ipc_message_utils.h中为大多数常见类型提供了此模板的特化。如果你定义了自己的类型，你还需要为其定义自己的ParamTraits特化。

有时，消息包含的值太多，不适合放在一个消息中。在这种情况下，我们定义一个单独的结构来保存这些值。例如，对于FrameMsg_Navigate消息，在navigation_params.h中定义了CommonNavigationParams结构。frame_messages.h使用IPC_STRUCT_TRAITS家族的宏定义了结构的ParamTraits特化。

### 发送消息  
消息通过“通道”发送（见下文）。在浏览器中，RenderProcessHost包含用于从浏览器的UI线程向渲染器发送消息的通道。RenderWidgetHost（RenderViewHost的基类）提供了一个用于方便的Send函数。

消息通过指针发送，IPC层将在它们被调度后删除它们。因此，一旦找到合适的Send函数，只需用一个新消息调用它：

```cpp
Send(new ViewMsg_StopFinding(routing_id_));
```

请注意，你必须指定路由ID，以便消息能够路由到接收端的正确视图/视图主机。RenderWidgetHost（RenderViewHost的基类）和RenderWidget（RenderView的基类）都有GetRoutingID()成员，你可以使用它们。

### 处理消息  
消息通过实现IPC::Listener接口来处理，其中最重要的函数是OnMessageReceived。我们有多种宏来简化该函数中的消息处理，以下示例最好地说明了这一点：

```cpp
MyClass::OnMessageReceived(const IPC::Message& message) {
  IPC_BEGIN_MESSAGE_MAP(MyClass, message)
    // 将调用OnMyMessage函数并传递消息。消息的参数将为你解包。
    IPC_MESSAGE_HANDLER(ViewHostMsg_MyMessage, OnMyMessage)  
    ...
    IPC_MESSAGE_UNHANDLED_ERROR()  // 这将为未处理的消息抛出异常。
  IPC_END_MESSAGE_MAP()
}

// 这个函数将使用从ViewHostMsg_MyMessage消息中提取的参数调用。
MyClass::OnMyMessage(const GURL& url, int something) {
  ...
}
```

你还可以使用IPC_DEFINE_MESSAGE_MAP来为你实现函数定义。在这种情况下，不要指定消息变量名称，它将声明一个OnMessageReceived函数并在给定类上实现其内容。

其他宏：

```cpp
IPC_MESSAGE_FORWARD: 这与IPC_MESSAGE_HANDLER相同，但你可以指定自己的类来发送消息，而不是发送到当前类。
IPC_MESSAGE_FORWARD(ViewHostMsg_MyMessage, some_object_pointer, SomeObject::OnMyMessage)
IPC_MESSAGE_HANDLER_GENERIC: 这允许你编写自己的代码，但你必须自己从消息中解包参数：
IPC_MESSAGE_HANDLER_GENERIC(ViewHostMsg_MyMessage, printf("Hello, world, I got the message."))
```

### 安全注意事项  
IPC中的安全漏洞可能会带来严重后果（文件盗窃、沙箱逃逸、远程代码执行）。请查阅我们的IPC安全文档，了解如何避免常见的陷阱。

## 通道  
IPC::Channel（定义在ipc/ipc_channel.h中）定义了用于跨管道通信的方法。IPC::SyncChannel提供了额外的功能，用于同步等待某些消息的响应（渲染器进程使用此功能，如下文“同步消息”部分所述，但浏览器进程从不使用）。

通道不是线程安全的。我们经常希望使用另一个线程上的通道发送消息。例如，当UI线程想要发送消息时，必须通过I/O线程。为此，我们使用IPC::ChannelProxy。它的API类似于常规通道对象，但代理消息到另一个线程以发送它们，并在接收时将消息代理回原始线程。它允许你的对象（通常在UI线程上）在通道线程（通常是I/O线程）上安装一个IPC::ChannelProxy::Listener，以过滤掉一些不需要代理的消息。我们使用这个来处理可以直接在I/O线程上处理的资源请求和其他请求。RenderProcessHost安装了一个RenderMessageFilter对象来进行这种过滤。

## 同步消息  
从渲染器的角度看，某些消息应该是同步的。这种情况大多发生在WebKit调用我们时，要求返回某些内容，但我们必须在浏览器中完成。例如，拼写检查和为JavaScript获取cookies就是这种消息的例子。为了防止在一个可能不稳定的渲染器上阻塞用户界面，禁止了同步的浏览器到渲染器的IPC。

注意：不要在UI线程上处理任何同步消息！你必须仅在I/O线程上处理它们。否则，应用程序可能会发生死锁，因为插件需要UI线程上的同步绘画，而这些操作将在渲染器等待来自

浏览器的同步消息时被阻塞。

### 声明同步消息  
同步消息使用IPC_SYNC_MESSAGE_*宏声明。这些宏具有输入和返回参数（非同步消息没有返回参数的概念）。对于一个有两个输入参数和一个返回参数的控制函数，你可以将2_1附加到宏名称后得到：

```cpp
IPC_SYNC_MESSAGE_CONTROL2_1(SomeMessage,  // 消息名称
                            GURL, //input_param1
                            int, //input_param2
                            std::string); //result
```

同样，你也可以声明路由到视图的消息，在这种情况下你会将"control"替换为"routed"，得到IPC_SYNC_MESSAGE_ROUTED2_1。你还可以有0个输入或返回参数。当渲染器必须等待浏览器做某事，但不需要结果时，使用没有返回参数的情况。我们在某些打印和剪贴板操作中使用了这种情况。

### 发送同步消息  
当WebKit线程发出同步的IPC请求时，请求对象（派生自IPC::SyncMessage）通过IPC::SyncChannel对象（也用于发送所有异步消息）分派到渲染器上的主线程。当SyncChannel收到同步消息时，将阻塞调用线程，并仅在收到回复时解锁。

当WebKit线程等待同步回复时，主线程仍然会接收来自浏览器进程的消息。这些消息将在WebKit线程醒来后被添加到其处理队列中。当收到同步消息回复时，线程将被解锁。请注意，这意味着同步消息回复可能会被乱序处理。

同步消息的发送方式与普通消息相同，输出参数被赋予构造函数。例如：

```cpp
const GURL input_param("http://www.google.com/");
std::string result;
content::RenderThread::Get()->Send(new MyMessage(input_param, &result));
printf("The result is %s\\n", result.c_str());
```

### 处理同步消息  
同步消息和异步消息使用相同的IPC_MESSAGE_HANDLER等宏来调度消息。消息的处理函数将具有与消息构造函数相同的签名，函数将简单地将输出写入输出参数。对于上述消息，你可以在OnMessageReceived函数中添加

```cpp
IPC_MESSAGE_HANDLER(MyMessage, OnMyMessage)
```

并编写：

```cpp
void RenderProcessHost::OnMyMessage(GURL input_param, std::string* result) {
  *result = input_param.spec() + " is not available";
}
```

### 将消息类型转换为消息名称  
如果你遇到崩溃并且你有消息类型，你可以将其转换为消息名称。消息类型将是一个32位值，高16位是类，低16位是ID。类基于ipc/ipc_message_start.h中的枚举，ID基于定义消息的文件中的行号。这意味着你需要获得Chromium的确切版本，才能准确获得消息名称。

在554011中，这个例子是Chromium修订版ad0950c1ac32ef02b0b0133ebac2a0fa4771cf20中的0x1c0098。这是0x1c类，它对应的是第40行，匹配ChildProcessMsgStart。ChildProcessMsgStart消息位于content/common/child_process_messages.h中，IPC将在第0x98行或第152行，即ChildProcessHostMsg_ChildHistogramData。

这种技术在处理由content::RenderProcessHostImpl::OnBadMessageReceived引起的崩溃时特别有用。
