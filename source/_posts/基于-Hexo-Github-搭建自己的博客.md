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


# 多地点管理博客

### 旧电脑上

- 首先在仓库 **yourname.github.io** 上新建一个分支用来存放博客的源文件。 `hexo d` 命令提交到master分支上的是站点的静态文件，在新的分支上，删除 `.git` 以外的所有文件夹。

- 将已有的Blog文件夹中的如下文件，拷贝到新的分支上，并提交至该分支。

  ```shell

  ├── _config.yml
  ├── package.json
  ├── scaffolds
  ├── source
  └── themes
      ├── clean-blog
      │   └── _config_clean-blog.yml
      └── yilia
          └── _config_yilia.yml
  # 为了不提交主题目录下的多余的文件
  # 我这里并没有改动主题里的源文件，只改动了_config.yml这个文件
  # 所以我只提交了yml配置文件。
  ```

### 新电脑上

- 克隆仓库 **yourname.github.io** 的分支上的内容到本地。当然新的电脑上，Node.js & Git & npm & hexo 的安装不可少

- 克隆主题的源文件到对应的目录下

- 执行下面的命令：

  ```shell
  npm install
  npm install hexo-deployer-git --save
  npm install hexo-server --save
  hexo clean && hexo g && hexo s # 本地访问测试
  ```

  ​