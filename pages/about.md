---
layout: page
title: About
description: 探索、思考、进取
keywords: Fangfang Hu, 胡方方
comments: true
menu: 关于
permalink: /about/
---

我是胡方方，人生寂寞是一种力量。

人经得起寂寞，就能获得自由。

耐不住寂寞，就会受人牵制。

## 联系

{% for website in site.data.social %}
* {{ website.sitename }}：[@{{ website.name }}]({{ website.url }})
{% endfor %}

## Skill Keywords

{% for category in site.data.skills %}
### {{ category.name }}
<div class="btn-inline">
{% for keyword in category.keywords %}
<button class="btn btn-outline" type="button">{{ keyword }}</button>
{% endfor %}
</div>
{% endfor %}
