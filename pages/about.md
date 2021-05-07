---
layout: page
title: About
description: 欢迎来到我的地盘
keywords: 小屁孩的梦, HKYZF
comments: true
menu: 关于
permalink: /about/
---

> 我是臭粑粑大朱。

> 写程序就像画画，艺术家大部分的时间其实都是在构图，思考，真正用画笔接触画布的时间其实占比很小。



**康师傅送的几句话：**

- 学习的思维方式：
  - 大处着眼，小处着手
  - 逆向思维，反证法
  - 透过现象看本质
- 两句话：
  - 小不忍则乱大谋
  - 识时务者为俊杰



## 联系方式

<ul>
{% for website in site.data.social %}
<li>{{website.sitename }}：<a href="{{ website.url }}" target="_blank">@{{ website.name }}</a></li>
{% endfor %}
{% if site.url contains 'mazhuang.org' %}
<li>
微信公众号：<br />
<img style="height:192px;width:192px;border:1px solid lightgrey;" src="{{ assets_base_url }}/assets/images/qrcode.jpg" alt="闷骚的程序员" />
</li>
{% endif %}
</ul>

## 技能

{% for skill in site.data.skills %}
### {{ skill.name }}
<div class="btn-inline">
{% for keyword in skill.keywords %}
<button class="btn btn-outline" type="button">{{ keyword }}</button>
{% endfor %}
</div>
{% endfor %}
