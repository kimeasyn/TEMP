---
title: "Template 상속"
layout: post
date: 2017-11-28
categories: Django
comments: true
---

#### Template 상속

- 여러 템플릿 파일별로 발생되는 중복 요소들을 **상속**을 통해 중복 제거

  (header, footer, nav-bar 등등)

- 상속은 여러번 할 수 있다.(다중)

- 부모 템플릿은 전체 레이아웃을 정의하며, 자식 템플릿이 재정의할 block 들을 정의해야한다

  ```
  { % block block명 % }
  { % endblock% }
  ```


- block 명은 중복이 불가능 하다

- 자식 템플릿은 부모 템플릿이 정의한 block 영역만 재정의 가능

- 상속 문법 : 자식 템플릿 코드 내에 최상단에 위치

  ```
  { % extends '부모템플릿 파일명' % }
  { % block 부모에서 정의한 block 명 % }
  	<html tags>...
  { % endblock % }
  ```

- 부모 template가 지정한 block 영역 외의 tag는 무시됨

  ​

#### 이중 상속

- 프로젝트 layout -> app별 layout -> 화면 Template 와 같은 이중 상속 추천

- 프로젝트 공통 layout 관리(위치)

  1. root 폴더 밑에 app,project와 동등한 level의 디렉토리에서 관리
  2. **/project/template 디렉토리에서 관리(추천)**
  3. ~~app 폴더 내부에 위치(비추천))~~

  > 어느것을 해도 크게 상관없으나 2번과 같은 방법으로 settings, root template  과 같은 공통 파일을 관리하는것이 더 용이

  ```python
  # 2번의 방법으로 코드를 관리할 경우
  # /project/template/settings.py
  TEMPLATES = [
      {
        ...
          'DIRS': [
              os.path.join(BASE_DIR, 'project', 'template')
              # /project/template 위치에 template파일이 존재한다는것을 명시
          ],
        ...
  ```

- 부모 템플릿에서 정의한 block들은 자식 템플릿에서 재정의 하지 않아도 자식 템플릿의 자식 템플릿들이 사용 가능