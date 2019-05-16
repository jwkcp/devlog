---
layout: post
title: 설정 파일로 파이썬 로깅 시작하기
tags: python
comments: true
---

처음 파이썬에서 로그를 쓰려면 어디서부터 시작해야 할지 막막하다. 별도의 라이브러리를 써야 하나? 뭐 좋은 방법이 있나? 이런 질문에 한 줄로 대답해보고 어떻게 사용하는지 실제 코드를 보면서 살펴보자. 이 글은 장황하게 파이썬 로깅 모듈 전체에 대해 설명하는 것이 아니라 실제 사용하는 방법 딱 그 범위 안에서 간단하게 설명한다.    
      
### 한 줄로 대답하는 파이썬 로깅
아래는 한 줄로 대답한 파이썬 로깅에 관한 내용이다. 물론 개인적 경험에 바탕을 두었으며 더 좋은 방법이 있을 수 있음을 밝힌다.    
     
**1. 별도 로깅 라이브러리를 설치해야 하나?**     
노! 파이썬 기본 모듈(logging)을 이용한다.
    
**2. 로거는 소스마다 매 번 만들어줘야 하나? 보니까 포맷터, 핸들러 등 할게 많고 이걸 매번 만든다면 좀 짜증나고 번거로울 것 같은데...**     
모듈 단위로 로거를 만들어 쓰도록 권하고 있는 편. 모듈이 뭔지 헷갈린다면 import xxx 처럼 xxx가 모듈이라고 생각하면 쉽다.   
    
**3. 중간에 로그 레벨을 바꾸려면 소스를 수정해야 하나?**    
소스 코드 내에 로그 레벨을 정하는 부분을 하드코딩 해두면 어쩔 수 없이 그래야 한다. 하지만 configparser나 logging.config를 이용하면 동적으로 바꿀 수 있다.     
    
**4. 권장하는 방법은?**    
logging.config를 통해 xxx.conf 파일을 만들로 이를 통해 로거를 컨트롤.

---

### 로거를 불러오는 코드
로거 설정 파일이 logging.conf라고 했을 때 파일 내용입니다. 핸들러가 3개 들어있는데 원하는 핸들러 1개만 넣어도 됩니다. 아래는 콘솔에 출력하는 핸들러(streamHandler), 그냥 파일로 쓰는 핸들러(fileHandler), 지정한 용량이 넘어가면 새 파일을 만들어 쓰는 핸들러(rotatingHandler)가 추가되어 있습니다. 실제로 사용할 땐 똑같이 파일에 쓰는 fileHanler와 rotatingFileHandler를 다 추가할 필요는 없겠죠?    
    
{% highlight python %}
[loggers]
keys=root, simpleLogger

[handlers]
keys=streamHandler, fileHandler, rotatingFileHandler

[formatters]
keys=simpleFormatter

[logger_root]
level=NOTSET
handlers=

[logger_simpleLogger]
level=INFO
handlers=streamHandler, fileHandler, rotatingFileHandler
qualname=여기에는getLogger로부를때사용할이름을넣는다.
propagate=1

[handler_streamHandler]
class=StreamHandler
formatter=simpleFormatter
args=(sys.stdout, )

[handler_fileHandler]
class=FileHandler
formatter=simpleFormatter
args=('python.log', 'a')

[handler_rotatingFileHandler]
class=handlers.RotatingFileHandler
formatter=simpleFormatter
args=('python_r.log', 'a', 10, 10)

[formatter_simpleFormatter]
format=%(asctime)s %(name)s %(funcName)-8s %(levelname)-8s - %(message)s
datefmt=
{% endhighlight %}
            
사용할 때는 이렇게 -
{% highlight python %}
import logging
import logging.config

logging.config.fileConfig('logging.conf')
logger = logging.getLogger('위에 qualname 항목에 넣은 이름을 여기에 넣는다.')
{% endhighlight %}
      

    
