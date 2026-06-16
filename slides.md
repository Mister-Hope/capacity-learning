---
theme: default
title: 电容器的电容
titleTemplate: '%s'
highlighter: shiki
transition: fade
mdc: true
layout: cover
colorSchema: dark
clickAnimation: card
fonts:
  sans: Nunito Sans
  mono: Fira Code
  provider: none
defaults:
  layout: default
  transition: fade
---

<div class="cover-chapter"><span class="cover-section">§</span> 10.4</div>

<h1 class="cover-title">电容器的电容</h1>

<div class="cover-subtitle">
  <span>授课教师：张伯望</span>
</div>

<div class="cover-school">东北育才学校</div>

<div class="cover-decoration abs-br m-8" aria-hidden="true">
  <svg viewBox="0 0 200 140" width="300" xmlns="http://www.w3.org/2000/svg">
    <line x1="30" y1="60" x2="85" y2="60" stroke="currentColor" stroke-width="3" stroke-linecap="round"/>
    <rect x="85" y="8" width="10" height="104" rx="5" fill="none" stroke="currentColor" stroke-width="3"/>
    <rect x="105" y="8" width="10" height="104" rx="5" fill="none" stroke="currentColor" stroke-width="3"/>
    <line x1="115" y1="60" x2="170" y2="60" stroke="currentColor" stroke-width="3" stroke-linecap="round"/>
    <line x1="50" y1="20" x2="50" y2="100" stroke="currentColor" stroke-width="1.5" stroke-dasharray="6 4" opacity="0.35"/>
    <line x1="150" y1="20" x2="150" y2="100" stroke="currentColor" stroke-width="1.5" stroke-dasharray="6 4" opacity="0.35"/>
    <text x="100" y="134" text-anchor="middle" style="font-size:13px" fill="#94a3b8" opacity="0.7">平行板电容器</text>
  </svg>
</div>

---
layout: default
---

## 我们学过哪些起电方式？

<div class="grid grid-cols-3 gap-4 mt-4">

<v-clicks>

<div class="card">
  <div class="font-semibold text-lg">摩擦起电</div>
  <div class="text-sm opacity-60 mt-1">通过摩擦使电子转移</div>
</div>

<div class="card">
  <div class="font-semibold text-lg">接触起电</div>
  <div class="text-sm opacity-60 mt-1">带电体接触使电荷转移</div>
</div>

<div class="card">
  <div class="font-semibold text-lg">感应起电</div>
  <div class="text-sm opacity-60 mt-1">电场作用下电荷重新分布</div>
</div>

</v-clicks>

</div>

<v-click>

<div class="mt-6 text-center opacity-70">
但这些方法只能让物体<span class="text-accent">带电</span>——
</div>

</v-click>

<v-click>

<div class="card card-highlight mt-5">
  <div class="text-2xl font-semibold"><mdi-head-question-outline class="text-accent text-3xl inline-block align-middle mr-3" />我们如何把电荷<span class="text-accent">存</span>起来？</div>
</div>

</v-click>

---
layout: default
---

<div class="tag-icon mb-4"><mdi-lightbulb-on-outline /> 电容器：储存电荷的容器</div>

<v-clicks>

<div class="card card-highlight">
  <div class="text-2xl font-semibold">
    定义：<span class="text-accent">相互靠近</span>且<span class="text-accent">彼此绝缘</span>的两个导体，构成电容器
  </div>
</div>

<div class="grid grid-cols-2 gap-3 mt-3">
  <div class="card">
    <div class="font-semibold text-lg">相互靠近</div>
    <div class="text-sm opacity-50 mt-1">电荷相互吸引，不会流失</div>
  </div>
  <div class="card">
    <div class="font-semibold text-lg">彼此绝缘</div>
    <div class="text-sm opacity-50 mt-1">没有导电通路，电荷无法逃逸</div>
  </div>
</div>

</v-clicks>

---
layout: default
---

# 哪些东西可以看成电容器？

<div class="grid grid-cols-2 gap-3 mt-6">

<v-clicks>

<div class="card">
  <div class="flex items-center gap-2 mb-1"><mdi-account class="text-accent-2 text-xl" /><span class="font-semibold text-lg">人 与 金属板</span></div>
  <div class="text-sm opacity-50">不接触即构成电容器</div>
</div>

<div class="card">
  <div class="flex items-center gap-2 mb-1"><mdi-weather-lightning class="text-accent-2 text-xl" /><span class="font-semibold text-lg">云层 与 大地</span></div>
  <div class="text-sm opacity-50">云层带电，大地感应</div>
</div>

<div class="card">
  <div class="flex items-center gap-2 mb-1"><mdi-earth class="text-accent-2 text-xl" /><span class="font-semibold text-lg">地球 与 火星</span></div>
  <div class="text-sm opacity-50">真空绝缘，彼此靠近</div>
</div>

<div class="card">
  <div class="flex items-center gap-2 mb-1"><mdi-all-inclusive class="text-accent-2 text-xl" /><span class="font-semibold text-lg">任意两个导体</span></div>
  <div class="text-sm opacity-50">靠近 + 绝缘 = 电容器</div>
</div>

</v-clicks>

</div>

---
layout: default
---

# 常见电容器

<div class="grid grid-cols-3 gap-3 mt-4">

<div class="card text-center">
  <img src="/images/陶瓷.png" alt="陶瓷电容器" class="placeholder-img" />
  <div class="font-semibold text-lg mt-2">陶瓷电容器</div>
  <div class="text-sm opacity-50 mt-1">固定电容 · 电路板常用</div>
</div>

<div class="card text-center">
  <img src="/images/电解.png" alt="电解电容器" class="placeholder-img" />
  <div class="font-semibold text-lg mt-2">电解电容器</div>
  <div class="text-sm opacity-50 mt-1">固定电容 · 大容量 · 有极性</div>
</div>

<div class="card text-center">
  <img src="/images/可变.png" alt="可变电容器" class="placeholder-img" />
  <div class="font-semibold text-lg mt-2">可变电容器</div>
  <div class="text-sm opacity-50 mt-1">旋转调节 · 改变正对面积</div>
</div>

</div>

---
layout: default
---

# 充电与放电

<CapacitorCircuit />

---
layout: default
---

# 充放电过程

<div class="grid grid-cols-2 gap-4 mt-4">

<div class="card">
  <div class="flex items-center gap-2 mb-3"><mdi-battery-charging class="text-accent-2 text-xl" /><span class="font-semibold text-lg">充电</span></div>
  <v-clicks>

  <div class="mb-2 text-sm">与正极相连的极板<span class="text-red-400">失去电子 → 带正电</span></div>
  <div class="mb-2 text-sm">与负极相连的极板<span class="text-accent-2">获得电子 → 带负电</span></div>
  <div class="mb-2 text-sm">两极板间<span class="text-accent">电势差逐渐升高</span>，直至等于电源电压</div>

  </v-clicks>
</div>

<div class="card">
  <div class="flex items-center gap-2 mb-3"><mdi-lightbulb-on-outline class="text-accent text-xl" /><span class="font-semibold text-lg">放电</span></div>
  <v-clicks>

  <div class="mb-2 text-sm">用导线连接两极板</div>
  <div class="mb-2 text-sm">电子从<span class="text-accent-2">负极板</span>经导线流向<span class="text-red-400">正极板</span></div>
  <div class="mb-2 text-sm">两极板电荷逐渐中和，<span class="text-accent">电势差降低</span></div>

  </v-clicks>
</div>

</div>

<v-click>

<div class="card card-highlight mt-4 text-center">
  <span class="text-accent font-semibold">电荷守恒</span>：充电不是创造电荷，放电不是消灭电荷——只是<span class="text-accent-2">转移</span>
</div>

</v-click>

---
layout: default
---

# 电容器能较好地储存电荷么？

<div class="card card-highlight mt-4">
  <div class="text-xl font-semibold"><mdi-head-question-outline class="text-accent text-2xl inline-block align-middle mr-3" />如果将充好电的电容器的<span class="text-red-400">负极接地</span>，电荷会流失吗？
  </div>
</div>

<v-clicks>

<div class="card mt-2 px-3 py-1.5">
  <div class="text-sm opacity-70">负极板上的负电荷与正极板上的正电荷<span class="text-accent">相互吸引</span>，正极板上的电荷被"拉住"，几乎不会流入大地。</div>
</div>

<div class="card mt-2 px-3 py-1.5">
  <div class="text-sm opacity-70">把导线、电阻等其他元件接在正极和负极两端，只要<span class="text-accent-2">不构成闭合回路</span>，相互靠近的两极板间吸引力仍会“留住”电荷。</div>
</div>

<div class="card card-highlight mt-2 px-3 py-1.5 text-center text-base">
  <span class="text-accent font-semibold">相互靠近</span> + <span class="text-accent-2 font-semibold">彼此绝缘</span> → 储存电荷的必要条件
</div>

</v-clicks>

---
layout: default
---

# 电容器的带电量

<div class="card card-highlight">
  <div class="text-xl font-semibold">
    充放电是电荷在两极板之间<span class="text-accent">转移</span>——那么，电容器的带电量如何计算？
  </div>
</div>

<v-click>

<div class="card card-highlight mt-3 text-center">

当一个极板带电量为 $+Q$ 时，另一个极板必然带电 $-Q$

<div class="text-lg">

电容器的带电量 = <span class="text-accent-2 font-bold">一个极板带电的绝对值</span> Q

</div>

</div>

</v-click>

---
layout: default
---

# 充电与放电中的能量转化

<div class="grid grid-cols-2 gap-3 mt-4">

<div class="card">
  <div class="text-xl font-semibold mb-3 text-center">充电</div>
  <v-clicks>

  <div class="mb-1">两极板间<span class="text-red">电势差逐渐增大</span></div>
  <div class="mb-1">电势差 → 电源电压</div>
  <div class="card-highlight rounded-lg p-2 mt-2 text-center">
    电容器<span class="text-red font-semibold">吸收</span>电能
  </div>

  </v-clicks>
</div>

<div class="card">
  <div class="text-xl font-semibold mb-3 text-center">放电</div>
  <v-clicks>

  <div class="mb-1">两极板间<span class="text-blue">电势差逐渐减小</span></div>
  <div class="mb-1">电流通过用电器</div>
  <div class="card-highlight rounded-lg p-2 mt-2 text-center">
    电容器<span class="text-blue font-semibold">释放</span>电能
  </div>

  </v-clicks>
</div>

</div>

<v-click>

<div class="card card-highlight mt-4 text-center font-semibold text-lg">
  电容器，在储存<span class="text-accent">电荷</span>的同时，还存储了<span class="text-accent">能量</span>
</div>

</v-click>

---
layout: default
---

# 什么是"本领"？

<div class="grid grid-cols-2 gap-4 mt-4">

<div class="card !py-3">
  <div class="text-lg font-semibold"><mdi-help-circle-outline class="text-accent text-xl inline-block align-middle mr-1" />质量是什么的"本领"？</div>
  <div v-click class="text-sm opacity-70 mt-2">

改变物体运动状态<span class="text-accent">难易</span>的本领

$m = \dfrac{F}{a}$

  </div>
</div>

<div class="card !py-3">
  <div class="text-lg font-semibold"><mdi-help-circle-outline class="text-accent text-xl inline-block align-middle mr-1" />电阻是什么的"本领"？</div>
  <div v-click class="text-sm opacity-70 mt-2">

阻碍电流通过<span class="text-accent">难易</span>的本领

$R = \dfrac{U}{I}$

  </div>
</div>

</div>

<v-click>

<div class="text-2xl font-semibold mt-6 text-center"><mdi-help-circle-outline class="text-accent-2 text-2xl inline-block align-middle mr-2" />那么，电容器的本领是什么？</div>

</v-click>

<v-click>

<div class="card card-highlight mt-3 text-center max-w-md mx-auto !py-3">
  <div class="text-xl font-semibold"><mdi-flash-outline class="text-accent text-2xl inline-block align-middle mr-2" /><span class="text-accent">储存电荷</span>的能力</div>
</div>

</v-click>

---
layout: default
transition: slide-left
preload: false
---

<div class="text-center mt-10">

<div
  v-motion
  :initial="{ opacity: 0, scale: 0.8, y: 40 }"
  :enter="{ opacity: 1, scale: 1, y: 0 }"
>

<mdi-help-circle-outline class="text-accent text-6xl mb-4" />

</div>

<div
  v-motion
  :initial="{ opacity: 0, y: 30 }"
  :enter="{ opacity: 1, y: 0, transition: { delay: 200 } }"
  class="text-4xl font-bold leading-relaxed mt-4"
>如何衡量各电容器的本领？</div>

<div
  v-motion
  :initial="{ opacity: 0, y: 20 }"
  :enter="{ opacity: 1, y: 0, transition: { delay: 400 } }"
  class="mt-6 text-lg opacity-60"
>

<mdi-account-group-outline class="text-accent-2 text-xl mr-1" /> 请根据充放电过程的分析，讨论并尝试设计一个实验方案。

</div>

</div>

---
layout: default
---

<ChargeSharingCircuit />

---
layout: default
---

# 实验结论

<v-clicks>

- 同一个电容器，Q 与 U 的比值是一个<span class="text-accent font-semibold">定值</span>

- 不同电容器，这个比值<span class="text-accent-2 font-semibold">不同</span>

</v-clicks>

---
layout: default
---

<WaterBucketAnalogy />

<v-click>

<div class="grid grid-cols-2 gap-4 mt-4">

<div class="card text-center">
  <div class="card-highlight rounded-lg p-2">

$V = S \cdot h$

</div>
</div>

<div class="card text-center">
  <div class="card-highlight rounded-lg p-2">

$Q = ? \cdot U$

</div>
</div>

</div>

</v-click>

---
layout: default
---

# 电容

<v-clicks>

<div class="font-bold text-xl mt-2">1. 定义</div>

<div class="text-lg mt-1">

电容器所带电荷量 Q 与两极板间电势差 U 的比值，反映电容器储存电荷的本领。

</div>

<div>

<div class="my-2 border-t border-slate-700/50"></div>

<span class="font-bold text-xl">2. 定义式：</span> $C = \dfrac{Q}{U}$

</div>

<div>

<div class="my-2 border-t border-slate-700/50"></div>

<span class="font-bold text-xl">3、单位：</span> 法拉，简称 **法**，符号 $F$，&ensp;$1\,\text{F} = 1\,\text{C/V}$

</div>

</v-clicks>

<v-click>

<div class="grid grid-cols-2 gap-3 mt-3">
  <div class="text-sm">

  $1\,\mu\text{F} = 10^{-6}\,\text{F}$ &nbsp;<span class="opacity-50">微法</span>

  </div>
  <div class="text-sm">

  $1\,\text{pF} = 10^{-12}\,\text{F}$ &nbsp;<span class="opacity-50">皮法</span>

  </div>
</div>

</v-click>

---
layout: default
---

# 课堂练习

<div class="card card-highlight mt-4">

<div class="text-xl font-semibold mb-3">

一个电容器的某一极板带电量为 $-2\times10^{-4}\,\text{C}$，两极板间电势差为 $10\,\text{V}$，它的电容是多少？

</div>

</div>

<v-click>

<div class="card mt-4">

**解：** 由 $C = \dfrac{Q}{U}$，其中 Q 为一个极板带电的绝对值，得

<div class="text-center text-xl mt-2">

$C = \dfrac{2\times10^{-4}}{10} = 2\times10^{-5}\,\text{F} = 20\,\mu\text{F}$

</div>

</div>

</v-click>
---
layout: default
---

<div class="flex flex-col gap-4">

<div v-click.left class="chat-msg chat-left">
  电容器上标的电压是什么？
</div>

<div v-click.right class="chat-msg chat-right">
  <span class="chat-em">额定工作电压</span>—— 电容器正常工作的最大电压
</div>

<div v-click.left class="chat-msg chat-left mt-3">
  从闪电中，我们能得到什么启示？
</div>

<div v-click.right class="chat-msg chat-right">
  云层与大地 → 天然电容器<br/>
  电势差过大 → 空气<span class="text-red-400 font-semibold">击穿</span> → 闪电放电
</div>

</div>

---
layout: default
---

# 工作电压与击穿电压

<v-clicks>

<div class="font-bold text-xl mt-2">1. 额定工作电压</div>

<div class="text-lg mt-1">

电容器上标注的电压——电容器<span class="text-accent font-semibold">长期、可靠工作</span>的最大电压。
</div>

<div>
<div class="my-3 border-t border-slate-700/50"></div>
<div class="font-bold text-xl">2. 击穿电压</div>
</div>

<div class="text-lg mt-1">

介质所能承受的<span class="text-red-400 font-semibold">极限电压</span>。超过此值，介质被<span class="text-red-400 font-semibold">击穿</span>——形成导电通路，不可逆损伤导致电容器永久损坏。

</div>

<div class="text-lg mt-2">

因此，额定工作电压 &lt; 击穿电压，留有余量，确保电容器<span class="text-accent font-semibold">稳定运行</span>。

</div>

</v-clicks>

---
layout: default
transition: slide-left
preload: false
---

<div class="text-center mt-10">

<div
  v-motion
  :initial="{ opacity: 0, scale: 0.8, y: 40 }"
  :enter="{ opacity: 1, scale: 1, y: 0 }"
>

<mdi-lightbulb-on-outline class="text-accent text-6xl mb-4" />

</div>

<div
  v-motion
  :initial="{ opacity: 0, y: 30 }"
  :enter="{ opacity: 1, y: 0, transition: { delay: 200 } }"
  class="text-3xl font-bold leading-relaxed mt-4"
>电容——电容器的储存本领——<br/>和什么有关？</div>

<div
  v-motion
  :initial="{ opacity: 0, y: 20 }"
  :enter="{ opacity: 1, y: 0, transition: { delay: 400 } }"
  class="mt-6 text-lg opacity-60"
>

<mdi-account-group-outline class="text-accent-2 text-xl mr-1" /> 以最简单的平行板电容器为例，展开小组讨论。

</div>

</div>

<div class="cover-decoration abs-br m-6" aria-hidden="true">
  <svg viewBox="0 0 200 140" width="200" xmlns="http://www.w3.org/2000/svg">
    <line x1="30" y1="60" x2="85" y2="60" stroke="currentColor" stroke-width="3" stroke-linecap="round"/>
    <rect x="85" y="8" width="10" height="104" rx="5" fill="none" stroke="currentColor" stroke-width="3"/>
    <rect x="105" y="8" width="10" height="104" rx="5" fill="none" stroke="currentColor" stroke-width="3"/>
    <line x1="115" y1="60" x2="170" y2="60" stroke="currentColor" stroke-width="3" stroke-linecap="round"/>
    <line x1="50" y1="20" x2="50" y2="100" stroke="currentColor" stroke-width="1.5" stroke-dasharray="6 4" opacity="0.35"/>
    <line x1="150" y1="20" x2="150" y2="100" stroke="currentColor" stroke-width="1.5" stroke-dasharray="6 4" opacity="0.35"/>
    <text x="100" y="134" text-anchor="middle" style="font-size:13px" fill="#94a3b8" opacity="0.7">平行板电容器</text>
  </svg>
</div>

---
layout: default
---

<ParallelPlateCapacitor />

---
layout: default
---

# 平行板电容器的电容

<div class="card card-highlight">

平行板电容器的电容由<span class="text-accent">结构参数</span>决定：

$$C = \frac{\varepsilon_r S}{4\pi k d}$$

<div class="grid grid-cols-3 gap-3 mt-3 text-sm opacity-70">
  <div>

$S$ — 极板正对面积

  </div>
  <div>

$d$ — 极板间距

  </div>
  <div>

$\varepsilon_r$ — 介质相对介电常数

  </div>
</div>

</div>

<v-click>

<div class="grid grid-cols-3 gap-3 mt-3">

<div class="text-center text-sm px-2 py-1.5 rounded-lg border border-slate-700/50 bg-slate-900/30">

$S$ <mdi-arrow-up-bold class="text-accent inline-block text-base" style="vertical-align: -0.15em" /> <span class="text-accent mx-1">→</span> C <mdi-arrow-up-bold class="text-accent inline-block text-base" style="vertical-align: -0.15em" />

</div>

<div class="text-center text-sm px-2 py-1.5 rounded-lg border border-slate-700/50 bg-slate-900/30">

$d$ <mdi-arrow-up-bold class="text-accent inline-block text-base" style="vertical-align: -0.15em" /> <span class="text-accent mx-1">→</span> C <mdi-arrow-down-bold class="text-accent inline-block text-base" style="vertical-align: -0.15em" />

</div>

<div class="text-center text-sm px-2 py-1.5 rounded-lg border border-slate-700/50 bg-slate-900/30">

$\varepsilon_r$ <mdi-arrow-up-bold class="text-accent inline-block text-base" style="vertical-align: -0.15em" /> <span class="text-accent mx-1">→</span> C <mdi-arrow-up-bold class="text-accent inline-block text-base" style="vertical-align: -0.15em" />

</div>

</div>

</v-click>



---
layout: center
class: text-center
---

# 课堂小结

<div class="grid grid-cols-2 gap-3 mt-3 text-left leading-snug">

<div class="card px-3 py-1.5">
  <div class="font-semibold text-lg mb-0.5">一、电容器</div>

  <div class="text-sm opacity-80">

  定义：<span class="text-accent font-semibold">相互靠近</span> + <span class="text-accent-2 font-semibold">彼此绝缘</span> 的导体

  </div>
  <div class="text-sm opacity-50 mt-0.5">—— 能够储存电荷的容器</div>

</div>

<div class="card px-3 py-1.5">
  <div class="font-semibold text-lg mb-0.5">二、电容</div>

  <div class="text-sm mt-0.5">

  $C = \dfrac{Q}{U}$，反映储存电荷的本领

  </div>
  <div class="text-sm opacity-50 mt-0.5">

  单位：法拉（$F$），常用 $\mu\text{F}$、$\text{pF}$

  </div>
</div>

</div>

<div class="card mt-2 px-3 py-1.5">

<div class="font-semibold text-lg mb-0.5">三、平行板电容器的电容</div>

$$C = \frac{\varepsilon_r S}{4\pi k d}$$

<div class="grid grid-cols-3 gap-3 mt-2 text-sm opacity-70">
  <div>

$S$ — 极板正对面积

  </div>
  <div>

$d$ — 极板间距

  </div>
  <div>

$\varepsilon_r$ — 介质相对介电常数

  </div>
</div>

</div>
---
layout: default
---

# 课后作业

<div class="grid grid-cols-2 gap-3 mt-2 text-[10px]">

<div>

**平行板电容器连接电源时（U 不变）**：

| | $C$ | $Q$ | $\varphi$ | $E_p$ | $E$ |
|---|---|---|---|---|---|
| $S \uparrow$ | <span v-click class="text-red-400 font-bold">↑</span> | <span v-click class="text-red-400 font-bold">↑</span> | <span v-click class="opacity-40">—</span> | <span v-click class="text-red-400 font-bold">↑</span> | <span v-click class="opacity-40">—</span> |
| $d \uparrow$ | <span v-click class="text-blue-400 font-bold">↓</span> | <span v-click class="text-blue-400 font-bold">↓</span> | <span v-click class="opacity-40">—</span> | <span v-click class="text-blue-400 font-bold">↓</span> | <span v-click class="text-blue-400 font-bold">↓</span> |
| $\varepsilon_r \uparrow$ | <span v-click class="text-red-400 font-bold">↑</span> | <span v-click class="text-red-400 font-bold">↑</span> | <span v-click class="opacity-40">—</span> | <span v-click class="text-red-400 font-bold">↑</span> | <span v-click class="opacity-40">—</span> |

</div>

<div>

**已经充电的平行板电容器（Q 不变）**：

| | $C$ | $U$ | $\varphi$ | $E_p$ | $E$ |
|---|---|---|---|---|---|
| $S \uparrow$ | <span v-click class="text-red-400 font-bold">↑</span> | <span v-click class="text-blue-400 font-bold">↓</span> | <span v-click class="text-blue-400 font-bold">↓</span> | <span v-click class="text-blue-400 font-bold">↓</span> | <span v-click class="text-blue-400 font-bold">↓</span> |
| $d \uparrow$ | <span v-click class="text-blue-400 font-bold">↓</span> | <span v-click class="text-red-400 font-bold">↑</span> | <span v-click class="text-red-400 font-bold">↑</span> | <span v-click class="text-red-400 font-bold">↑</span> | <span v-click class="opacity-40">—</span> |
| $\varepsilon_r \uparrow$ | <span v-click class="text-red-400 font-bold">↑</span> | <span v-click class="text-blue-400 font-bold">↓</span> | <span v-click class="text-blue-400 font-bold">↓</span> | <span v-click class="text-blue-400 font-bold">↓</span> | <span v-click class="text-blue-400 font-bold">↓</span> |

</div>

</div>