---
title: clang命令
date: 2016-09-16 20:40:04
category: ios
tags:
---

### Wiki

> Clang /ˈklæŋ/ is a compiler front end for the programming languages C, C++, Objective-C, Objective-C++, OpenMP, OpenCL, and CUDA. It uses LLVM as its back end and has been part of the LLVM release cycle since LLVM 2.6.

> It is designed to be able to replace the full GNU Compiler Collection (GCC). Its contributors include Apple, Microsoft, Google, ARM, Sony, Intel and Advanced Micro Devices (AMD). It is open-source software, with source code released under the University of Illinois/NCSA License, a permissive free software licence.

> [https://en.wikipedia.org/wiki/Clang](https://en.wikipedia.org/wiki/Clang)

#### LLVM

**LLVM** 是 `Low Level Virtual Machine` （底层虚拟机）的简称，这个库提供了与编译器相关的支持，能够进行程序语言的编译期优化、链接优化、在线编译优化、代码生成。可以作为多种语言编译器的后台来使用。


### clang -rewrite-objc
`clang -rewrite-objc` 的作用是把 oc 代码转写成 c/c++ 代码，推测 OC 底层的实现原理。

#### 使用方法

```
clang -rewrite-objc <Objective-C source file>
```

比如，在 OC 中，方法调用又称为消息发送，但消息发送是怎么实现的呢？

```Objective-C
- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.
    
    Person *p = [Person new];
    [p foo];
}
```

在终端输入

```bash
clang -rewrite-objc ViewController.m
```

在生成的 `ViewController.cpp` 文件中，可以找到 ViewDidLoad 方法改写后的实现：

```
static void _I_ViewController_viewDidLoad(ViewController * self, SEL _cmd) {
    ((void (*)(__rw_objc_super *, SEL))(void *)objc_msgSendSuper)((__rw_objc_super){(id)self, (id)class_getSuperclass(objc_getClass("ViewController"))}, sel_registerName("viewDidLoad"));


    Person *p = ((Person *(*)(id, SEL))(void *)objc_msgSend)((id)objc_getClass("Person"), sel_registerName("new"));
    ((void (*)(id, SEL))(void *)objc_msgSend)((id)p, sel_registerName("foo"));
}
```

也就是说 `[p foo]` 在动态编译的时候，会被转意为： `objc_msgSend(p, sel_registerName("foo")); `。


### 指定SDK

OC 代码转写成 C/C++ 代码时，在模拟器和真机上，有时候是有区别的。如果需要指定 SDK ，那么要结合`xcrun`命令。

真机：

```
xcrun -sdk iphoneos[ver] clang -rewrite-objc <inputs>
```

模拟器：
```
xcrun -sdk iphonesimulator[ver] clang -rewrite-objc <inputs>
```

可通过 `xcodebuild -showsdks` 来查看机器上的 SDK

```
OS X SDKs:
	OS X 10.11                    	-sdk macosx10.11

iOS SDKs:
	iOS 9.3                       	-sdk iphoneos9.3

iOS Simulator SDKs:
	Simulator - iOS 9.3           	-sdk iphonesimulator9.3

tvOS SDKs:
	tvOS 9.2                      	-sdk appletvos9.2

tvOS Simulator SDKs:
	Simulator - tvOS 9.2          	-sdk appletvsimulator9.2

watchOS SDKs:
	watchOS 2.2                   	-sdk watchos2.2

watchOS Simulator SDKs:
	Simulator - watchOS 2.2       	-sdk watchsimulator2.2

```


### 指定 framework
如果使用了iOS frameworks，（如UIKit） 或者 第三方 SDK （如听云），执行`clang -rewrite-objc`的时候会提示错误:
```
ViewController.h:9:9: fatal error: 'UIKit/UIKit.h' file not found
#import <UIKit/UIKit.h>
        ^
1 error generated.
```

这是因为没有引入 framework，clang 不知道去哪里找，需要用 -F 选项指定 要引入的 framework。

```
clang -rewrite-objc -F UIKit <inputs>
```

**如果指定了 sdk，则不需要指定 iOS 内部 frameworks**

```


之前在读 Apple 开源的 runtime 源码的时候，经常使用这个命令，就起了个简短的别名，方便使用：

1. 在~/.bashrc 或者 ~/.zshrc（for zsh 用户）中添加一行 `alias deoc="xcrun -sdk iphonesimulator9.3 clang -rewrite-objc"。（请先使用 `xcodebuild -showsdks` 查看自己电脑上的 sdk 做相应修改）
2. 保存，退出；打开一个新窗口，或者在当前窗口运行 `source ~/.bashrc（for bash users）` 或者 `source ~/.zshrc (for zsh users)`
3. 现在开始，使用 `deoc` 来代替 `xcrun -sdk iphonesimulator9.3 clang -rewrite-objc` ，后面接 OC 源文件 （和 -F framework，如果需要引入第三方 sdk）。


关于`clang`的更多使用方法，请参考:
1. [objc 中国-编译器](https://objccn.io/issue-6-2/)
2. man clang

Refrences:
1. [clang -rewrite-objc的使用点滴](http://blog.tingyun.com/web/article/detail/845)
