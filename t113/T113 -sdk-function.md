这个 SDK 是全志 **Tina Linux 5.0**（OpenWrt 派生 + 全志私有中间件）的 MangoPi 移植版，面向 **D1 / F133 / T113** 三颗 RISC-V/ARM SoC。它不只是一个内核，而是一整套**从 bootloader、内核、rootfs 到多媒体/AI/无线中间件的产品级发行版**。

------

## 一、支持的芯片与架构

| 芯片     | 代号       | 架构                   | 典型定位           |
| -------- | ---------- | ---------------------- | ------------------ |
| **D1**   | sun20iw1p1 | RISC-V C906 (RV64)     | 开发板、Linux 学习 |
| **F133** | sun20iw1p1 | RISC-V C906            | D1 的降本版，量产  |
| **T113** | sun8iw20p1 | **ARM Cortex-A7 双核** | 工业/HMI/多媒体    |

同一个 SDK 还保留了 c200s / h133 / h616 / r818 / r853 / v851 等大量 target 目录，说明它是从全志通用 Tina 树裁剪来的。当前树内实际配置的是 d1 / f133 / t113。

板级配置在 [device/config/chips/](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/device/config/chips/)，产品配置在 [target/allwinner/](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/target/allwinner/)。

------

## 二、系统与构建能力

- **U-Boot 2018 / brandy 2.0** + **Linux 5.4 LTS** 内核
- 两套构建体系：Tina（OpenWrt 的 `menuconfig`/`make`/`pack`）与 buildroot（D1/F133 的 BoardConfig 中声明）
- **文件系统**：squashfs、ubifs、ext4、f2fs、overlayfs、initramfs、ramfs
- **存储介质**：SPI NAND / SPI NOR / eMMC / SD 卡；UBI 坏块管理
- **镜像与烧录**：[imagecfg](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/device/config/common/imagecfg/)、分区表、TOC 打包；支持 PhoenixCard 烧 TF 卡、USB 量产工具
- **OTA/升级**：`swupdate`、`tina-upgrade`、`ota-burnboot`、`recovery` 分区恢复
- **安全启动**：TOC0/TOC1 签名、证书（[sign_config](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/device/config/common/sign_config/)、[cert](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/device/config/common/cert/)）— 可用于防篡改、可信启动

------

## 三、多媒体（这是全志最强的一块）

**硬件编解码（CedarX / libcedarx）**，见 [tina_multimedia/Config.in](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/package/allwinner/tina_multimedia/Config.in)：

| 类型          | 支持格式                                                     |
| ------------- | ------------------------------------------------------------ |
| 视频解码      | H.264、H.265/HEVC、MPEG1/2/4、H.263、VP6/VP8/VP9、AVS、MJPEG、WMV1/2/3 |
| 视频编码      | H.264、H.265、MJPEG                                          |
| 音频解码/解析 | AAC、MP3、WAV、FLAC、APE、AMR、OGG/Vorbis 等                 |
| 音频编码      | AAC、MP3、PCM、G.711 等                                      |
| 封装/解封装   | MP4、TS、MKV、AVI、FLV、MOV、MP3、AAC、WAV                   |

**中间件与应用**：

- `tplayer`（播放器）、`trecorder`（录像）、`tmetadataretriever`（元数据）
- `eyesee-mpp`：摄像头预览/拍照/录像/编码全流程框架（含 `awcdr` 行车记录仪方案）
- `sunxiCamera` / `libcamapi` / `libAWIspApi`（ISP 图像处理）
- GStreamer 全家桶、FFmpeg、SDL、MPG-streamer
- ALSA 音频框架：codec、ASRC、SPDIF、DMIC，`alsaconf-aw`、bluealsa、PulseAudio
- 摄像头传感器：OV/GC/SC/IMX 系列（OV5640、OV2640、IMX214/258/274…），V4L2 + UVC

**2D 图形加速**：`SUNXI_G2D`（缩放、旋转、混叠），配合 DRM/KMS、framebuffer、disp2 显示驱动。

------

## 四、网络与无线

| 方向               | 能力                                                         |
| ------------------ | ------------------------------------------------------------ |
| **Wi-Fi**          | STA/AP 模式，`wpa_supplicant`、`hostapd`、`wifimanager` 2.0、`softap`、`smartlinkd`（配网） |
| **Wi-Fi/蓝牙芯片** | XR819/XR819S、RTL8189/8723/8821/8822/8733、AP6212/6236/6255/6335/6356、AIC8800、QCA9377、MRVL8977 |
| **蓝牙**           | BlueZ、`btmanager`、BLE、A2DP/HFP（bluez-alsa、PulseAudio）、mesh |
| **有线**           | SUNXI GMAC + 外置 PHY（ICPlus 等）                           |
| **蜂窝**           | PPP 拨号、`4Gmanager`                                        |
| **协议/服务**      | MQTT（mosquitto）、SNMP、NTP、HTTP（nginx/apache）、mDNS、DLNA、RTSP、UDPXY、VPN（openconnect/vpnc）、DDNS、IPv6、代理/adblock |

------

## 五、显示与 GUI

[package/gui/](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/package/gui/) 提供多种人机界面方案：

- **LVGL 6 / LVGL 8**（littlevgl，带 demo 和驱动）— 嵌入式 HMI 首选
- **Qt**、**MiniGUI**、**DirectFB**、**GTK**
- **Wayland/Weston**、**Flutter (sunxi)**
- 配合 DRM/KMS、Framebuffer、触摸输入、`libdisplay`、`libminui`

------

## 六、AI 与语音

- **推理框架**：`MNN`、`NCNN`（[package/nn/](vscode-webview://11rs52abng6p2vp4lpano63d4soj37re1stufang2usrfpgu9oih/package/nn/)）
- **视觉**：`yolov3` demo、`eyesee-mpp/smartva`、`evevehicledetect`（车辆检测）、`tinacvr`
- **语音**：Amazon **AVS**（Alexa Voice Service）全套、`duilite`（思必驰）、`uvoice`、`sensory`、`speechmodule`、`libpryon-lite`
- 这些说明 SDK 原生面向**智能音箱、IPC 摄像头、行车记录仪、ADAS** 等产品

------

## 七、外设与工业接口

内核已启用：GPIO、I2C、SPI、PWM、UART、**CAN/CAN-FD**、IIO(ADC)、RTC、Watchdog、Thermal、CPU 调频、PMIC、USB Host/Device/Storage、USB Gadget、SDIO、SPI-NAND/NOR。

应用层配套：

- `powerkey`、`nativepower`（电源管理）
- `healthd`、`thermal_pwm`（温控）
- `usb-gadget`、`mtp`、`uvcout`（USB 摄像头输出）
- `smart_car`（智能车）、`tinacvr`（行车记录仪）、`rt_media`（实时媒体）
- `tvd_demo`、`tofdemo`、`tsc_demo`（触摸/TOF）

------

## 八、开发与调试工具

- BusyBox、OpenSSH / Dropbear、Python 3（含 sqlite/ssl/ctypes）
- `gdb`、`strace`、`perf`、`stress-ng`、`mtop`
- `asan_demo`（地址消毒）、`memleak_check`（内存泄漏）、`awsystrace`（性能追踪）
- `aw-upgrade`、`logwrapper`、`uevent-monitor`

------

## 九、当前这块板子（t113-bingpi_m2）实际启用了什么

默认 defconfig 启用了 **144 个包**，关键的有：

```
busybox / openssh-server / python3
wifimanager + demo / wpad-basic / hostapd-common / bluez-utils
tplayerdemo / camerademo / alsa-utils
swupdate / ota-burnboot
mtd-utils / uboot-envtools
```

也就是说：**开箱即得 Wi-Fi 联网、SSH 登录、Python、音视频播放、摄像头、OTA 升级**。其余能力（Qt/LVGL、GStreamer、EyeSee 摄像头方案、AI、4G、蓝牙音频等）需要 `make menuconfig` 勾选后重新编译。

------

## 十、能实现什么产品（典型场景）

| 场景                    | 对应 SDK 能力                                             |
| ----------------------- | --------------------------------------------------------- |
| **智能音箱**            | AVS/uvoice + ALSA + Wi-Fi/BT + tplayer                    |
| **行车记录仪 / DVR**    | tinacvr / awcdr + MIPI 摄像头 + H.264/265 硬编 + G-sensor |
| **IP 摄像头 / 门铃**    | eyesee-mpp + sunxiCamera + RTSP + H.265 + smartva         |
| **工业 HMI / 串口屏**   | LVGL/MiniGUI/Qt + DRM + 触摸 + CAN + 双网口               |
| **智能家居网关**        | Wi-Fi/BT + MQTT + 4G + IPv6 + OTA                         |
| **多媒体广告机/播放盒** | tplayer + HDMI/LCD + G2D + 网络播放                       |
| **AI 边缘盒子**         | MNN/NCNN + yolov3 + 摄像头 + 视频编码                     |
| **车载中控/仪表**       | smart_car + CAN + LVGL/Qt + 倒车影像                      |
| **学习/开发板**         | 完整 BSP、Python、SSH、开源驱动                           |

------

一句话：**这是一套能直接做产品的中低端多媒体 SoC 发行版**——底层 BSP 齐全，中间件（CedarX/ISP/Wi-Fi/BT/GUI/OTA）是现成的，上层可自由裁剪成音箱、摄像头、HMI、网关或 AI 终端。