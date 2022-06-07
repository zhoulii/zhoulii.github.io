---
title: 基于 Hexo + Github Pages + Github Actions 搭建个人博客
date: 2022-06-06 22:00:00
updated: 2022-06-06 22:00:00
tags: ["hexo","github pages","github actions"]
categories:
  - other
---

## 01.初始化仓库

```shell
npm install hexo-cli -g
hexo init zhoulii.github.io
cd zhoulii.github.io
git init
git add .
git commit -m "initialize zhoulii.github.io"
git remote add origin https://github.com/zhoulii/zhoulii.github.io.git
git push origin master
```

## 02.配置 Github Actions

在仓库中创建 `.github/workflows/pages.yml`，具体内容如下：

```shell
name: deploy zhoulii.github.io  
  
on:  
  push:  
    branches:  
      - master
  
jobs:  
  pages:  
    runs-on: ubuntu-latest  
    steps:  
      - uses: actions/checkout@v2  
      - name: Use Node.js 16.x  
        uses: actions/setup-node@v2  
        with:  
          node-version: '16'  
      - name: Cache NPM dependencies  
        uses: actions/cache@v2  
        with:  
          path: node_modules  
          key: ${{ runner.OS }}-npm-cache  
          restore-keys: |  
            ${{ runner.OS }}-npm-cache  
      - name: Install Dependencies  
        run: npm install  
      - name: Build  
        run: npm run build  
      - name: Deploy  
        uses: peaceiris/actions-gh-pages@v3  
        with:  
          github_token: ${{ secrets.GITHUB_TOKEN }}  
          publish_dir: ./public
```
需要注意两点：

- `node` 版本需要与本地机器一致（可使用 `node --version` 查看）
- 配置中的变量不需要手动指定

将 `.github/workflows/pages.yml` 同步到远程仓库，点击仓库主页的 `Actions` 标签，可以看到 `workflow` 的执行情况。

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/github-actions-hexo.png)

`workflow` 执行完成后，仓库会多出一个名为 `gh-pages` 的分支，`Hexo` 生成的静态网站文件即在该分支下。

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/gh-pages-branch.png)

## 03.配置 Github Pages

`Hexo` 生成的静态网站文件在  `gh-pages`  分支下，接下来配置 `Github Pages` 从 `gh-pages`  分支构建站点：

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/github-pages-hexo.png)

配置完成后，稍等个一两分钟，网站就可以访问了，网站首页如下：

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/hello-world-hexo.png)

## 04.参考资料

- [GitHub Pages | Hexo](https://hexo.io/docs/github-pages.html)
- [peaceiris/actions-gh-pages: GitHub Actions for GitHub Pages](https://github.com/peaceiris/actions-gh-pages)
- [博客之旅：从 Jekyll 到 Hexo](https://www.jianshu.com/p/eadeddb792c5)
