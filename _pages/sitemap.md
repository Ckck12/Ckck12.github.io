---
layout: archive
title: "Sitemap"
title_ko: "사이트맵"
title_zh: "网站地图"
permalink: /sitemap/
author_profile: true
---

{% include base_path %}

<p><span class="lang-en">A list of all the pages on this site. An <a href="{{ base_path }}/sitemap.xml">XML version</a> is also available.</span><span class="lang-ko" lang="ko">이 사이트의 전체 페이지 목록입니다. <a href="{{ base_path }}/sitemap.xml">XML 버전</a>도 있습니다.</span><span class="lang-zh" lang="zh-Hans">本站全部页面列表。另有 <a href="{{ base_path }}/sitemap.xml">XML 版本</a>。</span></p>

<h2><span class="lang-en">Pages</span><span class="lang-ko" lang="ko">페이지</span><span class="lang-zh" lang="zh-Hans">页面</span></h2>
{% for post in site.pages %}
  {% include archive-single.html %}
{% endfor %}

{% capture written_label %}'None'{% endcapture %}

{% for collection in site.collections %}
{% unless collection.output == false or collection.label == "posts" %}
  {% capture label %}{{ collection.label }}{% endcapture %}
  {% if label != written_label %}
  {% case label %}{% when "publications" %}{% assign label_en = "Publications" %}{% assign label_ko = "논문" %}{% assign label_zh = "论文" %}{% when "portfolio" %}{% assign label_en = "Projects" %}{% assign label_ko = "프로젝트" %}{% assign label_zh = "项目" %}{% else %}{% assign label_en = label %}{% assign label_ko = label %}{% assign label_zh = label %}{% endcase %}
  <h2><span class="lang-en">{{ label_en }}</span><span class="lang-ko" lang="ko">{{ label_ko }}</span><span class="lang-zh" lang="zh-Hans">{{ label_zh }}</span></h2>
  {% capture written_label %}{{ label }}{% endcapture %}
  {% endif %}
{% endunless %}
{% for post in collection.docs %}
  {% unless collection.output == false or collection.label == "posts" %}
  {% include archive-single.html %}
  {% endunless %}
{% endfor %}
{% endfor %}
