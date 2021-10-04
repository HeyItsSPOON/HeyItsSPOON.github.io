---
title: Github 블로그 구축하기 (2)
description: Github 블로그 구축부터 커스터마이징까지
categories:
 - project
tags: github
---

> Github 블로그 구축하기 (2) - 커스터마이징

<!-- more -->

[1. Github 블로그 구축하기 (1) - Github 블로그 생성](https://heyitsspoon.github.io/project/2020/10/23/github-blog-1/)

[2. Github 블로그 구축하기 (2) - 커스터마이징](https://heyitsspoon.github.io/project/2020/10/25/github-blog-2/)

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
* _data : 사이트에서 사용 할 데이터를 적절한 포맷으로 정리하는 디렉토리이다.
* _includes : 재사용하기 위한 파일을 담는 디렉토리로서, 필요에 따라 포스트나 레이아웃에 쉽게 삽입할 수 있다.
* _posts : 한마디로 말하면, 당신의 컨텐츠다. 중요한 것은 파일들의 명명규칙인데, 반드시 이 형식을 따라야 한다: YEAR-MONTH-DAY-title.MARKUP. 고유주소는 포스트 별로 각각 정의할 수 있지만, 날짜와 마크업 언어 종류는 오로지 파일명에 의해 결정된다.

주로 내가 수정 할 파일은 이렇게 네 개 인것 같다.


## liquid 언어란?
liquid는 Shopify[^2]가 개발한 Ruby로 작성된 오픈소스 템플릿 언어이며 주로 jeykyll 에서 템플릿을 처리 할 때 사용한다.
일반적으로 Liquid에서는 내용 출력 시 중괄호 2개를 사용하며 %를 이용하여 논리 연산을 한다.
jekyll 파일 이것 저것 열어보다 보면 뜬금없이 중괄호 2개로 감싸져있는 언어를 볼 수 있는데 이것이 liquid 언어이다.

## 설계하기
기존의 home, archives, categories, tags 4개의 메뉴 대신 category와 tag를 이용하여 category를 메인 메뉴로, tag를 서브 메뉴로 쓰는 2 depth 구조로 변경 할 계획이다.

## 블로그 수정하기
자, 이제 디렉토리 구조도 알았고 liquid 언어도 알았고 본격적으로 수정을 해보자.
일단 메뉴를 담고있는 헤더같은 경우엔 재사용을 하는 경우가 많으니 _include에 있을 확률이 매우 높다.
따라서 _include/_layout.html 을 가장먼저 살펴보았다. 아니나 다를까, 여기에 내가 원하던 정보들이 있었다.

```html
<header id="header" class="header" itemscope itemtype="http://schema.org/WPHeader">
      <div class="header-inner"> {% raw %}{% include _partials/header.html %}{% endraw %} </div>
</header>
```

![theme-apply](https://heyitsspoon.github.io/assets/images/github-blog/theme-apply.PNG "테마 적용 완료")

크롬 개발자 도구로 html 코드를 확인 후 비교 했을 때 _includes/_partials/header.html에서 메뉴 구성을 하는 걸 확인 할 수 있었다.

```html
<nav class="site-nav">
  .
  .
  .
  {% raw %} {% if site.menu %}{% endraw %}
    <ul id="menu" class="menu">
      {% raw %}{% for name_path in site.menu %}{% endraw %}
      {% raw %}  {% assign name = name_path[0] %}{% endraw %}
        {% raw %}{% assign path = name_path[1] %}{% endraw %}
        {% raw %}{% assign itemName = name | downcase %}{% endraw %}
        <li class="menu-item menu-item-{{ itemName }}">
          <a href="{{ path | relative_url }}" rel="section">
            {% raw %}{% if site.menu_icons.enable %}{% endraw %}
              <i class="menu-item-icon fa fa-fw fa-{{site.menu_icons[itemName] | default: 'question-circle' | downcase }}"></i> <br />
            {% raw %}{% endif %}{% endraw %}
            {% raw %}{{ __.menu[name] }}{% endraw %}
          </a>
        </li>
      {% raw %}{% endfor %}{% endraw %}
      .
      .
      . 
    </ul>
  {% raw %}{% endif %}{% endraw %}
  .
  .
  .
</nav>
```

```yml
# Local search
local_search:
  enable: true
  # if auto, trigger search by changing input
  # if manual, trigger search by pressing enter key or search button
  trigger: auto
  # show top n results per article, show all results by setting to -1
  top_n_per_article: 1
```

자 html 태그와 비교 해 보니 이 부분이 바로 내가 원하는 부분이다. site.menu라는 liquid 변수로 config 파일에 있는 menu 항목을 불러와 메뉴를 구성하는 것을 확인 할 수 있었다. 
나는 현재 about 페이지가 필요하니 about에 주석을 해제하도록 한다. 또 카테고리와 태그 대신, 직접 커스텀 한 메뉴를 넣을 것이기 때문에 categories와 tags는 주석처리 해주도록 한다.

![menu-apply](https://heyitsspoon.github.io/assets/images/github-blog/menu-apply.PNG "메뉴 적용 완료")

그렇다면 위와같이 기존에 있던 categories과 tag는 사라지고 대신 about 메뉴가 생긴 것을 볼 수 있다.

```yml
```

또한 원활한 블로그 탐방을 위해 블로그 내 검색 기능도 추가하려면 local_search 부분에서 enable 부분을 true로 변경 해 준다.

![search](https://heyitsspoon.github.io/assets/images/github-blog/search.PNG "메뉴 적용 완료")

검색 기능이 활성화 된 것을 확인 할 수 있다.

## 블로그 커스터마이징하기 -- 설계
이제 next 테마 기본 기능에서 변경 할 수 있는것은 변경하였고 본격적으로 커스터마이징에 들어가보자.



## 블로그 커스터마이징하기 -- 구현

***
[^1]: https://jekyllrb-ko.github.io/docs/structure/
[^2]: https://shopify.github.io/liquid/
