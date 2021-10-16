---
title:  "@PathVariable사용시 확장자 문제"
excerpt: "PathVariable사용시 확장자(extension)"

categories:
  - Error
tags:
  - [Spring, Web, Error]
 
date: 2021-10-15
last_modified_at: 2021-10-15
---





# @PathVariable 사용 시 확장자(extension) 문제



```java
 @GetMapping("/file/{filename}")
    public ResponseEntity<Resource> downloadFile(@PathVariable String filename) throws IOException {
      Resource resource = resourceLoader.getResource("classpath:" + filename);
      File file = resource.getFile();
      ...
}
```

위와 같이 맵핑 후에 요청을 한 결과 filename에 test.jpg가 들어와야 정상인데 확장자가 빠진 상태 인 test만 들어왔다.

그 결과 아래와 같은 오류가 나온다.

![image-20211016024545820](./img/image-20211016024545820.png)

말 그대로 test만 들어와서 class path에서 해당 리소스를 찾을 수 없다고 나온 것이다.



그래서 찾아본 결과 총 2가지 방법을 알아냈다.

```java
 @GetMapping("/file/{filename:.+}")
```

위 와같이 변경하는 방법.



**WebMvcConfigurationSupport상속후 설정파일 수정**하여 사용할 수도 있다.

```java
@Configuration
public class CustomWebConfiguration extends WebMvcConfigurationSupport {
     
    @Bean
    public RequestMappingHandlerMapping 
      requestMappingHandlerMapping() {
  
        RequestMappingHandlerMapping handlerMapping
          = super.requestMappingHandlerMapping();
        handlerMapping.setUseSuffixPatternMatch(false);
        return handlerMapping;
    }
}
```

위와 같이 설정을 해야지



```java
@GetMapping("/file/{filename}")
```

위와 같이 사용할 수 있다.