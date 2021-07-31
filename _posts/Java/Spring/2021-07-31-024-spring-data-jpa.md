---
layout: post
title: Spring Data JPA
categories: Spring
tags: [Spring, JPA]
---

### Repository 인터페이스
- Repository 인터페이스를 생성한후 JpaRepository<Entity, 기본키 타입> 을 상속받으면 **기본적인 CRUD 자동 생성**

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


#### 기본 사용법  

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

#### Query Method 형식
- findBy 뒤에 키워드가 붙음

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

[Spring-JPA 레퍼런스(링크)](https://docs.spring.io/spring-data/jpa/docs/2.5.0/reference/html/#preface)