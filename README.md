# 电容器的电容 — 公开课交互式课件

> 高中物理公开课课件 | 人教版必修三 第十章第4节 | 东北育才学校
>
> 在线地址：**[http://capacity-learning.mister-hope.com/](http://capacity-learning.mister-hope.com/)**

---

## 项目简介

基于 [Slidev](https://sli.dev/) + Vue 3 构建的交互式公开课课件，面向大教室（50+ 人），课题《电容器的电容》。

**不是 PowerPoint 的平替——是用 Web 技术重新思考"什么是令人惊艳的课件"。** 玻璃态卡片、平滑页面过渡、4 个物理模拟交互组件、完整的授课逐字稿。

## 快速开始

```bash
pnpm install
pnpm dev
```

浏览器打开 `http://localhost:3030`，使用键盘 **← →** 或翻页笔翻页。

## 课件结构（24 页）

| 页码 | 内容                                | 类型                             |
| ---- | ----------------------------------- | -------------------------------- |
| 1    | 封面                                | 渐变标题 + 呼吸动画              |
| 2    | 引入：三种起电方式 → 如何储存电荷？ | 卡片 v-click                     |
| 3    | 电容器定义：相互靠近 + 彼此绝缘     | 定义卡片                         |
| 4    | 发散举例：地球/火星、云层/大地……    | MDI 图标卡片                     |
| 5    | 常见电容器：陶瓷、电解、可变        | 实物照片                         |
| 6    | 充电与放电交互演示                  | **CapacitorCircuit 组件**        |
| 7    | 充放电过程文字解析 + 电荷守恒       | 卡片 v-click                     |
| 8    | 为什么能保持电荷？（先问后答）      | 紧凑卡片                         |
| 9    | 电容器的带电量（$+Q$ / $-Q$）       | LaTeX 公式                       |
| 10   | 能量转化：充电 vs 放电              | 对比卡片                         |
| 11   | 什么是"本领"？（质量/电阻类比）     | 类比卡片                         |
| 12   | 提问：不同电容器储存本领？          | 动画过渡 + 小组讨论              |
| 13   | 电荷共享实验                        | **ChargeSharingCircuit 组件**    |
| 14   | 实验结论（Q/U 比值）                | v-click 列表                     |
| 15   | 水桶类比                            | **WaterBucketAnalogy 组件**      |
| 16   | 电容定义：$C = Q/U$                 | 三节逐条出现                     |
| 17   | 课堂练习（$20\,\mu\text{F}$）       | LaTeX 计算题                     |
| 18   | 电容器上标的电压？                  | 聊天气泡（4 次点击）             |
| 19   | 工作电压与击穿电压                  | v-click 逐条                     |
| 20   | 转折：电容和什么有关？              | 动画过渡 + 小组讨论              |
| 21   | 平行板电容器交互实验                | **ParallelPlateCapacitor 组件**  |
| 22   | 平行板电容器的电容公式              | $C = \varepsilon_r S/(4\pi k d)$ |
| 23   | 课堂小结                            | 两栏紧凑卡片                     |
| 24   | 课后作业（U/Q 不变动态分析）        | 两张表格 v-click                 |

## 交互式组件

| 组件                     | 功能                                           |
| ------------------------ | ---------------------------------------------- |
| `CapacitorCircuit`       | 充放电电路演示，电子流动画，实时 I-t 曲线      |
| `ChargeSharingCircuit`   | 电荷共享实验，S₁/S₂ 双刀开关，电压表读数       |
| `WaterBucketAnalogy`     | 水桶注水 ↔ 电容器充电同步动画，拖拽交互        |
| `ParallelPlateCapacitor` | 平行板电容器，S/d/U 三滑块，介质切换，实时 C/Q |

## 设计系统

- **玻璃态卡片**：`backdrop-filter: blur(16px)` + 半透明背景
- **深蓝渐变背景**：`#090d1a → #0c1122 → #0f1425`
- **暖金 `#e2a846`** + **深蓝 `#3b82f6`** 双强调色
- **基础字号 22px**，教室后排清晰可读
- **系统字体栈**（不依赖 Google Fonts）
- **聊天气泡系统**：左侧蓝色（提问）/ 右侧金色（回答）
- **封面渐变标题** + 亮度呼吸动画

## 技术栈

- [Slidev](https://sli.dev/) — 演示文稿框架
- Vue 3（Composition API + `<script setup>`）
- KaTeX — LaTeX 数学公式渲染
- MDI（Material Design Icons）— 图标系统
- UnoCSS — 原子化 CSS
- `pnpm` — 包管理

## 项目文件

```
slides.md              — 主课件（24 页 Markdown + HTML）
style.css              — 全局设计系统 + SVG 保护
global-top.vue         — 顶栏（章节↔课题切换 + Transition 动画）
global-bottom.vue      — 底栏（装饰线，无页码）
components/            — 4 个交互式 Vue 组件（自动注册）
public/                — 学校 logo、电容器实物照片
content/
  思路.md              — 课程设计思路
  教案.md              — 公开课教案
  逐字稿.md            — 24 页完整逐句台词
  电容器的电容-公开课教案.docx  — Word 版（供打印）
init-slidev-courseware/ — 课件创建规范（供其他教师复用）
```

## 构建与部署

```bash
pnpm build      # 输出到 dist/
pnpm export     # 导出 PDF（可选）
```

静态站点部署：`dist/` 目录可直接部署到任意静态服务器（Nginx、GitHub Pages、Vercel 等）。

## 许可

MIT
