---
layout: post
title:  "TUI 경험해보기"
date:   2016-01-17 22:11:43
categories: archieve
image: toast.jpg
comments: true
---

1주차 기술교육 강의 중 Toast UI 예제를 직접 실습해본다.
# Calendar 실습

## Step 1. NodeJS 설치 + bower 설치(윈도우)
[nodejs](http://nodejs.org) 에서 latest 버전을 다운 받아 설치한다.<br>
cmd 창에서<br>
`npm i --global bower`
을 입력하여 bower를 설치. 만약 npm명령어가 실행 안되면 환경변수에 Path를 확인한다.<br>
`C:\nodejs` 에 설치되었다면 해당 경로를 Path에 추가하면 된다.

## Step 2. tui-code-snippet, tui-component-calendar 설치
`bower install tui-code-snippet`<br>
`bower install tui-component-calender`<br>
을 입력하여 해당 컴포넌트를 설치한다.
![Small Test Image]({{ site.baseurl }}/content/images/contents3_2.jpg)<br>

## Step 3. 테스트 html 코드로 작성 및 결과 화면
아래 코드는 [github-calendar-튜토리얼](https://github.com/nhnent/tui.component.calendar/wiki/Calendar-Tutorial)에서 참고했다.<br>
![Small Test Image]({{ site.baseurl }}/content/images/contents3_3.jpg)<br>
아래는 위 코드의 실행 결과<br>
![Small Test Image]({{ site.baseurl }}/content/images/contents3_4.jpg)<br>

## Step 4. github Sample 페이지 이해하기
우선 sample 페이지의 css를 가져와서, 헤드에 추가했다.<br>
`<link href="css/common.css" rel="stylesheet" />`
<br>
아래의 코드는 Sample에서 참고했다.<br>

{% highlight html %}
<div id="layer" class="layer"> <!-- 기준 엘리먼트 -->
    <div class="calendar-header">
        <a href="#" class="rollover calendar-btn-prev-year"><<</a> <!-- 이전년 버튼 (생략가능) -->
        <a href="#" class="rollover calendar-btn-prev-month"><</a> <!-- 이전달 버튼 (생략가능) -->
        <strong class="calendar-title"></strong> <!-- 달력의 타이틀 (생략가능) -->
        <!--<strong class="calendar-title-year"></strong> &lt;!&ndash; 달력의 타이틀 (생략가능) &ndash;&gt;-->
        <!--<strong class="calendar-title-month"></strong> &lt;!&ndash; 달력의 타이틀 (생략가능) &ndash;&gt;-->
        <a href="#" class="rollover calendar-btn-next-month">></a> <!-- 다음달 버튼 (생략가능) -->
        <a href="#" class="rollover calendar-btn-next-year">>></a> <!-- 다음년 버튼 (생략가능) -->
    </div>
    <div class="calendar-body">
        <table cellspacing="0" cellpadding="0">
            <thead>
            <tr>
                <th class="sun">Su</th><th>Mo</th><th>Tu</th><th>We</th><th>Th</th><th>Fa</th><th class="sat">Sa</th>
            </tr>
            </thead>
            <tbody>
            <tr class="calendar-week"> <!-- 달력의 한 주에 해당하는 엘리먼트 컨테이너 -->
                <td class="calendar-date"></td> <!-- 날짜가 표시될 엘리먼트 -->
                <td class="calendar-date"></td>
                <td class="calendar-date"></td>
                <td class="calendar-date"></td>
                <td class="calendar-date"></td>
                <td class="calendar-date"></td>
                <td class="calendar-date"></td>
            </tr>
            </tbody>
        </table>
    </div>
    <div class="calendar-footer">
        <p>Today: <em class="calendar-today"></em></p>
    </div>
</div>
{% endhighlight %}


![Small Test Image]({{ site.baseurl }}/content/images/contents3_5.jpg)<br>

css 까지 적용하면 위와 같이 활용이 가능하다.<br> 다음에는 datePicker을 찾아봐야겠다.<br>
아마 투표서비스 개발할 때 캘린더 기능은 활용이 가능할 것이다.<br>
틈틈히 Toast UI 다른 component들을 들여다 봐야겠다.<br>

오늘은 여기까지!<br>



