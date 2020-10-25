---
title: Github 블로그 구축하기 (1)
description: Github 블로그 구축부터 커스터마이징까지
categories:
 - github
tags:
---

> Github 블로그 구축하기 (1) - Github 블로그 생성

<!-- more -->

[1. Github 블로그 구축하기 (1) - Github 블로그 생성](https://heyitsspoon.github.io/tutorial/2020/10/23/github-blog-1/)




# 목차
[1. 목차](#목차)

[2. 개요](#개요)

[2.1 블로그를 만든 이유](#블로그를-만든-이유)

[3. Github 블로그 만들기](#Github-블로그-만들기)

[3.1 Github 블로그를 선택한 이유](#Github-블로그를-선택한-이유)

[3.2 사전 작업](#사전-작업)

[4. 마치며](#마치며)




# 개요
## 블로그를 만든 이유
사실 블로그를 개설한 것이 이번이 처음은 아니다.
학교를 졸업하기 전부터 `언젠가는 나만의 기술 블로그를 만들어야지` 라고 생각은 하고 있었고 실제로 도메인을 하나 구매 후 웹호스팅 사이트를 이용해서 블로그를 개설한 적이 있었다.
하지만 막 학부생을 졸업한 시점에서 이렇다 할 제대로 된 프로젝트도 없었고[^1] 과연 내가 어느 분야로 갈지 진로에 대한 확신도 없는 상태에서 무작정 블로그를 개설하다 보니
어떤 것을 업로드 해야 하고 어떻게 작성해야 할지 잘 알지 못했고 디자인적인 부분도 욕심을 부리다 보니 결국 이도 저도 못하고 호스팅 기간이 만료되어 그대로 증발하고 말았다.

그렇다면 지금은 어떤가? DevOps라는 직업군을 알게 되고 DevOps가 되기로 마음먹었고 해당 직군으로 취직[^2] 하였으며 회사 내에서 작고 큰 프로젝트들을 한두 개씩 맡기 시작했다.
이제 나의 강점이 어떤 것인지, 내가 흥미 있는 분야가 어떤 것인지, 내가 부족한 부분이 무엇인지 대충이나마 감을 잡기 시작했다.
따라서 회사를 다닌 지 약 1년이 조금 안 되는 지금 시점이 블로그를 개설 하는 가장 적절한 시기라고 생각했다.

주로 시스템적인 부분과 개발 부분에서 내가 공부 한 것들, 진행한 프로젝트들에 대해 올릴 예정이며,
분석하고 얘기하는 것을 좋아하기 때문에 가끔 다른 분야에 관한 이야기도 포스팅할 예정이다.





# Github 블로그 만들기
## Github 블로그를 선택한 이유
네이버 블로그, 카카오 티스토리, 워드프레스 등 블로그 플랫폼은 상당히 다양하다.
그렇다면 많고 많은 블로그 종류 중에 Github 블로그[^3]를 선택한 이유는 무엇일까?
블로그를 만들기 전 고려했던 요소가 몇 가지 있다.

    디자인적인 부분이나 메뉴 구성에서 커스터마이징이 쉬워야 한다.
    플랫폼을 사용하는 사용자가 적당히 많아야 한다.
    코드 쓰기가 편해야 한다.
    무료여야 한다.

네이버 블로그나 카카오 티스토리는 블로그 구성이 쉽고 이용자도 많아서 포스팅하기가 쉽지만, 한정된 테마로 블로그를 꾸며야 하고 디자인적인 요소를 수정하기도 쉽지 않다.
워드프레스 역시 블로그 구축이 쉽고 커스터마이징을 할 수 있다는 장점이 있지만 직접 웹호스팅을 해야 하고 추가적인 기능을 이용하려면 요금을 내야 하는 단점이 있다.
노션같은 경우도 상당히 편집이 쉽고 디자인적인 부분도 마음에 들었지만 무료 버전은 한 파일에 최대 용량이 5MB라는 단점이 있었다.

Github 블로그는 위의 조건을 모두 만족하면서도 깃허브와의 연동이 쉽다는 장점이 있다.
물론 Github 블로그에도 단점은 있는데, git과 코딩에 대한 지식이 어느 정도 있어야 구축할 수 있다.
하지만 이런 점도 정보통신 분야를 전공하는 사람으로서 어느정도 전문성을 가진 점으로 다가왔고 새로운 지식을 습득하는 건 항상 재미있는 일이기 때문에 고민 않고 Github 블로그를 선택하였다.
무엇보다 직접 공부해서 구축했다고 말하면 간지나지 않는가.

> Github pages 주의 사항

Github 페이지를 사용 시 주의해야 할 사항 역시 분명히 존재한다.
해당 내용은 Github pages에서 제공하는 주의사항에 관한 내용이다. 잘 읽어보고 본인의 상황에 맞게 플랫폼을 선택하길 바란다.

    GitHub Pages 소스 저장소의 권장 제한은 1GB입니다.
    게시 된 GitHub 페이지 사이트는 1GB를 초과 할 수 없습니다.
    GitHub 페이지 사이트의 대역폭 제한은 한 달에 100GB입니다.
    GitHub 페이지 사이트의 builds 제한은 시간당 10회 입니다.




## 사전 작업
### 저장소(repository) 만들기 [^4]

Github 블로그를 만들기 위해서는 우선 Github에 새로운 repository를 생성해야한다.
![new-repository](/assets/images/github-blog/new-repository.png "새 저장소 만들기")
![repository-create](/assets/images/github-blog/repository-create.png "새 저장소 만들기")
Github에 접속하여 새로운 repository를 생성한다. 이 때 Repository name은 [github username].github.io 형식으로 생성해야 username.github.io 도메인으로 생성되는 Github 페이지에 접속 할 수 있다.
Add a README file에 체크 후 생성하면 약 1분에서 5분 후에 README 파일의 내용이 보이는 username.github.io 페이지를 볼 수 있다.
![new-page](/assets/images/github-blog/new-page.png "블로그 생성")
자 이제 나만의 Github 블로그를 생성하기 위한 기본적인 작업이 끝났다.



### 프레임워크 선택하기

Github Page는 Github에서 제공하는 정적사이트 (Static Website) 호스팅 서비스이다.
잠깐 동적사이트와 정적사이트의 차이점이 무엇인지 알아보자.
![static](/assets/images/github-blog/static.png "정적사이트")
정적사이트는 클라이언트가 웹서버로 요청을 보내고, 웹서버는 단순히 로컬 저장소에 저장되어있는 html, img, css 파일 등을 사용자에게 보여주는 방식이다.
따라서 정적사이트는 단순히 기존에 만들어져있는 정보를 보여주는 것이기 때문에 컨텐츠가 거의 변경되지 않는 포트폴리오나 소개글 등에 적합하다.

![static](/assets/images/github-blog/dynamic.png "동적사이트")
그에 반에 동적사이트는 클라이언트가 웹서버로 요청을 보내면, url로 들어온 정보를 통해 웹 어플리케이션에서 해당 정보에 맞는 처리를 한 후
데이터베이스에 있는 자료를 불러와 클라이언트에게 보여준다.
실시간으로 처리되어야 하는 댓글이나 로그인, 회원가입 기능 혹은 쇼핑몰 장바구니 등 상황에 맞게 페이지를 보여주어야 할 때 적합하다.

이러한 정적 페이지를 간편하게 만들기 위해서는 프레임워크를 사용해야 한다. 간단히 생각해서 워드프레스 같은 기능이라 보면 된다.
물론 디자인에 능하고 a부터 z까지 직접 구축할 수 있으면 프레임워크를 사용 할 필요가 없지만 우리는 시간이 없기 때문에 프레임워크를 이용해 사이트를 구축하도록 하자.

정적 사이트를 구축하기 위한 프레임워크 역시 다양한 종류가 있다. Next.js, Hugo, Gatsby 등 쉽고 간편하고 훌륭한 프레임워크가 많이 있다.
나는 현재 가장 많이 사용하기도 하고 제공하는 테마도 다양하며 Github에서 공식적으로 지원하는 jekyll[^5]을 이용하여 블로그를 구축 할 예정이다.
무엇보다 지킬은 쉽게 구축하는 방법이 많이 알려져 있기 때문에 초보자가 따라하기 매우 쉽다는 장점이 있다.
각 프레임워크를 비교한 글은 구글에 검색하면 많이 나오기 때문에 굳이 따로 첨부하지는 않겠다.
특히 Gatsby 공식 홈페이지에 가면 타 프레임워크와 비교하여 개츠비가 어떤 장점이 있는지 나와있다.[^6]



### 테마 선택하기

무료로 제공하는 지킬 테마는 어마어마하게 다양하다.
아래 사이트에 접속하면 다양한 테마들을 볼 수 있으니 각자 마음에 드는 테마를 선택해서 적용하면 된다.
* http://jekyllthemes.org/
* https://jekyllthemes.io/free
* http://themes.jekyllrc.org/
* https://github.com/topics/jekyll-theme

테마를 선택 할 때 역시 몇가지 기준을 정해서 선택했는데,
    디자인이 깔끔해야한다.
    디자인 요소 중 사진적 요소가 적어야한다. (디자이너가 아니기 때문에 사진의 비중이 크면 부담스럽기 때문이다.)
    메뉴 커스터마이징이 쉬워야한다.
    반응형 웹이여야 한다.
    시간대별로 글 목록을 볼 수 있는 메뉴가 있어야한다.
    사이드바가 존재해야 한다.
    사이트 가독성이 좋아야한다.
    Github 최근 업데이트가 너무 옛날이면 안된다.

이 모든 사항을 만족하는 테마가 몇 개 없긴 했지만 이 중에서 추가적으로 원하는 기능은 커스터마이징 해서 사용하기로 하고 몇 가지 테마를 골라보았다.
* NexT - https://simpleyyt.com/jekyll-theme-next/
* Hyde - https://hyde.getpoole.com/
* Lanyon - https://lanyon.getpoole.com/

셋 다 디자인도 깔끔하고 단순하며 위의 조건에 어느정도 충족하지만 그중에서도 NexT 테마를 선택한 이유는 디자인이 제일 예뻐서이다.
다른 매력적인 테마도 많으니 데모 페이지를 보고 본인 취향의 테마를 신중히 정하면 된다.
자 아무튼 테마를 선택했으니 이제 테마를 적용해보도록 하자.



### 테마 적용하기

![static](/assets/images/github-blog/go-repository.png "테마 선택")
![static](/assets/images/github-blog/copy-zip.png "테마 다운로드")
테마를 선택하였으면 해당 테마의 repository로 이동 할 수 있는 링크가 있을 것이다.
NexT repository로 이동 후 테마를 zip 파일로 다운받은 후 압축을 풀어준다.

현재 서술할 적용법은 기본적으로 git-cmd를 기준으로 설명하는 것이기 때문에 기본적으로 git과 git 명령어를 사용 할 줄 안다는 전제하에 이야기 하겠다.
![static](/assets/images/github-blog/copy-url.png "url 복사")
우선 github에서 아까 생성하였던 github 블로그 url을 복사해준다.

```bash
    git clone https://github.com/HeyItsSPOON/heyitsspoon.github.io.git
```
![static](/assets/images/github-blog/copy-theme.png "테마 적용")
git-cmd를 열어 작업 할 디렉토리로 이동 후 git clone 명령어를 이용해 해당 repository를 로컬 PC에 불러온다.
그 후 다운받은 테마를 repository에 복사한다.

```yml
# Site
title: Hey It's SPOON!🥄
subtitle: SPOON'S WORKBENCH
description: SPOON'S WORKBENCH
author: Yuna Kim
# Support language: de, en, fr-FR, id, ja, ko, pt-BR, pt, ru, zh-Hans, zh-hk, zh-tw
language: en

# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com' and baseurl as '/child'
url: "https://heyitsspoon.github.io"
baseurl: ""
```
jekyll은 기본적으로 config.yml 파일에 있는 기본설정을 따라간다.
테마 적용을 위해 간단한 설정 변경 후에 push를 진행하도록 하자.
* title: 사이트의 타이틀
* subtitle: 사이트의 서브타이틀
* description: title과 동일하게 페이지 head에 표시 될 내용. 사용하는 테마가 있고 사용하지 않는 테마가 있다.
* author: 사이트 작성자
* language: 다국어 플러그인
* url: 해당 사이트의 실제 url. 사이트에 url을 할당해서 실행한다. bundle exec jekyll serve를 사용하면 해당 설정은 무시되고 로컬에서 실행된다.
* baseurl: url 뒤에 붙는 기본 경로. 주로 url의 최상위에 있지 않은 사이트를 만들 때 사용된다. 여기서는 최상위 루트에서 실행되므로 공백으로 비워둔다.

적용되는 부분이나 config 내용은 테마마다 다르므로 헷갈린다면 직접 적용하면서 변경해도 좋다.
일단 모든 테마마다 공통적으로 사용하는 기본 설정은 이정도이므로 변경 후에 적용하도록 한다.

```bash
git add .
git commit -m "Theme apply"
git push
```
git-cmd로 현재 작업중인 repository 디렉토리로 이동 후에 바뀐 파일을 검사하고 commit 후에 push 해주도록 한다.

![static](/assets/images/github-blog/theme-apply.png "테마 적용 완료")
그 후 다시 url로 접속 해보면 깔끔하게 적용 된 테마를 만날 수 있다.



### Ruby 및 jekyll, bundler 설치

jekyll은 Ruby라는 컴퓨터언어로 만들어졌다. 따라서 해당 프레임워크를 제대로 다루기 위해서는 Ruby를 설치해야한다.
Ruby는 Ruby installer[^7] 페이지에서 쉽게 다운받을 수 있다.

해당 페이지에 들어가 개발자 키트가 포함된 버전을 자신의 운영체제 비트에 맞게 다운받는다. 
주의 할 점은 반드시 path에 추가하는 부분에 체크하고 설치를 진행하길 바란다.
추후에 따로 추가 할 수도 있지만 상당히 귀찮기 때문에 자동으로 추가 하도록한다.

```bash
ruby --version
```
Ruby 설치가 완료되었으면 git-cmd에서 명령어로 제대로 설치되었는지 확인한다.
아래와 같이 버전 정보가 나오면 제대로 설치 된 것이다.

    ruby 2.6.6p146 (2020-03-31 revision 67876) [x64-mingw32]

```bash
gem install bundler jekyll
```
이제 Ruby가 설치되었으므로 github 블로그 디렉토리로 이동해 gem 명령어를 통해 jekyll과 bundler를 설치한다.
gem이란 CentOS의 yum, ubuntu의 apt같은 개념으로 루비에서 쓰이는 라이브러리 패키지이다.
cmd 환경에서 쉽게 패키지를 다운로드 받을 수 있다.

bundler는 루비에서 gem 라이브러리 의존성을 위해 사용되는 패키지로 보통 테마를 받을 때
Gemfile이 함께 있는 걸 볼수가 있는데 이 Gemfile에 다양한 Gem이 등록되어있다.
이 Gemfile에 Gem을 추가해서 사용하기 위해 bundler를 설치한다.

```bash
bundle install
```
bundle install로 Gem을 등록하도록 한다.



### 주의사항

```
Bundler could not find compatible versions for gem "ruby ":                                                               
In Gemfile:                                                                                                               ruby                                                                                                                                                                                                                                            
github-pages was resolved to 193, which depends on
    listen (= 3.1.5) was resolved to 3.1.5, which depends on
    rb-inotify (~> 0.9, >= 0.9.7) was resolved to 0.9.10, which depends on
        ffi (>= 0.5.0, < 2) was resolved to 1.9.25, which depends on
        ruby  (>= 2.0, < 2.6) x64-mingw32
    
github-pages was resolved to 193, which depends on
    nokogiri (>= 1.8.2, < 2.0) was resolved to 1.8.5, which depends on
    ruby  (>= 2.2, < 2.6 x64-mingw32
      
Could not find gem 'ruby  (>= 2.0, < 2.6)', which is required by gem 'nokogiri
(>= 1.8.2, < 2.0)', in any of the relevant sources:
    the local ruby installation 
```

위와 같이 간혹 테마 적용 시 다운받은 테마 bundler 버전과 시스템에 설치된 bundler 버전이 맞지 않아 발생하는 오류가 있다.

해당 오류 발생 시 해결 방법은 두가지가 있는데,
테마 사이트 버전을 올리거나 시스템에 설치 된 버전을 변경하는 것이다.

* 사이트 버전 변경
기존 repository에 있던 Gemfile.lock을 삭제하고 bundle install을 진행한다
```bash
bundle install
```

* 시스템에 설치된 버전 변경
```bash
bundler --version
gem uninstall bundler -v [현재 설치되어있는 bundler 버전]
gem install bundler -v [설치하고자 하는 bundler 버전]
```
bundler --version 명령어로  현재 설치되어있는 버전을 확인 후에 사이트에 맞는 버전으로 재설치 한 후 진행한다.



### 로컬에서 블로그 실행하기

Github page는 git을 push한다고 바로바로 적용되는 것이 아니기 때문에 짧게는 거의 실시간으로 반영되기도 하고 길게는 5분이상이 걸릴 때도 있다.
이럴 때 bundler를 이용하여 page를 localhost에서 실행시켜 적용사항을 바로바로 확인 할 수 있다.

```bash
bundle exec jekyll serve
```

위 명령어를 실행 후 Server running... 이라는 결과가 나오면 localhost:4000 (혹은 127.0.0.1:4000) 으로 접속하여 실시간으로 변경 된 블로그를 확인 할 수 있다.
단 실제로 적용된 것은 아니니 꼭 수정 후에 git push를 이용해 제대로 github에 올려주도록 하자.

bundle exec jekyll serve 명령어로 이미 로컬에서 블로그를 실행시키고 있는 와중에 블로그를 변경해도 바뀐 사항이 보이지 않으니 꼭 Ctrl+C로 명령어를 끝내고 다시 실행시켜 변경사항을 확인하도록 하자.



# 마치며
블로그의 기초가 되는 뼈대를 만들었으니 다음 포스팅에선 본격적으로 커스터마이징을 진행 할 예정이다.
부디 이번엔 일주일에 포스팅 한개 목표를 지키길 바라며 글을 마친다.

***

[^1]: 물론 학부생 때 진행했던 프로젝트는 있었지만 제대로 정리도 안 되어있을 뿐더러 어디에 게재하기도 민망한 수준이였다.
[^2]: 정확히는 시스템 엔지니어로 입사했고 DevOps를 지향하고있다.
[^3]: 정식 명칭은 Github page 이다.
[^4]: https://pages.github.com/
[^5]: https://jekyllrb-ko.github.io/
[^6]: https://www.gatsbyjs.com/features/jamstack/gatsby-vs-jekyll-vs-hugo/
[^7]: https://rubyinstaller.org/downloads/