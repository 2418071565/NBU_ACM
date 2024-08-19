NBU ACM💭💡🎈
==========================

## **简介**

由宁波大学集训队编写，用于集训队队员学习分享算法知识。网站使用[MkDocs](https://github.com/mkdocs/mkdocs) 和 [Material for MkDocs](https://github.com/squidfunk/mkdocs-material?tab=readme-ov-file) 部署在 [github pages](https://pages.github.com/)。

[网页链接](https://2418071565.github.io/NBU_ACM/)

## **如何提交博客？**

### **前置知识**

首先你需要了解以下内容：

- 基本的命令行使用经验

- git 版本控制工具

- markdown 标记语言

关于上面的工具可以参考以下网站：

[B站教程](https://www.bilibili.com/video/BV1u94y1n73L/?spm_id_from=333.337.search-card.all.click&vd_source=0de771c86d90f02a6cab8152f6aa173f)

[Markdown 官方教程](https://markdown.com.cn/basic-syntax/)

[git 官方文档](https://git-scm.com/book/zh/v2)

这里基本了解 git 的基本用法即可，会基本的 `add`、 `commit` 和 `push`，能处理冲突即可。Markdown 本身比较简单，语法不太懂的时候去看一下文档既可以了。

### **搭建本地环境**

首先我们要确定本地有 `python` 环境：

```
$ python3 --version
Python 3.11.2
```

没有 `python` 环境的参考教程：[Python安装与环境配置](https://blog.csdn.net/m0_57081622/article/details/127180996)，安装 3.10 版本以上的 python。

之后就是安装 `MkDocs`：

```
$ pip install mkdocs
```

确定是否安装成功：

```
$ mkdocs --version
mkdocs, version 1.5.3 from C:\Users\86155\AppData\Local\Programs\Python\Python311\Lib\site-packages\mkdocs (Python 3.11)
```

下一个安装 `Material for MkDocs`

```
$ pip install mkdocs-material
```

上面某一步有问题的，可以联系学长。如果都没问题就可以将项目 `clone` 本地了：

```
$ git clone https://github.com/2418071565/NBU_ACM.git
```

在本地编写你的博客，编写完成后，使用 `git` 命令 `push` 到 github 仓库即可。

在本地你可以使用 `mkdocs serve` 在本地部署网站，查看你的实时修改，运行命令后，在浏览器中打开网址 `127.0.0.1:8000`。

### **博客编写**


编写博客要修改的只有两个地方，首先在 `/docs` 目录下的对应知识分类中创建对应的文件夹及对应的文件，我们以 **字典树** 为例，我们会在 `/docs/string/Trie/` 路径下创建 `Trie.md` 文件，我们的博客就在该文件中编写。

想在我们的网址中渲染对应的页面，就要修改文件 `mkdocs.yml`，你需要 `nav` 导航项中添加你博客的标题（知识点名字）及对应的文件路径。还是以 **字典树** 为例，我们会添加如下内容：

```
nav:
  - 字符串:
    - 字典树: String/Trie/Trie.md
```

一级缩进是页面最上面的导航栏，二级缩进是页面左侧的导航栏，三级缩进会在左侧对应项添加子导航栏，可以在本地尝试一下，摸索一下。

完成这两步就可以正常的进行博客编写了。

### **一些规范**

为了增加文档的可读性，这里对文档提出以下几点规范：


- 每个 markdown 文档中，**不能存在一级标题**，每个大标题只能从二级标题开始，因为使用一级标题会覆盖文档页头的标题。且标题最多不要超过 4 级。**标题字体要加深**


- 英文和中文间要加空格，公式和文字间也要加空格

- 公式要使用 Latex 编写

- 文章最后尽量给出参考的博客或文章

