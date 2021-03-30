---
layout: post
title: (Mybatis) resultMap, association, collection
categories: Spring
tags: [Spring, Mybatis]
---

●resultMap : 복잡한 결과매핑을 할때 사용함 (Map은 Mapping을 의미)

 - id : resultMap의 아이디

 - type : resultMap의 자료형(=resultType)

```xml
<resultMap id="baseResultMap" type="Comment">
    <id column="comment_no" jdbcType="BIGINT" property="commentNo"/>
    <result column="user_id" jdbcType="VARCHAR" property="userId" />
    <result column="reg_date" jdbcType="TIMESTAMP" property="regDate" />
    <result column="comment_content" jdbcType="VARCHAR" property="commentContent" />
</resultMap>
<select id="selectCommentByPrimaryKey" parameterType="Long" resultMap="baseResultMap">
    SELECT comment_no, user_id, comment_content, reg_date FROM tcomment
    WHERE comment_no = #{commentNo}
</select>
```

○ id : 구분자 역할을 하는 속성(기본키)

○ result : 일반 속성 (column(테이블) -> property(객체) 맵핑한다고 생각)

  - column : 테이블의 컬럼명

  - jdbcType : 테이블의 자료형

  - javaType : 자바의 자료형

  - property : 자바의 속성명

○ constructor : 생성자를 통한 맵핑, 생성자 파라미터에 명시된 순서대로 arg를 입력해야함

```xml
<resultMap id="constructorResultMap" type="Comment">
    <constructor>
        <idArg column="comment_no" javaType="long"/>
        <arg column="user_id" javaType="string"/>
        <arg column="reg_date" javaType="date"/>
        <arg column="comment_content" javaType="string"/>
    </constructor>
</resultMap>
<select id="selectCommentByPrimaryKey" parameterType="Long" resultMap="constructorResultMap">
    SELECT comment_no, user_id, comment_content, reg_date FROM tcomment
    WHERE comment_no = #{commentNo}
</select>
```

 - idArg : 구분자역할의 속성(기본키) / arg : 일반 속성

○ association : 1:1 관계의 테이블을 조인할 때 사용 (조인 객체)

  ex) 1개 Comment 에는 1명의 User만 존재

  - property : 조인객체명 / javaType : 조인객체의 자료형

```java
private User user;
```

→ 자바빈파일의 자료형=javaType(User), 객체명=property(user) 일치

```xml
<resultMap id="associationResultMap" type="Comment">
    <id column="comment_no" jdbcType="BIGINT" property="commentNo"/>
    <result column="user_id" jdbcType="VARCHAR" property="userId" />
    <result column="reg_date" jdbcType="TIMESTAMP" property="regDate" />
    <result column="comment_content" jdbcType="VARCHAR" property="commentContent" />
    <association property="user" javaType="User">
        <id property="userId" column="user_id"/>
        <result property="userName" column="user_name"/>
    </association>
</resultMap>
<select id="selectCommentByPrimaryKeyAssociation" parameterType="long" resultMap="associationResultMap">
    SELECT c.comment_no, c.user_id, c.comment_content, c.reg_date, u.user_name FROM tcomment c, tuser u
    WHERE c.user_id = u.user_id AND comment_no = #{commentNo}
</select>
```

○ collection : 1:M 관계의 테이블을 조인할 때 사용 (조인 객체)

  ex) 1개의 Comment에는 M개의 Reply가 존재

 - property : 조인객체명 / ofType : collection의 자료형(지네릭)

```java
private List<Reply> replies;
```

→ 자바빈파일의 컬렉션의 자료형(지네릭)=ofType(Reply), 객체명=property(replies) 일치

```xml
<resultMap id="collectionResultMap" type="Comment">
    <id column="comment_no" jdbcType="BIGINT" property="commentNo"/>
    <result column="user_id" jdbcType="VARCHAR" property="userId" /> 
    <result column="reg_date" jdbcType="TIMESTAMP" property="regDate" />
    <result column="comment_content" jdbcType="VARCHAR" property="commentContent" />
    <collection property="replies" ofType="Reply">
        <id property="replyId" column="reply_id"/>
        <result property="userId" column="user_id"/>
        <result property="replyContent" column="reply_content"/>
        <result property="regDate" column="reg_date2"/>
    </collection>
</resultMap>
<select id="selectCommentByPrimaryKeyCollection" parameterType="long" resultMap="collectionResultMap">
    SELECT c.comment_no, c.user_id, c.comment_content, c.reg_date, r.reply_content, r.reg_date AS reg_date2 FROM tcomment c, reply r
    WHERE c.comment_no = r.comment_no AND c.comment_no = #{commentNo}
</select>
```