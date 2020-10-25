---
title: Github 블로그 구축하기 (2)
description: Github 블로그 구축부터 커스터마이징까지
categories:
 - github
tags:
---

> Github 블로그 구축하기 (2) - 커스터마이징

<!-- more -->

[1. Github 블로그 구축하기 (1) - Github 블로그 생성](https://heyitsspoon.github.io/tutorial/2020/10/23/github-blog-1/)
[2. Github 블로그 구축하기 (2) - 커스터마이징](https://heyitsspoon.github.io/tutorial/2020/10/25/github-blog-2/)

# 목차
[1. 목차](#목차)

[2. 구조 파악하기](#구조-파악하기)

[2.1 jekyll 디렉토리 구조](#jekyll-디렉토리-구조)

[2.2 liquid 언어란?](#liquid-언어란?)

[2.3 구조 분석하기](#구조-분석하기)

[3. 블로그 커스터마이징하기](#블로그-커스터마이징하기)

# 구조 파악하기
## jekyll 디렉토리 구조 [^1]
```
.
├── _config.yml
├── _data
│   └── members.yml
├── _drafts
│   ├── begin-with-the-crazy-ideas.md
│   └── on-simplicity-in-technology.md
├── _includes
│   ├── footer.html
│   └── header.html
├── _layouts
│   ├── default.html
│   └── post.html
├── _posts
│   ├── 2007-10-29-why-every-programmer-should-play-nethack.md
│   └── 2009-04-26-barcamp-boston-4-roundup.md
├── _sass
│   ├── _base.scss
│   └── _layout.scss
├── _site
├── .jekyll-metadata
└── index.html # 올바른 머리말을 가진 'index.md' 도 가능
```
우선 커스터마이징을 위해서는 jekyll 디렉토리 구조를 파악 할 필요가 있다.
디렉토리 구조에 관해서는 jekyll 공식 홈페이지에 자세히 나와있다.
모든 디렉토리를 다 확인 할 필요는 없을 것 같고 필요한 부분은 공식 문서의 디렉토리 설명을 참조하였다.

* _config.yml : 환경설정 정보를 보관한다. 명령어를 실행할 때 여러가지 옵션들을 추가할 수도 있지만, 그렇게 따로 외우는 것보다 이 파일에 정의해두는게 더 편리하다.
* _includes : 재사용하기 위한 파일을 담는 디렉토리로서, 필요에 따라 포스트나 레이아웃에 쉽게 삽입할 수 있다. {% include file.ext %} 와 같이 Liquid 태그를 사용하면 _includes/file.ext 파일에 담긴 코드가 삽입된다.
* _posts : 한마디로 말하면, 당신의 컨텐츠다. 중요한 것은 파일들의 명명규칙인데, 반드시 이 형식을 따라야 한다: YEAR-MONTH-DAY-title.MARKUP. 고유주소는 포스트 별로 각각 정의할 수 있지만, 날짜와 마크업 언어 종류는 오로지 파일명에 의해 결정된다.

주로 내가 수정 할 파일은 이렇게 세 개 인것 같다.


## liquid 언어란?
liquid는 Shopify[^2]가 개발한 Ruby로 작성된 오픈소스 템플릿 언어이며 주로 jeykyll 에서 템플릿을 처리 할 때 사용한다.
일반적으로 Liquid에서는 내용 출력 시 중괄호 2개를 사용하며 %를 이용하여 논리 연산을 한다.
jekyll 파일 이것 저것 열어보다 보면 뜬금없이 중괄호 2개로 감싸져있는 언어를 볼 수 있는데 이것이 liquid 언어이다.

## 구조 분석하기
자, 이제 디렉토리 구조도 알았고 liquid 언어도 알았고 본격적인 구조 분석을 해보자.
일단 메뉴를 담고있는 헤더같은 경우엔 재사용을 하는 경우가 많으니 _include에 있을 확률이 매우 높다.
따라서 _include/_layout.html 을 가장먼저 살펴보았다. 아니나 다를까, 여기에 내가 원하던 정보들이 있었다.

```html
<header id="header" class="header" itemscope itemtype="http://schema.org/WPHeader">
      <div class="header-inner"> {% include _partials/header.html %} </div>
</header>
```


무작정 모든 을 커스터마이징 하기 위해 크롬의 개발자도구를 이용해 약간의 html 분석과


```html
<nav class="site-nav">
  {% if site.swiftype_key or site.algolia_search.enable or site.tinysou_Key or site.local_search.enable %}
    {% assign hasSearch = true %}
  {% endif %}

  {% if site.menu %}
    <ul id="menu" class="menu">
      {% for name_path in site.menu %}
        {% assign name = name_path[0] %}
        {% assign path = name_path[1] %}
        {% assign itemName = name | downcase %}
        <li class="menu-item menu-item-{{ itemName }}">
          <a href="{{ path | relative_url }}" rel="section">
            {% if site.menu_icons.enable %}
              <i class="menu-item-icon fa fa-fw fa-{{site.menu_icons[itemName] | default: 'question-circle' | downcase }}"></i> <br />
            {% endif %}
            {{ __.menu[name] }}
          </a>
        </li>
      {% endfor %}

      {% if hasSearch %}
        <li class="menu-item menu-item-search">
          {% if site.swiftype_key %}
            <a href="javascript:;" class="st-search-show-outputs">
          {% elsif site.local_search.enable or site.algolia_search.enable %}
            <a href="javascript:;" class="popup-trigger">
          {% endif %}
            {% if site.menu_icons.enable %}
              <i class="menu-item-icon fa fa-search fa-fw"></i> <br />
            {% endif %}
            {{ __.menu.search }}
          </a>
        </li>
      {% endif %}
    </ul>
  {% endif %}

  {% if hasSearch %}
    <div class="site-search">
      {% include _partials/search.html %}
    </div>
  {% endif %}
</nav>
```

```yml
menu:
  home: /
  #categories: /categories/
  #about: /about/
  archives: /archives/
  tags: /tags/
  #sitemap: /sitemap.xml
  #commonweal: /404.html
```

# 블로그 커스터마이징하기

***
[^1]: https://jekyllrb-ko.github.io/docs/structure/
[^2]: https://shopify.github.io/liquid/
