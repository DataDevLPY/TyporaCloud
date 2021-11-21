## 批量操作

![截屏2021-07-10 下午7.56.25](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220044954.png?token=AWS37JKTVMKPJHTLLGPL4DLBTJ32I)



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

    //批量添加
    public void batchAdd(List<Object[]> batchArgs){
        bookDao.batchAddBook(batchArgs);
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

    void batchAddBook(List<Object[]> batchArgs);
}

```





```java
package cn.peiyang.jdbc.dao;


import cn.peiyang.jdbc.entity.Book;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Repository;

import java.util.Arrays;
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


    @Override
    public void batchAddBook(List<Object[]> batchArgs) {
        String sql = "insert into t_book values(?,?,?)";
        int[] ints = jdbcTemplate.batchUpdate(sql,batchArgs);
        System.out.println(Arrays.toString(ints));

    }
}

```











