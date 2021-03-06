---
layout: post
title: 구글 드라이브를 이미지 서버로 사용하기 (feat. 지킬(jekyll))
tags: jekyll tips
comments: true
---
  
## 문제
지킬(jekyll)과 같이 자체 이미지 호스팅이 사실 상 어려운(깃허브도 용량제한이 있으므로) 서비스에 블로깅을 하는 경우 이미지를 어디에 올려서 삽입해야 할지 늘 고민입니다. imgur의 경우 외부 호스팅 용도로 사용을 하지 말라고 한다고 하니 언제 막힐지, 올린 이미지가 다 깨지진 않을지 걱정입니다. postimages.org가 추천할만하고 이 블로그도 이미지를 지금까지 여기서 호스팅을 했지만 가~끔 서비스가 다운되기도 하고 역시 언제 중단될지 모르니 불안한 마음을 없앨 순 없었습니다.

## 구글 드라이브
구글 드라이브를 이용해서 호스팅할 수는 없을까 생각을 했습니다. 결론적으로는 가능합니다. 별다른 꼼수 없이 구글이 제공한 iframe을 이용하면 됩니다.  

**순서**  

1. 구글에 이미지를 올리고 '공개'로 변경하여 공개된 URL을 얻습니다.
2. 이 URL로 들어가보면 이미지만 보이지 않고 구글의 메뉴가 포함된 이미지가 보입니다.
3. 여기서 우측 상단은 점 세 개 메뉴를 누릅니다. (다운로드 버튼 옆에 있습니다.)
4. 그러면 '항목 삽입...' 이란 메뉴가 나오고 이걸 누르면 iframe 코드가 나옵니다.
5. 4번에서 얻은 코드를 지킬 포스팅에 그냥 넣어주면 됩니다. 그러면 이미지가 임베딩 됩니다.
  
어떻게 보이는지 보고 싶으시면 [이 포스팅](https://devlog.jwgo.kr/)을 참조하세요.   

