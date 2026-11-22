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

推送到 `main` 分支后，GitHub Actions 自动构建并发布到 `gh-pages` 分支；在仓库 Settings → Pages 中选择 `gh-pages` 分支的 `/` 目录。

- 源码仓库：https://github.com/wangyifan/linux-blog
- 公网地址：https://wangyifan.github.io/linux-blog/
