---
layout: post
title: vscode(비주얼스튜디오코드)에서 sys.argv 터미널 파라메터가 있는 경우 디버깅 방법
tags: tools
comments: true
---

# 문제

vscode를 써서 명령줄 인수가 있는 경우 그냥 디버그 실행을 누르면 예외가 발생하면서 실패합니다. 어떻게 해야 할까요?  

---

# 해결방법

vscode에서 좌측 디버그 탭을 누르면 상단에 톱니바퀴 모양이 있습니다. 이걸 누르면 `launch.json`이 열리는데요. 여기에 인수를 넣어주면 됩니다. 아래 예시에서는 `src.txt`와 `dict.txt`를 인수로 지정했습니다. `configurations` 안에 `args`에 리스트 형으로 넣을 수 있습니다.  

{% highlight json %}
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Python: Current File",
            "type": "python",
            "request": "launch",
            "program": "${file}",
            "console": "integratedTerminal",
            "args": [
                "src.txt",
                "dict.txt"
            ]
        }
    ],
}
{% endhighlight %}