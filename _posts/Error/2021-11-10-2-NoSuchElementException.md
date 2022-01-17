---
title:  "NoSuchElementException"
excerpt: ""

categories:
  - Error
tags:
  - [Spring, Web, Error, NoSuchElementException, Optional]
 
date: 2021-11-10
last_modified_at: 2021-11-10
---





# NoSuchElementException

`java.util.NoSuchElementException`는 존재하지 않는 것을 가져오려고 할 때 발생한다.





## Spring 예외처리

`**@ExceptionHandler(NoSuchElementException.class)** `을 사용하여 일반적인 예외처리 핸들러를 만들 수 있다. 



## Optional 예외 해결법

Optional은 null처리를 유연하게하고자 도입한 것으로 예상이 된다.

Optional으로 감싼 객체를 얻으려면 `get()`메소드를 사용해서 주로 안에 있는 객체를 얻는다. 그러나 여기서 안에 있는 객체가 null이라면 `NoSuchElementException` 예외가 발생한다.

1. `isPresent()`
   - if문을 사용해서 내부 객체가 있는지 확인.
2. `orElse()`
   - 인자로 null인 경우 다른 값을 가지도록 설정가능.
   - null경우 인자가 반환.
3. `orElseGet()`
   - 인자로 람다 표현식을 사용하여 함수를 실행
4. `orElseThrow()`
   - null일 경우 인자의 예외처리.

