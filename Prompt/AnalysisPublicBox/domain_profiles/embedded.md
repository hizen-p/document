# Embedded Domain Profile

适用：

- BSP
- Bootloader
- Linux Kernel
- Driver
- RTOS
- Embedded Application

---

## 专项模块

boot_chain_analysis

【重要度：5｜必须阅读】

分析实际涉及：

Boot ROM
↓
Bootloader
↓
Kernel
↓
Device Tree
↓
Driver
↓
RootFS
↓
Application

---

cross_compile_analysis

【重要度：5｜必须阅读】

分析：

Host
↓
Toolchain
↓
Sysroot
↓
Target

---

hardware_arch_analysis

【重要度：5｜必须阅读】

分析：

CPU 架构。

内存。

外设。

硬件依赖。

---

device_tree_analysis

【重要度：4｜强烈建议】

仅存在 DTS/DTB 时分析。

---

kernel_driver_analysis

【重要度：5｜必须阅读】

仅涉及 Kernel Driver 时分析。

---

rootfs_analysis

【重要度：4｜强烈建议】

仅涉及 RootFS 时分析。
