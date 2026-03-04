# Claude Code × Token 省法 — Instagram 卡片项目

## 项目概述

这是一个单文件 HTML 项目，用于生成 8 张 Instagram 卡片（1080×1350px），供导出为 PNG。
主文件：`claude-code-ig-posts.html`

## 技术结构

单文件 HTML，包含：
- 内联 CSS（约 400 行）
- 内联 JS（约 350 行）
- Base64 编码的图标 PNG（4 个图案，嵌入 `ICONS` 数组）
- 外部依赖：Google Fonts、html2canvas（CDN）

## 卡片尺寸

- 预览尺寸：324 × 405px
- 导出尺寸：1080 × 1350px（`SCALE = 1080 / 324`）

## 8 张卡片 ID

| ID  | 内容                    |
|-----|-------------------------|
| #p1 | 封面（Cover）           |
| #p2 | 引入（Problem）         |
| #p3 | 总览（Overview）        |
| #p4 | 习惯一（Chat 聊清楚）   |
| #p5 | 习惯二（CLAUDE.md）     |
| #p6 | 习惯三（先计划再执行）  |
| #p7 | 习惯四（/clear）        |
| #p8 | 总结（Summary）         |

## 配色系统

### CSS 变量（两套主题）

**经典暗色（theme-classic）**
```css
--bg: #1a1814; --tp: #f5f0e8; --tm: #b0a898; --tb: #8a7e72; --ac: #f5c84a; --at: #1a1200;
```

**暖调（theme-warm）**
```css
--warm-cover-bg: #D87858;  /* 橙色封面 */
--warm-stmt-bg:  #BCD1CA;  /* 鼠尾草绿 */
--warm-bg:       #E3D9CD;  /* 沙色 */
/* 卡片背景色 #FAF9F5 */
```

主题切换：`setTheme('classic')` / `setTheme('warm')`
每张卡片通过 `.bg-orange` / `.bg-sage` / `.bg-sand` 指定背景

## 图案系统（Doodle System）

### 4 个图案（存储在 `ICONS` 数组）

- `wave` — 波形线
- `cloud` — 思维云（含箭头和气泡）
- `network` — 三节点网络图
- `refresh` — C + 波浪尾

图案来源：用户提供的黑底白线 JPG，Python PIL 抠图提取为透明 PNG，Base64 嵌入。

### 3 种颜色样式

| 类名     | 效果                              |
|----------|-----------------------------------|
| `v-blob` | 黑色线条 + `#FAF9F5` 有机形背景  |
| `v-dark` | 纯黑色线条，无背景                |
| `v-light`| `#FAF9F5` 浅色线条，无背景       |

CSS filter 实现：
- `v-blob` / `v-dark`：`filter: invert(1) brightness(0)`（白→黑）
- `v-light`：`filter: brightness(0) invert(1) sepia(0.08) saturate(0.5)`

### Doodle 交互规则

- 默认层级：`z-index: 0`，在文字下方
- 悬浮时：`z-index: 2`，浮到文字上方可操作
- 文字层：`pointer-events: none`，鼠标可穿透触达下方图案
- 拖动：点击图案主体拖动
- 缩放：悬浮后显示四个角的圆点手柄，四个方向均可缩放
- 删除：悬浮后右上角红色 × 按钮

### 全局拖拽状态（重要）

使用单一全局对象 `_drag` + 单一 `document.mousemove` 监听器，避免多实例干扰：

```js
let _drag = null; // { el, mode, sx, sl, st, sw, sh, wrap, img, corner }
```

## 已知设计决策

1. **`overflow: visible`**：`.post` 不裁剪，否则四角缩放手柄会被截断
2. **`offsetWidth` 而非 `getBoundingClientRect()`**：滚出视口的卡片 `getBoundingClientRect()` 返回 0，用 `offsetWidth` 获取实际尺寸
3. **每个 `initDoodle` 不挂 `document` 监听器**：只在 `mousedown` 时写入 `_drag`，统一由顶层处理

## 字体

```
'Playfair Display' — 标题、大字
'Source Serif 4'  — 正文、说明
'DM Sans'         — 标签、UI 元素
```

## 下次优化方向（建议）

- [ ] 移动端触控支持（touch events）
- [ ] 图案旋转功能
- [ ] 支持上传自定义图案（File API → Base64）
- [ ] 图案位置保存/恢复（localStorage）
- [ ] 更多图案素材
- [ ] 导出时自动隐藏所有操作手柄

## 沟通习惯（给 Claude 的提示）

用户偏好：
- 简洁直接，不要过多解释
- 修改时只动需要改的地方，不要重写整个文件
- 先理解清楚再动手，有歧义主动问
- 视觉/交互需求请用："元素 + 默认状态 + 交互后状态 + 边界条件" 格式描述
