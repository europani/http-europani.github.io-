---
layout: post
title: Bean Validation 유효성 검사
categories: Spring
tags: [Spring]
---

### Bean Validation 이란?  
Bean Validation 2.0 (JSR-380)이라는 기술 표준으로 검증 어노테이션를 통해 유효성을 검사 할 수 있는 인터페이스  
가장 많이 쓰는 구현체는 `hibernate Validator`이다. (ORM의 hibernate와는 무관하다)

### 0. 설정

```gradle
implementation 'org.springframework.boot:spring-boot-starter-validation'
```


### 1. 도메인

```java
@Data
public class MemberDTO {
    private Long id;
    @NotBlank
    private String name;

    @NotEmpty
    @Email
    private String email;

    @NotEmpty
    @Range(min=8, max=16, message="비밀번호는 8~16자여야 합니다")
    private String password;

    private String address;
}

```

- `@NotNull` : null 허용 안함
  - cf) `@NonNull` : Spring의 어노테이션으로 null 값이 들어오면 NPE를 발생  
   (`@Validated` 없이 동작, Java의 `@Nonnull`을 wrapping 하고 있음) 
- `@NotEmpty` : null과 공백값("")을 허용 안함
- `@NotBlank` : null과 빈값(" ")을 허용 안함
- `@Max` : 최대 값 설정
- `@Range` : 범위 값 설정
- `@Email` : 이메일형식

- `message` 속성 : 에러 메시지를 커스텀할 수 있다

### 2. Controller
- `@Validated` 가 설정된 객체의 유효성을 검증하여 에러 발생 시 FieldError, ObjectError를 생성하여 BindingResult에 추가된다

```java
@RequestMapping("/member")
@Controller
@AllArgsConstructor
public class MemberController {
    private MemberService memberService;

    @GetMapping("/signup")
    public String signupForm(Model model) {
        // 타임리프에서 랜더링 에러가 안나기 위해 비어있는 객체를 넣어준다
        model.addAttribute("member", new MemberDTO());

        return "member/memberForm";
    }

    @PostMapping("/signup")
    public String signup(@Validated @ModelAttribute("member") MemberDTO dto, BindingResult bindingResult, Model model) {
        // 에러시 다시 폼으로 이동
        if (bindingResult.hasErrors()) {    
          return "member/memberForm";
        }
      
        // 성공시 로직처리
        memberService.signup(dto);

        return "redirect:/member";
    }
}
```

- `@Validated` : 해당 객체를 검증하겠다고 설정  
    (`@Valid`를 포함하며 groups 기능이 추가되어 있다. 다만, groups 기능을 잘 사용하지 않는다)
- `BindingResult` 클래스 : 에러가 발생 할 경우 에러 내용을 담는 객체, 에러가 발생한 속성의 이름으로 에러메시지가 담긴다    
  <span style="color:red">(반드시 @Validated 객체 뒤에 와야 한다)</span>
- `@ModelAttribute("member") MemberDTO dto` = `model.addAttribute("member", dto)`  
  - 이경우 모델명을 `member`로 설정했기 때문에 뷰에서 에러메시지에 접근할 때 `${member.email}`로 가능하다


### 3. View

```html
<form name="memberForm" method="post" th:object="${member}">
  <table>
  <input type="hidden" th:field="*{id}">
    <tr>
      <th>
        <label for="name">이름</label>
      </th>
      <td>
        <input type="text" th:field="*{name}">
        <p th:if="${#fields.hasErrors('name')}" th:errors="*{name}">errorMessage</p>
      </td>
    </tr>
    <th>
        <label for="email">이메일</label>
      </th>
      <td>
        <input type="email" th:field="*{email}">
        <p th:if="${#fields.hasErrors('email')}" th:errors="*{email}">errorMessage</p>
      </td>
    </tr>
    <th>
        <label for="password">비밀번호</label>
      </th>
      <td>
        <input type="text" th:field="*{password}">
        <p th:if="${#fields.hasErrors('password')}" th:errors="*{password}">errorMessage</p>
      </td>
    </tr>
    <th>
        <label for="address">주소</label>
      </th>
      <td>
        <input type="text" th:field="*{address}">
        <p th:if="${#fields.hasErrors('address')}" th:errors="*{address}">errorMessage</p>
      </td>
    </tr>
  </table>
  <th:block th:if="*{id}">
      <button class="btn btn-primary">수정</button>
      <button type="button" class="btn btn-cancel" onclick="deleteCheck();" style="float: right;">삭제</button>
  </th:block>
  <th:block th:else="*{id}">
      <button class="btn btn-primary">등록</button>
  </th:block>
</form>
```
→`th:if="*{id}"`를 통해 id가 없으면 createForm이기에 등록 표시 / id가 있으면 updateForm이기에 수정&삭제 표시