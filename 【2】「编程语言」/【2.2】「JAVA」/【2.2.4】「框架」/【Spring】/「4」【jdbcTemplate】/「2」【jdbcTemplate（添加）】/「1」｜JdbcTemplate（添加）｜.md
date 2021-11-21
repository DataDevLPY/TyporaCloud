## 添加



1. 对应数据库创建实体类

```sql
create table t_book(
	user_id		bigint(20),
	username	varchar(100),
	ustatus		varchar(50)
)
```

```java
package cn.peiyang.jdbc.entity;

import lombok.Getter;
import lombok.Setter;

@Setter
@Getter
public class User {
    private String userId;
    private String username;
    private String ustatus;
}

```

2. 编写service和dao

（1）在dao进行数据库添加操作

```java
package cn.peiyang.jdbc.dao;

import cn.peiyang.jdbc.entity.Book;

public interface BookDao {
    public void add(Book book);
}

```

```java
package cn.peiyang.jdbc.entity;

import lombok.Getter;
import lombok.Setter;

@Setter
@Getter
public class Book {
    private String userId;
    private String username;
    private String ustatus;
}

```

```java
package cn.peiyang.jdbc.service;


import cn.peiyang.jdbc.dao.BookDao;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class BookService {
    //注入dao
    @Autowired
    private BookDao  bookDao;


}

```



（2）调用JdbcTemplate对象里面update方法实现添加操作

第一个参数：sql语句

第二个参数：可变参数，设置sql语句值

```java
package cn.peiyang.jdbc.dao;


import cn.peiyang.jdbc.entity.Book;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Repository;

@Repository
public class BookDaoImpl implements BookDao{

    //注入JdbcTemplate
    @Autowired
    private JdbcTemplate jdbcTemplate;
    
    
    //添加方法
    @Override
    public void add(Book book) {

        String sql="insert into t_book values(?,?,?)";
        
        //调用实现方法
        Object[] args = {book.getUserId(),book.getUsername(),book.getUstatus()};
        int update = jdbcTemplate.update(sql,args);
        System.out.println(update);
    }
}
```



测试

```java
package cn.peiyang.jdbc.test;

import cn.peiyang.jdbc.entity.Book;
import cn.peiyang.jdbc.service.BookService;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class bookTest {

    @Test
    public void testBook(){
        ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");

        BookService bookService = context.getBean("bookService",BookService.class);

        Book book = new Book();
        book.setUserId("1");
        book.setUsername("java");
        book.setUstatus("a");
        bookService.addBook(book);

    }

}
```