---
title: 欢迎来到我的博客
published: 2026-10-18
tags: [公告, 随笔]
category: 博客
draft: false
---

# 欢迎来到我的博客

这是使用 [Fuwari](https://github.com/saicaca/fuwari)（基于 [Astro](https://astro.build/)）搭建的个人博客。

在这里我会记录技术学习、算法竞赛（信奥 / OI）相关内容，以及一些生活随笔。

## 如何写文章

文章存放在 `src/content/posts/` 目录下，采用 Markdown 格式，并在文件头部添加 Frontmatter：

```markdown
---
title: 文章标题
published: 2026-10-18
tags: [标签1, 标签2]
category: 分类
draft: false
---

正文内容……
```

把 `draft` 设为 `false` 即可发布。

## 本地预览

```bash
pnpm install
pnpm dev
```

访问 `http://localhost:4321` 即可实时预览。

祝你阅读愉快！
