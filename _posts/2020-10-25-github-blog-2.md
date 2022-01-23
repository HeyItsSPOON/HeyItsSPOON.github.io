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
우선 커스터마이징을 위해서는 jekyll 디렉토리 구조를 파악 할 필요가 있습니다.
디렉토리 구조에 관해서는 jekyll 공식 홈페이지에 자세히 나와있습니다.
모든 디렉토리를 다 확인 할 필요는 없을 것 같고 필요한 부분은 공식 문서의 디렉토리 설명을 참조하였습니다.

* _config.yml : 환경설정 정보를 보관한다. 명령어를 실행할 때 여러가지 옵션들을 추가할 수도 있지만, 그렇게 따로 외우는 것보다 이 파일에 정의해두는게 더 편리하다.
* _data : 사이트에서 사용 할 데이터를 적절한 포맷으로 정리하는 디렉토리이다.
* _includes : 재사용하기 위한 파일을 담는 디렉토리로서, 필요에 따라 포스트나 레이아웃에 쉽게 삽입할 수 있다.
* _posts : 한마디로 말하면, 당신의 컨텐츠다. 중요한 것은 파일들의 명명규칙인데, 반드시 이 형식을 따라야 한다: YEAR-MONTH-DAY-title.MARKUP. 고유주소는 포스트 별로 각각 정의할 수 있지만, 날짜와 마크업 언어 종류는 오로지 파일명에 의해 결정됩니다.

주로 제가 수정 할 파일은 이렇게 네 개 인것 같습니다.


## liquid 언어란?
liquid는 Shopify[^2]가 개발한 Ruby로 작성된 오픈소스 템플릿 언어이며 주로 jeykyll 에서 템플릿을 처리 할 때 사용합니다.
일반적으로 Liquid에서는 내용 출력 시 중괄호 2개를 사용하며 %를 이용하여 논리 연산을 합니다.
jekyll 파일 이것 저것 열어보다 보면 뜬금없이 중괄호 2개로 감싸져있는 언어를 볼 수 있는데 이것이 liquid 언어입니다.

## 블로그 수정하기
자, 이제 디렉토리 구조도 알았고 liquid 언어도 알았으니 본격적으로 수정을 해봅시다.
일단 메뉴를 담고있는 헤더같은 경우엔 재사용을 하는 경우가 많으니 _include에 있을 확률이 매우 높습니다.
따라서 _include/_layout.html 을 가장먼저 살펴보았습니다. 

```html 
<header id="header" class="header" itemscope itemtype="http://schema.org/WPHeader">
      <div class="header-inner"> {% raw %}{% include _partials/header.html %}{% endraw %} </div>
</header>
```

![theme-apply](https://heyitsspoon.github.io/assets/images/github-blog/theme-apply.PNG "테마 적용 완료")

크롬 개발자 도구로 html 코드를 확인 후 비교 했을 때 _includes/_partials/header.html에서 메뉴 구성을 하는 걸 확인 할 수 있었습니다.

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
.
.
.
menu:
  home: /
  #categories: /categories/
  about: /about/
  archives: /archives/
  #tags: /tags/
  #sitemap: /sitemap.xml
  #commonweal: /404.html

menu_icons:
  enable: true
  KeyMapsToMenuItemKey: NameOfTheIconFromFontAwesome
  home: home
  about: user
  categories: th
  schedule: calendar
  tags: tags
  archives: archive
  sitemap: sitemap
  commonweal: heartbeat
.
.
.
```

html 태그와 비교 해 보니 이 부분이 바로 제가 원하는 부분입니다. site.menu라는 liquid 변수로 config 파일에 있는 menu 항목을 불러와 메뉴를 구성하는 것을 확인 할 수 있었습니다. 
저는 현재 about 페이지가 필요하니 about에 주석을 해제하도록 합시다. 또 카테고리와 태그 대신, 직접 커스텀 한 메뉴를 넣을 것이기 때문에 categories와 tags는 주석처리 해주도록 합니다.

![menu-apply](https://heyitsspoon.github.io/assets/images/github-blog/menu-apply.PNG "메뉴 적용 완료")

그렇다면 위와같이 기존에 있던 categories과 tag는 사라지고 대신 about 메뉴가 생긴 것을 볼 수 있습니다.

메뉴 아이콘은 https://fontawesome.com/v4.7/icons/ 사이트를 참고하여 원하는 아이콘의 이름을 menu_icons 항목에 넣어주면 자동으로 삽입됩니다.

```yml
.
.
.
# Local search
local_search:
  enable: true
  # if auto, trigger search by changing input
  # if manual, trigger search by pressing enter key or search button
  trigger: auto
  # show top n results per article, show all results by setting to -1
  top_n_per_article: 1
.
.
.
```

또한 원활한 블로그 탐방을 위해 블로그 내 검색 기능도 추가하려면 local_search 부분에서 enable 부분을 true로 변경 해 줍니다.

![search](https://heyitsspoon.github.io/assets/images/github-blog/search.PNG "메뉴 적용 완료")

검색 기능이 활성화 된 것을 확인 할 수 있습니다.


## 블로그 커스터마이징하기
그럼 이제 본격적으로 커스터마이징 하기 위해 구조를 설계 해 봅시다. 
기존의 메뉴를 그대로 쓰기 보다는 타 블로그 사이트 처럼 제가 원하는 메뉴를 추가하여 카테고리를 나누려고 합니다. 
category와 tag를 이용하여 category를 메인 메뉴로, tag를 서브 메뉴로 써서 기존 메뉴보다 다양하게 글을 분류할 수 있고 정적 페이지이지만 동적으로 메뉴를 추가할 수 있도록 수정 할 예정입니다.
또한 상단 header에는 category와 tag를 이용한 드롭다운 메뉴를 만들고 우측 사이드바에도 custom 공간에 해당 메뉴를 만들어 조금 더 메뉴 이동이 쉽도록 만들어 보려고 합니다.

### 커스텀 메뉴 추가하기
우선 main menu로 사용 할 categories를 헤더에 출력해봅시다.

```html

.
.
.
    {% raw %}<ul id="menu" class="menu">
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
      {% endfor %}{% endraw %}
.
.
.
```
위 코드를 보니 `site.menu`에서 메뉴 정보를 불러오고, `__.menu[name]`에서 메뉴 이름을 출력 하는 것 같습니다.
`site.menu`에 project 메뉴 정보를 추가하기 위해 `_config.yml` 파일을 수정합니다.
project 메뉴는 category의 project와 연결 할 예정이기 때문에, `/category/#/project` 링크와 연결해줍니다.

```yml
.
.
.
menu:
  home: /
  #categories: /categories/
  about: /about/
  archives: /archives/
  #tags: /tags/
  project: /category/#/project
  #sitemap: /sitemap.xml
  #commonweal: /404.html

  menu_icons:
  enable: true
  KeyMapsToMenuItemKey: NameOfTheIconFromFontAwesome
  home: home
  about: user
  categories: th
  schedule: calendar
  tags: tags
  archives: archive
  sitemap: sitemap
  commonweal: heartbeat
  project: pencil-square
.
.
.
```
config 파일 수정 후 메뉴 탭을 보시면 아이콘만 출력되고 메뉴의 이름은 출력되지 않는 것을 확인할 수 있습니다. 해당 메뉴 이름 출력 부분은 위 코드상에서 `{{ __.menu[name] }}`에 해당되는데 해당 변수에 `project` 가 선언되지 않아 출력이 안되는 것 입니다. 따라서 변수 선언을 해주어야 하는데, 해당 변수는 `_data/default.yml` 아래에서 선언 할 수 있습니다.

```yml
.
.
.
menu:
  home: Home
  archives: Archives
  categories: Categories
  tags: Tags
  about: About
  search: Search
  schedule: Schedule
  sitemap: Sitemap
  commonweal: Commonweal 404
  project: Project
.
.
.
```

위 작업까지 마치고 나면 project 메뉴가 정상적으로 추가 된 것을 확인 할 수 있습니다.

### 드롭다운 메뉴 만들기
드롭다운 메뉴를 만들기 위해서는 먼저 tag로 리스트를 만들어야 합니다. 메뉴 아래에 해당 카테고리 아래에 있는 tag 정보를 리스트로 만들어 출력하는 코드를 추가합니다.

```html
      .
      .
      .
      {% raw %}{% for name_path in site.menu %}
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
            {% for post in site.categories[name] %}
              {% if post.tags %}
                {% assign tag_list = tag_list | concat: post.tags | uniq %}
              {% endif %}
            {% endfor %}
            {% if site.categories[name] %}
              <ul class="dropdown-content" >
              {% for tag in tag_list %}  
                {% assign tag_url_encode = tag | url_encode | replace: '+', '%20' %}
                  <li class="dropdown-list">
                    <a class = "dropdown-item" href="{{ '/tag/#/' | relative_url | append: tag_url_encode }}">{{ tag }}</a>
                  </li> 
              {% endfor %}
              </ul>
            {% endif%}
        </li>
      {% endfor %}{% endraw %}
      .
      .
      .
```

먼저 해당 메뉴와 동일한 이름을 가진 카테고리에 있는 포스트를 전부 검사하여 태그가 있는지 검사하고, 태그가 있으면 해당 태그를 리스트에 저장 후에 리스트를 출력하는 코드를 작성하였습니다.

![search](https://heyitsspoon.github.io/assets/images/github-blog/menu-apply-list.PNG "태그 리스트 생성 완료")

이렇게 코드를 작성 한 후에 블로그에 적용시켜보면 카테고리 아래 태그 리스트가 생성되는 것을 확인 할 수 있습니다.

이제 이것을 드롭다운 메뉴로 만들기 위해 CSS를 적용시켜 보겠습니다.
메뉴 css는 `_sass/_common/components/header/header.scss` 경로에 있습니다.

```css
    @at-root %menu-a-hover { border-bottom-color: $menu-link-hover-border; }
    &:hover { 
      @extend %menu-a-hover; 
      -webkit-transform: scaleX(1);
      -ms-transform: scaleX(1);
      transform: scaleX(1); // reveal
    }
  }

  .fa { margin-right: 5px; }
}


.use-motion .menu-item { opacity: 0; }

.dropdown-content {
  display: none;
  position: absolute;
  background-color: darken(#ffffff, 3%);
  min-width: 100%;
  box-shadow: 5px;
  box-sizing: border-box;

  a {
    display: block;
    font-size: 13px;
    line-height: inherit;
    border-bottom: 1px solid $menu-link-border;
    transition-property: border-color;
    @include the-transition();

    @at-root %menu-a-hover { border-bottom-color: $menu-link-hover-border; }
    &:hover { 
      @extend %menu-a-hover; 
    }
  }
}

.menu-item:hover .dropdown-content {
  padding: 0px;
  margin: 0px;
  min-width: 100%;
  display: block;
  // font-size: 17px;
}

.dropdown-list{
  list-style: none;
  position: relative;
}

.dropdown-item {
  padding: 0px;
  margin: 0px;
  font-size: 15px;
}

.dropdown-item:after {
  padding: 0px;
  margin: 0px;
  display:block;
  content: '';
  border-bottom: solid 3px #2b2a2a;  
  transform: scaleX(0);  
  transition: transform 250ms ease-in-out;
}

.dropdown-item:hover:after { 
  padding: 0px;
  margin: 0px;
  transform: scaleX(1); 
  min-width: 100%;
}
```

저는 css를 잘 모르기 때문에 인터넷에 있는 드롭다운 메뉴 css를 적당히 짜집기 해서 작성하였습니다. 핵심은 메뉴에 커서를 가져다 대면 숨겨져있던 tag 리스트를 띄우는 것입니다.
또한 숨겨져있던 tag 리스트가 뜰 때 포스트가 해당 리스트를 가리면 안되므로 `position`과 `z-index`를 적절하게 설정하여 리스트가 제일 앞에 뜰 수 있게끔 설정합니다.

![search](https://heyitsspoon.github.io/assets/images/github-blog/menu-tag-list.PNG "태그 css 적용 완료")

설정이 완료되면 위와 같이 메뉴 아래 태그 리스트가 드롭다운 메뉴로 활성화되는 것을 확인 할 수 있습니다.

### 사이드 메뉴 만들기
포스트를 보는 중에도 메뉴를 빠르게 전환 할 수 있도록 사이드 메뉴를 만들어 봅시다. 
이미 next 테마에는 커스텀 할 수 있는 side bar 공간이 있기 때문에, 남는 공간을 활용하여 만들어 봅시다.
경로는 `_includes/_custom/sidebar.html` 입니다.

```html
{% raw %}<div class="side-custom">
{% if site.menu %}
<ul id="sidemenu" class="sidemenu">
  {% for name_path in site.menu %}
    {% assign name = name_path[0] %}
    {% assign path = name_path[1] %}
    {% assign itemName = name | downcase %}
    <li class="side-menu-item" id="side-menu-item-list">
      <a id="side-menu-item" href="{{ path | relative_url }}" rel="section">
        {{ __.menu[name] }}
      </a>
         {% for post in site.categories[name] %}
            {% if post.tags %}
               {% assign tag_list = tag_list | concat: post.tags | uniq %}
            {% endif %}
         {% endfor %}
         {% if site.categories[name] %}
         <ul class="side-dropdown-content" >
         {% for tag in tag_list %}
            {% assign tag_url_encode = tag | url_encode | replace: '+', '%20' %}
                  <li class="side-dropdown-item">
                     <a id="side-dropdown-item" href="{{ '/tag/#/' | relative_url | append: tag_url_encode }}">{{ tag }}</a>
                  </li>      
         {% endfor %}   
         </ul> 
         {% endif%}
    </li>
  {% endfor %}
</ul>
{% endif %}
</div>{% endraw %}
```
드롭다운 메뉴를 만들 때의 코드와 거의 유사하기 때문에 따로 추가 설명을 적지는 않겠습니다.
작성한 코드를 바탕으로 css도 꾸며주면 사이드 메뉴도 완성입니다.

```css
.sidemenu{
    -webkit-padding-start: 5px;

    li {
        list-style: none;
        padding: 5px 0px 5px 0px;
        margin-bottom: 0px;
        font-size: 15px;
        text-align: left;
        
    }

    a {
        border-bottom: none;
        text-decoration-line: none;
        text-decoration-style: none;
        text-decoration: none;
    }

    a:hover{
        color: #87d9ff;
    }

    .side-menu-item::before{
        display: inline-block;
        vertical-align: middle;
        //padding: 0px 5px 0px 0px;
    }
}
#side-menu-item-list{
    border-bottom: 0.5px solid #333333;
}

#side-menu-item-list:last-child{
    border-bottom: 0px;
}

#side-menu-item{
    font-weight: 400;
    color: #f5f5f5;
    padding-left: 10px;
    padding-bottom: 0px;
}

#side-menu-item:hover{
    color: #87d9ff;
}

.side-dropdown-content{
    padding-left: 20px;
    border-bottom: 0px;
}
.side-dropdown-item{
    height: 15px;
    border-bottom: 0px;
}

.side-dropdown-item::before {
    content: "-";
}

.side-dropdown-item::before:hover {
    color: #87d9ff;
}
```
```css
.sidebar {
  .
  .
  .
  overflow: scroll;
  .
  .
  .
```

다른 side menu와의 혼동을 방지하기 위해 `_sass/sidebar` 경로 아래에 `sidebar-custom.scss` 파일을 생성하여 사이드메뉴 css를 따로 설정해줍니다.
또한 사이드 메뉴가 늘어나 잘리는것을 방지하기 위해 기존 `sidebar` div가 일정 크기를 벗어나게 되면 스크롤을 생성해주는 css도 넣어줍니다.

![search](https://heyitsspoon.github.io/assets/images/github-blog/side-menu.PNG "사이드 메뉴 적용 완료")

Overview를 확인해보시면 프로필 아래 커스텀 공간에 메뉴 리스트가 생성 된 것을 확인하실 수 있습니다.

# 마치며
생각보다 구조를 분석하고 커스터마이징 하는데에 시간이 오래 걸려 예상 했던 것 보다는 훨씬 늦어졌지만 그래도 원하는 대로 적절하게 커스터마이징이 된 것 같습니다.
추후에 더 수정하거나 추가 할 사항이 있으면 추가해가며 블로그를 꾸려나갈 예정입니다.
사실 블로그 커스터마이징 하는데 시간을 너무 오래 들여서 그냥 티스토리 블로그를 쓸까 후회도 많이 했지만 나름 원하는 기능을 넣고 나니 역시 직접 만들길 잘 했다는 생각이 듭니다.
다음에는 더 알차고 재밌는 포스팅을 하길 바라며 이만 글을 마치겠습니다.

***
[^1]: https://jekyllrb-ko.github.io/docs/structure/
[^2]: https://shopify.github.io/liquid/
