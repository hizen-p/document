# 全志 T113 平台 DSP 预置音频算法使用说明

> 本文整理自 SDK 源码分析。结论先行：**DSP 硬件与固件都在，但当前 SDK 默认"装好了却没接线"——音频算法全套处于未加载状态。**

---

## 概述：三层结构

全志音频能力可分成三层来看：

1. **DSP 算法层** —— 预置算法本体，闭源固件 `dsp0.bin`
2. **主核 RPAF 调用层** —— 用户态库与 ALSA 插件
3. **CPU 侧软件音频库** —— 不依赖 DSP 的替代方案

当前 SDK 中，前两层的开关默认全部关闭。

---

## 一、DSP 预置算法本体：闭源固件 `dsp0.bin`

真正由全志预置的音频算法都打包在固件里，**不提供源码**：

```
device/config/chips/t113/bin/dsp0.bin    # Tensilica Xtensa HiFi 固件
device/config/chips/d1/bin/dsp0.bin
device/config/chips/f133/bin/dsp0.bin
```

固件对外暴露的算法清单写在头文件中，即你所能看到的"功能目录"：

`package/allwinner/rpaf/libawrpaf/include/awrpaf_component.h:14-34`

| 枚举值 | 组件 | 功能 | 归类 |
| --- | --- | --- | --- |
| 0 | `SND_DSP_COMPONENT_AGC` | 自动增益控制 | 前处理 |
| 1 | `SND_DSP_COMPONENT_AEC` | 回声消除 | 前处理 |
| 2 | `SND_DSP_COMPONENT_NS` | 噪声抑制 | 前处理 |
| 3 | `SND_DSP_COMPONENT_DECODEC` | 解码（**暂保留**） | — |
| 4 | `SND_DSP_COMPONENT_EQ` | 均衡器 | 后处理 |
| 5 | `SND_DSP_COMPONENT_MIXER` | 左右声道混音（**暂保留**） | 后处理 |
| 6 | `SND_DSP_COMPONENT_DRC` | 动态范围控制 | 后处理 |
| 7 | `SND_DSP_COMPONENT_REVERB` | 混响 | 后处理 |
| 8 | `SND_DSP_COMPONENT_RESAMPLE` | 重采样 | 通用 |
| 9 | `SND_DSP_COMPONENT_OTHER` | 其他 | 通用 |
| 20+ | `SND_DSP_COMPONENT_USER` | 用户自定义 | — |

> 注意：**固件中没有视频/音频解码算法**，`DECODEC` 明确标注"暂保留"。DSP 的定位是音频前/后处理。

---

## 二、主核侧调用层：`package/allwinner/rpaf/`

这是 SDK 中可以实际阅读和调用的部分：

```
package/allwinner/rpaf/
├── libawrpaf/                       # 用户态库，与 DSP 通信
│   ├── include/awrpaf_component.h       # 组件 API（单算法/流式/独立模式）
│   ├── awrpaf-common.h                  # ioctl 命令、PCM 参数、组件配置结构
│   └── awrpaf_component.c
├── awrpaf-plugin/                   # ALSA extplug 插件（type awrpaf）
│   └── awrpaf_plugin.c
├── conf/30-awrpaf.conf              # 预置算法的启用配置
└── demo/main.c                      # 独立算法测试程序
```

### 2.1 预置算法配置 —— `30-awrpaf.conf`

`package/allwinner/rpaf/conf/30-awrpaf.conf`：

```
pcm.awrpaf_playback {
    type awrpaf
    algos_desc {
        0 { algo_name eq  algo_type 4 }   # 均衡器
        1 { algo_name drc algo_type 6 }   # 动态范围控制
    }
    enable 1
    dspid 0
    card_name "audiocodec"
    slave.pcm "default_playback"
}

pcm.awrpaf_capture {
    type awrpaf
    algos_desc {
        0 { algo_name aec algo_type 1 }   # 回声消除
        1 { algo_name ns  algo_type 2 }   # 噪声抑制
    }
    ...
}
```

这里的 `algo_type` 就是上文枚举值：`1=AEC、2=NS、4=EQ、6=DRC`。

### 2.2 三种调用方式

| 方式 | 说明 |
| --- | --- |
| **ALSA 方式**（推荐） | 应用打开 `pcm.awrpaf_playback` / `pcm.awrpaf_capture`，插件自动把音频流送入 DSP 并生效 |
| **API 方式** | `awrpaf_component_algo_config_set(dspid, card, algo_index, algo_type, stream, enable)`（见 `awrpaf_component.h:38`） |
| **独立算法模式** | `awrpaf_component_independence_init / process / release`，可单独喂一段 PCM 做处理 |

调试示例见 `package/allwinner/rpaf/demo/main.c`，支持 `algo install / read / process` 子命令。

---

## 三、内核侧驱动

| 路径 | 作用 |
| --- | --- |
| `lichee/linux-5.4/sound/soc/sunxi/sunxi-rpaf/component/` | `component-core.c`、`component-driver.c` —— 组件管理与 ioctl |
| `lichee/linux-5.4/sound/soc/sunxi/sunxi-rpaf/sunxi-hifi/` | `rpmsg_hifi.c`（与 DSP 的 rpmsg 通道）、`sunxi-hifi-{codec,pcm,dmic,daudio}.c` |
| `lichee/linux-5.4/drivers/dsp/dsp_debug.c` | DSP 日志/调试设备 |
| 开关 | `CONFIG_SND_SUNXI_RPAF`（Kconfig 中 `default n`） |

---

## 四、CPU 侧的"预置音频功能"（不依赖 DSP）

若不开 DSP，全志另提供了一批跑在 CPU 上的音频库/插件：

| 包 | 说明 | 位置 |
| --- | --- | --- |
| **alsa-conf-aw** | 板级 `asound.conf`（默认 dmix/dsnoop/softvol 通路） | `package/allwinner/alsa-conf-aw/files/t113/t113-bingpi_m2/asound.conf` |
| **alsa-plugins-aw** | ALSA 插件：`p-eq`（均衡器，`libeq.a`）、`aw`（重采样，`libsrc.a`） | `package/allwinner/alsa-plugins-aw/files/` |
| **peq** | 参数均衡器（软件实现 `eq.c` / `eq_mid.c`） | `package/allwinner/peq/` |
| **libaec-xt** | 新唐 AEC 回声消除算法（软件） | `package/allwinner/libaec-xt/` |
| **libspeex-lite** | Speex 重采样 | `package/allwinner/libspeex-lite/` |
| **resample** | 重采样工具 | `package/allwinner/resample/` |
| **aactd** | Allwinner Audio Calibration Tool Daemon（音频校准） | `package/allwinner/aactd/` |
| **alsa-ucm-aw** | ALSA UCM 场景配置（不同板子的播放/录音场景） | `package/allwinner/alsa-ucm-aw/ucm_configs/` |

此外，硬件音频通路（ADC/DAC、DMIC、SPDIF、DAUDIO/I2S、ASRC）位于内核 `sound/soc/sunxi/` 下，通过板级 DTS 的 `&codec`、`&dmic`、`&daudio`、`&spdif` 节点配置（如 mic 增益、DAC/ADC 的 DRC/HPF 开关，在 `device/config/chips/t113/configs/mq_r/board.dts` 的 `&codec` 里）。

---

## 五、当前板子（t113-bingpi_m2）的实际状态

| 项 | 状态 |
| --- | --- |
| `alsa-conf-aw` | 已启用（只有普通通路，无 DSP） |
| `libawrpaf` / `awrpaf_plugin` / `awrpaf_demo` | 未启用 |
| `peq` / `libaec-xt` / `alsa-plugins-aw` / `alsa-ucm-aw` | 未启用 |
| 内核 `CONFIG_SND_SUNXI_RPAF` | not set |
| 板级 DTS `&sunxi_rpaf_dsp0` | `status = "disabled"` |
| `dsp0` 分区 | 在 `device/config/chips/t113/configs/mq_r/sys_partition.fex:66-70` 被**注释掉** |

因此，这块板子的音频目前走的是 **CPU + 内置 codec 的普通 ALSA 通路**。全志预置的 DSP 算法（AEC/NS/EQ/DRC…）都"躺"在 `dsp0.bin` 里，但没有被加载。

### 硬件与固件现状

| 层面 | 内容 | 状态 |
| --- | --- | --- |
| **硬件** | Tensilica Xtensa HiFi DSP（固件标识 `LX7.1.4`，R528/sun8iw20 同族） | 芯片里有 |
| **固件** | `dsp0.bin`（261KB，FreeRTOS + AW-SOUND-CORE，版本 `sun8iw20_fpga_v0.5.0`） | 文件在，未加载 |
| **内核驱动** | `sunxi-rpaf` + `sunxi_amp`（rpmsg/mailbox） | 均未使能 |
| **设备树** | `&sunxi_rpaf_dsp0` | 所有板子均 `status = "disabled"` |
| **分区** | `sys_partition.fex` 里的 `dsp0` 分区 | 被注释掉 |
| **用户态** | `libawrpaf` / `awrpaf_plugin` / `awrpaf_demo` | 全部未勾选 |
| **源码** | DSP 侧算法与工程 | **不提供**，只有二进制 |

也就是说：**驱动链路完整、可以打开，但默认固件中 DSP 处于掉电/未启动状态。**

---

## 六、能力边界

### 6.1 打开之后能实现什么

DSP 固件里现成可用的算法如下：

| 场景 | 可用算法 | 典型产品功能 |
| --- | --- | --- |
| **录音/通话前处理** | AEC + NS + AGC | 对讲机、可视门铃、语音助手、会议麦克风、车载免提 |
| **播放后处理** | EQ + DRC + REVERB | 智能音箱调音、Soundbar、电视/广告机音效 |
| **通用** | RESAMPLE | 降低主核负载、统一采样率 |
| 保留未实现 | DECODEC、MIXER | 目前不可用 |

**实际收益**：把 AEC/NS/EQ 这类持续运算从 Cortex-A7 卸载到 DSP，可降低 CPU 占用与音频延迟，适合低功耗常听（always-on）场景。

### 6.2 不能实现什么（重要）

| 想法 | 可行性 | 原因 |
| --- | --- | --- |
| 用 DSP 做**视频解码** | 不可行 | 视频解码固定走 VE（`/dev/cedar_dev`），DSP 不参与 |
| 用 DSP 做**音频解码**（MP3/AAC） | 不可行 | 枚举里的 `DECODEC` 明确"暂保留"，固件未实现 |
| 自己写算法跑在 DSP 上 | 基本不行 | 没有 Tensilica Xtensa 工具链、HiFi 库与 DSP 侧 SDK；`SND_DSP_COMPONENT_USER=20` 只是协议预留 |
| 修改/替换预置算法参数之外的逻辑 | 不可行 | 算法是闭源二进制，只能 enable/disable 并选择算法索引 |
| 直接拿来做通用信号处理 | 不可行 | 固件是固定功能音频框架，不是开放 DSP 运行时 |

所以"预置音频功能"的准确含义是：**你只能调用全志已经烧进 `dsp0.bin` 的那几个算法，不能编程。**

---

## 七、两条落地路线

### 路线 A：只要音频效果，不想折腾（推荐）

直接使用 CPU 侧、有源码的方案：

- **EQ**：`package/allwinner/peq/`（`eq.c` 参数均衡器，可改）
- **AEC**：`package/allwinner/libaec-xt/`（新唐 AEC）
- **EQ/重采样插件**：`package/allwinner/alsa-plugins-aw/`（`p-eq`、`libsrc`）
- **重采样**：`package/allwinner/libspeex-lite/`、`package/allwinner/resample/`

优点：全部开源/可改、`menuconfig` 勾选即用、不涉及固件和分区。
缺点：占 CPU，效果与延迟不如 DSP。

### 路线 B：启用 DSP 预置算法

需要同时修改 4 处（缺一不可）：

1. **板级 DTS**：`&sunxi_rpaf_dsp0 { status = "okay"; };`
2. **内核配置**：`CONFIG_SND_SUNXI_RPAF=y`（自动 select `RPMSG` / `RPMSG_SUNXI_AMP`），可选 `CONFIG_DSP_DEBUG=y`
3. **分区 + 启动**：放开 `sys_partition.fex` 的 `dsp0` 分区（`dsp0.fex`），确认 `env.cfg` 的 `boot_dsp0` 能加载固件
4. **用户态**：勾选 `libawrpaf`、`awrpaf_plugin`，部署 `30-awrpaf.conf` 到 `/etc/alsa/conf.d/`

**验证顺序：**

```
dmesg | grep -iE "rpaf|dsp|amp"        # 看 DSP 是否启动
cat /sys/kernel/debug/...              # rpmsg/mailbox 通道
ls /dev/ | grep -i dsp                 # dsp_debug 节点（若开）
aplay -L | grep awrpaf                 # ALSA 插件是否注册
awrpaf_demo -h                         # 独立算法测试
```

**风险提示：**

- 固件为 `fpga_v0.5.0` 早期版本，且分区默认被注释，说明全志/MangoPi 并未在此板上验证过 DSP 通路，可能需要 FAE 支持；
- 首次启用大概率要调 mailbox/rpmsg 地址、固件加载地址和时钟，不是"勾个开关"就能稳定跑；
- 没有 DSP 源码，出问题只能抓 `dsp_debug` 日志。

---

## 八、一句话定位

**预置算法的"功能清单"在 `awrpaf_component.h`，配置在 `30-awrpaf.conf`，调用库在 `libawrpaf`，算法本体在 `dsp0.bin`，驱动在 `sound/soc/sunxi/sunxi-rpaf/`。**

**现状与取舍：** DSP 存在但未启用，音频全靠 CPU。启用后可用 AEC/NS/AGC（录音）与 EQ/DRC/REVERB（播放）这几组预置算法，适用于对讲、语音助手、音箱调音等场景；**不能**实现视频/音频解码与自定义 DSP 算法。若只是要音效，建议先走 CPU 侧的 `peq` / `libaec-xt`；只有对 CPU 占用或延迟有硬要求时，才值得投入精力打通 DSP 通路。
