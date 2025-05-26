---
layout: post
title:  "为站点添加分页功能"
date:   2025-05-25 7:30:34 +0800
categories: site pagination
---

[Jekyll](https://jekyllrb.com/) 的内置主题 minima 默认没启用分页功能，所以如果文章多就会使得页面拉长，不利于浏览。要启用分页功能，需要按照 Jekyll 文档操作：

## 1 启用分页（pagniation）功能

1. 启用 `jekyll-paginate` 插件。此插件在 Jekyll 2 中是内置标准插件，但在 Jekyll 3 中已经移除内置，需要自行添加——在 Gemfile 和 `_config.yml` 的 `plugins` 中分别添加；
2. 并在在`_config.yml` 中通过添加 `paginate: 10` 来启用分页功能；
3. 在 `_config.yml` 中定义分页目标页 `paginate_path: "/blog/page:num/"`

## 2 启用分页

1. 将分页页面存储为 `.html` 后缀，也就是将分页页面保存为 HTML，只有 HTML 页面才能使用分页功能；
2. 在分页页面使用 Liquid 脚本语言调用分页参数；

代码如下：

      <!-- This loops through the paginated posts -->
      {% for post in paginator.posts %}
        <h1><a href="{{ post.url }}">{{ post.title }}</a></h1>
        <p class="author">
          <span class="date">{{ post.date }}</span>
        </p>
        <div class="content">
          {{ post.content }}
        </div>
      {% endfor %}

      <!-- Pagination links -->
      <div class="pagination">
        {% if paginator.previous_page %}
          <a href="{{ paginator.previous_page_path }}" class="previous">
            Previous
          </a>
        {% else %}
          <span class="previous">Previous</span>
        {% endif %}
        <span class="page_number ">
          Page: {{ paginator.page }} of {{ paginator.total_pages }}
        </span>
        {% if paginator.next_page %}
          <a href="{{ paginator.next_page_path }}" class="next">Next</a>
        {% else %}
          <span class="next ">Next</span>
        {% endif %}
      </div>
