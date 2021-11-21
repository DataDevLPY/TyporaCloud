## 查询返回某个值

1. 查询表里面有多少条记录，返回是某个值

2. 使用JdbcTemplate实现查询返回某个值的代码

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
package cn.peiyang.jdbc.dao;

import cn.peiyang.jdbc.entity.Book;

public interface BookDao {
    public void add(Book book);
    public void update(Book book);
    public void delete(String id);
    public int selectCount();
}

```



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

    @Override
    public void update(Book book) {
        String sql = "update t_book set username=?, ustatus=? where user_id=?";
        Object[] args = {book.getUsername(),book.getUstatus(),book.getUserId()};
        int update = jdbcTemplate.update(sql,args);
        System.out.println(update);
    }

    @Override
    public void delete(String id) {
        String sql = "delete from t_book where user_id=?";
        int update = jdbcTemplate.update(sql,id);
        System.out.println(update);
    }

    @Override
    public int selectCount() {

        String sql = "select count(*) from t_book";
        int count = jdbcTemplate.queryForObject(sql,Integer.class);
        return count;
    }
}

```







```java
package cn.peiyang.jdbc.service;


import cn.peiyang.jdbc.dao.BookDao;
import cn.peiyang.jdbc.entity.Book;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class BookService {
    //注入dao
    @Autowired
    private BookDao bookDao;

    public void addBook(Book book){
        bookDao.add(book);
    }

    public void updateBook(Book book){
        bookDao.update(book);
    }

    public void deleteBook(String id){
        bookDao.delete(id);
    }

    //查询表记录数
    public int findCount(){
        return bookDao.selectCount();
    }


}
```







```java
package cn.peiyang.jdbc.test;

import cn.peiyang.jdbc.service.BookService;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class bookTest {

    @Test
    public void testBook(){
        ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");

        BookService bookService = context.getBean("bookService",BookService.class);
      
      
        System.out.println(bookService.findCount());
    }

}
```





## 查询返回某个值

1. 场景：查询图书详情



2. JdbcTemplate

![截屏2021-07-10 下午7.05.40](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-07-10 下午7.05.40.png)

```java
package cn.peiyang.jdbc.service;


import cn.peiyang.jdbc.dao.BookDao;
import cn.peiyang.jdbc.entity.Book;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class BookService {
    //注入dao
    @Autowired
    private BookDao bookDao;

    public void addBook(Book book){
        bookDao.add(book);
    }

    public void updateBook(Book book){
        bookDao.update(book);
    }

    public void deleteBook(String id){
        bookDao.delete(id);
    }

    //查询表记录数
    public int findCount(){
        return bookDao.selectCount();
    }

    //查询表记录数
    public Book findOne(String id){
        return bookDao.findBookInfo(id);
    }

}
```



```java
package cn.peiyang.jdbc.entity;

import lombok.Getter;
import lombok.Setter;

@Setter
@Getter
public class Book {

    @Override
    public String toString() {
        return "Book{" +
                "userId='" + userId + '\'' +
                ", username='" + username + '\'' +
                ", ustatus='" + ustatus + '\'' +
                '}';
    }

    private String userId;
    private String username;
    private String ustatus;
}

```



```java
package cn.peiyang.jdbc.dao;

import cn.peiyang.jdbc.entity.Book;

public interface BookDao {
    public void add(Book book);
    public void update(Book book);
    public void delete(String id);
    public int selectCount();
    Book findBookInfo(String id);
}

```



```java
package cn.peiyang.jdbc.dao;


import cn.peiyang.jdbc.entity.Book;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.BeanPropertyRowMapper;
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

    @Override
    public void update(Book book) {
        String sql = "update t_book set username=?, ustatus=? where user_id=?";
        Object[] args = {book.getUsername(),book.getUstatus(),book.getUserId()};
        int update = jdbcTemplate.update(sql,args);
        System.out.println(update);
    }

    @Override
    public void delete(String id) {
        String sql = "delete from t_book where user_id=?";
        int update = jdbcTemplate.update(sql,id);
        System.out.println(update);
    }

    @Override
    public int selectCount() {

        String sql = "select count(*) from t_book";
        int count = jdbcTemplate.queryForObject(sql,Integer.class);
        return count;
    }

    @Override
    public Book findBookInfo(String id) {
        String sql = "select * from t_book where user_id = ?";
        Book book = jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<Book>(Book.class),id);
        return book;

    }
}

```





## 查询返回对象



```java
package cn.peiyang.jdbc.service;


import cn.peiyang.jdbc.dao.BookDao;
import cn.peiyang.jdbc.entity.Book;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class BookService {
    //注入dao
    @Autowired
    private BookDao bookDao;

    public void addBook(Book book){
        bookDao.add(book);
    }

    public void updateBook(Book book){
        bookDao.update(book);
    }

    public void deleteBook(String id){
        bookDao.delete(id);
    }

    //查询表记录数
    public int findCount(){
        return bookDao.selectCount();
    }

    //查询表记录数
    public Book findOne(String id){
        return bookDao.findBookInfo(id);
    }

    //查询返回对象
    public List<Book> findAll(){
        return bookDao.findAllBookInfo();

    }


}
```



```java
package cn.peiyang.jdbc.dao;

import cn.peiyang.jdbc.entity.Book;

import java.util.List;

public interface BookDao {
    public void add(Book book);
    public void update(Book book);
    public void delete(String id);
    public int selectCount();
    Book findBookInfo(String id);

    List<Book> findAllBookInfo();
}
```



```java
package cn.peiyang.jdbc.dao;


import cn.peiyang.jdbc.entity.Book;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Repository;

import java.util.List;

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

    @Override
    public void update(Book book) {
        String sql = "update t_book set username=?, ustatus=? where user_id=?";
        Object[] args = {book.getUsername(),book.getUstatus(),book.getUserId()};
        int update = jdbcTemplate.update(sql,args);
        System.out.println(update);
    }

    @Override
    public void delete(String id) {
        String sql = "delete from t_book where user_id=?";
        int update = jdbcTemplate.update(sql,id);
        System.out.println(update);
    }

    @Override
    public int selectCount() {

        String sql = "select count(*) from t_book";
        int count = jdbcTemplate.queryForObject(sql,Integer.class);
        return count;
    }

    @Override
    public Book findBookInfo(String id) {
        String sql = "select * from t_book where user_id = ?";
        Book book = jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<Book>(Book.class),id);
        return book;

    }

    @Override
    public List<Book> findAllBookInfo() {
        String sql = "select * from t_book";
        List<Book> bookList= jdbcTemplate.query(sql, new BeanPropertyRowMapper<Book>(Book.class));
        return bookList;
    }
}
```





