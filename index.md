---
title: Azure OpenAI 练习
permalink: index.html
layout: home
---

# Azure OpenAI 练习

以下练习旨在支持 [Microsoft Learn](https://learn.microsoft.com/training/browse/?terms=OpenAI) 上的模块。


{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Labs'" %} {% for activity in labs  %}
- [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {% endfor %}
