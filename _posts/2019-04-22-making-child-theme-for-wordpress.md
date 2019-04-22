---
layout: post
title: 워드프레스에서 자식 테마 1분만에 만들기
tags: wordpress
comments: true
---

자식 테마는 그냥 워드프레스 테마 디렉토리에 새로운 디렉토리를 하나 만들고(보통 부모테마 뒤에 -child를 붙인다.) 파일 3개 넣어주면 끝입니다.   
    
twenty 라는 부모 테마가 있다고 가정하겠습니다. 디렉토리 구조는 다음과 같습니다.    
screenshot.png는 워드프레스 테마 메뉴에서 썸네일을 보여주는 역할을 합니다. 안 넣어줘도 되지만 넣어주면 보기가 좋습니다. 보통 부모 테마 디렉토리에 screenshot.png를 복사해 넣는 방법을 씁니다.   
    
```
wp-content
  - themes
    - twenty
    - twenty-child
      - functions.php
      - screenshot.png
      - style.css
```
    
### functions.php
{% highlight php %}
<?php
  function theme_enqueue_styles() {
    wp_enqueue_style( 'parent-style', get_template_directory_uri() . '/style.css' );
    wp_enqueue_style( 'child-style', get_stylesheet_directory_uri() . '/style.css', array( 'parent-style' ) );
  }
  add_action( 'wp_enqueue_scripts', 'theme_enqueue_styles' );
?>
{% endhighlight %}
    
### screenshot.png
부모 테마에서 복사해 넣거나, 자기가 만든 이미지 파일 사용.

### style.css
여러 다른 정보를 더 넣어줄수도 있지만 아래 2줄이 핵심입니다. 이렇게만 넣어도 자식 테마가 동작합니다.    
    
{% highlight css %}
/*
  Theme Name: twenty-child
  Template: twenty
*/
{% endhighlight %}
   
