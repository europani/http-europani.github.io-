---
layout: post
title: '[JPA] Spring Data JPA'
categories: Spring
tags: [Spring, JPA]
---
Spring Data JPA는 데이터 접근 계층에서 지루하고 반복적인 CRUD 문제를 해결하기 위해 기본적인 CRUD를 처리하는 인터페이스가 정의되어 있다.

### Repository 인터페이스
- Repository 인터페이스를 생성한후 `JpaRepository<Entity, 기본키 타입>` 을 상속받으면 **기본적인 CRUD 자동 생성**

```java
@Repository
public interface MemberRepository extends JpaRepository<MemberEntity, Integer> {

     int countByName(String name);

     Optional<Members> findByUsername(String username);

     @Transactional
     @Modifying     // DML문을 처리할 경우 사용
     @Query("UPDATE Members m SET m.password=:password WHERE m.username=:username")
     int changePassword(@Param("username") String username, @Param("password") String password);
}
```
- 기본적인 CRUD가 구현된 인터페이스를 상속받고 있기 때문에 따로 정의 할 필요는 없음.
- 필요에 따라 메서드를 추가하거나 오버라이딩해서 사용함.


#### JpaRepository
- JPA를 사용 할 수 있도록 정의된 인터페이스
- JpaRepository → PagingAndSortingRepository → CrudRepository 순으로 extends 되어있다.

```java
public interface JpaRepository<T, ID> extends PagingAndSortingRepository<T, ID> {

	List<T> findAll();

	List<T> findAll(Sort sort);

	List<T> findAllById(Iterable<ID> ids);

	<S extends T> List<S> saveAll(Iterable<S> entities);

    ...
}

```

#### CrudRepository
- JPA의 기본적인 CRUD가 구현되어 있는 인터페이스

```java
public interface CrudRepository<T, ID> extends Repository<T, ID> {

  <S extends T> S save(S entity);               // 해당 entity 저장

  Iterable saveAll(Iterable entities)           // 해당 entity 복수 저장

  Optional<T> findById(ID primaryKey);          // ID로 entity 찾기(1개)

  Iterable<T> findAll();                        // 모든 entity 찾기

  boolean existsById(ID primaryKey);            // ID로 entity 존재여부 확인

  long count();                                 // 사용가능한 entity 수 반환

  void delete(T entity);                        // 해당 entity 삭제

  void deleteById(ID id)                        // ID로 entity 삭제

  void deleteAll();                             // 모든 entity 삭제

  void deleteAll(Iterable<? extends T> entities);  // 해당 entity 복수 삭제

  ...
}
```

### Controller
```java
@Controller
@RequestMapping("/member")
public class MemberController {

    @Autowired
    private MemberRepository memberRepository;

    @GetMapping("")
    public String memberList(Model model) {
        List<MemberEntity> memberList = memberRepository.findAll(Sort.by(Sort.Direction.ASC, "name"));

        model.addAttribute("memberList", memberList);

        return "/member/memberList";
    }

    @GetMapping("/{id}")
    public String memberDetail(@PathVariable("id") Long id, Model model) {
        Optional<MemberEntity> memberWrapper = memberRepository.findById(id);
        MemberEntity member = memberWrapper.get();

        model.addAttribute("member", member);

        return "/member/memberDetail";
    }

    @PostMapping("/create")
    public String memberCreate(MemberEntity memberEntity) {

        memberRepository.save(memberEntity);

        return "redirect:/member";
    }

    @PostMapping("/{id}/update")
    public String memberUpdate(@PathVariable("id") Long id, MemberEntity memberEntity, 
                                RedirectAttributes ra) {

        memberRepository.save(memberEntity);

        ra.addAttribute("updateId", id);
        ra.addFlashAttribute("msg", "수정에 성공하였습니다.");

        return "redirect:/member/{updateId}/update";
    }

    @PostMapping("/{id}/delete")
    @ResponseBody
    public void memberDelete(@@PathVariable("id") Long id) {

        memberRepository.deleteById(id);
    }

}
```

### 기본 사용법  

|기능|메서드|설명|
|:---:|:---:|:---:|
|목록조회|findAll()|엔티티 전체 목록 출력|
|상세조회|findById(id)|엔티티 1개 출력|
|생성(Create)|save(entity)|해당 엔티티 저장|
|수정(Update)|save(entity)|해당 엔티티 수정|
|삭제(delete)|delete(entity) or deleteById(id)|해당 엔티티 삭제|
|전체삭제|deleteAll()|모든 엔티티 삭제|

\* 생성과 수정에서 save()의 차이점
- 생성과 수정에서 모두 save()를 사용하지만 생성은 **persist()** 수정은 **merge()** 사용한다. SimpleJpaRepository의 save()에서 확인할 수 있다.

```java
public class SimpleJpaRepository<T, ID> implements JpaRepositoryImplementation<T, ID> {
    ...

    @Transactional
	public <S extends T> S save(S entity) {

		if (entityInformation.isNew(entity)) {
			em.persist(entity);
			return entity;
		} else {
			return em.merge(entity);
		}
	}

    ...
}
```
1. 생성[persist] : entity의 id 값이 없을 때는 create를 실행한다
2. 수정[merge] : entity의 id 값이 존재할 때는 select -> update를 실행한다. 
   - HTTP PUT처럼 update 쿼리실행 : 모든 컬럼의 데이터가 변경된다
   - <span style="color:red">update 쿼리를 사용하고 싶으면 merge보단 변경감지(Dirty Check) 사용을 추천한다</span> 

#### Query Method 방식
- findBy 뒤에 키워드가 붙음
- `findBy` 메서드는 항상 `Optional` 타입으로 반환된다.
- 쿼리메서드 방식은 메서드명이 길어지기 때문에 일반적으로 선호되지 않는다.

|Keyword|메서드|Query|
|:---:|:---:|:---:|
|And|findByLastnameAndFirstname|where lastname = ? and firstname = ?|
|Or|findByLastnameOrFirstname|where lastname = ? or firstname = ?|
|Is, Equals|findByFirstname,findByFirstnameEquals|where firstname = ?|
|Between|findByStartDateBetween|where startDate between ? and ?|
|GreaterThan|findByAgeGreaterThan|where age > ?|
|GreaterThanEqual|findByAgeGreaterThanEaual|where age >= ?|
|IsNull, Null|findByAge(Is)Null|where age is null|
|Like|findByFirstnameLike|where firstname like ?|
|StartingWith|findByFirstnameStartingWith|where firstname like ?|
|Containing|findByFirstnameContaing|where firstname like ?|
|OrderBy|findByAgeOrderByLastnameDesc|where age = ? order by lastname desc|
|Not|findByLastnameNot|where lastname <> ?|

```java
@Repository
public interface MemberRepository extends JpaRepository<MemberEntity, Integer> {

    List<MemberEntity> findByLastnameOrderByIdDesc(String lastname);
}
```


#### @Query 방식
- 이름 길어지는 쿼리메서드 방식 대신 어노테이션을 사용하여 JPQL을 입력하는 방법을 사용할 수 있다.
- 쿼리메서드 방식보다 선호된다.
- 다만, 정적 값을 가지는 한계가 있기에 동적 쿼리를 위해서 `QueryDSL`을 사용할 수 있다.

```java
@Repository
public interface MemberRepository extends JpaRepository<MemberEntity, Integer> {

    @Query("SELECT m FROM MemberEntity m WHERE lastname=:lastname ORDER BY m.id DESC")
    List<MemberEntity> getListDesc(@Param("lastname") String lastname);
}
```

### 벌크 연산
- **벌크 연산은 영속성 컨텍스트를 무시하고 DB에 직접 쿼리를 날린다**
  - 데이터 유효성을 위해 벌크 연산 후 영속성 컨텍스트를 초기화 한 후 데이터를 사용해야 한다(clear)
  - `clearAutomatically = true` 옵션을 사용하면 쿼리 실행 후 clear를 자동으로 한다

```java
@Modifying(clearAutomatically = true)       // clearAutomatically 설정
@Query("update Member m set m.age = m.age + 1 where m.age >= :age")
int bulkAgePlus(@Param("age") int age);
```


### Pageable 인터페이스 (페이징처리)
- `Pageable` 인터페이스 : 페이지 처리에 필요한 정보를 전달하는 용도의 타입
- `PageRequest` 클래스 : `Pageable`의 구현체
  - protected 이기 때문에 `new`를 사용하지 못하고 `of()`를 통해 사용한다

```java
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;

@Test
public void testpageDefault() {
    // 1페이지 10개
    Pageable pageable = PageRequest.of(0, 10);
    Page<Memo> result = memoReopsitory.findAll(pageable);

    System.out.println(result.getTotalPages());         // 전체 페이지수
    System.out.println(result.getTotalElements());      // 전체 갯수
    System.out.println(result.getNumber());             // 현재 페이지번호 (0부터)
    System.out.println(result.getSize());               // 페이지당 갯수
    System.out.println(result.hasNext());               // 다음페이지 존재여부
    System.out.println(result.isFirst());               // 시작페이지 여부


    // 정렬
    Sort sort = Sort.by("mno").descending();
    Pageable pageable2 = PageRequest.of(0, 10, sort);
    Page<Memo> result2 = memoReopsitory.findAll(pageable2);

    Sort sort1 = Sort.by("mno").descending();
    Sort sort2 = Sort.by("mnoText").ascending();
    Sort sortAll = sort1.and(sort2);
    Pageable pageable3 = PageRequest.of(0, 10, sortAll);
    Page<Memo> result3 = memoReopsitory.findAll(pageable3);
}
```

`Page<Memo> result = memoReopsitory.findAll(pageable);`  
→ findAll()에 파라미터로 `pageable`을 넘겨주면 페이징 처리 쿼리가 실행되고 결과를 리턴타입으로 지정된 `Page<Entity타입>` 객체로 저장

#### Page 객체
- **추가적인 count 쿼리 결과**를 같이 반환하는 타입
- 페이지 번호가 0부터 시작

```java
public interface Page<T> extends Slice<T> {
    int getTotalPages();     //전체 페이지 수
    long getTotalElements(); //전체 데이터 수
    <U> Page<U> map(Function<? super T, ? extends U> converter);    //변환기
}
```
→ 갯수를 출력할 수 있는 메소드를 갖고 있다

- count 쿼리 분리가능 : `countQuery` 속성 사용
  - 여러 테이블을 조인하는 쿼리일 경우 **count 쿼리에도 조인이 걸리기** 때문에 성능에 문제가 생기기에 분리 하는 것이 좋다

```java
@Query(value = “select m from Member m”,
         countQuery = “select count(m.username) from Member m”)
Page<Member> findMemberAllCountBy(Pageable pageable);
```

#### Slice 객체
- count 결과는 없고 다음페이지 여부(더보기) 확인을 위해 **limit+1**개를 결과를 반환하는 타입

```java
public interface Slice<T> extends Streamable<T> {
    int getNumber();
    int getSize();
    int getNumberOfElements();
    List<T> getContent();
    boolean hasContent();
    Sort getSort();
    boolean isFirst();
    boolean isLast();
    boolean hasNext();
    boolean hasPrevious();
    Pageable getPageable();
    Pageable nextPageable();
    Pageable previousPageable();    //이전 페이지 객체
    <U> Slice<U> map(Function<? super T, ? extends U> converter);   //변환기
}

```

#### Controller에서 페이징 사용
- Spring data JPA는 페이징과 정렬 기능을 Controller에서 사용 할 수 있게 제공해 준다

```java
@GetMapping("/members")
public Page<Member> list(Pageable pageable) {
    Page<Member> page = memberRepository.findAll(pageable);
    return page;
}
```
- 파라미터로 `Pageable`을 받으면 `PageRequest` 객체를 생성해서 요청 파라미터를 담는다
- 요청 파라미터 종류
  - `/members?page=0&size=3&sort=id,desc&sort=username,desc`
  - page : 현재 페이지 (0부터 시작)
  - size : 한 페이지당 갯수
  - sort : 정렬 조건  ex) sort=속성,정렬방향


[Spring-JPA 레퍼런스(링크)](https://docs.spring.io/spring-data/jpa/docs/2.5.0/reference/html/#preface)