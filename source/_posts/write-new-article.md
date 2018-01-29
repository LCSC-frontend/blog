---
title: '怎么在这里写文章'
author: tuffy
---
在我们博客写一篇新文章

<!-- more -->

# 拉取blog

1. 在你觉得喜欢的目录下，执行命令克隆 blog 代码
```
git clone https://github.com/LCSC-frontend/blog.git
```
2. 进入 blog 目录，安装相应的依赖包
```
cd blog
npm install
```

# 写文章

3. 在 blog/source/_posts/ 中新建 md 文件，于此处编写文章
4. 执行下面命令，可在本地进行更改，并在 localhost：4000 预览你修改的效果
```
hexo s
```
5. 写完之后执行命令生成静态文件：
```
hexo g
```
6. 将新生成的文件部署到 gitPage 上：
```
hexo d
```
7. 打开相应地址 [我们的博客](https://lcsc-frontend.github.io/) 即可看到最新的修改。（如果没有，刷新一下试试）
