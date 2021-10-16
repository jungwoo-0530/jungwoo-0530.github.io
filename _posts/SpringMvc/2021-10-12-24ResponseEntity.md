# ResponseEntity - 파일 다운로드



#### 파일 리소스를 읽어오는 방법

- 스프링 ResourceLoader 사용하기

#### 파일 다운로드 응답 헤더에 설정할 내용

- Content-Disposition: 사용자가 해당 파일을 받을 때 사용할 파일 이름
- Content-Type: 어떤 파일인가
- Content-Length: 얼마나 큰 파일인가

#### 파일의 종류(미디어 타입) 알아내는 방법

- http://tika.apache.org/

#### ResponseEntity

- 응답 상태 코드 
- 응답 헤더
- 응답 본문





```java
    @Autowired
    private ResourceLoader resourceLoader;
```

classpath를 이용하여 원하는 file을 가져올 수 있습니다.

```java
@GetMapping("/file/{filename:.+}")
public ResponseEntity<Resource> downloadFile(@PathVariable String filename)
throws IOException {
	Resource resource = resourceLoader.getResource("classpath:" + filename); 
 
  File file = resource.getFile();
  
	Tika tika = new Tika();
	String type = tika.detect(file);
  
	return ResponseEntity.ok()
    .header(HttpHeaders.CONTENT_DISPOSITION, "attachment; filename=\"" + resource.getFilename() + "\"")
    .header(HttpHeaders.CONTENT_TYPE, type) .header(HttpHeaders.CONTENT_LENGTH,
                                                    String.valueOf(file.length()))
    .body(resource);
}
```



```java
ResponseEntity<Resource> downloadFile(@PathVariable String filename)
```

<Resource>를 붙여서 응답본문에 대한 타입을 정의할 수 있다. 

```java
retrun ResponseEntity.ok()
```

ResponseEntity에 ok 응답을 만들겠다. 이것만으로 상태 코드값이 정의. ok 코드니깐 200

```java
.header(HttpHeaders.CONTENT_DISPOSITION, "attachement; filename=\"" + resource.getFilename() + "\"")
```

헤더에 설정을 해주면 좋은 값들이 사용자가 파일을 다운로드 받을 때, 사용자가 다운로드 받을 때 판업창이 열리면서 어떠한 이름으로 다운로드 받을지 정할 수 있는 것.

```java
File file = resource.getFile();

Tika tika = new Tika();
String mediaType = tika.detect(file);

.header(HttpHeaders.CONTENT_TYPE, mediaType)
```

이 파일이 어떠한 파일(미디어 타입)인지 알려주는 것.

tika는 아래와 같이 의존성을 추가한다.

```xml
<dependency>
    <groupId>org.apache.tika</groupId>
    <artifactId>tika-core</artifactId>
    <version>2.1.0</version>
</dependency>
```



```java
File file = resource.getFile();

.header(HttpHeaders.CONTENT_LENGTH, String.valueOf(file.length())
```

이 파일에 크기가 얼마나 되는지 Length로 알려주는 헤더

```java
.body(resource)
```

최종적으로 바디에 리소스를 넣어주면 끝.





#### 참고

- https://spring.io/guides/gs/uploading-files/
- https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Disposition
- https://www.baeldung.com/java-file-mime-type