---
title: Firefly部署Cloudflare Workers流程及优选
published: 2026-07-01
description: 记录给自己看的教程
image: ./images/17.jpg
tags:
  - Firefly部署
  - Cloudflare
category: 博客指南
draft: false
lang: ""
---
## 需要准备的东西
1. 开魔法上网，减少等待的时间少一些麻烦。
2. [Git - Downloads (git-scm.com)](https://git-scm.com/downloads)：用于管理本地代码并与 GitHub 仓库同步。
3. [Node.js — Run JavaScript Everywhere (nodejs.org)](https://nodejs.org/en)：Firefly 基于 Node.js，因此需要先安装它。
4. 一个 [GitHub](https://github.com/) 账号：用于创建仓库并托管 Firefly 项目文件。
5. 一个 [Cloudflare](https://cloudflare.com/) 账号：用于创建 Pages 项目并绑定域名。
6. [黑曜石（Obsidian）](https://2x.nz/posts/obsidian/)：这是一个可视化 Markdown 编辑器。由于 Firefly 的文章和页面都以 Markdown 文件保存，准备一个顺手的编辑器会方便很多。
7. 下载[Visual Studio Code](https://code.visualstudio.com/)用于修改各种配置同时使用内置多开终端
## 流程图

本地部署Firefly，编写文章 -> 推送更改到远程Github仓库 -> Cloudflare Pages检测到仓库更新自动构建新的网站静态文件 -> 网站成功更改
## 首先，在本地部署 Firefly

1. Fork仓库：
    [https://github.com/CuteLeaf/Firefly](https://github.com/CuteLeaf/Firefly)
2. 打开Visual Studio Code
3. 然后将仓库克隆到本地：`git clone <你的仓库URL>`。如果可以使用 SSH，后续推送通常会更方便。
4. 先全局安装 `pnpm`：`npm install -g pnpm`。如果 npm 在国内下载较慢，可以尝试切换镜像源，例如 [npmmirror 镜像站](https://npmmirror.com/)。
5. 接着在项目根目录安装依赖：`pnpm install` 和 `pnpm add sharp`。
6. 完成以上步骤后，Firefly 就已经成功部署到本地了。
## 修改基础信息
### 站点配置
 配置文件`src/config/siteConfig.ts`
 
- title：站点标题
- subtitle：站点副标题
- site_url：站点 URL
```
export const siteConfig: SiteConfig = {
  title: "He",
  subtitle: "和",
  site_url: "https://blog.131231.xyz",
  description: "Firefly 是一款基于 Astro 框架...",
  keywords: ["Firefly", "Astro", "博客"],
  lang: "zh_CN",
};
```
favicon：站点图标
图片放到根目录`public/favicon`内
```
favicon 配置
            src: "/favicon/zdtb.jpg",

```
### 个人资料

个人资料配置控制侧边栏中的用户资料卡片，包括头像、名字、签名和社交链接。

配置文件`src/config/profileConfig.ts`

- avatar：头像
- name：名字
- bio：签名
- links：社交链接列表
### 更多配置
[官方文档](https://docs-firefly.cuteleaf.cn/zh/guide/getting-started.html)
## 写文章
使用 黑曜石（Obsidian）和Visual Studio Code
- 首先，在项目根目录执行：`pnpm new-post <这里填写你的文章标题>`
- 然后，在根目录下的 `src/content/posts` 文件夹中会多出一个 `xxx.md`文件
- 打开这个文件后，你会看到一组基础元数据，其中有几项最需要关注。
	- title：文章标题
	- published：文章创建时间
	- description：文章描述，正常会显示在文章标题下面
	- image：文章封面图（同目录需要写 `./` 如：`./images/xxx.png`）
	- tag：文章标签
	- categories：文章分类
## 本地预览，然后发布到Github
当你觉得文章写得差不多时，可以在项目根目录执行：`pnpm dev`。稍等片刻后，就能在本地预览博客效果。
- 接下来就可以使用 Git 把这些改动发布到 GitHub。
	- 首先，让 Git 知道你是谁：`git config --global user.name "你的GitHub用户名"` 和 `git config --global user.email "你的GitHub邮箱@example.com"`
	- 然后，将远程仓库地址改成 SSH 形式（如果你本来就是通过 SSH 克隆的，就不需要再修改）：`git remote set-url origin git@github.com:xxx/xxx`
	- 随后，提交所有变更：`git add .`
	- 接着，创建一次本地提交：`git commit -m "项目初始化"`
	- 最后，把本地更改推送到远程仓库：`git push`
```
git add .
git commit -m "项目初始化"
git push
```
- 完成后，你的 GitHub 仓库里就应该能看到新的提交记录了。
## 在Cloudflare部署Firefly
 - 前往Cloudflare的 Workers 和 Pages 页面，点击创建Worker
 - 然后选择Github，选择Firefly的仓库，随后设置构建命令：`pnpm build` 
 - 点进Firefly，在域里添加路由，选择你都域名，修改成`blog.131231.xyz/*`顺便关闭自带的两个Worker URL
 - 到DNS记录里添加一条CNAME名称填blog，目标填优选过域名
 - 访问添加到路由里的域名即可访问你的博客！
 - 随后，你只需要在本地编写文章，然后[使用Git将更改推送到远程仓库](https://2x.nz/posts/fuwari/#%E6%9C%AC%E5%9C%B0%E9%A2%84%E8%A7%88%E7%84%B6%E5%90%8E%E5%8F%91%E5%B8%83%E5%88%B0github)，Cloudflare就会自动部署，更新你的博客！