---
layout: post
title: Spring Data JPA
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
    public String memberDetail(@PathVariable("id") int id, Model model) {
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

    @PostMapping("/update")
    public String memberUpdate(MemberEntity memberEntity, RedirectAttributes ra) {

        memberRepository.save(memberEntity);
        ra.addFlashAttribute("msg", "수정에 성공하였습니다.");

        return "redirect:/member/update" + memberEntity.getId();
    }

    @PostMapping("/delete")
    @ResponseBody
    public void memberDelete(@RequestParam("id") int id) {

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

### Pageable 인터페이스 (페이징처리)
- Pageable 인터페이스 : 페이지 처리에 필요한 정보를 전달하는 용도의 타입
- PageRequest 클래스 : `Pageable`의 구현체
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


[Spring-JPA 레퍼런스(링크)](https://docs.spring.io/spring-data/jpa/docs/2.5.0/reference/html/#preface)