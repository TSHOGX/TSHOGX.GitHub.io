---
title: hexo & GitHub Pages
mathjax: true
date: 2019-04-07 17:39:13
tags: 笔记
categories: 计算机
---
# 目录结构解析

## scaffolds
模板文件夹，新建文件时的默认填充内容

比如`front-matter`

## source
用户资源

除了`_post`，其他 `_` 开头的全部隐藏

Markdown和HTML会被解析并放到`public`中，其他被拷贝过去

## themes
主题文件夹

根据主题生成静态页面

# 命令行

`$ hexo new [layout] <title>` 新页面创建

文章layout就是scaffold中的模板，默认模板是在config文件中选择的。如果layout名词包含空格，需要加双引号

可以自己建立模板

| 布局  | 路径           |
| ----- | -------------- |
| post  | source/_posts  |
| page  | source         |
| draft | source/_drafts |

不被处理的文章：`layout: false`

`$ hexo g -d` generate and deply

`$ hexo publish [layout] <title>` 之前如果的new生成layout为drafts的文件，建立时被保存到`source/_drafts`中，现在可以通过publish移动到`_posts`中

## 写作
### Front-matter

以下是预先定义的参数，您可在模板中使用这些参数值并加以利用。

| 参数       | 描述                 | 默认值       |
| ---------- | -------------------- | ------------ |
| layout     | 布局                 |              |
| title      | 标题                 |              |
| date       | 建立日期             | 文件建立日期 |
| updated    | 更新日期             | 文件更新日期 |
| comments   | 开启文章的评论功能   | true         |
| tags       | 标签（不适用于分页） |              |
| categories | 分类（不适用于分页） |              |
| permalink  | 覆盖文章网址         |              |

### 分类和标签
只有文章支持分类和标签，您可以在 `Front-matter` 中设置。在其他系统中，分类和标签听起来很接近，但是在 Hexo 中两者有着明显的差别：分类具有顺序性和层次性，也就是说 `Foo, Bar` 不等于 `Bar, Foo`；而标签没有顺序和层次。
``` yaml
categories:
- Diary
tags:
- PS3
- Games
```
**分类方法的分歧**

如果您有过使用WordPress的经验，就很容易误解Hexo的分类方式。WordPress支持对一篇文章设置多个分类，而且这些分类可以是同级的，也可以是父子分类。但是Hexo不支持指定多个同级分类。下面的指定方法：
```yaml
categories:
- Diary
- Life
```
会使分类`Life`成为`Diary`的子分类，而不是并列分类。因此，有必要为您的文章选择尽可能准确的分类。



## 添加图片

**绝对路径**：放在`source/images`文件夹中，通过`![](/images/image.jpg)`访问

图片既可以在首页内容中访问到，也可以在文章正文中访问到。


**相对路径**：放在文章自己的目录中文章的目录可以通过配置_config.yml来生成。

```yaml
_config.yml
post_asset_folder: true
```

将`_config.yml`文件中的配置项`post_asset_folder`设为`true`后，执行命令`$ hexo new post_name`，在`source/_posts`中会生成文章`post_name.md`和同名文件夹`post_name`。将图片资源放在`post_name`中，文章就可以使用相对路径引用图片资源了。

```
_posts/post_name/image.jpg
![](image.jpg)
```
上述是`markdown`的引用方式，图片只能在文章中显示，但无法在首页中正常显示。

如果希望图片在文章和首页中同时显示，可以使用标签插件语法。
```
_posts/post_name/image.jpg
{% asset_img image.jpg This is an image %}
```

**CDN引用**：上传到[图床](https://share1223.com/free.html)



# 博客搭建以及在多电脑同步

1. 按照[hexo](https://hexo.io/zh-cn/)官网下载`Node.js`和`git`，然后下载hexo并按照他的说明在本地建立博客文件夹
2. 把本地的文件夹上传到`username.github.io`的`master`分支
3. 在该仓库新建`hexo`分支，在设置`->Settings->Branches->Default branch`中把默认分支换成`hexo`
4. 克隆hexo分支到本地，在终端进入此文件目录（删除原来的，保留`.git`文件夹），把本地博客文件夹中的所有文件copy到克隆的文件目录中（删除原目录中的`.git`文件夹，包括themes中的）
5. 把所有文件提交到`hexo`分支上，要修改deploy时只需要在此目录执行`hexo g -d`就行

同步：
* 在其他电脑克隆此仓库的hexo分支到本地；
* 进入此目录，执行`npm install`下载所需要的`node_modules`文件夹内的，然后就可以继续操作了

> 由于仓库有一个`.gitignore`文件，里面默认是忽略掉 `node_modules`文件夹的，也就是说仓库的`hexo`分支并没有存储该目录，所以需要`install`下

每次换电脑，需要`git pull`更新本地仓库

写完需要`git commit -m "message"`, `git push`，新加文件了需要先`git add .`再执行上面的
