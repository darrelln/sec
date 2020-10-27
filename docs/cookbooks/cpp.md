---
layout: default
title: C++
parent: COOKBOOKS
has_children: false
has_toc: false
---

{% include toc.html %}

## Standalone C++ Apps
Need to use static linking to prevent the target needing the runtime installed:

![C++ runtime missing error](images/cpp/cpp-runtime-needed.png)

To overcome this, statically link the application. In VisualStudio, right-clink the project and go to `Configuration Properties > Code Generation > C/C++` and change `Runtime Library` to `/MT` for release, and `/MTd` for debug:

![VisualStudio Static Linking](images/cpp/vs-static-linking.png)

The app should now run self-contained on a target that does not have the runtime installed.

