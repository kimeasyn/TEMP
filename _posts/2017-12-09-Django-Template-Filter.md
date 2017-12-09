---
layout: post
date: 2017-12-09
title: "Django Template Filter"
comments: true
categories: Django
---

#### Django Template Filter

- 템플릿 변수값 변환을 위한 함수이며, `|` (파이프)를 이용하여 다수 필터 함수를 연결 할 수 있다.(chaining)

  `{% raw %}{{var|filter1}} {{var|filter2:arg}}{{var|filter2:arg|filter3}}{% endraw %} `

- 빌트인 Filter가 지원되며 app별로 custom filter 추가 가능



#### Date / Time Filter

- 날짜/시간을 지정 포맷으로 출력

- 인자가 주어지지 않으면 기본값인 "DATE_FORMAT" 으로 적용되어 출력

  ```html
  {% raw %}{{ datetime_object|date: "D d M Y"}}			<!-- Sat 09 12 2017 -->
  {{ datetime_object|date: "DATE_FORMAT"}}				<!-- 기본값 Dec.09, 2017 5 p.m -->
  {{ datetime_object|date: "DATETIME_FORMAT"}}			<!-- 12/09/2017 -->
  {{ datetime_object|date: "SHORT_DATE_FORMAT"}}			<!-- 12/09/2017 5 p.m -->
  {{ datetime_object|time: "TIME_FORMAT"}}				<!-- 5 p.m -->{% endraw %}
  ```

- 시간차 표현 포맷 지정

  ```html
  {% raw %}{{ past_date|timesince}}				<!-- 현재시간 기준 now - past_date -->
  {{ past_date|timesince:criteria_dt}}			<!-- 기준시간 기준criteria_dt - past_date -->
  {{ future_date|timeuntil}}						<!-- 현재시간 기준 future_date - now -->
  {{ future_date|timeuntil:past_dt}}				<!-- 기준시간 기준 future_date - past_date -->{% endraw %}
  ```

- datetime 객체를 비교할때 둘다 timezone이 지정되거나 둘다 timezone이 지정되지 않아야 함

- 비교하는 datetime의 timezone지정 여부가 다를 경우 빈 문자열 출력



#### Default / Default_if_none filter

- default: 값이 **False** 일 때 지정한 디폴트 값으로 출력

- 값이 none이거나 빈문자열/빈리스트...등등

- default_if_none: 값이 None일 경우, 지정 디폴트 값으로 출력

  ```html
  {% raw %}{{value|default:"nothing"}}
  {{value|default_if_none:"nothing"}}		<!-- value가 False가 아니고 none 일때만 해당 -->{% endraw %}
  ```



#### Join filter

- 순회가능한 객체를 지정 문자열로 연결

- `str.join(list)`와 동일

  ```html
  {% raw %}{{ value|join: "," }}
  <!-- value가 ['hello', 'my', 'world'] 일경우 "hello,my,world" 로 출력-->{% endraw %}
  ```



#### Line breaks/ Line breaks br filter

- TextArea등을 출력할 때 ~~필수~~ 유용

- Line breaks: text의 빈 줄은 `<p>`로 묶어주고 개행일 경우 `<br>` 태그로 출력

- Line breaks br: 모든 개행을 `<br>`로 출력

  ```html
  {% raw %}{{text|linebreaks}}
  {{text|linebreaksbr}}{% endraw %}
  ```



#### Random filter

- 지정한 리스트 중에서 임의의 값 출력



#### safe filter

- HTML Escaping이 출력되지 않도록 문자열을 SafeString으로 변환
- value에 `<>` 등을 포함한 태그 등이 있을 경우 escaping을 하지 않고 Tag로 동작할 수 있게 출력
- autoescapting이 off로 되어있을 경우 safe filter 는 작동하지 않는다.

