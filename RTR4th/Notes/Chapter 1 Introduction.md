---
title: Chapter 1 Introduction 简介
tags:
  - RTR4
  - 计算机图形学
  - 实时渲染
aliases:
  - RTR4 第一章
  - 实时渲染导论
---

# 概述

本章为全书建立统一的**数学语言、几何语言和术语**，是后续章节的"公共语法"。

---

# 实时渲染

> [!important] 定义
> **实时渲染（Real-Time Rendering）**：在计算机上快速生成图像，是计算机图形学中互动性最强的领域。

**三要素**：交互性 + 三维场景 → 二维图像 + 图形加速硬件（GPU）

## 帧率与刷新率

| 概念 | 说明 | 单位 |
|------|------|------|
| 显示速率 | 每秒显示的帧数 | FPS |
| 刷新率 | 屏幕每秒刷新次数 | Hz |

> [!example] 电影放映机：24 FPS 显示，但快门每帧照亮 3 次 → 刷新率 72 Hz

**帧率参考**：
- 6 FPS：开始有交互感
- 30-72 FPS：游戏目标帧率
- **90 FPS**：VR 最低要求（延迟 >15ms 会影响流畅感）

---

# 1.1 内容概览

本书涵盖：[[Chapter 2 The Graphics Rendering Pipeline|渲染管线]] → [[Chapter 3 The Graphics Processing Unit|GPU]] → [[Chapter 4 Transform|变换]] → [[Chapter 5 Shading Basics|着色]] → [[Chapter 7 Shadows|阴影]] → [[Chapter 8 Light and Color|光与颜色]] → [[Chapter 9 Physically Based Shading|PBR]] → [[Chapter 11 Global Illumination|全局光照]] → [[Chapter 14 Volumetric and Translucency Rendering|体渲染]] → [[Chapter 16 Polygonal Techniques|多边形技术]] → [[Chapter 19 Acceleration Algorithms|加速算法]] → [[Chapter 23 Graphics Hardware|图形硬件]] → [[Chapter 24 The Future|未来方向]]

---

# 1.2 符号与定义

## 1.2.1 数学记号

### 基本记法（表 1.1）

| 类型 | 记法 | 例子 |
|------|------|------|
| 角度 | 小写希腊字母 | $\alpha, \phi, \rho, \theta$ |
| 标量 | 小写斜体 | $a, b, t, u_k, v, w_{ij}$ |
| 向量/点 | 小写粗体 | $\mathbf{a}, \mathbf{u}, \mathbf{v}, \mathbf{h}(\rho)$ |
| 矩阵 | 大写粗体 | $\mathbf{A}, \mathbf{M}, \mathbf{T}$ |
| 平面 | $\pi$ | $\pi: \mathbf{n} \cdot \mathbf{x} + d = 0$ |
| 三角形 | $\triangle$ | $\triangle \mathbf{v}_0\mathbf{v}_1\mathbf{v}_2$ |

> [!note] 例外：着色方程中的 $L$（辐射度）、$E$（辐照度）、$\sigma_s$（散射系数）等沿用文献惯例

### 向量与齐次坐标

**列向量**（默认形式）：

$$
\mathbf{v} = \begin{bmatrix} v_x \\ v_y \\ v_z \end{bmatrix} = (v_x, v_y, v_z)^T
$$

**齐次坐标**（区分点与向量）：

$$
\text{向量（方向）}: \begin{bmatrix} v_x \\ v_y \\ v_z \\ 0 \end{bmatrix}, \quad
\text{点（位置）}: \begin{bmatrix} v_x \\ v_y \\ v_z \\ 1 \end{bmatrix}
$$

> [!tip] $w=0$ 表示方向向量（不受平移影响），$w=1$ 表示点

**数字下标**：$\mathbf{v} = (v_0, v_1, v_2)^T$（算法实现时更方便）

### 矩阵记法

**元素访问**（公式 1.1）：

$$
\mathbf{M} = \begin{bmatrix}
m_{00} & m_{01} & m_{02} \\
m_{10} & m_{11} & m_{12} \\
m_{20} & m_{21} & m_{22}
\end{bmatrix}, \quad m_{ij} = \text{第 } i \text{ 行第 } j \text{ 列}
$$

> [!warning] **先行后列**，$m_{01}$ 是第 0 行第 1 列

**列/行向量表示**（公式 1.2）：

$$
\mathbf{M} = (\mathbf{m}_{,0} \ \mathbf{m}_{,1} \ \mathbf{m}_{,2}) = \begin{bmatrix} \mathbf{m}_{0,}^T \\ \mathbf{m}_{1,}^T \\ \mathbf{m}_{2,}^T \end{bmatrix}
$$

其中 $\mathbf{m}_{,j}$ 为第 $j$ 列，$\mathbf{m}_{i,}$ 为第 $i$ 行

### 平面方程

$$
\pi: \mathbf{n} \cdot \mathbf{x} + d = 0
$$

| 条件 | 空间位置 |
|------|----------|
| $\mathbf{n} \cdot \mathbf{x} + d > 0$ | 正半空间 |
| $\mathbf{n} \cdot \mathbf{x} + d < 0$ | 负半空间 |
| $\mathbf{n} \cdot \mathbf{x} + d = 0$ | 在平面上 |

> 应用：裁剪、视锥体测试、碰撞检测、阴影体

### 运算符（表 1.2）

| 标记 | 说明 |
|------|------|
| $\mathbf{a} \cdot \mathbf{b}$ | 点积（方向关系，光照计算） |
| $\mathbf{a} \times \mathbf{b}$ | 叉积（垂直向量，法线计算） |
| $\mathbf{v}^T$ | 转置 |
| $\mathbf{v}^{\perp}$ | 二维垂直向量：$(v_x, v_y)^{\perp} = (-v_y, v_x)$ |
| $\vert \mathbf{A} \vert$ | 矩阵行列式 |
| $\vert a \vert$ | 标量绝对值 |
| $\|\mathbf{v}\|$ | 向量长度 |
| $x^+$ | 正部：$x > 0$ 保留，否则为 0 |
| $x^{\mp}$ | 饱和：限制到 $[0,1]$ |

### Clamp 运算（公式 1.3-1.4）

$$
x^+ = \begin{cases} x, & x > 0 \\ 0, & \text{otherwise} \end{cases} \tag{1.3}
$$

$$
x^{\mp} = \begin{cases} 1, & x \ge 1 \\ x, & 0 < x < 1 \\ 0, & \text{otherwise} \end{cases} \tag{1.4}
$$

> 应用：$\max(0, \mathbf{n} \cdot \mathbf{l})$ 只保留正面光照；颜色/透明度限制到 $[0,1]$

### 阶乘与二项式系数（公式 1.5-1.6）

$$
n! = n(n-1)(n-2) \cdots 3 \cdot 2 \cdot 1, \quad 0! = 1 \tag{1.5}
$$

$$
\binom{n}{k} = \frac{n!}{k!(n-k)!} \tag{1.6}
$$

> 应用：Bezier 曲线、Bernstein 多项式、插值

### 特殊函数（表 1.3）

| 函数 | 说明 |
|------|------|
| $\operatorname{atan2}(y, x)$ | 二元反正切，范围 $[-\pi, \pi]$（普通 $\arctan$ 只有 $(-\frac{\pi}{2}, \frac{\pi}{2})$） |
| $\log(n)$ | 自然对数 $\log_e(n)$（非 $\log_{10}$） |

### 坐标系与区间

**标准基**：

$$
\mathbf{e}_x = \begin{pmatrix} 1 \\ 0 \\ 0 \end{pmatrix}, \quad
\mathbf{e}_y = \begin{pmatrix} 0 \\ 1 \\ 0 \end{pmatrix}, \quad
\mathbf{e}_z = \begin{pmatrix} 0 \\ 0 \\ 1 \end{pmatrix}
$$

**区间表示**：$[a,b]$ 闭区间，$(a,b)$ 开区间，$[a,b)$ 左闭右开

**颜色**：$(red, green, blue)$，每分量范围 $[0,1]$

---

## 1.2.2 几何定义

### 渲染图元

基本图元：**点、线、三角形**

> [!tip] 历史例外：Pixel-Planes（球体）、NVIDIA NV1（椭球体）

### 术语

| 术语 | 定义 |
|------|------|
| **Model / Object** | 一组几何实体（可包含渲染图元或高级形式如 Bezier 曲面） |
| **Scene** | 待渲染环境 = 几何 + 材质 + 光照 + 相机 |

---

## 1.2.3 Shading 的双重含义

| 上下文 | 含义 | 例子 |
|--------|------|------|
| 视觉外观 | 表面看起来怎样 | shading model, toon shading |
| 可编程阶段 | GPU 程序 | vertex shader, shading language |

> [!warning] 需根据上下文判断

---

# 常见误区

| 误区 | 正解 |
|------|------|
| 点和向量"长得一样" | 几何意义不同：点=位置，向量=方向；齐次坐标用 $w$ 区分 |
| $m_{ij}$ 下标随便记 | **先行后列** |
| 平面方程只是公式 | $\mathbf{n} \cdot \mathbf{x} + d$ 的符号可判断点在平面哪侧 |
| shader = 着色器程序 | shading 也可能指"视觉外观" |

---

# 资源

> [!tip] 配套网站 [realtimerendering.com](https://www.realtimerendering.com)
> - 线性代数/三角学附录
> - 碰撞检测、实时光线追踪章节（在线）
> - 勘误与参考文献

**相关链接**：
- [中文翻译仓库](https://github.com/Morakito/Real-Time-Rendering-4th-CN)
- [参考文献合集](https://github.com/QianMo/Real-Time-Rendering-4th-Bibliography-Collection)
