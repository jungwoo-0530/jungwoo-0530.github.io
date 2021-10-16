---
title:  "지원하는 메소드 아규먼트와 리턴 타입"
excerpt: ""

categories:
  - SpringMvc
tags:
  - [Spring, Web, Mvc]
 
date: 2021-10-09
last_modified_at: 2021-10-09
---



# 지원하는 메소드 아규먼트와 리턴 타입

#### 핸들러 메소드 아규먼트: 주로 요청 그 자체 또는 요청에 들어있는 정보를 받아오는데 사용한다.

| 핸들러 메소드 아규먼트                                       | 설명                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| WebRequest                  NativeWebRequest ServletRequest(Response) HttpServletRequest(Respon se) | 요청 또는 응답 자체에 접근 가능한 API                        |
| InputStream                                Reader                                   OutputStream                             Writer | 요청 본문을 읽어오거나, 응답 본문을 쓸 때 사용할 수 있는 API |
| PushBuilder                                                  | 스프링 5, HTTP/2 리소스 푸쉬에 사용                                    이미지 같은 것을 미리 push한다. 속도가 빨라질 수 있음. |
| HttpMethod                                                   | GET, POST, ... 등에 대한 정보                                |
| Locale                                                        TimeZone                                                      ZoneId | LocaleResolver가 분석한 요청의 Locale 정보                   |
| @PathVariable                                                | URI 템플릿 변수 읽을 때 사용                                 |
| @MatrixVariable                                              | URI 경로 중에 키/값 쌍을 읽어 올 때 사용                     |
| @RequestParam                                                | 서블릿 요청 매개변수 값을 선언한 메소드  아규먼트 타입으로 변환해준다.  단순 타입인 경우에 이 애노테이션을 생략할 수 있다. |
| @RequestHeader                                               | 요청 헤더 값을 선언한 메소드 아규먼트 타입으로 변환해준다.   |
| ...                                                          | ...                                                          |

참고: https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-arguments

#### 핸들러 메소드 리턴: 주로 응답 또는 모델을 랜더링할 뷰에 대한 정보를 제공하는데 사용한다.

| @ResponseBody            | 리턴 값을 HttpMessageConverter를 사용해 응답 본문으로 사용한다. |
| ------------------------ | ------------------------------------------------------------ |
| HttpEntity ReponseEntity | 응답 본문 뿐 아니라 헤더 정보까지, 전체 응답을 만들 때 사용한다. |
| String                   | ViewResolver를 사용해서 뷰를 찾을 때 사용할 뷰 이름.         |
| View                     | 암묵적인 모델 정보를 랜더링할 뷰 인스턴스                    |
| Map Model                | (RequestToViewNameTranslator를 통해서) 암묵적으로 판단한 뷰 랜더링할 때 사용할 모델 정보 |
| @ModelAttribute          | (RequestToViewNameTranslator를 통해서) 암묵적으로 판단한 뷰 랜더링할 때 사용할 모델 정보에 추가한다. 이 애노테이션은 생략할 수 있다. |

참고: https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-returntypes