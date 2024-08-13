---
layout: post
title: For Developers
---

#### _See also: docs in the source code - [https://chromium.googlesource.com/chromium/src/+/HEAD/docs/README.md](https://chromium.googlesource.com/chromium/src/+/HEAD/docs/README.md)

### Start here

-   [Get the Code: Checkout, Build, & Run](https://www.chromium.org/developers/how-tos/get-the-code)
-   [Contributing code](https://chromium.googlesource.com/chromium/src/+/HEAD/docs/contributing.md)

### How-tos

#### _Note that some of these guides are out-of-date.

#### Getting the Code

-   [Quick reference](https://www.chromium.org/developers/quick-reference)  of common development commands.
-   Look at our  [Git Cookbook](https://chromium.googlesource.com/chromium/src/+/HEAD/docs/git_cookbook.md)  for a helpful walk-through, or the  [Fast Intro to Git Internals](https://www.chromium.org/developers/fast-intro-to-git-internals)  for a background intro to git.
-   [Changelogs for Chromium and Blink](https://www.chromium.org/developers/change-logs).

#### Development Guides

-   Debugging on  [Windows](https://www.chromium.org/developers/how-tos/debugging-on-windows),  [Mac OS X](https://www.chromium.org/developers/how-tos/debugging-on-os-x),  [Linux](https://chromium.googlesource.com/chromium/src/+/HEAD/docs/linux/debugging.md)  and  [Android](https://chromium.googlesource.com/chromium/src/+/HEAD/docs/android_debugging_instructions.md).
-   [Threading](https://chromium.googlesource.com/chromium/src/+/HEAD/docs/threading_and_tasks.md)
-   [Subtle Threading Bugs and How to Avoid Them](https://www.chromium.org/developers/design-documents/threading/suble-threading-bugs-and-patterns-to-avoid-them)
-   [Visual Studio tricks](https://www.chromium.org/developers/how-tos/visualstudio-tricks)
-   [Debugging GPU related code](https://www.chromium.org/developers/how-tos/debugging-gpu-related-code)
-   [How to set up Visual Studio debugger visualizers](https://www.chromium.org/developers/how-tos/how-to-set-up-visual-studio-debugger-visualizers)  to make the watch window more convenient
-   [Linux Development](https://chromium.googlesource.com/chromium/src/+/HEAD/docs/linux_development.md)  tips and porting guide
-   [Mac Development](https://www.chromium.org/developers/how-tos/mac-development)
-   [Generated files](https://www.chromium.org/developers/generated-files)
-   [Chromoting (Chrome Remote Desktop) compilation](https://chromium.googlesource.com/chromium/src/+/HEAD/docs/chromoting_build_instructions.md)
-   [Editing dictionaries](https://www.chromium.org/developers/how-tos/editing-the-spell-checking-dictionaries)
-   Editors Guides
    -   [Atom](https://www.chromium.org/developers/using-atom-as-your-ide)
    -   [Eclipse](https://www.chromium.org/developers/using-eclipse-with-chromium)
    -   [Emacs cscope](https://www.chromium.org/developers/how-tos/cscope-emacs-example-linux-setup)
    -   [QtCreator](https://chromium.googlesource.com/chromium/src/+/HEAD/docs/qtcreator.md)
    -   [SlickEdit](https://www.chromium.org/developers/slickedit-editor-notes)
    -   [Sublime Text](https://chromium.googlesource.com/chromium/src/+/HEAD/docs/sublime_ide.md)
    -   [Visual Studio Code](https://chromium.googlesource.com/chromium/src/+/HEAD/docs/vscode.md)
-   [Learning your way around the code](/blog/2024/08/13/learning-your-way-around-the-code)
-   [Guide to Important Libraries, Abstractions, and Data Structures](https://www.chromium.org/developers/libraries-guide)
    -   [Important Abstractions and Data Structures](https://www.chromium.org/developers/coding-style/important-abstractions-and-data-structures)
    -   [Smart Pointer Guidelines](https://www.chromium.org/developers/smart-pointer-guidelines)
    -   [String usage](https://www.chromium.org/developers/chromium-string-usage)
-   [//base Newsletters](https://www.chromium.org/developers/base-newsletters)
-   [Android WebView](https://www.chromium.org/developers/androidwebview)
-   [GitHub Collaboration](https://www.chromium.org/developers/github-collaboration)

See also: All  [How-tos](https://www.chromium.org/developers/how-tos).

### [Blink](https://www.chromium.org/developers/#blink)

-   [Blink Project](https://www.chromium.org/blink)
    -   [DOM Team](https://www.chromium.org/teams/dom-team)
    -   [Binding Team](https://www.chromium.org/teams/binding-team)
    -   [Layout Team](https://www.chromium.org/teams/layout-team)
    -   [Memory Team](https://www.chromium.org/blink/memory-team)
    -   [Paint Team](https://www.chromium.org/teams/paint-team)
    -   [Style Team](https://www.chromium.org/teams/style-team)
    -   [Animation Team](https://www.chromium.org/teams/animations)
    -   [Input Team](https://www.chromium.org/teams/input-dev)
-   [Running and Debugging the Blink web tests (pka layout tests)](https://chromium.googlesource.com/chromium/src/+/HEAD/docs/testing/web_tests.md)
-   [Blink Sheriffing](https://www.chromium.org/blink/sheriffing)
-   [Web IDL interfaces](https://www.chromium.org/developers/web-idl-interfaces)
-   [Class Diagram: Blink Core to Chrome Browser](https://www.chromium.org/developers/class-diagram-webkit-webcore-to-chrome-browser)
-   [Rebaselining Tool](http://code.google.com/p/chromium/wiki/RebaseliningTool)
-   [How repaint works](https://docs.google.com/a/chromium.org/document/d/1jxbw-g65ox8BVtPUZajcTvzqNcm5fFnxdi4wbKq-QlY/edit)
-   [Phases of Rendering](https://docs.google.com/a/chromium.org/document/d/1UkxPz9GDQXLBZcbw5OeUQpk1VIq_BKhm6BGvWJ5mKdU/edit)
-   [Web Platform Tests](https://chromium.googlesource.com/chromium/src/+/HEAD/docs/testing/web_platform_tests.md)
-   [Baseline computation and some line layout docs](https://docs.google.com/a/chromium.org/document/d/1OP49xbB-D7A0qKNAwFTOfbDL-1dYxu74Jp38ZKAS6kk/edit)
-   [Fast Text Autosizer](http://tinyurl.com/fasttextautosizer)

### Testing and Infrastructure

-   [Tests](https://www.chromium.org/developers/testing)
    -   [Tour of the Chromium Continuous Integration Console](https://www.chromium.org/developers/testing/chromium-build-infrastructure/tour-of-the-chromium-buildbot)
    -   [WebKit Layout Tests](https://www.chromium.org/developers/testing/webkit-layout-tests)
    -   [Flakiness Dashboard HOWTO](https://www.chromium.org/developers/testing/flakiness-dashboard)
    -   [Frame Rate Test](https://www.chromium.org/developers/testing/frame-rate-test)
    -   [GPU Testing](https://www.chromium.org/developers/testing/gpu-testing)
    -   [GPU Recipe](https://www.chromium.org/system/errors/NodeNotFound)
    -   [WebGL Conformance Tests](https://www.chromium.org/developers/testing/webgl-conformance-tests)
    -   [Blink, Testing, and the W3C](https://www.chromium.org/blink/blink-testing-and-the-w3c)
    -   [The JSON Results format](https://www.chromium.org/developers/the-json-test-results-format)
-   [Browser Tests](https://www.chromium.org/developers/testing/browser-tests)
-   [Handling a failing test](https://www.chromium.org/developers/tree-sheriffs/handling-a-failing-test)
-   [Running Chrome tests](http://code.google.com/p/chromium/wiki/RunningChromeUITests)
-   [Reliability Tests](https://www.chromium.org/system/errors/NodeNotFound)
-   [Using Valgrind](https://www.chromium.org/system/errors/NodeNotFound)
-   [Page Heap for Chrome](https://www.chromium.org/developers/testing/page-heap-for-chrome)
-   [Establishing Blame for Memory usage via Memory_Watcher](https://www.chromium.org/developers/memory_watcher)
-   [GPU Rendering Benchmarks](https://www.chromium.org/developers/design-documents/rendering-benchmarks)
-   [Infra documentation](https://chromium.googlesource.com/infra/infra/+/HEAD/doc/index.md)
-   [Contacting a Trooper](https://chromium.googlesource.com/infra/infra/+/HEAD/doc/users/contacting_troopers.md)

### Performance

-   [Adding Performance Tests](https://www.chromium.org/developers/testing/adding-performance-tests)
-   [Telemetry: Performance testing framework](https://www.chromium.org/developers/telemetry)
    -   [Cluster Telemetry](https://sites.google.com/corp/google.com/cluster-telemetry/home): Run benchmarks against the top 10k web pages (Googlers-only)
-   [Memory](https://www.chromium.org/Home/memory)
-   [Profiling Tools](https://www.chromium.org/developers/profiling-chromium-and-webkit):
    -   [Thread and Task Profiling and Tracking](https://www.chromium.org/developers/threaded-task-tracking)  (about:profiler) Also allows diagnosing per-task heap usage and churn if Chrome runs with "--enable-heap-profiling=task-profiler".
    -   [Tracing tool](https://www.chromium.org/developers/how-tos/trace-event-profiling-tool)  (about:tracing)
    -   [Deep Memory Profiler](https://www.chromium.org/developers/deep-memory-profiler)
    -   Investigating  [Windows Binary Sizes](https://www.chromium.org/developers/windows-binary-sizes)
    -   Windows-specific issues can be profiled with  [UIforETW](https://randomascii.wordpress.com/2015/09/01/xperf-basics-recording-a-trace-the-ultimate-easy-way/)
    -   [Leak Detection](https://www.chromium.org/developers/leak-detection)
-   [Perf Sheriffing](https://www.chromium.org/developers/tree-sheriffs/perf-sheriffs)

### Sync

-   [Sync](https://www.chromium.org/developers/design-documents/sync)

### Diagnostics

-   [Diagnostics](https://www.chromium.org/developers/diagnostics)

### Documentation hosted in / generated by source code

-   [depot_tools](http://commondatastorage.googleapis.com/chrome-infra-docs/flat/depot_tools/docs/html/depot_tools.html)
-   [C++ use in Chromium](http://chromium-cpp.appspot.com/)
-   [GN](https://gn.googlesource.com/gn/): Meta-build system that generates NinjaBuild files; Intended to be GYP replacement.
-   [MB](https://chromium.googlesource.com/chromium/src/+/HEAD/tools/mb#): Meta-build wrapper around both GN and GYP.
-   [Chrome Infra](https://chromium.googlesource.com/infra/infra/+/HEAD/doc/index.md)

### Practices

-   [Core Product Principles](https://www.chromium.org/developers/core-principles)
    -   [No Hidden Preferences](https://www.chromium.org/developers/core-principles/no-hidden-preferences)
-   [Contributing code](https://chromium.googlesource.com/chromium/src/+/main/docs/contributing.md)
    -   [Coding style](https://www.chromium.org/developers/coding-style)
        -   [C++ Dos and Don'ts](https://www.chromium.org/developers/coding-style/cpp-dos-and-donts)
        -   [Cocoa Dos and Don'ts](https://www.chromium.org/developers/coding-style/cocoa-dos-and-donts)
        -   [Web Development Style Guide](https://www.chromium.org/developers/web-development-style-guide)
        -   [Java](https://www.chromium.org/developers/coding-style/java)
        -   [Jinja](https://www.chromium.org/developers/jinja)
    -   [Becoming a Committer](https://www.chromium.org/getting-involved/become-a-committer)
    -   [Gerrit Guide (Googler/Non-Googler)](https://www.chromium.org/developers/gerrit-guide)
    -   [Create experimental branches to work on](https://www.chromium.org/developers/experimental-branches)
    -   [Committer's responsibility](https://www.chromium.org/developers/committers-responsibility)
    -   [OWNERS Files](https://chromium.googlesource.com/chromium/src/+/HEAD/docs/code_reviews.md)
    -   [Try server usage](https://chromium.googlesource.com/chromium/src/+/HEAD/docs/infra/trybot_usage.md)
    -   [Commit queue](https://www.chromium.org/developers/testing/commit-queue)
    -   [Tips for minimizing code review lag across timezones](https://www.chromium.org/developers/contributing-code/minimizing-review-lag-across-time-zones)
-   [Starting to work on a new web platform feature](https://www.chromium.org/blink/launching-features)
-   [Filing bugs](https://www.chromium.org/for-testers/bug-reporting-guidelines)
    -   [Severity Guidelines for Security Issues](https://www.chromium.org/developers/severity-guidelines)
-   [Network bug triage](https://www.chromium.org/developers/design-documents/network-stack/network-bug-triage)
-   [GPU bug triage](https://docs.google.com/document/d/1Sr1rUl2a5_RBCkLtxfx4qE-xUIJfYraISdSz_I6Ft38/edit#heading=h.vo10gbuchnj4)
-   [Ticket milestone punting](https://www.chromium.org/system/errors/NodeNotFound)
-   [Tree Sheriffs](https://www.chromium.org/developers/tree-sheriffs)
-   [Useful extensions for developers](https://www.chromium.org/developers/useful-extensions)
-   [Adding 3rd-party libraries](https://www.chromium.org/developers/adding-3rd-party-libraries)
-   [Shipping changes that are enterprise-friendly](https://www.chromium.org/developers/enterprise-changes)

Design documents

-   [Getting around the source code directories](/blog/2024/08/13/getting-around-the-chromium-source-code)
-   [Tech Talks: Videos & Presentations](https://www.chromium.org/developers/tech-talk-videos)
-   [Engineering design docs](https://www.chromium.org/developers/design-documents)
-   [User experience design docs](https://www.chromium.org/user-experience)
-   _Sharing design documents on Google drive: share on Chromium domain_  If on private domain, share with self@chromium.org, then log in with self@chromium.org, click "Shared with Me", right-click "Make a copy", then set the permissions: "Public on the web" or "Anyone with the link", generally "Can comment". It a good idea to then mark your local copy  _(PRIVATE)_  and only edit the public copy.

### Communication

-   [General discussion groups](https://www.chromium.org/developers/discussion-groups)
-   [Technical discussion groups](https://www.chromium.org/developers/technical-discussion-groups)
-   [Slack](https://www.chromium.org/developers/slack)
-   [IRC](https://www.chromium.org/developers/irc)
-   [Development calendar and release info](https://www.chromium.org/developers/calendar)
-   [Common Terms & Techno Babble](http://code.google.com/p/chromium/wiki/Glossary)
-   [Public calendar for meetings discussing new ideas](https://www.chromium.org/developers/public-calendar-for-meetings-discussing-new-ideas)
-   Questions or problems with your Chromium account? Email  [accounts@chromium.org](mailto:accounts@chromium.org).

### Status

-   [Status Update Email Best Practices](https://www.chromium.org/developers/status-update-email-best-practices)
-   [chromestatus.com](http://chromestatus.com/)

Usage statistics

-   [MD5 certificate statistics](https://www.chromium.org/developers/md5-certificate-statistics)

### Graphics

-   [Graphics overview and design docs](https://www.chromium.org/developers/design-documents/chromium-graphics)

### External links

-   Waterfalls
    -   [Continuous build](http://build.chromium.org/p/chromium/waterfall)  ([console](http://build.chromium.org/p/chromium/console))
    -   [Memory](http://build.chromium.org/p/chromium.memory/waterfall)  ([console](http://build.chromium.org/p/chromium.memory/console))
    -   [For Your Information build](http://build.chromium.org/p/chromium.fyi/waterfall)  ([console](http://build.chromium.org/p/chromium.fyi/console))
    -   [Try Server](http://build.chromium.org/p/tryserver.chromium.linux/waterfall)
-   [Build Log Archives (chromium-build-logs)](http://chromium-build-logs.appspot.com/)
-   [Bug tracker](https://bugs.chromium.org/p/chromium/issues/list)
-   [Code review tool](https://chromium-review.googlesource.com/)
-   [Viewing the source](https://chromium.googlesource.com/chromium/src/)
-   [Glossary](http://code.google.com/p/chromium/wiki/Glossary)  (acronyms, abbreviations, jargon, and technobabble)
