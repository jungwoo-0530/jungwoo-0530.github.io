# @RequestMapping(요청 매개변수)

요청 매개변수란 ?

우리가 요청을 보낼 때 key value에 해당하는 것을 쿼리 파라메타로 보내는 경우가 있고 폼 데이터로 형식으로 보내는 경우가 있다.

ex) 쿼리 매개변수 : /event/{id}?name=jungwoo



#### @RequestParam

- 요청 매개변수에 들어있는 단순 타입 데이터를 메소드 아규먼트로 받아올 수 있다.

- 값이 반드시 있어야 한다.

  - required=false 또는 Optional을 사용해서 부가적인 값으로 설정할 수도 있다.
  - String이 아닌 값들은 타입 컨버전을 지원한다.

- Map<String, String> 또는 MultiValueMap<String, String>에 사용해서 모든 요청 매개변수를 받아 올 수도 있다.

- 이 애노테이션은 생략 할 수 잇다.

- HttpServletRequest 객체와 같은 역할은 한다.

- @RequestParam("가져올 데이터의 이름") [데이터타입] [가져온데이터를 담을 변수]

  - ```java
    @GetMapping("/hello")
    public String checkId(@RequestParam("id") String id){
      model.addAttribute("id", id); 
      model.addAttribute("pwd", pwd); 
      return "/hello";
    
    }
    ```

  - ```java
    public String method1(@RequestParam("id") int id) { ... } 
    public String method2(@RequestParam("id") int id, 
                          @RequestParam("name") 
                          String name, @RequestParam("file") MultipartFile file) { ... } 
    public String method3(@RequestParam Map<String, String> params) { ... } 
    public String method4( @RequestParam(value="id", required=false
                                         , defaultValue="-1") int id) { ... } 
    public String method5(@RequestParam int id) { ... } 
    
    출처: https://springsource.tistory.com/13 [Rednics Blog]
    ```

    1. method1() :: 이 메소드에서 선언은 id 요청 파라미터를 int 타입으로 변환해서 메소드의 id 파라미터에 넣어줌

    2. method2() :: @RequestParam은 하나 이상의 파라미터에 적용할 수 있음, 스프링의 내장 변환기가 다룰수 있는 모든 타입을 지원함

    3. method3() :: @RequestParam에 파라미터 이름을 지정하지 않고 Map<String, String> 으로 선언하여 파라미터를 담은 맵으로 받을 수 있고, 이 때 파라미터의 이름은 맵의 키에, 값은 맵의 값에 담겨 전달

    4. method4() :: @RequestParam을 사용했다면 해당 파라미터가 반드시 있어야 하지만 **선택적으로 하고싶다면 required 엘리먼트를 false로 설정**. 요청 파라미터가 존재하지 않을 때의 default값도 설정할 수 있다. 해당 메소드는 required와 defaultValue 엘리먼트를 설정한 예

    5. method5() :: 메소드 파라미터의 이름과 요청 파라미터의 이름이 일치하기 때문에@RequestParam의 이름 엘리먼트를 생략한 예

    6. String, int 와 같은 단순 타입인 경우에는 @RequestParam을 아예 생략할 수도 있는데 이 때는 메소드 파라미터와 같은 이름의 요청 파라미터 값을 받음. 하지만 파라미터의 개수가 많고 종류가 다양해지면 코드를 이해하는데 불편할 수 있기 때문에 명시적으로 @RequestParam을 부여해주는 것을 권장한다.

#### 요청 매개변수란?

- 쿼리 매개변수
- 폼 데이터(HTTP 바디 부분에 있는 것)



#### 아규먼트 vs 파라미터

```

Parameter :	매개변수,	함수와 메서드 입력 변수(Variable) 명
Argument : 전달인자,인자,	함수와 메서드의 입력 값(Value)

```



#### 참고;

- https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-requestparam



