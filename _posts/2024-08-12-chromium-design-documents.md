---
layout: post
title: Design Documents
---

## Start Here: Background Reading

-   [Multi-process Architecture](https://www.chromium.org/developers/design-documents/multi-process-architecture): Describes the high-level architecture of Chromium  **Note:**  Most of the rest of the design documents assume familiarity with the concepts explained in this document.
-   [How Blink works](https://docs.google.com/document/d/1aitSOucL0VHZa9Z2vbRJSyAIsAz24kX8LFByQ5xQnUg)  is a high-level overview of Blink architecture.
-   The "Life of a Pixel" talk ([slides](http://bit.ly/lifeofapixel)  /  [video](http://bit.ly/loap-2020-video)) is an introduction to Chromium's rendering pipeline, tracing the steps from web content to displayed pixels.
-   [somewhat outdated]  [How Chromium Displays Web Pages](https://www.chromium.org/developers/design-documents/displaying-a-web-page-in-chrome): Bottom-to-top overview of how WebKit is embedded in Chromium

## See Also

-   [Design docs in source code](https://chromium.googlesource.com/chromium/src/+/HEAD/docs/README.md)
-   [Design doc template](https://docs.google.com/document/d/14YBYKgk-uSfjfwpKFlp_omgUq5hwMVazy_M965s_1KA/edit)

## General Architecture

-   [Conventions and patterns for multi-platform development](https://www.chromium.org/developers/design-documents/conventions-and-patterns-for-multi-platform-development)
-   [Extension Security Architecture](http://webblaze.cs.berkeley.edu/2010/secureextensions/): How the extension system helps reduce the severity of extension vulnerabilities
-   [HW Video Acceleration in Chrom{e,ium}{,OS}](https://docs.google.com/a/chromium.org/document/d/1LUXNNv1CXkuQRj_2Qg79WUsPDLKfOUboi1IWfX2dyQE/preview#heading=h.c4hwvr7uzkfl)
-   [Inter-process Communication](https://www.chromium.org/developers/design-documents/inter-process-communication): How the browser, renderer, and plugin processes communicate
-   [Multi-process Resource Loading](https://www.chromium.org/developers/design-documents/multi-process-resource-loading): How pages and images are loaded from the network into the renderer
-   [Plugin Architecture](https://www.chromium.org/developers/design-documents/plugin-architecture)
-   [Process Models](https://www.chromium.org/developers/design-documents/process-models): Our strategies for creating new renderer processes
-   [Profile Architecture](https://www.chromium.org/developers/design-documents/profile-architecture)
-   [SafeBrowsing](https://www.chromium.org/developers/design-documents/safebrowsing)
-   [Sandbox](https://chromium.googlesource.com/chromium/src/+/HEAD/docs/design/sandbox.md)
-   [Security Architecture](http://crypto.stanford.edu/websec/chromium/): How Chromium's sandboxed rendering engine helps protect against malware
-   [Startup](https://chromium.googlesource.com/chromium/src/+/HEAD/docs/design/startup.md)
-   [Threading](https://chromium.googlesource.com/chromium/src/+/HEAD/docs/design/threading.md): How to use threads in Chromium

Also see the documentation for  [V8](http://code.google.com/apis/v8/), which is the JavaScript engine used within Chromium.

## UI Framework

-   [UI Development Practices](https://www.chromium.org/developers/design-documents/ui-development-practices): Best practices for UI development inside and outside of Chrome's content areas.
-   [Views framework](https://www.chromium.org/developers/design-documents/chromeviews): Our UI layout layer used on Windows/Chrome OS.
-   [views Windowing system](https://www.chromium.org/developers/design-documents/views-windowing): How to build dialog boxes and other windowed UI using views.
-   [Aura](https://www.chromium.org/developers/design-documents/aura): Chrome's next generation hardware accelerated UI framework, and the new ChromeOS window manager built using it.
-   [NativeControls](https://www.chromium.org/developers/design-documents/native-controls): using platform-native widgets in views.
-   [Focus and Activation with Views and Aura](https://www.chromium.org/developers/design-documents/aura/focus-and-activation).
-   [Input Event Routing in Desktop UI](https://chromium.googlesource.com/chromium/src/+/HEAD/docs/ui/input_event/index.md).

**Graphics**

-   [Overview](https://www.chromium.org/developers/design-documents/chromium-graphics)
-   [GPU Accelerated Compositing in Chrome](https://www.chromium.org/developers/design-documents/gpu-accelerated-compositing-in-chrome)
-   [GPU Feature Status Dashboard](https://www.chromium.org/developers/design-documents/gpu-accelerated-compositing-in-chrome/gpu-architecture-roadmap)
-   [Rendering Architecture Diagrams](https://www.chromium.org/developers/design-documents/rendering-architecture-diagrams)
-   [Graphics and Skia](https://www.chromium.org/developers/design-documents/graphics-and-skia)
-   [RenderText and Chrome UI text drawing](https://www.chromium.org/developers/design-documents/rendertext)
-   [GPU Command Buffer](https://www.chromium.org/developers/design-documents/gpu-command-buffer)
-   [GPU Program Caching](https://docs.google.com/a/chromium.org/document/d/1Vceem-nF4TCICoeGSh7OMXxfGuJEJYblGXRgN9V9hcE/edit)
-   [Compositing in Blink/WebCore: from WebCore::RenderLayer to cc::Layer](https://docs.google.com/presentation/d/1dDE5u76ZBIKmsqkWi2apx3BqV8HOcNf4xxBdyNywZR8/edit?usp=sharing)
-   [Compositor Thread Architecture](https://www.chromium.org/developers/design-documents/compositor-thread-architecture)
-   [Rendering Benchmarks](https://www.chromium.org/developers/design-documents/rendering-benchmarks)
-   [Impl-side Painting](https://www.chromium.org/developers/design-documents/impl-side-painting)
-   [Video Playback and Compositor](http://www.chromium.org/developers/design-documents/video-playback-and-compositor)
-   [ANGLE architecture presentation](https://docs.google.com/presentation/d/1CucIsdGVDmdTWRUbg68IxLE5jXwCb2y1E9YVhQo0thg/pub?start=false&loop=false)

**Network stack**

-   [Overview](https://www.chromium.org/developers/design-documents/network-stack)
-   [Network Stack Objectives](https://www.chromium.org/developers/design-documents/network-stack/network-stack-objectives)
-   [Crypto](https://www.chromium.org/developers/design-documents/crypto)
-   [Disk Cache](https://www.chromium.org/developers/design-documents/network-stack/disk-cache)
-   [HTTP Cache](https://www.chromium.org/developers/design-documents/network-stack/http-cache)
-   [Out of Process Proxy Resolving Draft [unimplemented]](https://www.chromium.org/system/errors/NodeNotFound)
-   [Proxy Settings and Fallback](https://www.chromium.org/developers/design-documents/network-stack/proxy-settings-fallback)
-   [Debugging network proxy problems](https://www.chromium.org/developers/design-documents/network-stack/debugging-net-proxy)
-   [HTTP Authentication](https://www.chromium.org/developers/design-documents/http-authentication)
-   [View network internals tool](https://www.chromium.org/system/errors/NodeNotFound)
-   [Make the web faster with SPDY](http://www.chromium.org/spdy/)  pages
-   [Make the web even faster with QUIC](https://www.chromium.org/quic)  pages
-   [Cookie storage and retrieval](https://www.chromium.org/developers/design-documents/network-stack/cookiemonster)

**Security**

-   [Security Overview](https://www.chromium.org/chromium-os/chromiumos-design-docs/security-overview)
-   [Protecting Cached User Data](https://www.chromium.org/chromium-os/chromiumos-design-docs/protecting-cached-user-data)
-   [System Hardening](https://www.chromium.org/chromium-os/chromiumos-design-docs/system-hardening)
-   [Chaps Technical Design](https://www.chromium.org/developers/design-documents/chaps-technical-design)
-   [TPM Usage](https://www.chromium.org/developers/design-documents/tpm-usage)
-   [Per-page Suborigins](https://www.chromium.org/developers/design-documents/per-page-suborigins)
-   [Encrypted Partition Recovery](https://www.chromium.org/developers/design-documents/encrypted-partition-recovery)
-   [XSS Auditor](https://www.chromium.org/developers/design-documents/xss-auditor)

## Input

-   See  [chromium input](https://www.chromium.org/teams/input-dev)  for design docs and other resources.

## Rendering

-   [Multi-column layout](https://www.chromium.org/developers/design-documents/multi-column-layout)
-   [Style Invalidation in Blink](https://docs.google.com/document/d/1vEW86DaeVs4uQzNFI5R-_xS9TcS1Cs_EUsHRSgCHGu8/)
-   [Blink Coordinate Spaces](https://www.chromium.org/developers/design-documents/blink-coordinate-spaces)

## Building

-   [IDL build](https://www.chromium.org/developers/design-documents/idl-build)
-   [IDL compiler](https://chromium.googlesource.com/chromium/src/+/HEAD/third_party/blink/renderer/bindings/IDLCompiler.md)

See also the documentation for  [GYP](https://code.google.com/p/gyp/w/list), which is the build script generation tool.

## Testing

-   [Layout test results dashboard](https://www.chromium.org/developers/design-documents/layout-tests-results-dashboard)
-   [Generic theme for Test Shell](https://www.chromium.org/developers/design-documents/generic-theme-for-test-shell)
-   [Moving LayoutTests fully upstream](https://www.chromium.org/system/errors/NodeNotFound)

## Feature-Specific

-   [about:conflicts](https://www.chromium.org/developers/design-documents/about-conflicts)
-   [Accessibility](https://www.chromium.org/developers/design-documents/accessibility): An outline of current (and coming) accessibility support.
-   [Auto-Throttled Screen Capture and Mirroring](https://www.chromium.org/developers/design-documents/auto-throttled-screen-capture-and-mirroring)
-   [Browser Window](https://www.chromium.org/developers/design-documents/browser-window)
-   [Chromium Print Proxy](http://www.chromium.org/developers/design-documents/google-cloud-print-proxy-design): Enables a cloud print service for legacy printers and future cloud-aware printers.
-   [Constrained Popup Windows](https://www.chromium.org/developers/design-documents/constrained-popup-windows)
-   [Desktop Notifications](https://www.chromium.org/developers/design-documents/desktop-notifications)
-   [DirectWrite Font Cache for Chrome on Windows](https://www.chromium.org/developers/design-documents/directwrite-font-cache)
-   [DNS Prefetching](https://www.chromium.org/developers/design-documents/dns-prefetching): Reducing perceived latency by resolving domain names before a user tries to follow a link
-   [Download Page (Material Design Refresh)](https://docs.google.com/document/d/1XkUDOc6085tir4D5yYEyjL2GsIGBslJBHXiNQDzJawI/edit)
-   [Embedding Flash Fullscreen in the Browser Window](https://www.chromium.org/developers/design-documents/embedding-flash-fullscreen-in-the-browser-window)
-   [Extensions](https://www.chromium.org/developers/design-documents/extensions): Design documents and proposed APIs.
-   [Find Bar](https://www.chromium.org/developers/design-documents/find-bar)
-   [Form Autofill](https://www.chromium.org/developers/design-documents/form-autofill): A feature to automatically fill out an html form with appropriate data.
-   [Geolocation](https://docs.google.com/a/chromium.org/document/pub?id=13rAaY1dG0nrlKpfy7Txlec4U6dsX3PE9aXHkvE37JZo): Adding support for  [W3C Geolocation API](http://www.w3.org/TR/geolocation-API/)  using native WebKit bindings.
-   [Generic-Sensor](https://www.chromium.org/developers/design-documents/generic-sensor)  : Access sensor data
-   [IDN in Google Chrome](https://www.chromium.org/developers/design-documents/idn-in-google-chrome)
-   [IndexedDB](https://www.chromium.org/developers/design-documents/indexeddb)  (early draft)
-   [Info Bars](https://www.chromium.org/developers/design-documents/info-bars)
-   [Installer](https://www.chromium.org/developers/installer): Registry entries and shortcuts
-   [Instant](https://www.chromium.org/developers/design-documents/instant)
-   [Isolated Sites](https://www.chromium.org/developers/design-documents/isolated-sites)
-   [Linux Resources and Localized Strings](https://www.chromium.org/developers/design-documents/linuxresourcesandlocalizedstrings): Loading data resources and localized strings on Linux.
-   [Media Router & Web Presentation API](https://www.chromium.org/developers/design-documents/media-router)
-   [Memory Usage Backgrounder:](https://www.chromium.org/developers/memory-usage-backgrounder) Some information on how we measure memory in Chromium.
-   [Mouse Lock](https://www.chromium.org/developers/design-documents/mouse-lock)
-   Omnibox Autocomplete: While typing into the omnibox, Chromium searches for and suggests possible completions.
    -   [HistoryQuickProvider](https://www.chromium.org/omnibox-history-provider): Suggests completions from the user's historical site visits.
-   [Omnibox/IME Coordination](https://www.chromium.org/developers/design-documents/omnibox-ime-coordination)
-   [Omnibox Prefetch for Default Search Engines](https://www.chromium.org/developers/design-documents/omnibox-prefetch-for-default-search-engines)
-   [Ozone Porting Abstraction](https://chromium.googlesource.com/chromium/src/+/HEAD/docs/ozone_overview.md)
-   [Password Form Styles that Chromium Understands](https://www.chromium.org/developers/design-documents/form-styles-that-chromium-understands)
-   [Password Generation](https://www.chromium.org/developers/design-documents/password-generation)
-   [Pepper plugin implementation](https://www.chromium.org/developers/design-documents/pepper-plugin-implementation)
-   [Plugin Power Saver](https://docs.google.com/document/d/1r4xFSsR4gtjBf1gOP4zHGWIFBV7WWZMgCiAHeepoHVw/edit?usp=sharing)
-   [Preferences](https://www.chromium.org/developers/design-documents/preferences)
-   [Prerender](https://www.chromium.org/developers/design-documents/prerender)
-   [Print Preview](https://www.chromium.org/developers/design-documents/print-preview)
-   [Printing](https://www.chromium.org/developers/design-documents/printing)
-   [Rect-based event targeting in views](https://www.chromium.org/developers/design-documents/views-rect-based-targeting): Making it easier to target views elements with touch.
-   [Replace the modal cookie prompt](https://www.chromium.org/developers/design-documents/cookie-prompt-replacement)
-   [SafeSearch](https://www.chromium.org/developers/design-documents/safesearch)
-   [Sane Time](https://www.chromium.org/developers/design-documents/sane-time): Determining an accurate time in Chrome
-   [Secure Web Proxy](https://www.chromium.org/developers/design-documents/secure-web-proxy)
-   [Service Processes](https://www.chromium.org/developers/design-documents/service-processes)
-   [Site Engagement](https://www.chromium.org/developers/design-documents/site-engagement): Tracking user engagement with sites they use.
-   [Site Isolation](https://www.chromium.org/developers/design-documents/site-isolation): In-progress effort to improve Chromium's process model for security between web sites.
-   [Software Updates: Courgette](https://www.chromium.org/developers/design-documents/software-updates-courgette)
-   [Sync](https://www.chromium.org/developers/design-documents/sync)
-   [Tab Helpers](https://www.chromium.org/system/errors/NodeNotFound)
-   [Tab to search](https://www.chromium.org/tab-to-search): How to have the Omnibox automatically provide tab to search for your site.
-   [Tabtastic2 Requirements](https://www.chromium.org/developers/design-documents/tabtastic-2-requirements)
-   [Temporary downloads](https://www.chromium.org/system/errors/NodeNotFound)
-   [TimeTicks](https://docs.google.com/document/d/1ypBZPZnshJ612FWAjkkpSBQiII7vYEEDsRkDOkVjQFw/edit?usp=sharing): How our monotonic timer, TimeTicks, works on different OSes
-   [UI Mirroring Infrastructure](https://www.chromium.org/developers/design-documents/ui-mirroring-infrastructure): Describes the UI framework in ChromeViews that allows mirroring the browser UI in RTL locales such as Hebrew and Arabic.
-   [UI Localization](https://www.chromium.org/developers/design-documents/ui-localization): Describes how localized strings get added to Chromium.
-   [User scripts](https://www.chromium.org/developers/design-documents/user-scripts): Information on Chromium's support for user scripts.
-   [Video](https://www.chromium.org/developers/design-documents/video)
-   WebSocket: A message-oriented protocol which provides bidirectional TCP/IP-like communication between browsers and servers.
    -   [Design doc](https://docs.google.com/document/d/11n3hpwb9lD9YVqnjX3OwzE_jHgTmKIqd6GvXE9bDGUg/edit): mostly still relevant but some parts have been revised. In particular, mojo is used for the communication between Blink and //content now.
    -   [WebSocket handshake](https://docs.google.com/document/d/1r7dQDA9AQBD_kOk-z-yMi0WgLQZ-5m7psMO5pYLFUL8/edit): The HTTP-like exchange before switching to WebSocket-style framing in more detail.
    -   [Obsolete design doc](https://docs.google.com/document/d/1_R6YjCIrm4kikJ3YeapcOU2Keqr3lVUPd-OeaIJ93qQ/pub): WebSocket code has been drastically refactored. Most of the code described in this doc is gone. It is mostly only of historical interest.
-   [Web MIDI](https://www.chromium.org/developers/design-documents/web-midi)
-   [WebNavigation API internals](https://www.chromium.org/developers/design-documents/webnavigation-api-internals)
-   [Web NFC](https://www.chromium.org/developers/design-documents/web-nfc)

## OS-Specific

-   **Android**
    -   [Java Resources on Android](https://www.chromium.org/developers/design-documents/java-resources-on-android)
    -   [JNI Bindings](https://www.chromium.org/developers/design-documents/android-jni)
    -   [Android WebView docs](https://www.chromium.org/developers/androidwebview)
    -   [Child process importance](https://docs.google.com/document/d/1PTI3udZ6TI-R0MlSsl2aflxRcnZ5blnkGGHE5JRI_Z8/edit?usp=sharing)
-   **Chrome OS**
    -   See the  [Chrome OS design documents](https://www.chromium.org/chromium-os/chromiumos-design-docs)  section.
-   **Mac OS X**
    -   [AppleScript Support](https://www.chromium.org/developers/design-documents/applescript)
    -   [BrowserWindowController Object Ownership](https://www.chromium.org/system/errors/NodeNotFound)
    -   [Confirm to Quit](https://www.chromium.org/developers/design-documents/confirm-to-quit-experiment)
    -   [Mac App Mode](https://www.chromium.org/developers/design-documents/appmode-mac)  (Draft)
    -   [Mac Fullscreen Mode](https://www.chromium.org/developers/design-documents/fullscreen-mac)  (Draft)
    -   [Mac NPAPI Plugin Hosting](https://www.chromium.org/developers/design-documents/mac-plugins)
    -   [Mac specific notes on UI Localization](https://www.chromium.org/developers/design-documents/ui-localization/mac-notes)
    -   [Menus, Hotkeys, & Command Dispatch](https://www.chromium.org/developers/design-documents/command-dispatch-mac)
    -   [Notes from meeting on IOSurface usage and semantics](https://www.chromium.org/developers/design-documents/iosurface-meeting-notes)
    -   [OS X Interprocess Communication (Obsolete)](https://www.chromium.org/developers/design-documents/os-x-interprocess-communication)
    -   [Password Manager/Keychain Integration](https://www.chromium.org/developers/design-documents/os-x-password-manager-keychain-integration)
    -   [Sandboxing Design](https://www.chromium.org/developers/design-documents/sandbox/osx-sandboxing-design)
    -   [Tab Strip Design (Includes tab layout and tab dragging)](https://www.chromium.org/developers/design-documents/tab-strip-mac)
    -   [Wrench Menu Buttons](https://www.chromium.org/developers/design-documents/wrench-menu-mac)
-   **iOS**
    -   [WKWebView Problems Solved by web// layer](https://docs.google.com/document/d/1qgFVIhUdQf_RxhzF-sAsETvo7CNqSLmzlz5YY7rfRe0/edit)

## Other

-   [64-bit Support](https://www.chromium.org/developers/design-documents/64-bit-support)
-   [Layered Components](https://www.chromium.org/developers/design-documents/layered-components-design)
-   [Closure Compiling Chrome Code](https://docs.google.com/a/chromium.org/document/d/1Ee9ggmp6U-lM-w9WmxN5cSLkK9B5YAq14939Woo-JY0/edit#heading=h.34wlp9l5bd5f)
-   [content module](https://www.chromium.org/developers/content-module)  /  [content API](https://www.chromium.org/developers/content-module/content-api)
-   [Design docs that still need to be written](http://code.google.com/p/chromium/wiki/DesignDocsToWrite)  (wiki)
-   [In progress refactoring of key browser-process architecture for porting](https://www.chromium.org/developers/Web-page-views)
-   [Network Experiments](https://www.chromium.org/network-speed-experiments)
-   [Transitioning InlineBoxes from floats to LayoutUnits](https://docs.google.com/a/chromium.org/document/d/1fro9Drq78rYBwr6K9CPK-y0TDSVxlBuXl6A54XnKAyE/edit)
