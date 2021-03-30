---
layout: post
title: (Mybatis) 자바 Annotation
categories: Spring
tags: [Spring, Mybatis]
---

마이바티스는 어노테이션보다는 xml을 더 많이씀.

● Mapper 인터페이스

```java
@Mapper
public interface CommentMapper {
    @Select("SELECT comment_no, user_id, comment_content, reg_date FROM tcomment"
		+ "WHERE comment_no = #{commentNo}")
    @Results({
        @Result(column = "comment_no", property = "commentNo", jdbcType = JdbcType.BIGINT, id = true),
        @Result(column = "user_id", property = "userId", jdbcType = JdbcType.VARCHAR),
        @Result(column = "reg_date", property = "regDate", jdbcType = JdbcType.TIMESTAMP),
        @Result(column = "comment_content", property = "commentContent", jdbcType = JdbcType.LONGVARCHAR)
    })
    Comment seleCommentByPrimaryKey(Long commentNo);
}
```

● Repository 클래스

```java
public class CommentAnnoRepository {
    private SqlSessionFactory getSqlSessionFactory() {
	String resource = "mybatis/mybatis-config.xml";
	InputStream inputStream;
	try {
	    inputStream = Resources.getResourceAsStream(resource);
	} catch (IOException e) {
	    throw new IllegalArgumentException(e);
	}
	return new SqlSessionFactoryBuilder().build(inputStream);
    }
    
    public Comment selectCommentByPrimaryKey(Long commentNo) {
	SqlSession sqlSession = getSqlSessionFactory().openSession();
	try {
	    return sqlSession.getMapper(CommentMapper.class).selectCommentByPrimaryKey(commentNo);
	} finally {
	    sqlSession.close();
	}
    }
    
    public Integer insertComment(Comment comment) {
	SqlSession sqlSession = getSqlSessionFactory().openSession();
	try {
	    Integer result = sqlSession.getMapper(CommentMapper.class).insertComment(comment);
	    if (result > 0) {
	        sqlSession.commit();
	    }
	    return result;
	} finally {
	    sqlSession.close();
	}
    }
    
    public static void main(String[] args) {
        CommentAnnoRepository c = new CommentAnnoRepository();
        System.out.println(c.selectCommentByPrimaryKey(1L));
        
        Comment comment = new Comment(9L, "user9", new Date(), "test");
        c.insertComment(comment);
    }
}
```