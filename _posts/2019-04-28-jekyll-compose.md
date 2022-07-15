---
title: Jekyll 之笔
date: 2019-04-28 08:50 +0800
---

在 Jekyll 中使用命令简化写作。

## 安装

在应用的 Gemfile 中添加：

    gem 'jekyll-compose', group: [:jekyll_plugins]

然后执行：

    $ bundle

## 用法

安装完毕之后（如上），运行 `bundle exec jekyll help` ，会看到：

通过帮助会列出可提供命令：

```sh
  draft      # 新建指定名字的文章草稿
  post       # 新建指定名字的文章
  publish    # 将草稿发布（将文章由草稿文件夹移至文章文件夹）并且设定日期
  unpublish  # 将文章移至草稿文件夹
  page       # 新建指定名字的页面
```

创建你的新页面：

    $ bundle exec jekyll page "My New Page"

创建新文章：

    $ bundle exec jekyll post "My New Post"

创建文章草稿：

    $ bundle exec jekyll draft "My new draft"

发布草稿文章：

    $ bundle exec jekyll publish _drafts/my-new-draft.md
    # 或者可以指定发布日期
    $ bundle exec jekyll publish _drafts/my-new-draft.md --date 2014-01-24

撤回文章：

    $ bundle exec jekyll unpublish _posts/2014-01-24-my-new-draft.md

## 配置

要定制插件默认配置，编辑 jekyll 配置文件中 `jekyll_compose` 部分。

### 自动在编辑器中打开新草稿或者文章

```yaml
  jekyll_compose:
    auto_open: true
```

确认你已经设置 `EDITOR` 或者 `JEKYLL_EDITOR` 变量。
例如，如果你想要使用 Atom 编辑器默认可以打开新建的文章或者草稿，可以添加下面设置到你的 shell 配置中：
```
export JEKYLL_EDITOR=atom
```

后面设置优先，会覆盖前面的 `EDITOR` 值。

### 设置草稿或者文章的默认前端值

如果你希望在新建文章或者草稿的时候会添加一些默认的前端值，那么就可以设置 `draft_default_front_matter` 和 `post_default_front_matter` 的配置，例如：

```yaml
  jekyll_compose:
  draft_default_front_matter:
    description:
    image:
    category:
    tags:
  post_default_front_matter:
    description:
    image:
    category:
    tags:
    published: false
    sitemap: false
```

这些也可以自动添加：
 - `date` 属性设置时间戳。
 - `title` 属性设置标题


