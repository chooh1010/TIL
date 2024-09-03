## SOLID 적용

```java
 public class MemberServiceImpl implements MemberService {
 	private final MemberRepository memberRepository = new MemoryMemberRepository();
 }
```

지금 코드는 역할과 구현을 분리했다.

다형성도 활용하고, 인터페이스와 구현 객체를 분리했다.

하지만 인터페이스뿐만 아니라 구현 클래스에도 의존하고 있다.

기능을 확장해서 변경하면, 클라이언트 코드에 영향을 준다. 따라서 OCP를 위반한다.

```java
 public class MemberServiceImpl implements MemberService {
 	private final MemberRepository memberRepository;
 }
```

인터페이스에만 의존하도록 코드를 변경했지만 구현체가 없이 코드를 실행할 수 없다.

이 문제를 해결하려면 누군가가 클라이언트인  `MemberServiceImpl`에 `MemberRepository`의 구현 객체를 대신 생성하고 주입해주어야 한다.

```java
 public class AppConfig {
 	public MemberService memberService() {
		 return new MemberServiceImpl(new MemoryMemberRepository());
    }
 }
```

AppConfig는 생성한 객체 인스턴스의 참조를 생성자를 통해서 주입해준다.

```java
 public class MemberServiceImpl implements MemberService {
 	private final MemberRepository memberRepository;
 	public MemberServiceImpl(MemberRepository memberRepository) {
		 this.memberRepository = memberRepository;
    }
 }
```

이런식으로 생성자를 만들면 생성자를 통해 구현 객체가 주입된다.

```java
AppConfig appConfig = new AppConfig();
memberService = appConfig.memberService();
```

memberService를 사용할 때 appConfig가 구현 객체가 주입된 memberServiceImpl을 반환해 주어서 사용할 수 있다.

`MemberServiceImpl`은 의존관계에 대한 고민은 외부(AppConfig)에 맡기고 실행에만 집중하면 된다.

객체를 생성하고 연결하는 역할과 실행하는 역할이 명확히 분리되었다.

appConfig 객체는 memoryMemberRepository객체를 생성하고 그 참조값을 memberServiceImpl을 생성하면서 생성자로 전달한다.

이를 DI(Defendency Injection) 의존관계 주입이라 한다.

### 제어의 역전 IoC(Inversion of  Control)

위 예시처럼 AppConfig가 프로그램에 대한 제어 흐름에 대한 권한을 가진다.

이렇듯 프로그램의 제어 흐름을 직접 제어하는 것이 아니라 외부에서 관리하는 것을 제어의 역전이라 한다.

### 의존관계 주입 DI(Dependency Injection)

애플리케이션 실행 시점(런타임)에 외부에서 실제 구현 객체를 생성하고 클라이언트에 전달해서 클라이언트와 서버의 실제 의존관계가 연결되는 것을 의존관계 주입이라 한다.

의존관계 주입을 사용하면 클라이언트 코드를 변경하지 않고, 클라이언트가 호출하는 대상의 타입 인스턴스를 변경할 수 있다.

의존관계 주입을 사용하면 정적인 클래스 의존관계를 변경하지 않고, 동적인 객체 인스턴스 의존관계를 쉽게 변경할 수 있다.

AppConfig 처럼 객체를 생성하고 관리하면서 의존관계를 연결해 주는 것을 IoC 컨테이너 또는 DI 컨테이너라 한다.

### 스프링으로 전환

```java
@Configuration
 public class AppConfig {
    @Bean
 	public MemberService memberService() {
 		return new MemberServiceImpl(memberRepository());
    }
 }
```

설정을 구성한다는 뜻의 @Configuration을 붙여주고 각 메서드에 @Bean을 붙여준다. 이렇게 하면 스프링 컨테이너에 스프링 빈으로 등록한다.

```java
ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
MemberService memberService = 
applicationContext.getBean("memberService", MemberService.class);
```

ApplicationContext를 스프링 컨테이너라 한다.

기존에는 개발자가 AppConfig를 사용해서 직접 객체를 생성하고 DI를 했지만, 이제부터는 스프링 컨테이너를 통해서 사용한다.

스프링 컨테이너는 @Configuration이 붙은 AppConfig를 설정 정보로 사용한다. 여기서 @Bean이라 적힌 메서드를 모두 호출해서 반환된 객체를 스프링 컨테이너에 등록한다. 이를 스프링 빈이라 한다.

이전에는 개발자가 필요한 객체를 AppConfig를 사용해서 직접 조회했지만, 스프링 컨테이너를 통해서 필요한 스프링 빈(객체)를 찾을 수 있다.

기존에는 개발자가 직접 자바코드로 모든 것을 했다면 이제부터는 스프링 컨테이너에 객체를 스프링 빈으로 등록하고, 스프링 컨테이너에서 스프링 빈을 찾아서 사용하도록 변경되었다.