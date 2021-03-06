---
title: 使用Hexo搭建博客
date: 2017-03-21 00:51:38
tags: [Hexo, 博客]
categories: 技术
comments: true
---

最近在整理笔记的时候，发现OneNote跟EverNote这样的工具都不够方法，主要是排版，引用，语法强调跟层次结构上面很难满足需求，后来又看了简书之类的平台，本来是为了找一个写技术文档的地方的，最后发现了用Hexo来建立自己的博客的方式，在满足自己需求的前提下，还能够拥有自己的专属域名，觉得很cool，就研究了一下。


Hexo是一款基于Node.js的静态博客框架，所谓的静态博客，就是说文章是build成一个个的静态页面，而不是存在数据库里面动态加载。

![](hexo_xmind.png)

上面这张图给出了利用Hexo搭建自己的博客所涉及到的几个方面, 归纳起来就是下面几个问题：


>(1). 如何搭建博客？
>(2). Host在哪里？
>(3). 怎么发布？
>(4). 域名管理
>(5). 代码管理 


# 如何搭建网站：使用Hexo #

使用Hexo来搭建一个博客系统，包含下面几个部分的工作：


## Hexo环境配置 ##

[Hexo](http://hexo.io/zh-cn/) 是基于Node.js的，安装好Node以后，直接通过npm就可以安装Hexo以及相关的扩展。然后因为是Hexo是通过Git来发布信息的，所以Git，以及Github（或者其他的替代产品，e.g.国内的BitBucket，CitCafe）的账号也是必须的。

准备工作：
```
(1). 安装Node，并通过npm安装Hexo
(2). 安装Git并且注册GitHub账号 
```
*Note: 直到后面真正发布网站之前，Git其实是并不需要的，为了不引起一些不必要的疑惑，这里讲跳过Git相关的具体内容。*

Node 下载并安装好了以后，在命令行输入
```
$ npm --version

```
测试一下 npm 是不是装好了（npm 是node 用来管理各种package的工具，类似于Visual Studio里面的nuget，关于npm包管理工具的详细信息，可以参考[npm 模块安装机制简介](http://www.ruanyifeng.com/blog/2016/01/npm-install.html)）。

一切OK以后，然后就可以安装Hexo了。真正安装的时候，比想象中要简单得多的多，命令行里面输入下面的命令就搞定了：
```
$ npm install -g hexo-cli
```
安装好以后，输入
```
$ hexo
```
看是否出现Commands的提示，有就说明一切正常，可以进行下一步了

## 新建一个Blog网站 ##

安装 Hexo 完成后，请执行下列命令，Hexo 将会在指定文件夹中新建所需要的文件。

```
$ hexo init <folder>
$ cd <folder>
$ npm install
```
经过这三个简单的命令，一个Hex的博客网站就建成了。默认情况下，会安装一个landscape的主题，和一篇hello world的文章。

## 进行本地测试 ##
在命令行里面输入
```
$ hexo s
```
就可以启动一个本地的service
```
$ hexo s
INFO  Start processing
INFO  Hexo is running at http://localhost:4000/. Press Ctrl+C to stop.

```
在浏览器输入[localhost:4000](http://localhost:4000/) (4000是默认端口)就可以看到下面的网站首页了

<div align="center">
<img src="hello_world.png" width="80%" align="center">
</div>

完成以后，会生成一个Hexo项目的文件夹，名字就是前面 *init* 命令参数里面的*folder*，各个文件夹的含义参考[官方网站说明](https://hexo.io/zh-cn/docs/setup.html)。

## 往Blog里面添加一篇新的文章##

当测试环境调通以后，下面就尝试自己添加一篇blog文章吧，方法也是超级简单，只需要执行下面的命令就可以了
```
$ hexo new <title>
```
然后打开 [/source/_posts]() 就会发现一个 *title.md* 命名的文件，例如我的title是MyFirstBlog,于是就是生产一个名为 MyFirstBlog.md 文件，还有一个同名文件夹用来存放一些图片之类的资源文件供文章引用。

```
|--souce
  |--_posts
     |--MyFirstBlog
     |--MyFirstBlog.md
```
打开 MyFirstBlog.md，可以看到如下的内容

<div align="center">
<img src="fisrt_blg.png" width="80%" align="center">
</div>

文件最上方用 --- 隔开的部分叫做 Front-matter，用来定义文件本身的一些属性，名字，日期什么的，还可以配置其他的Catagory,Tag之类的信息，具体内容可以参考 [官方文档Front-matter](https://hexo.io/zh-cn/docs/front-matter.html) 章节。

剩下的部分就是正文了，用 Markdown 写文章成了这几年的一种主流方式，个人感觉是一种非常简洁的方式，语法比较简单，解释转换出来的效果也很干净，看起来觉得很舒服，或者说很专业，有点早年用Latex写论文的感觉，但是要容易很多。关于 Markdown语法，网上有很多的资料介绍，搜索 *“为什么要用 Markdown 写文章”* 就会出来很多介绍文章，这里就不详细展开了。当然，任何事情都有两面性，Markdown 也有它不足的地方，比如图片的引用，[反思Markdown：Markdown的长与短](https://sspai.com/post/37340) 里面总结的很好，可以花几分钟时间看看。

需要说明的是，用Markdown写文档，并不是像Word一样所见即所得的，所以需要有专门的编辑器来进行一定的解释转换，晚上工具很多，到底哪个好也是看个人喜好，尝试了好几种的工具以后，偶然发现我软的 VS Code 可以支持 Markdown，个人就深深的喜欢上了。

到这里为止，我们已经能够完整的在本地用Hexo搭建一个自己的博客了，Hexo还有很多其他的扩展功能，比如说
```
--设置Blog的主题
--启用Catalog/Tag
--添加第三方Comments插件
```
为了不会因为走的太远而打断思路，这些扩展功能将放在后面进行介绍。在完成了本地环境的搭建以后，我们接下来要做的，是将这个Blog搬到真正可以puglic访问的网络上。

# Host在哪里：Cloud 或者Git Pages #

## Git Pages ##

## Git ##

如何注册GitHub以及建立Repository,可以参考 [20分钟教你使用hexo搭建github博客](http://www.jianshu.com/p/e99ed60390a8),。

在最开是的需求里面，Git 是用来将生成的静态网站内容同步到GitHib上去的, 所以这里需要注册GitHub账号并且下载客户端, 实际上, 我们最会用到两个Git Repository, 一个用来上传Build出来的静态网站, 另外一个这是用来管理我们自己整个Hexo的project, 以便于我们可以随时的在不同的电脑上面进行修改. 其中的关系可以用下面的图来表示：

[//]:![](git_repo.png)>
<div align="center">
<img src="git_repo.png" width="80%" align="center">
</div>

需要注意的是，在create repository的时候，名字必须是{username}.github.io, 也就是GitHub默认分配给你账号的域名。详细原因在后面讲Deploy的章节来解释


## 第三方Cloud Service ##


# 如何发布：deploy到git repository #


# 域名管理 #

## 购买域名 ##
## 解析绑定域名到自己的Service ##

# 代码管理 #

## 新建一个Git Repository或者 Branch ##
## Project 结构： 那些需要同步 ##

# 一些扩展功能的介绍 #

## 扩展功能：设置Blog的主题Theme ##
## 扩展功能: Catalog/Tag ##
## 扩展功能：第三方Comments插件的使用 ##

# 存在的问题 #


## Markdown语法高亮支持的语言列表 ##
Markdown可以在引用的 block 里面指定代码 highlight 时候使用的语言，
```csharp
// begin code block with " ```csharp " to specify language as C#
using System;

namespace Demo
{
  public class DemoClass: IHighlight
  {
    private string _language;

    public string GetLanguage()
    {
      return this._language;
    }
  }

}
// end code block with " ``` "
```

支持的全部语言列表参见 [Syntax highlighting in markdown] (https://support.codebasehq.com/articles/tips-tricks/syntax-highlighting-in-markdown)


# Refrence #
[npm 模块安装机制简介](http://www.ruanyifeng.com/blog/2016/01/npm-install.html)
[Hexo官方网站](https://hexo.io/zh-cn/)
[Markdown 中文文档](https://markdown-zh.readthedocs.io/en/latest/blockelements/)
[Markdown 简单的世界](https://wizardforcel.gitbooks.io/markdown-simple-world/content/)
[Xmind 官方网站](http://www.xmind.net/)
[让思路更清晰——我是怎样使用思维导图的](https://blog.coding.net/blog/coding-mindmap)
[iPic + Typora，方便快捷地在 Markdown 中插图](https://sspai.com/post/36275)
[如何在 Markdown 注释一段文字](http://www.jianshu.com/p/9be87e7e15bf)
[MarkDown使用小技巧](http://www.jianshu.com/p/9d94660a96f1)
[MarkDown编辑器 Typora](https://typora.io/)
[反思Markdown：Markdown的长与短](https://sspai.com/post/37340)
