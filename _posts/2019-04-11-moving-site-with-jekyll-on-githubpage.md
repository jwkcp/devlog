---
layout: post
title: 깃허브 페이지(Github page)에서 지킬(Jekyll)로 돌리는 사이트 이전하기
tag: jekyll
comments: true
---

기존에 구글 검색 콘솔에 등록해서 색인도 잘 되어 있고 PageRank 순위도 잘 받고 있는 사이트를 새로운 도메인으로 이전하려면 어떻게 해야 할까요. 일반적으로 많이 쓰이고 권장되는 방법은 .htaccess 파일을 통한 301 Redirect를 이용하는 것입니다. 하지만 깃허브 페이지는 정적 페이지를 서빙하므로 서버 사이드에서 301 Redirect를 호출하기 위해 .htaccess를 사용할 수 없습니다. (넣어봐야 동작하지 않아요.)    
    
아래 기존 도메인 http://old.example.com 에서 새 도메인 http://new.example.com 으로 이동하는 예를 통해 필요한 사항을 살펴보겠습니다. 이 방법은 여러 시도를 통해 제가 했던 방법으로 [깃허브 페이지는 jekyll의 플러그인 기능을 통해 하라](https://help.github.com/en/articles/redirects-on-github-pages)고 되어 있지만 게시물 리다이렉트에 문제가 있었어서 head를 수정하는 방법을 함께 사용했습니다.    

---
    
# 플러그인 설치 및 설정 (1/5)
**설치**    
```gem install jekyll-redirect-from```으로 [jekyll-redirect-from](https://github.com/jekyll/jekyll-redirect-from)을 설치합니다.
     
**/_config.yml 설정**     
```
gems:
    - jekyll-redirect-from

whitelist:
    - jekyll-redirect-from
```
위와 같이 whitelist에 jekyll-redirect-from을 써주지 않으면 보안 상의 이유로 jekyll은 해당 플러그인을 동작시키지 않으니 주의하세요.    
    
# /index.html의 front-matter 수정 (2/5)
front-matter는 YAML 파일 시작 시 작성하는 게시물 환경 설정을 영역을 뜻하는데 여기에 ```redirect_to: http://new.example.com```을 입력합니다. 저의 경우에 이 파일은 처음 URL을 치고 들어왔을 때 새로운 주소로 리다이렉트 하기 위해 수정했습니다.    
    
# /_includes/head.html 수정 (3/5)
이렇게 클라이언트의 head에 meta와 link, 스크립트를 넣는 것은 302 redirect 이지만 각 게시물의 리다이렉트를 위해 사용했습니다. 그렇지 않으면 각 게시물 마다 front-matter를 수정해줘야 할 것 같은데 이건 좀 시간 낭비라고 생각했어요. 
     
참, 302 redirect는 301 redirect과 다르게 임시 리다이렉트를 뜻합니다. 301 redirect는 영구 리다이렉트이구요. 방문자는 어쨌든 새로운 주소로 이동하고 체감할수도 없지만, 검색엔진은 이 둘을 구분합니다. 302 redirect는 클라이언트에서 처리하는 방식이고, 임시 리다이렉트이기 때문에 웹사이트 주소가 일시적으로 바꿔었다고 생각하고, 301 redirect는 서버에서 처리하는 방식이고 영구 리다이렉트이기 때문에 웹사이트가 아예 새로운 주소로 이사갔다고 인식하게 됩니다. 301 리다이렉트를 이용하면 검색엔진은 새로운 주소로 색인을 시작합니다. (head.html로 나눠져 있지 않다면 각 게시물의 템플릿 레이아웃에 작성하시면 됩니다.)
     
```
<meta http-equiv="refresh" content="0; url=http://new.example.com{{ page.url }}" />
<script>
if (window.location.href.indexOf('http://old.example.com{{ page.url }}') === 0) {
    window.location.href = 'http://new.example.com{{ page.url }}';
}
</script>
<link rel="canonical" href="http://new.exmaple.com{{ page.url }}" />
```
     
# jekyll build (4/5)
작성한 소스를 다시 빌드하고 push 해주세요.
     
# 마무리 (5/5)
구글 검색 콘솔에 등록 후 사이트맵을 다시 제출합니다.     
보통 구글에서 색인 정리에 며칠이 걸리니 당분간 기존 사이트를 완전히 없애지 말고 기다리시는게 좋습니다.
    
---

이렇게 작업한 후 기존 페이지가 모두 새로운 페이지로 잘 리다이렉트 되는 것을 확인했습니다. 구글 애널리틱스로 새로운 주소로 트래픽이 유입되기 시작했구요. 다만, 구글 검색 콘솔에서 '주소 변경' 메뉴가 있는데 여기서 2번째 과정(301 리다이렉트를 확인하는 과정)이 계속 실패해서 구글 검색 콘솔에서 주소 변경은 완료하지 못했습니다. 깃허브 페이지에서 지킬로 운영하는 사이트 중에 이 과정을 완료했던 분이 계시다면 댓글로 알려주시면 다른 사용자들에게 도움이 될 것 같습니다. 구글에서는 301 redirect를 권장하지만 302 redirect도 재색인에 사용하는 것 같아요. 하지만 이 부분은 문서로 확인하지 못했기 때문에 100% 확신할 수 없었습니다.    
    
기존에 운영하던 사이트라면 새로운 주소로 이동할 때는 꼭 301 redirect(적어도 302 redirect)를 사용하시기 바랍니다. 404 Page NotFound는 최악의 선택입니다. 그동안 얻었던 검색 순위 점수를 다 잃어버릴 수도 있습니다. 워드프레스와 같이 동적 페이지로 서비스하는 사이트는 .htaccess를 이용해 간편하게 302 redirect를 설정하실 수 있습니다. 깃허브 페이지는 꽤 시간을 잡아먹었어요..   
