---
layout: post
title: 本月日常与新学的Django框架笔记
date: 2020-05-30
categories: [笔记]
author: Kingfish404
tags: [笔记]
comments: true
toc: true
desc: 好久没写博客了，现在来补一下
---

## Daily

好几个月没有写博客了，期间经历的最大的事情可能就是新冠疫情了吧，本来以为几个月就会过去了，没想到一直持续到今天，国内还好，国外已经有几百万的确诊病例了，我其实挺期待开学的，想念学校的南湖图书馆了。

<!-- more -->

在家里待着的确相比学校更安全，也节约了挺多通勤的时间，但是随着时间的推移，我的学习效率也开始下降了，家里还是没法提供一个比得上学校的环境让我好好学习，唉。

> 人往往等到失去后才懂得珍惜

## Django框架笔记

上个月匆匆忙忙组了一个队伍，参加计算机设计大赛，因此临时学了1天的Django框架，做出了一个Demo，并且居然幸运的通过了校赛，不过感觉省赛不会那么的顺利了。

### Django简介

Django是一个基于Python的Web框架，只需要一条命令就可以安装好，提供了许多开箱即用的工具

```shell
pip install django
# 或者 python3 -m pip instal django
```

使用Django建立新的项目，下面命令会建立一个**mystie**目录

```shell
django-admin startproject mysite
# django-admin startproject [项目名]
# Django默认的项目树
#    manage.py
#    mysite/
#       __init__.py
#       settings.py
#       urls.py
#       asgi.py
#       wsgi.py
```

启动Django项目，默认是开发者模式启动，需要在setting.py里修改DEBUG的值并添加ALLOWED_HOSTS来以生产模式部署

```shell
python manage.py runserver
# python manage.py runserver [可选端口]
# python manage.py runserver 8080
# python manage.py runserver 0:8000
```

为Django项目添加新的应用

```shell
python manage.py startapp polls
# python manage.py startapp [应用名]
```

更多Django相关的笔记，见我的另外一篇博客。

最后编辑于 2020-10-16

## REF

* [Django官方文档](https://docs.djangoproject.com/)