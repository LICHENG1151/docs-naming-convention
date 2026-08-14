<!--
name: 2017-Transformer 本篇导航（示例）
creator: Li Cheng
created: 2026-07-28
modified: 2026-08-14
-->

# 2017 · 注意力机制取代循环结构

> 本文件夹是「论文总结子文件夹」示例，演示 v2 结构：**原文 + 总结 + 导航 README** 同处归档。

## 速查卡

| 项 | 内容 |
|----|------|
| 英文原名 | Attention Is All You Need |
| 作者 | Vaswani et al.（Google Brain / Google Research） |
| 年份 | 2017 |
| 出处 | NeurIPS 2017 |
| 代码 | https://github.com/tensorflow/tensor2tensor |
| 状态 | 已消化 |
| 一句话 | 完全丢掉循环与卷积，只用自注意力 + 前馈网络做序列转换，且训练可沿序列维度并行。 |

## 文件清单

| 文件 | 用途 |
|------|------|
| [`总结.md`](./总结.md) | 总结正文（★ 必读） |
| `Attention Is All You Need.pdf` | **论文原文**，照抄原名，保真备查（本示例未随档，实际使用时放入） |
| `README.md` | 本导航卡 |

> 原文体积大或版权受限而未随档时，如本例，在此注明「原文未随档」，并保证速查卡的 `url` 可回溯原文。
