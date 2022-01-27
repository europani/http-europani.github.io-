---
layout: post
title: '[스프링부트] Spring Security'
categories: Spring
tags: [Spring, Spring Security]
---

### Security 개념
- Spring Security : 보안솔루션을 제공하는 스프링 하위 프레임워크
  - Spring Security는 기본적으로 인증 절차를 거친 후 인가 절차를 진행한다
  - Principal을 아이디, Credential을 비밀번호로 사용하는 Credential 기반 인증 방식을 사용한다

- 인증(Authentication) : 자신이 누구인지 확인하는 작업 
  - ex) 로그인
- 권한(Authorization) : 특정 부분에 접근할 수 있는지 확인하는 작업
  - ex) 등급

### 1. Configuration
```gradle

dependencies {
  ...
  implementation 'org.springframework.boot:spring-boot-starter-security'
  ...
}
```

#### SecurityConfig
```java
@AllArgsConstructor
@EnableWebSecurity
@Configuration
@EnableGlobalMethodSecurity(securedEnabled = true, prePostEnabled = true)
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    private final UserService userService;
    private CustomFailureHandler customFailureHandler;

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    // static 파일은 권한 통과
    @Override
    public void configure(WebSecurity web) throws Exception {     
        web.ignoring().requestMatchers(PathRequest.toStaticResources().atCommonLocations());
            .antMatchers("/h2-console/**", "/scss/**", "/error");
    }

    // 인가 설정
    @Override
    protected void configure(HttpSecurity http) throws Exception {    
        // 페이지 권한 설정
        http.authorizeRequests()
                .antMatchers("/user/**").hasRole("USER")
                .antMatchers("/admin/**").hasRole("ADMIN")
                .antMatchers("/", "/login").permitAll()
                .antMatchers("/image/**").permitAll()
                .anyRequest().authenticated();    // anyRequest() : 위를 제외한 나머지
        // 로그인 설정
        http.formLogin()
                .loginPage("/login")
                .loginProcessingUrl("/loginProcess")    // default: /login(POST) 변경시
                .defaultSuccessUrl("/")
                .failureHandler(customFailureHandler)
             // .usernameParameter("id")                // default: username 변경시
             // .passwordParameter("pwd")               // default: password 변경시
                .permitAll();                   // ★ 반드시 필요 (로그인페이지 권한 제거)
        // 로그아웃 설정
        http.logout()
                .logoutRequestMatcher(new AntPathRequestMatcher("/logout"))
                .logoutSuccessUrl("/")
                .invalidateHttpSession(true);
        // 403 예외처리
        http.exceptionHandling()
                .accessDeniedPage("/denied");
        // CSRF 비활성화
        http.csrf().disable();
    }

    // 로그인 인증 설정 (UserDetailsService 구현시 제거)
    // @Override
    // protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    //     auth.userDetailsService(userService).passwordEncoder(new BCryptPasswordEncoder());
    // }
}
```
- `@EnableWebSecurity` : Spring Security 활성화를 위한 어노테이션
- `WebSecurityConfigurerAdapter` : Spring Security 설정파일로써 사용하기 위해 상속

1\. `configure(WebSecurity web)` : 처음으로 WebSecurity에 거치게 된다
  - PathRequest.toStaticResources().atCommonLocations() : `/src/main/resources/static/` 이하의 기본 정적폴더들은 통과시킨다
    - `/css/**`, `/js/**`, `/images/**`, `/webjars/**`, `/favicon.*` 등
  - antMatchers(Matchers) : `/src/main/resources/static/` 이하의 다음과 같은 Matchers들을 추가로 통과시킨다

<br/>

2\. `configure(HttpSecurity http)` : WebSecurity를 통과 한 다음 HttpSecurity를 거치게 된다
- 페이지 권한설정
  - hasRole(role) : 해당 권한만
  - hasAnyRole(role1, role2, ...) : 해당 권한들만
  - permitAll() : 권한무관하게 전부 허용
  - denyAll() : 권한무관하게 전부 제한
  - authenticated() : 인증된 사용자만 (= 모든 권한)
  - anonymous() : 인증되지 않은 사용자만
  - hasIpAddress(String) : 해당 IP만


![](https://user-images.githubusercontent.com/48157259/132278915-5e67fd65-ecde-4bae-b99d-e571f781fec7.png)
- 구현해야 할 인터페이스 : `UserDetails`, `UserDetailsService`

(1) 사용자가 ID, Pwd를 사용하여 `AuthenticationFilter`로 요청이 들어온다  
(2) `UsernamePasswordAuthenticationToken`이 토큰을 발급한다  
(3) 생성된 토큰을 `AuthenticationManger`에게 전달한다  
(4) 전달받은 토큰을 `AuthenticationProvider`에게 전달하여 아이디와 비밀번호를 조회하는 인증을 요구한다.  
  \- 매니저는 실제로 인증을 처리할 여러개의 `AuthenticationProvider`를 갖고 있다  
(5) 조회된 아이디는 `UserDetailsService`로 전달되며 전달받은 아이디를 기반으로 사용자의 데이터를 조회한다.  
(6) `UserDetails`에 조회된 데이터가 담긴다.  
(7) 조회된 데이터 `AuthenticationProvider`에게 전달  
(8) 인증 처리 후 인증된 토큰을 `AuthenticationManger`에게 전달  
(9) 인증된 토큰을 `AuthenticationFilter`에게 전달  
(10) 인증된 토큰을 `SecurityContextHolder`에 저장

#### 관련 객체 소개
- `Authentication` 객체 : 인증에 필요한 정보, 인증 후에는 `SecurityContext`에 보관된다

- `AuthenticationManger` : 인증 요청을 받고 Authentication 객체를 적절한 Provider를 찾아 넘긴다. 
```java
public interface AuthenticationManager {
	Authentication authenticate(Authentication authentication) throws AuthenticationException;
}
```

- `AuthenticationProvider` : 실제 **인증**이 진행 되는 곳, 인증된 사용자는 `UserDetailsService`로 넘어간다
```java
public interface AuthenticationProvider {
	Authentication authenticate(Authentication authentication) throws AuthenticationException;

	boolean supports(Class<?> authentication);
}
```

- `UserDetailsService` : **인가**가 진행 되는 곳, 인증된 사용자의 인가를 위해 데이터를 조회한다 (인터페이스 구현 - 조회 메서드)

- `UserDetails` : 사용자의 데이터가 담기는 객체 (인터페이스 구현 - 데이터 필드 및 옵션)

### 2. Entity, DTO
#### (0) UserRole
```java
@Getter
@RequiredArgsConstructor
public enum UserRole {
    ADMIN("ROLE_ADMIN", "관리자"),
    MANAGER("ROLE_MANAGER", "직원"),
    USER("ROLE_USER", "사용자");

    private final String key;
    private final String description;
}
```
→ 스프링 시큐리티의 권한 코드에는 항상 `ROLE_`이 접두사로 있어야한다

#### (1) User
```java
@Getter
@ToString
@NoArgsConstructor
@AllArgsConstructor
@Builder
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String username;

    private String password;

    private String auth;

    @Enumerated(EnumType.STRING)
    private UserRole role;
}
```

#### (2) UserDTO
```java
@Builder
@NoArgsConstructor
@AllArgsConstructor
@Getter
@Setter
public class UserDTO {
    private String username;
    private String password;
    private String auth;
}
```

#### (3) ★ CustomUserDetails (DTO)
  - `UserDetails` 구현 - 7개 메서드 오버라이드
  - `UserDetails` : Spring Security에서 **사용자 정보를 담는 인퍼페이스**

```java
public class CustomUserDetails implements UserDetails {

    private User user;

    public CustomUserDetails(User user) {
        this.user = user;
    }

    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {    // 권한 반환
        Collection<GrantedAuthority> auth = new ArrayList<GrantedAuthority>();
        auth.add(new SimpleGrantedAuthority(user.getAuth()));
        return auth;
    }

    @Override
    public String getPassword() {     // 비밀번호 반환
        return user.getPassword();
    }

    @Override
    public String getUsername() {     // id 반환 (unique 값)
        return user.getUsername();
    }

    @Override
    public boolean isAccountNonExpired() {    // 계정만료여부 반환(true : 만료되지 않음)
        return true;
    }

    @Override
    public boolean isAccountNonLocked() {    // 계정잠금여부 반환(true : 잠금되지 않음)
        return true;
    }

    @Override
    public boolean isCredentialsNonExpired() {    // 비밀번호 만료여부 반환(true : 만료되지 않음)
        return true;
    }

    @Override
    public boolean isEnabled() {    // 계정 활성화여부 반환(true : 활성화)
        return true;
    }
}
```

### 3. Repository
```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByUsername(String username);
}
```

### 4. ★ Service
  - `UserDetailsService` 구현 - `loadUserByUser(String string)` 오버라이드
  - `UserDetailsService` : Spring Security에서 인가를 위해 **사용자 정보를 불러오는 인퍼페이스**

```java
@AllArgsConstructor
@Service
public class UserService implements UserDetailsService {

    private UserRepository userRepository;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        User user = userRepository.findByUsername(username)
                .orElseThrow(() -> new UsernameNotFoundException(username));

        return new CustomUserDetails(user);
    }


    // 회원가입 추가구현
    public Long signup(UserDTO userDTO) {
        // 패스워드 암호화
        BCryptPasswordEncoder passwordEncoder = new BCryptPasswordEncoder();
        userDTO.setPassword(passwordEncoder.encode(userDTO.getPassword()));

        return userRepository.save(dtoToEntity(userDTO)).getId();
    }

    User dtoToEntity(UserDTO userDTO) {
        User user = User.builder()
                .username(userDTO.getUsername())
                .password(userDTO.getPassword())
                .build();
        return user;
    }
```
- `loadUserByUsername`을 통하여 입력된 username으로 해당 계정이 존재하는지 체크한다.
- 반환 타입으로 `UserDetails`를 사용하며 UserDetails를 구현한 `CustomUserDetails`로 데이터를 넘겨 인증에 사용된다.


### 5. Controller
```java
@AllArgsConstructor
@Controller
public class UserController {

    private UserService userService;

    @RequestMapping("/login")       // GET과 POST 모두를 위해 @RequestMapping 사용
    public String index(){

        return "member/login";
    }

    @PostMapping("/signup")
    public String signup(UserDTO userDTO) {
        userService.signup(userDTO);

        return "redirect:/signup";
    }

    @GetMapping("/logout")
    public String logout(HttpServletRequest request, HttpServletResponse response) {
        new SecurityContextLogoutHandler().logout(request, response, SecurityContextHolder.getContext().getAuthentication());

        return "redirect:/";
    }

    @GetMapping("/denied")
    public String denied() {
        return "403";
    }
}
```


### 6. View
```gradle

dependencies {
  ...
  implementation 'org.thymeleaf.extras:thymeleaf-extras-springsecurity5'
  ...
}
```

- authorize : 인증/인가 관련 필터 적용
  - `isAuthenticated()` : 인증된 회원에게만 출력 (=모든 권한)
  - `isAnonymous()` : 인증 안된 회원에게만 출력 
  - `hasRole(role)` : 해당 권한을 갖은 회원에게만 출력
  - `hasAnyRole(role, ...)` : 해당 권한들을 갖은 회원에게만 출력

- authentication : 인증된 객체 정보 출력
  - `pricipal` : 인증된 객체의 모든 정보 출력
  - `principal.authorities` : 인증된 객체의 권한들 출력
  - `name` : 인증된 객체의 username(ID) 출력

```html
<!DOCTYPE html>
<html lang="ko" xmlns:th="http://www.thymeleaf.org"
      xmlns:sec="http://www.thymeleaf.org/extras/spring-security">


<div sec:authorize="isAuthenticated()">
  This content is only shown to authenticated users.
</div>
<div sec:authorize="hasRole('ADMIN')">
  This content is only shown to administrators.
</div>
<div sec:authorize="hasRole('USER')">
  This content is only shown to users.
</div>

Logged user: <span sec:authentication="name">Bob</span>
Roles: <span sec:authentication="principal.authorities">[ROLE_USER, ROLE_ADMIN]</span>

</html>
```

<츌처>
https://mangkyu.tistory.com/77