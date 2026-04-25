---
title: "Hexo + GitHub Pages 学术博客搭建：从零到部署 | Building an Academic Blog"
date: 2026-04-22 13:00:00
tags: [Hexo, GitHub, 教程, Academic]
categories: 技术
cover: /img/default.png
abstract: 本文系统性地介绍基于 Hexo 静态站点生成器与 GitHub Pages 服务搭建个人学术博客的技术方案，涵盖环境配置、主题定制、自动化部署等关键环节。
---

## 摘要 | Abstract

本文系统性地介绍基于 Hexo 静态站点生成器与 GitHub Pages 持续部署服务搭建个人学术博客的完整技术方案。内容涵盖开发环境配置、主题选型与定制、学术化样式改造以及基于 GitHub Actions 的自动化部署流水线搭建等关键环节，旨在为有类似需求的研究者提供可复现的技术参考。

## 1. 引言 | Introduction

在学术研究与技术交流中，个人博客作为一种轻量级的知识发布平台，具有建设成本低、维护便捷、内容自主可控等优势。相较于 WordPress 等传统内容管理系统，基于静态站点生成器（Static Site Generator, SSG）的方案在安全性、性能与部署灵活性方面具有显著优势 [1]。

Hexo 是一款基于 Node.js 的开源静态博客框架，以其丰富的主题生态和简洁的 Markdown 写作工作流著称。配合 GitHub Pages 的免费托管与 GitHub Actions 的持续集成能力，可以构建一套零成本、全自动的学术博客系统。

## 2. 相关工作 | Related Work

目前主流的静态站点生成方案包括：

- **Hugo** — 基于 Go 语言，构建速度极快，但模板语法学习曲线较陡
- **Jekyll** — GitHub Pages 原生支持，Ruby 生态成熟，但构建速度较慢
- **Next.js / Nuxt.js** — 全栈框架的 SSG 模式，功能强大但对纯博客场景过于复杂
- **Hexo** — Node.js 生态，主题丰富（如 Butterfly、Fluid），插件体系完善

本文选择 Hexo + Butterfly 主题的技术方案，主要基于其良好的中文学术社区支持与开箱即用的 KaTeX 数学公式渲染能力。

## 3. 方法 | Methodology

### 3.1 环境配置

系统要求：Node.js >= 18.0，npm 或 yarn 包管理器。

```bash
# 全局安装 Hexo CLI
npm install -g hexo-cli

# 初始化项目
hexo init blog && cd blog
npm install
```

### 3.2 主题安装与配置

Butterfly 主题可通过 npm 直接安装，无需手动克隆仓库：

```bash
npm install hexo-theme-butterfly
```

在 `_config.yml` 中指定主题：

```yaml
theme: butterfly
```

### 3.3 学术化定制

为了赋予博客学术气质，需进行以下定制：

1. **元信息调整** — 设置正式的站点标题与描述
2. **菜单结构** — 增加「研究」等学术导航项
3. **数学公式支持** — 启用 Butterfly 内置的 KaTeX 渲染引擎
4. **自定义样式** — 添加摘要区块、引用列表等学术排版元素

KaTeX 的启用仅需在 `_config.butterfly.yml` 中添加：

```yaml
math:
  use: katex
  per_page: true
  katex:
    copy_tex: true
```

### 3.4 自动化部署

利用 GitHub Actions 实现推送即部署的 CI/CD 流程：

```yaml
# .github/workflows/deploy.yml
name: Deploy
on:
  push:
    branches: [main]
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
      - run: npm install
      - run: npx hexo generate
      - uses: actions/deploy-pages@v4
```

## 4. 结果与讨论 | Results & Discussion

采用上述方案搭建的博客系统具有以下特征：

- **构建性能** — Hexo 在中等规模（< 100 篇文章）下的全站构建时间通常在数秒以内
- **部署效率** — GitHub Actions 工作流从触发到部署完成约需 1–2 分钟
- **维护成本** — 仅需关注 Markdown 内容创作，基础设施由 GitHub 托管

潜在的改进方向包括：引入评论系统（如 Giscus）、集成学术引用管理（如 DOI 自动解析）、以及支持 LaTeX 长文排版等。

## 5. 结论 | Conclusion

本文介绍了基于 Hexo + GitHub Pages + GitHub Actions 搭建个人学术博客的完整技术方案。该方案以零成本实现了从写作到部署的全流程自动化，适合作为研究者个人知识管理与学术交流的基础设施。

## 参考文献 | References

1. "Static Site Generator," *Wikipedia*, 2026. [Online]. Available: https://en.wikipedia.org/wiki/Static_site_generator
2. Hexo Official Documentation. [Online]. Available: https://hexo.io/docs/
3. hexo-theme-butterfly Documentation. [Online]. Available: https://butterfly.js.org/
4. GitHub Actions Documentation. [Online]. Available: https://docs.github.com/en/actions
