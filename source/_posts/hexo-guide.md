---
title: Hexo + GitHub Pages 博客搭建指南
date: 2026-04-22 13:00:00
tags: [Hexo, GitHub, 教程]
categories: 技术
cover: /img/default.png
---

## 前言

使用 Hexo 搭建个人博客并部署到 GitHub Pages，完全免费且维护方便。

## 安装

```bash
npm install -g hexo-cli
hexo init blog
cd blog
npm install
```

## 写作

```bash
hexo new "文章标题"
hexo clean && hexo generate
hexo server  # 本地预览
```

## 部署

通过 GitHub Actions 自动构建和部署，push 即上线。

## 总结

Hexo 生态成熟、主题丰富，配合 GitHub Pages 是搭建免费个人博客的最佳方案之一。
