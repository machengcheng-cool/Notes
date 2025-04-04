# day1

##  1. GKI（Generic Kernel Image）

#### 1.1 GKI概念

GKI：是Android 推出的通用内核镜像（Generic Kernel Image），旨在解决内核碎片化的问题，他将硬件驱动与内核分离，驱动以模块化的方式加载。

#### 1.2 为什么要用GKI

+ 兼容性：同一个内核镜像可以运行在不同的硬件平台上，只需替换驱动模块
+ 维护简化：Google公司只需要维护内核模块、厂商只需要维护驱动模块
+ 强制要求：从Android 12起，所有的设备必须使用GKI

**对RK3566的影响**

+ 编译时会生成boot.img（GKI内核）和vendor_boot.img（驱动模块）
+ 厂商需确保驱动以模块化形式编译

```
检查GKI
adb shell uname -a  # 查看内核版本（应包含 "android14-5.10" 等 GKI 标识）
adb shell ls /lib/modules  # 查看动态加载的驱动模块
```



## 2. Go配置

#### 2.1 Go配置的概念

- **Android Go** 是 Google 为 **低内存设备（1-2GB RAM）** 优化的轻量级系统版本。
- 通过精简系统服务、预装轻量应用（如 Google Go 代替 Chrome）降低内存占用。

#### 2.2 关键优化

- 内存压缩：更激进的进程管理，减少后台应用占用。
- 存储优化：文件系统针对低速存储（如 eMMC）优化。
- 应用限制：默认禁用非必要服务，替换为 Go 版应用（如 YouTube Go）。

 **对 2GB 内存设备的必要性**

- 即使硬件是 RK3566（通常支持 4GB+），如果产品定位为 **低成本 2GB 设备**，启用 GO 配置可显著提升流畅度。
- **注意**：GO 配置可能关闭部分功能（如多窗口模式），需权衡功能与性能。

```
检查Go配置
adb shell getprop | grep ro.config.low_ram  # 返回 "true" 表示 GO 启用
adb shell pm list packages | grep .go  # 查看预装的 Go 版应用（如 com.google.android.apps.youtube.go）
```



## 3. Clang

#### 3.1 前言

在编译Android 12+系统时，在./build.sh的编译选项中一般会有一个`-C`选项，这个`-C`选项的编译说明为：**Build kernel with Clang**，

#### 3.2 Clang的概念
