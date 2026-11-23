# 我的个人博客

《Linux基础及应用》课程大作业，使用 Hugo + PaperMod 搭建，部署到 GitHub Pages。

## 本地运行

```bash
hugo server --bind 0.0.0.0 -D
```

浏览器访问 `http://<虚拟机IP>:1313`。

## 构建

```bash
hugo --minify
```

生成的静态文件位于 `public/`。

## 部署

推送到 `main` 分支后，GitHub Actions 自动构建并部署到 GitHub Pages（使用官方 Pages Actions，首次运行会尝试自动启用 Pages）。

- 源码仓库：https://github.com/wyf3052173070/linux-blog
- 公网地址：https://wyf3052173070.github.io/linux-blog/
