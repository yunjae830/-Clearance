### [뒤로가기]( https://yunjae830.github.io/-Clearance/file/Spring )



# Spring 어노테이션

### @Bean과 @Component 차이

- ##### @Bean : 개발자가 생성하지 않은 외부 라이브러리 객체를 사용 할 때, 이 어노테이션을 선언한 반환하는 객체를 빈으로 등록합니다.

- ##### @Component : 개발자가 직접 생성한 클래스를 사용 할 때, 어노테이션을 선언한 곳(객체)를 빈으로 등록합니다.



### 기본 Bean 설정

- ##### @Controller : MVC 컨트롤러 임을 명시합니다.

- ##### @Service : 비즈니스 로직을 처리하는데 사용합니다.

- ##### @Repository : DAO, 퍼시스턴스로써 DB를 처리하는데 사용합니다.

- ##### @RestController : @ResponseBody를 기본으로 하는 컨트롤러(@Controller)임을 선언합니다.

### DI(Dependency Injection)처리

- ##### @Autowired : Spring framework 전용이며 타입에 맞춰서 연결합니다.

- ##### @Inject : 자바(JSR)의 기존 어노테이션으로 타입에 맞춰서 연결합니다.

- ##### @Resource : 자바(JSR)의 기존 어노테이션으로 이름에 맞춰서 연결합니다.

- ##### @Qualifier : 같은 이름의 빈을 지시자를 넣어 특정한 빈을 만들어서 연결합니다.

- * JSR : 자바 표준 어노테이션



### 기타 Bean 설정

- ##### @Required : 설정 상의 프로퍼티를 필수로 설정해야 합니다.

- ##### @Scope : 스프링은 싱글톤이 기본인데 범위를 변경하고 싶을 때 사용합니다.

- ##### @PostConstruct : 스프링에서 객체 생성 후 호출 전 수행으로 사용합니다.

- ##### @PreDesTroy : 스프링에서 객체 생성 후 호출 전 수행으로 사용합니다.

- ##### @RequestMapping : URL을 매핑할 수 있습니다.

  - value(path) : URL을 매핑합니다.
  - method : 요청 method를 매핑합니다.
  - params : 요청 파라미터를 매핑합니다.
  - headers : 특정 헤더 값에 매핑합니다.

- ##### @RequestParam : key, value에 따라 파라미터를 매핑합니다.

- ##### @SessionAttribues : Session정보의 model 값을 유지합니다.

- ##### @RequestBody : request body 정보를 받습니다.

- ##### @ResponseBody : json 형태로 전송할 때 사용합니다.

- ##### @PathVariable : URL의 일부를 파싱합니다.

- ##### @CookieValue : 현재 사용자의 존재하는 쿠키 값 추출