为了在远端 Web 服务器加载的网页中通过 JavaScript 调用 Chromium 浏览器中的 C++ 函数，您需要使用 V8 引擎 API 实现 JavaScript 和 C++ 的交互。此方案需要您在 Chromium 的渲染进程中注入自定义的 JavaScript 函数，然后将其绑定到 C++ 代码。

## 实现方案概述

1. **步骤1：** 在渲染进程中通过 V8 API 暴露自定义的 JavaScript 函数。
2. **步骤2：** 将暴露的 JavaScript 函数绑定到 C++ 实现。
3. **步骤3：** 在来自远端 Web 服务器的网页中调用该 JavaScript 函数，实现与 C++ 逻辑的交互。

## 1. **步骤 1：创建自定义的 JavaScript 函数**

### 1.1 修改 `render_frame_impl.cc`

在 `render_frame_impl.cc` 中使用 V8 API 暴露自定义的 JavaScript 函数。在 `RenderFrameImpl::DidCreateScriptContext` 方法中添加自定义 JavaScript 函数的绑定逻辑：

```cpp
#include "v8/include/v8.h"
#include "gin/public/context_holder.h"
#include "gin/arguments.h"
#include "gin/function_template.h"
#include "gin/wrappable.h"

// 定义一个 C++ 函数，用于在 JavaScript 中调用
void HelloWorldFunction(const v8::FunctionCallbackInfo<v8::Value>& args) {
  v8::Isolate* isolate = args.GetIsolate();
  v8::HandleScope handle_scope(isolate);
  v8::Local<v8::String> message = v8::String::NewFromUtf8(isolate, "Hello from C++!").ToLocalChecked();
 
  // 将返回值传递给 JavaScript
  args.GetReturnValue().Set(message);
}

```

## 2. **步骤 2：将暴露的函数绑定到 C++ 实现**

```cpp
void RenderFrameImpl::DidCreateScriptContext(v8::Local<v8::Context> context, int32_t world_id) {

  // 以上为原有代码，在函数的最后添加下列代码

  v8::Isolate* isolate = context->GetIsolate();
  v8::HandleScope handle_scope(isolate);

  // 获取全局对象
  v8::Local<v8::Object> global = context->Global();

  // 将 C++ 函数绑定到全局对象的 HelloWorld 方法
  global->Set(context, v8::String::NewFromUtf8(isolate, "HelloWorld").ToLocalChecked(),
              v8::Function::New(context, [](const v8::FunctionCallbackInfo<v8::Value>& info) {
                HelloWorldFunction(info);
              }).ToLocalChecked()).Check();

}
```

在此代码中，我们将一个 C++ 函数 `HelloWorldFunction` 绑定到了 JavaScript 的全局对象上，并命名为 `HelloWorld`。当 JavaScript 调用 `HelloWorld` 时，它将返回 C++ 函数的结果。

## 3. **步骤 3：在远端网页中调用 JavaScript 函数**

在远端服务器托管的网页中，可以像调用普通 JavaScript 函数一样调用 `HelloWorld`：

```html
<!DOCTYPE html>
<html>
<head>
  <title>Remote Page</title>
  <script>
    function callCppFunction() {
      const result = HelloWorld();
      console.log(result);  // 应该打印 "Hello from C++!"
    }
  </script>
</head>
<body>
  <h1>Remote Web Page</h1>
  <button onclick="callCppFunction()">Call C++ Function</button>
</body>
</html>
```

当点击按钮时，`HelloWorld` 函数会在浏览器的渲染进程中被调用，返回 C++ 代码中的结果，结果会在控制台中输出。

## 编译和运行

完成上述修改后，重新编译 Chromium：

```bash
ninja -C out/Default chrome
```

编译完成后，运行您的定制浏览器，打开远端服务器的网页并点击按钮，应该能够看到 C++ 函数的输出。

## 总结

通过这种方法，您可以在远程加载的 Web 页面中通过 JavaScript 调用 Chromium 中的 C++ 函数。这种方式依赖于 V8 引擎 API，将 C++ 函数暴露给 JavaScript，并通过自定义的 JavaScript 函数进行调用。这是实现跨语言交互的标准方式，可以在您的定制浏览器中正确编译和运行。
