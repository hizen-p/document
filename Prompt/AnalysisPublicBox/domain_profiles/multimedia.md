# Multimedia Domain Profile

适用：

- Audio
- Video
- Codec
- Streaming
- Media Framework

---

## media_pipeline_analysis

【重要度：5｜必须阅读】

分析实际数据流：

Input
↓
Capture
↓
Buffer
↓
Decode
↓
Process
↓
Encode
↓
Output

不存在阶段删除。

---

## buffer_memory_analysis

【重要度：5｜必须阅读】

分析：

Buffer 生命周期。

创建。

传递。

引用。

释放。

如果源码存在再分析：

DMA。

DMABUF。

mmap。

Zero Copy。

---

## timestamp_sync_analysis

【重要度：4｜强烈建议】

存在时分析：

PTS。

DTS。

Clock。

同步。

---

## thread_model_analysis

【重要度：5｜必须阅读】

分析真实线程：

Capture。

Decode。

Process。

Encode。

Render。

Network。

---

## hardware_acceleration_analysis

【重要度：4｜强烈建议】

只依据实际代码分析：

V4L2。

VAAPI。

NVENC。

QSV。

ISP。

GPU。

Vendor Codec。
