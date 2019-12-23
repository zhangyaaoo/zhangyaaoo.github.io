---
title: 基于 Hexo & Github 搭建自己的博客
date: 2019-12-18 19:43:06
tags: Hexo
categories: Hexo
---

# Install Node.js & Git & npm

```shell
sudo apt install nodejs git npm
# 安装版本查看
node --version
git --version
npm --version

```

# Install Hexo

```shell
sudo npm install hexo-cli -g
# 查看安装的版本
hexo --version
```

# Starting...

### 初始化博客的工作目录

```shell 
hexo init Blog
# 本地检验
cd Blog; hexo s
# 浏览器输入 localhost:4000 测试 Ctrl+C 结束
```

### 新建一篇文章

```shell
# 尝试新建自己的第一篇文章
hexo n "基于-Hexo-GitHub-搭建自己的博客"
# 编辑 source/_posts/基于-Hexo-GitHub-搭建自己的博客.md
hexo clean
hexo g
hexo s
# 浏览器输入 localhost:4000 本地测试， Ctrl+C 结束
```

### 部署自己博客到GitHub

- `sudo npm install --save hexo-deployer-git` 安装hexo部署功能的插件

- 创建 yourname.github.io 的仓库

- 修改配置文件 `_config.yml` 

  ```shell
  # Deployment
  ## Docs: https://hexo.io/docs/deployment.html
  deploy:
    type: git
    repo: https://github.com/zhangyaaoo/zhangyaaoo.github.io.git
    branch: master
  ```

- `hexo d` 部署到GitHub

- `zhangyaaoo.github.io` 访问测试

### 修改主题

 - 下载主题 `git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia` 

 - 修改博客配置文件 `_config.yml` 

   ```shell
   # Extensions
   ## Plugins: https://hexo.io/plugins/
   ## Themes: https://hexo.io/themes/
   theme: yilia
   ```

- `hexo clean` 

- `hexo g` 

- `hexo s` 浏览器输入 localhost:4000 本地测试， Ctrl+C 结束

- `hexo d` 部署到GitHub 

