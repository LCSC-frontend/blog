---
title: '怎么在这里写文章'
tags: [hexo, new article]
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

# 编辑和上传

3. 在 blog/source/_posts/ 中新建 md 文件，于此处编写文章(注意：新建的 md 文件名中不带中文)
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
7. 将修改 push 到 github 上：
```
git add .
git commit -m "xxx"
git push
```
8. 打开相应地址 [我们的博客](https://lcsc-frontend.github.io/) 即可看到最新的修改。（如果没有，刷新一下试试）

# 文章基本格式
```
---  // 文章的开头都有这个部分，用来写文章title，文章的标签，文章作者。用“---”隔起来
title: xxx
tags: [tag1, tag2, tag3]  // 为文章打上标签，之后可以在“标签选项中看到”
author: tuffy
---
xxxxx  // 在“---”和“<!-- more -->”之间的是文章的摘要，首页部分显示的是这里的内容，如果不写<!-- more -->会整篇文章都显示在首页

<!-- more -->  // 以下是正文
...
```
如果要在文章中放图片的话，在 themes/next/source/upload 中新建你文章的文件夹（一般取跟文章同名）在里面放这篇文章用到的图片，而文章中则以路径 “/upload/article-name/img-name.PNG” 去引用。

