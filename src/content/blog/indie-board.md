---
title: '从零构建 Indie Board：一次 Bleeding Edge 技术栈的探索之旅'
description: '记录使用 Nuxt 4 + UnoCSS + DaisyUI + Drizzle ORM 构建独立产品发现榜的完整过程，包括技术选型的反复探索和踩坑经验。'
pubDate: 'Dec 17 2025'
---

最近我完成了一个小项目 —— **Indie Board**（独立产品发现榜），一个极简的 Product Hunt 风格应用。这篇文章记录了整个开发过程，特别是在技术选型上的反复探索和踩坑经验。

> 🔗 项目地址：[🚀 极简独立产品发现榜 - Nuxt 4 + UnoCSS + Drizzle ORM](https://github.com/LouisLau-art/indie-board)

## 项目目标

构建一个极简的 Web 应用，用于展示和发现独立开发者的产品：

- 📋 产品列表（按点赞数排序）
- ➕ 提交新产品
- 👍 投票系统（防刷机制）
- 🔄 实时更新（轮询）
- 🌙 暗色模式

**技术要求**：使用"激进"的技术栈，能用最新版就用最新版。

---

## 第一阶段：技术选型

### 确定核心框架

这部分比较直接：

| 技术  | 选择                           | 版本            |
| --- | ---------------------------- | ------------- |
| 框架  | Nuxt                         | 4.2.2         |
| UI  | Vue                          | 3.5.25        |
| 数据库 | Better-SQLite3 + Drizzle ORM | 12.x + 0.45.x |
| 运行时 | Bun                          | latest        |

Vue 3.5 带来了两个很棒的特性：

- **Reactive Props Destructure** - 可以直接解构 props 并保持响应性
- **useTemplateRef** - 更优雅的模板引用方式

```vue
<script setup lang="ts">
// Vue 3.5+ Reactive Props Destructure
const { product } = defineProps<{ product: Product }>()

// Vue 3.5+ useTemplateRef
const titleInput = useTemplateRef<HTMLInputElement>('titleInput')
</script>
```

### CSS/UI 方案的纠结

这里开始了漫长的探索之旅...

**初始想法**：使用 UnoCSS（原子化 CSS 引擎）。

UnoCSS 是 Vue 生态的产物，由 Anthony Fu 开发，比 Tailwind CSS 更轻量、更快。于是我选择了：

```ts
// uno.config.ts - 初始配置
export default defineConfig({
  presets: [
    presetUno(),
    presetAttributify({ prefix: 'un-' }),
    presetIcons({ scale: 1.2 }),
  ],
})
```

---

## 第二阶段：UI 组件库的反复横跳

### 尝试 1：手写组件 + UnoCSS Shortcuts

最开始我用 UnoCSS 的 `shortcuts` 功能手写了所有组件：

```ts
shortcuts: {
  'btn': 'px-4 py-2 rounded-lg font-medium transition-all duration-200',
  'btn-primary': 'btn bg-emerald-500 hover:bg-emerald-600 text-white',
  'card': 'bg-white dark:bg-gray-800 rounded-2xl shadow-lg p-5',
  'input': 'w-full px-4 py-3 rounded-xl border border-gray-200',
}
```

**问题**：功能能用，但 UI 看起来很"素"，缺乏精心设计的细节。

### 尝试 2：Naive UI

我提出想用 UI 组件库来避免"造轮子"。于是我尝试了 [Naive UI](https://www.naiveui.com/)：

```json
{
  "naive-ui": "^2.43.2",
  "@bg-dev/nuxt-naiveui": "^2.0.0"
}
```

**优点**：

- Vue 3 原生，TypeScript 完美支持
- 16k+ GitHub stars，社区活跃
- 使用 CSS-in-JS，与 UnoCSS 无冲突

**但我有顾虑**：想要更纯粹的 UnoCSS 生态方案，而且Naive UI可能有点太重量级了。

### 尝试 3：Onu UI

发现了 [Onu UI](https://github.com/onu-ui/onu-ui) —— 专为 UnoCSS 设计的组件库：

```json
{
  "onu-ui": "^1.1.5",
  "@onu-ui/preset": "^1.1.5"
}
```

**踩坑**：启动报错！

```
ERROR  Cannot convert from keyword to hex
```

原因是 `@onu-ui/preset` 的颜色处理与 UnoCSS 66.x 不兼容。这个库虽然声称活跃，但实际上对最新版 UnoCSS 的支持有问题。

### 尝试 4：又回到手写组件

经过一番折腾，暂时回到了 UnoCSS shortcuts + 手写组件的方案。

**这时候用户说了一句关键的话**：

> "折腾来折腾去还是回到了原点，如果我们用了 git 来管理版本，是不是 rollback 会很方便？"

是的！这提醒了我立即初始化 Git 并提交一个稳定版本：

```bash
git init
git add -A
git commit -m "feat: Indie Board with Nuxt 4 + UnoCSS + Drizzle ORM"
```

有了 Git 备份，就可以放心地继续实验了。

### 尝试 5：DaisyUI（最终方案）

我想试试 [DaisyUI](https://daisyui.com/)。DaisyUI 是基于 Tailwind CSS 的语义化组件库，但有社区维护的 UnoCSS preset。

```json
{
  "@ameinhardt/unocss-preset-daisy": "^1.1.8",
  "daisyui": "^4.12.24"
}
```

**又踩坑了**：最初我用了 DaisyUI 5.x，结果报错：

```
ERROR  addVariant is not a function
```

**原因**：DaisyUI 5.x 使用了 Tailwind CSS 4 的新 API，但 `@ameinhardt/unocss-preset-daisy` 还没跟进更新。

**解决方案**：降级到 DaisyUI 4.12.24（4.x 的最新版）。

```ts
// uno.config.ts - 最终配置
import { presetDaisy } from '@ameinhardt/unocss-preset-daisy'

export default defineConfig({
  presets: [
    presetUno({ dark: 'class' }),
    presetDaisy({ themes: ['emerald', 'forest'] }),
    presetIcons({ scale: 1.2 }),
  ],
})
```

**终于成功了！** DaisyUI 提供了漂亮的组件类：

```html
<div class="card bg-base-100 shadow-xl">
  <div class="card-body">
    <button class="btn btn-primary">提交产品</button>
  </div>
</div>
```

---

## 第三阶段：修复细节问题

### 字体问题

DaisyUI 的 `emerald` 主题使用了 serif 字体，导致英文显示为衬线体，与中文不协调。

**解决方案**：在全局样式中覆盖主题字体：

```css
html {
  font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', 
    'PingFang SC', 'Hiragino Sans GB', 'Microsoft YaHei', sans-serif;
}

:root {
  --font-sans: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', 
    'PingFang SC', 'Hiragino Sans GB', 'Microsoft YaHei', sans-serif;
}
```

### TypeScript 类型问题

Nitro 的 `defineNitroPlugin` 报 TypeScript 错误。

**原因**：Nuxt 的类型是动态生成在 `.nuxt/types` 目录的，需要运行 `nuxi prepare` 后才能识别。

**解决方案**：

1. 运行 `bunx nuxi prepare`
2. 在 IDE 中重启 TypeScript Server

---

## 经验教训

### 1. 先用 Git，再实验

在尝试新技术之前，**一定要先提交一个稳定版本**。这样即使实验失败，也能轻松回滚。

```bash
# 创建实验分支
git checkout -b experiment/try-new-ui

# 实验成功 → 合并
git checkout main && git merge experiment/try-new-ui

# 实验失败 → 删除
git checkout main && git branch -D experiment/try-new-ui
```

### 2. "Bleeding Edge" 有代价

使用最新版依赖听起来很酷，但：

- **生态兼容性问题**：第三方库可能还没跟进
- **文档滞后**：官方文档可能还没更新
- **社区支持有限**：遇到问题难以搜到解决方案

**建议**：核心框架可以用最新版，但辅助库（如 UI 组件库的 preset）要检查兼容性。

### 3. 不要执着于"完美"的技术栈

我在 UI 方案上反复横跳了 5 次：

1. 手写 UnoCSS → 太素
2. Naive UI → 不够"纯粹 UnoCSS"
3. Onu UI → 兼容性问题
4. 又手写 → 回到原点
5. DaisyUI → 最终方案

回头看，其实 Naive UI 完全能满足需求。过度追求"技术纯粹性"反而浪费了时间。

### 4. 版本号的陷阱

我在配置 `@ameinhardt/unocss-preset-daisy` 时写错了版本号（写成了 `^8.0.1`，实际最新版是 `^1.1.8`）。

**教训**：手写版本号之前，先用 `npm view <package> version` 确认最新版本。

---

## 最终技术栈

| 类别     | 技术             | 版本      |
| ------ | -------------- | ------- |
| 框架     | Nuxt           | 4.2.2   |
| UI 框架  | Vue            | 3.5.25  |
| CSS 引擎 | UnoCSS         | 66.5.10 |
| UI 组件  | DaisyUI        | 4.12.24 |
| ORM    | Drizzle ORM    | 0.45.1  |
| 数据库    | Better-SQLite3 | 12.5.0  |
| 运行时    | Bun            | latest  |

---

## 总结

这次项目最大的收获不是最终的代码，而是在技术选型上的探索过程。

**记住几个关键点**：

1. ✅ 用 Git 管理实验，放心大胆尝试
2. ✅ 检查第三方库对最新版的支持情况
3. ✅ 不要为了"技术纯粹性"牺牲开发效率
4. ✅ 版本号要查证，不要凭印象

最后，项目地址：[🚀 极简独立产品发现榜 - Nuxt 4 + UnoCSS + Drizzle ORM](https://github.com/LouisLau-art/indie-board)

欢迎 Star ⭐ 和 Fork！

---

**感谢阅读！** 🚀
