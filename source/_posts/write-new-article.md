---
title: '怎么在这里写文章'
---
写一篇新文章

<!-- more -->

# 拉取blog

1. 在你觉得喜欢的目录下，执行命令克隆 blog 代码
```
git clone https://github.com/LCSC-frontend/blog.git
```
2. 进入blog目录，安装相应的依赖包
```
cd blog
npm install
```
3. 执行下面命令，可在本地进行更改，并在 localhost：4000 预览你修改的效果
```
hexo s
```
4. 写完之后执行命令生成静态文件：
```
hexo g
```
5. 将新生成的文件部署到gitPage上：
```
hexo d
```
6. 打开相应地址 [我们的博客](https://lcsc-frontend.github.io/) 即可看到最新的修改。（如果没有，刷新一下试试）
