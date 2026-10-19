这个 SDK 采用典型的 **OpenWrt 派生目录结构 + 全志 BSP 层**。顶层一共 13 个目录、4 个文件，各司其职。

------

## 一、顶层总览

```
Tina-Linux/
├── Config.in              # Kconfig 总入口（menuconfig 主菜单）
├── Makefile               # 只有一行：include build/main.mk
├── rules.mk               # 全局规则（OpenWrt 风格）
├── README.md              # 编译/烧录说明
├── .config / .config.old  # 当前/上次的 menuconfig 结果
│
├── build/                 # ★ 构建系统核心（54 个 .mk/.sh）
├── config/                # Kconfig 菜单定义
├── target/                # ★ 产品/板型定义与 rootfs 骨架
├── package/               # ★ 软件包（26 大分类）
├── device/                # ★ 芯片/板级硬件配置、打包烧录配置
├── lichee/                # ★ BSP：U-Boot/SPL + Linux 内核
├── toolchain/             # 交叉编译工具链源码构建
├── tools/                 # 主机端构建工具（68 个）
├── scripts/               # 辅助脚本（feeds/config/pack 等）
├── dl/                    # 源码包缓存（779 个 tar 包）
├── prebuilt/              # 预编译工具链（riscv/arm/aarch64/host gcc）
├── out/                   # ★ 编译输出（按 board 分目录）
└── logs/                  # 编译日志
```

★ = 日常最常打交道的目录。

------

## 二、逐目录详解

### 1. `build/` — 构建系统核心

所有构建逻辑都在这里，顶层 `Makefile` 只是引入 [build/main.mk](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/build/main.mk)。

| 文件                                                         | 作用                                            |
| ------------------------------------------------------------ | ----------------------------------------------- |
| [build/main.mk](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/build/main.mk) | 总调度                                          |
| [build/toplevel.mk](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/build/toplevel.mk) | 顶层目标：`menuconfig`、`defconfig`、`config`   |
| [build/envsetup.sh](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/build/envsetup.sh) | `source` 后提供 `lunch`、`mboot`、`pack` 等命令 |
| [build/board.mk](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/build/board.mk) | 板子/芯片变量解析                               |
| [build/kernel.mk](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/build/kernel.mk) / [kernel-build.mk](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/build/kernel-build.mk) | 内核编译、`board.dts` 拷贝                      |
| [build/package.mk](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/build/package.mk) 等 | 软件包下载/解压/编译/打包流程                   |
| [build/image.mk](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/build/image.mk) | 根文件系统镜像生成                              |
| [build/product.mk](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/build/product.mk) / [product-config.mk](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/build/product-config.mk) | 产品配置                                        |
| [build/aw-upgrade.mk](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/build/aw-upgrade.mk) | 全志升级包                                      |
| 各种 `*.mk`                                                  | autotools/cmake/scons/quilt 等构建方式适配      |

------

### 2. `config/` — Kconfig 菜单定义

menuconfig 里各子页面的选项定义：

| 文件                                                         | 对应菜单                       |
| ------------------------------------------------------------ | ------------------------------ |
| [config/Config-build.in](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/config/Config-build.in) | Global build settings          |
| [config/Config-images.in](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/config/Config-images.in) | Target Images（镜像格式/压缩） |
| [config/Config-kernel.in](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/config/Config-kernel.in) | Kernel build options           |
| [config/Config-systeminit.in](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/config/Config-systeminit.in) | System init                    |
| [config/Config-devel.in](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/config/Config-devel.in) | Development                    |
| [config/top_config.in](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/config/top_config.in) | 顶层附加项                     |

> 各软件包自己的 `Config.in` 分散在 `package/*/` 下，构建时由 `tmp/.config-package.in` 汇总。

------

### 3. `target/` — 产品/板型定义

```
target/
├── Config.in          # 板子选择菜单（CONFIG_TARGET_xxx）
├── Makefile
├── allwinner/         # ★ 全志产品目录
│   ├── t113-mq_r/     # 某块板的产品定义
│   ├── t113-common/   # 芯片公共定义
│   ├── f133-mq_r/
│   ├── d1-nezha/
│   ├── generic/       # 通用 rootfs/镜像/签名/OTA 模板
│   └── ...            # 共 40+ 个 target
├── imagebuilder/      # 镜像构建器
├── sdk/               # SDK 打包
└── toolchain/         # 工具链 target
```

每个 `target/allwinner/<board>/` 里有：

- `defconfig` / `defconfig_min` / `defconfig_ota` — 顶层配置模板
- `TinaProducts.mk` + `<board>.mk` — 产品名/继承关系
- `busybox-init-base-files/` — **rootfs 覆盖文件**（init.d 脚本、wpa_supplicant.conf 等）
- `modules.mk` — 内核模块清单

`generic/` 里放跨平台公共内容：`base-files`、`boot-resource`（开机 logo）、`image/`、`sign_config/`、`toc/`、`swupdate/`、`version/`、各内核版本的通用 config。

------

### 4. `package/` — 软件包（SDK 的功能主体）

共 **26 个分类、约 700 个包**：

| 目录                                                         | 内容                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [package/allwinner/](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/package/allwinner/) | **78 个全志私有包**：多媒体、摄像头、Wi-Fi/BT、升级、电源、车机、AI demo |
| [package/libs/](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/package/libs/) | 185 个基础库（openssl、curl、json、sqlite…）                 |
| [package/lang/](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/package/lang/) | 115 个语言运行时（python、lua、node…）                       |
| [package/utils/](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/package/utils/) | 107 个工具（busybox、mtd-utils…）                            |
| [package/luci/](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/package/luci/) | 94 个 LuCI Web 界面模块                                      |
| [package/network/](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/package/network/) | 34 个网络服务（mqtt、ssh、ntp、vpn…）                        |
| [package/routing/](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/package/routing/) | 路由协议                                                     |
| [package/security/](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/package/security/) | 安全组件                                                     |
| [package/multimedia/](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/package/multimedia/) | GStreamer、FFmpeg、SDL、pulseaudio                           |
| [package/gui/](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/package/gui/) | LVGL、Qt、MiniGUI、Wayland、Flutter、GTK                     |
| [package/nn/](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/package/nn/) | MNN、NCNN                                                    |
| [package/avs/](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/package/avs/) / [thirdparty/](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/package/thirdparty/) | 语音方案（Alexa、思必驰等）                                  |
| [package/kernel/](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/package/kernel/) | 内核模块包                                                   |
| [package/system/](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/package/system/) | 系统组件（procd 等）                                         |
| [package/base-files/](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/package/base-files/) / [busybox-init-base-files/](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/package/busybox-init-base-files/) | rootfs 基础骨架                                              |

每个包的目录结构通常是：`Makefile` + `Config.in` + `patches/` + `files/`。

------

### 5. `device/` — 硬件与打包配置（全志私有）

```
device/config/
├── chips/                 # ★ 按芯片划分
│   ├── d1/  f133/  t113/
│   │   ├── configs/<board>/   # BoardConfig.mk、board.dts、sys_config.fex…
│   │   ├── bin/               # 预编译 boot0/fes1/optee/u-boot 二进制
│   │   ├── boot-resource/     # logo 资源
│   │   └── tools/             # cardscript.fex 等烧录脚本
│   └── ...
└── common/                # ★ 通用打包/签名配置
    ├── imagecfg/          # image_linux.cfg、image_nor.cfg（打包清单）
    ├── partition/         # 分区模板
    ├── toc/               # TOC0/TOC1/boot_package 安全启动镜像
    ├── sign_config/       # 签名配置
    ├── cert/              # 证书
    ├── tools/             # usbtool/cardtool 量产工具
    ├── dtb/  hdcp/  debug/  version/
```

------

### 6. `lichee/` — BSP（Bootloader + 内核）

```
lichee/
├── brandy-2.0/            # 全志 bootloader 框架
│   ├── spl-pub/           # boot0 / SPL
│   ├── u-boot-2018/       # U-Boot（含 configs/、arch/arm/dts/）
│   └── tools/             # build.sh、工具链
└── linux-5.4/             # Linux 5.4 LTS 内核源码
    ├── arch/{arm,riscv}/  # 分别对应 T113 / D1·F133
    ├── drivers/           # 含全志私有驱动（disp、g2d、vin、cedar…）
    ├── .config            # 实际内核配置
    └── arch/arm/boot/dts/board.dts   # 从板级配置拷入
```

------

### 7. `toolchain/` — 交叉工具链

源码形式构建的 gcc/binutils/gdb/glibc/musl/kernel-headers，以及 `wrapper/`（编译参数包装）。

> 实际编译常直接用 `prebuilt/gcc/linux-x86/{riscv,arm,aarch64,host}` 下的预编译工具链。

------

### 8. `tools/` — 主机端工具

构建过程中在 PC 上运行的 68 个工具：`mkimage`、`mtd-utils`、`squashfs`、`e2fsprogs`、`lz4/xz/zlib`、`bison/flex`、`pkg-config`、`qemu`、`upslug2`、`aw_tools` 等。

------

### 9. `scripts/` — 辅助脚本

| 脚本                           | 作用                            |
| ------------------------------ | ------------------------------- |
| `feeds` / `feeds.conf.default` | 软件包源管理                    |
| `config`                       | Kconfig 前端（menuconfig 后端） |
| `build_rootfs.sh`              | 构建 rootfs                     |
| `pack_img.sh` / `flashing/`    | 打包/烧录                       |
| `create-new-pro.sh`            | 新建产品                        |
| `kconfig.pl` 等                | Kconfig 解析                    |
| `checkpatch.pl`                | 补丁检查                        |

------

### 10. `dl/` — 源码包缓存

779 个第三方源码压缩包（python、gstreamer、ffmpeg、qt…）。下载过的包放这里，避免重复下载。

### 11. `prebuilt/` — 预编译工具链

`prebuilt/gcc/linux-x86/{riscv,arm,aarch64,host}`，开箱即用的交叉编译器。

### 12. `out/` — 编译输出

```
out/
├── host/                  # 主机端工具安装目录（staging）
├── serversocket           # 编译加速 socket
└── t113-bingpi_m2/        # 按 board 命名
    ├── boot.img / rootfs.img / uImage / zImage
    ├── pack/ 或 image/    # 最终烧录镜像
    ├── packages/          # 各 ipk 包
    ├── staging_dir/       # 中间产物
    └── md5sums / sha256sums
```

> 当前 `.config` 选的是 `t113-bingpi_m2`，所以只有这个目录有产物。

### 13. `logs/` — 日志

`logs/package/`、`logs/target/` 下按包名记录编译日志，编译失败先看这里。

------

## 三、构建流程如何串起来

```
source build/envsetup.sh   →  lunch 选 target
        │
        ▼
target/allwinner/<board>/defconfig  ──拷贝──▶  .config
        │
        ├─ build/toplevel.mk 解析 .config
        ├─ device/config/chips/<chip>/configs/<board>/  ──▶ 板级 fex/dts
        ├─ package/*/Makefile 按 .config 编译软件包  ──▶ out/.../packages
        ├─ lichee/linux-5.4 编译内核（用板级 config-5.4）
        ├─ lichee/brandy-2.0 编译 U-Boot/SPL
        └─ device/config/common/imagecfg/ 打包  ──▶ out/<board>/*.img
```

主要命令：

| 命令                | 做什么            |
| ------------------- | ----------------- |
| `make menuconfig`   | 改 `.config`      |
| `make`              | 编 rootfs + 内核  |
| `mboot`             | 编 U-Boot / boot0 |
| `pack`              | 打包成烧录镜像    |
| `./build.sh config` | 交互式选板子      |

------

## 四、按目的找目录（速查）

| 你想做的事             | 去哪个目录                                                   |
| ---------------------- | ------------------------------------------------------------ |
| 改板级引脚/设备树      | [device/config/chips/](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/device/config/chips/) |
| 加/删软件包            | [package/](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/package/) + `menuconfig` |
| 改某板默认配置         | [target/allwinner//defconfig](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/target/allwinner/) |
| 改开机脚本/rootfs 文件 | `target/allwinner/<board>/busybox-init-base-files/`          |
| 改内核驱动/内核 config | [lichee/linux-5.4/](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/lichee/linux-5.4/) |
| 改 U-Boot              | [lichee/brandy-2.0/u-boot-2018/](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/lichee/brandy-2.0/u-boot-2018/) |
| 改镜像打包/分区/签名   | [device/config/common/](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/device/config/common/) |
| 改构建逻辑             | [build/](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/build/) |
| 找编译产物             | [out/](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/out/) |
| 看编译报错             | [logs/](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/logs/) |

