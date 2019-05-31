---
layout: post
title: 5분 만에 살펴보는 노드(Node.js) 로그 설정 및 사용법
tags: nodejs
comments: true
---
     
각 언어나 라이브러리마다 유명하게 자주 그리고 오랫동안 쓰이는 로그 라이브러리가 있습니다. 예를 들어, 파이썬은 기본 내장된 logging, 리덕스(redux)는 redux-logging, 자바는 log4j, C#은 log4net 등이 그렇습니다. 노드는 어떤 로그 라이브러리를 많이 사용할까요?   
    
# Winston? Winston!
여기저기 잠시 구글링해보니 노드에서는 [winston](https://github.com/winstonjs/winston)을 많이 쓰고 있었습니다. 현재(2019.05) 버전 3.x까지 나와있고, Star를 13,340개 받은 프로젝트네요. 거의 독보적으로 많이 쓰는 라이브러리인 것 같습니다. 좋습니다. 이걸로 정하겠습니다.        

---
     
# 기본 사용법
1. ```winston.js```를 만들고 여기에 ```logger```를 생성한 다음 export default로 내보냅니다.
2. 로그를 찍을 소스에서 ```import winston from 'winston'```으로 불러온 다음
3. ```winston.info('This is info')``` 와 같이 로그를 찍으면 됩니다.
    
파일로 찍을지, 콘솔로 찍을지 어떻게 설정하냐구요? 그건 ```winston.js```에서 설정합니다. 아래 글을 더 읽어보세요.    

---

# winston.js (설정)
아래는 winston 깃허브에서 제공하는 공식 사용법 예시에서 몇 가지 불필요한 부분을 제거한 소스입니다.    
    
```
const logger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),  
  transports: [    
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' })
  ]
});

if (process.env.NODE_ENV !== 'production') {
  logger.add(new winston.transports.Console({
    format: winston.format.simple()
  }));
}
```
    
잘 보면 별 것 없습니다. 간단하고 쉽게 설정할 수 있네요.    
logger를 createLogger라는 함수를 통해 생성하는데 여기서 기본 로거의 수준(level: 'info')이나 포맷(format: winston.format.json())을 정해줍니다. 그리고 transports 부분이 나옵니다. transport는 사전적 의미로 '운송' '실어나르다' 는 뜻입니다. 로그를 어디다 퍼 나를 것인지 정하는 것이지요. 파일로 보낼지, 콘솔로 보낼지 여기서 결정합니다. 다른 언어의 로거를 써보셨다면 핸들러(handler)와 같다고 보시면 됩니다. 각 transport(트렌스포트)마다 레벨도 정해줄 수 있고요. 파일명도 정할 수 있습니다. 당연히 리스트 형태로 transport를 받을 수 있게 해두었기 때문에 입맛에 맛게 추가, 삭제할 수 있습니다.    

여기까지 입니다. 여기까지만 알아도 로그를 찍고 사용하는데 충분합니다. 더 상세한 사항은 로그를 쓰면서 부족한 부분을 채워가면 됩니다. 처음부터 완벽하려하면 주객이 전도되기 십상인 분야가 특히 소프트웨어 개발 쪽이니까요.   

아래 있는 if문은 반드시 필요한 부분은 아니지만 유용한 부분입니다. 궁금하시다면 몇 분 더 투자해보시죠.    
    
보통 노드를 배포할 때, 혹은 개발이나 테스트 환경에서 NODE_ENV라는 환경변수명을 많이 사용합니다. 딱 저렇게 법으로 정해져있는 건 아니지만 보편적으로 널리 쓰이는 명칭이니 따르면 좋습니다. 커뮤니케이션 비용이 크게 줄어드니까요. 다시 본론으로 돌아가서 NODE_ENV 환경변수가 production으로 되어 있지 않다면 콘솔 로그도 찍겠다는 뜻입니다. 개발할 땐 아무래도 콘솔로 올라가는 로그가 도움이 됩니다. 반면 배포하는 서버에서 NODE_ENV 환경변수가 production이라는 문자열로 설정되어 있다면 콘솔로그는 찍히지 않고 위에서 설정한 파일 로그만 찍히게 됩니다. 가만, 다시 보니 logger.add 라는 함수로 간단히 로거를 하나 추가했네요. winston이 괜히 인기있는게 아닌가 봅니다.          
       
여기까지 입니다. 다시 정리해보면,    
     
winston.js 파일을 만들고 위에 소스를 붙여넣고 winston을 임포트 한 후 winston.error('hello')와 같이 쓰면 됩니다.    
    