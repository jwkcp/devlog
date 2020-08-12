---
layout: post
title: 아이폰 미리알림 각 항목 생성일 보는 방법
tags: apple ios
comments: true
---

아이폰이나 맥의 미리알림(영어로 Reminders)은 간단 명료해서 편하고 좋은데 생성일, 수정일같은 기본적인 정보를 보기 어렵게 해둬서 좀 불편할때가 있다. 아이폰 미리알림의 각 항목의 생성일을 알고 싶다면 아래 스크립트를 맥에서 실행하면 된다. 이 방법을 쓰려면 아이폰과 동기화된 맥이 있어서 아이폰에 입력된 미리알림을 맥에서도 볼 수 있어야 한다.  

맥은 `스크립트 편집기`라는 프로그램을 쓰면 된다. 맥 기본 내장 프로그램이고 `기타` 프로그램 묶음에 들어 있다.  

스크립트 편집기에서 아래 스크립트를 붙여넣고 망치 아이콘을 누르면 컴파일되면서 코드에 문제가 있는지 알려준다. 이후 그 옆의 실행 버튼을 누르면 팝업 메시지가 뜨는데 미리알림 중 어떤 목록의 생성일을 가져올지 묻는 창이다. 예를 들어 목록 이름이 `용돈`이라면 이 팝업창에 `용돈`이라고 치면 된다.

이 스크립트가 표기된 원문은 [여기](https://discussions.apple.com/thread/8570915)에서 확인할 수 있다.   

---

{% highlight applescript %}
-- reminder_creation.applescript
-- Tested on macOS High Sierra 10.13.6 (17G65)
-- For a given reminder list, get the name and creation date of each reminder item
-- format, and display it.

use scripting additions

set rnames to {}
set cdates to {}
set fmt to ""

tell application "Reminders"	
	launch
	try
		set theRemList to text returned of (display dialog "Enter the Reminder list name" default answer "" with icon note)
		
	on error errmsg number errnbr
		if errnbr is equal to -128 then
			display alert "User cancelled... ending." giving up after 10
		end if
		return
	
	end try
	
	set {rnames, cdates} to {name, creation date} of every reminder of list theRemList
end tell

-- Simultaneously loop through reminder name and creation date lists
-- and display the reminder name with its creation date

repeat with i from 1 to number of items in rnames
	set fmt to fmt & (item i of rnames) & " : " & (item i of cdates) & return
end repeat

tell application "System Events"	
	display dialog fmt as text with title "Reminder Items Creation Dates"
end tell

return
{% endhighlight %}
