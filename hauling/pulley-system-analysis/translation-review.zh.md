# 翻译校订记录

校订日期：2026 年 9 月 20 日。由 codex agent 逐段对照本目录英文稿（`pulley-system-analysis.en.md`）与中文稿（`pulley-system-analysis.zh.md`）审校并直接修订。

| 原文术语／表述 | 中文处理 |
| --- | --- |
| pulley system | 滑轮系统 |
| mechanical advantage (MA) | 机械增益 |
| efficiency | 效率 |
| ideal / effective / calculated MA | 理想 MA／有效 MA／计算 MA |
| PCD (progress capture device) | 进度捕获装置 |
| tractor | 牵引点 |
| direction-changing system | 变向系统 |
| input / output element | 输入／输出元件 |
| stroke distance | 行程距离 |
| collapse rate | 系统收拢速率 |
| V-rig / Z-rig / V on V / Z on Z | 保留英文原名（V-rig、Z-rig、"V on V"、"Z on Z"） |
| degree (of a term) | （项的）次数 |

## 主要修改点

- 修正数学与逻辑问题：Z-rig 公式统一为 1+P2+P1P2；澄清倒置 V-rig 缺的是零次项"1"而非一次项 P1；把含混的"1+PN"明确为 1+NP 并补充 1+3×0.8=3.4；两处误称"计算效率"的数值改为"计算 MA"。
- 统一技术术语（见上表），清理残留 setup，改善受力路径、复位频率、行程等段落表达。

## 说明

原文中 Z-rig 公式在"About the terms"一节写作 1+P1+P1P2，与"In a nutshell"一节的 1+P2+P1P2 及"P1 出现一次、P2 出现两次"的说明不一致；审校按后者（更显式、更自洽的推导）统一为 1+P2+P1P2。

## 完整性核对

- en.md / zh.md 均 255 行、10 个标题、28 个图片引用（`images/01.jpg` ~ `28.png`），一一对应。
- 全部数学表达式（9:1、11:1、Z-rig、2^N 等）保留。

## 安全信息核对

- 本文为理论分析文章，无具体操作安全警告；作者前言中关于"非同行评议、术语可能不同于惯例、勿被花哨词语误导"的免责说明完整保留。
