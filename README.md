# 🚀 Louis's Tech Blog

我的个人技术博客，记录学习与开发过程中的心得体会。

**在线访问**: [louislau-art.github.io/tech-blog](https://louislau-art.github.io/tech-blog)

## ✨ 特性

- ⚡ **Astro 5** - 零 JS 开销的静态站点生成器
- 🎨 **Tailwind CSS v4** - 原子化 CSS 框架
- 🌙 **暗色模式** - 跟随系统 + 手动切换
- 💬 **Giscus 评论** - 基于 GitHub Discussions
- 🚀 **GitHub Pages** - 自动部署 (GitHub Actions)
- 📱 **响应式设计** - 移动端友好
- 📝 **Markdown/MDX** - 专注内容写作

## 📁 项目结构

```
tech-blog/
├── src/
│   ├── content/blog/     # 📝 博客文章 (.md)
│   ├── components/       # 🧩 Astro 组件
│   ├── layouts/          # 📐 页面布局
│   ├── pages/            # 🔗 路由页面
│   └── styles/           # 🎨 全局样式
├── public/               # 📦 静态资源
└── .github/workflows/    # ⚙️ 自动部署配置
```

## 🛠️ 常用命令

| 命令 | 说明 |
|------|------|
| `bun install` | 安装依赖 |
| `bun run dev` | 启动开发服务器 (localhost:4321) |
| `bun run build` | 构建生产版本 |
| `bun run preview` | 预览构建产物 |
| `bun run publish` | **一键发布** (git add + commit + push) |

## ✍️ 写作流程

1. 在 `src/content/blog/` 目录下创建新的 `.md` 文件
2. 添加 frontmatter:
   ```markdown
   ---
   title: '文章标题'
   description: '文章描述'
   pubDate: 'Dec 15 2025'
   ---
   ```
3. 用 Markdown 编写内容
4. 运行 `bun run publish` 一键发布
5. 等待 1-2 分钟自动部署完成

## 🔧 本地开发

```bash
# 克隆项目
git clone https://github.com/LouisLau-art/tech-blog.git
cd tech-blog

# 安装依赖
bun install

# 启动开发服务器
bun run dev
```

访问 http://localhost:4321/tech-blog/

## 📜 License

MIT License © 2025 LouisLau-art
