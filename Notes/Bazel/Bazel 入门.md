> 不太会用啊，感觉以后会忘
> 那随便写写吧

# 一、概览

Bazel 中有几个重要的概念
```
WORKSPACE文件
BUILD文件
package
target
rule
```
## 1. WORKSPACE

用于标识整个项目的根目录与声明外部库，在项目根目录搞一个即可

## 2. BUILD

用于标识一个包 (package)

BUILD 文件内会标识若干个目标 (target)

形如：

```bazel
cc_library(
	name = "xxlib",
	...
)
```

这样就算定义了一个 Cpp 库的 target
日后需要依赖该库的时候，可以这样来访问：

```bazel
cc_library(
	name = "app",
	deps = [
		PATH:xxlib,
	],
)
```

注意这里的 `PATH` 指的是 package 的路径，而 xxlib 则是 target 名

所以依赖一个 target 需要写 package:target

## 3. Rule

形如：

```bazel
cc_library
cc_binary
cc_test
filegroup
...
```

的东西都叫规则 (rule)，用来描述 target 的类型

# 二、示例

比如一个这样的 BUILD 文件

```bazel
cc_binary(
    name = "server",
    srcs = [
        "ServerConfig.cpp",
        "Server.cpp",
        "main.cpp",
        ...
    ],
    deps = [
        "//comm/utils:threadpool",
        "//comm/utils:iostream",
        "//comm/utils:mq",
    ],
    copts = [
	    "-std=c++11",
		"-DDEBUG",
	],
	linkopts = [
	],
)
```

其意为，声明一个名为 server 的二进制 target，依赖 comm/utils/ 下的 threadpool、iostream、mq 几个 target，编译选项为 -std=c++11 -DDEBUG，不设置链接选项

# 三、新版 Bazel 怎么用

新版 Bazel 去掉了 WORKSPACE，取而代之的是 MODULE.bazel 文件

形如

```bazel
module(
    name = "hello",
    version = "0.1.0",
)

bazel_dep(
    name = "rules_cc",
    version = "0.2.26",
)
```

意为声明了名为 hello 的模块，版本为 0.1.0
其中依赖了 rules_cc 这个外部库（也就是以前的 cc_library、cc_binary 这些规则的来源）

BUILD 文件则需要显式加载默认规则

```bazel
load(
    "@rules_cc//cc:defs.bzl",
    "cc_binary",
    "cc_library",
)

cc_library(
    name = "greeter",
    srcs = [
        "greeter.cc",
    ],
    hdrs = [
        "greeter.h",
    ],
)

cc_binary(
    name = "hello",
    srcs = [
        "main.cc",
    ],
    deps = [
        ":greeter",
    ],
)
```

@.... 即为外部库