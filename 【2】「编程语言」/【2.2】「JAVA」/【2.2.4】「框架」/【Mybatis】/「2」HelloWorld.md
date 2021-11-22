

```java
package com.peiyang.mybatis.bean;


import lombok.Getter;
import lombok.Setter;

@Setter
@Getter
public class Employee {
    private int id;
    private String lastName;
    private String email;
    private String gender;

    @Override
    public String toString() {
        return "Employee{" +
                "id=" + id +
                ", lastName='" + lastName + '\'' +
                ", email='" + email + '\'' +
                ", gender='" + gender + '\'' +
                '}';
    }
}

```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.peiyang.mybatis.EmployeeMapper">
    <!--
    namespace:名称空间
    id:唯一标识
    resultType:返回值类型
    -->
    <select id="selectEmp" resultType="com.peiyang.mybatis.bean.Employee">
        select id, last_name lastName, email, gender from tbl_employee where id = #{id}
    </select>
</mapper>
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="gnayiepil521"/>
            </dataSource>
        </environment>
    </environments>

    <!--将我们写好的sql映射文件一定要注册到全局配置文件中-->
    <mappers>
        <mapper resource="EmployeeMapper.xml"/>
    </mappers>
</configuration>
```



## 测试

```java
package com.peiyang.mybatis.test;

import com.peiyang.mybatis.bean.Employee;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;

public class MyBatisTest {

    /**
     * 1、根据xml配置文件（全局配置文件）创建一个SqlSessionFactory对象
     *      有数据源一些运行环境信息
     * 2、sql映射文件：配置了每一个sql，以及sql的封装规则等。
     * 3、将sql映射文件注册在全局配置文件中
     * 4、写代码
     *      1）、根据全局哦诶只文件得到SqlSessionFactory
     *      2）、使用sqlSession工厂，获取到sqlSession对象使用它来执行增删改查
     *          一个sqlSession就是代表和数据库的依次绘画，用完关闭
     *      3）、使用sql的唯一标识来告诉MyBatis执行哪个sql。sql都是保存在sql映射文件中的
     * @throws IOException
     */

    @Test
    public void test() throws IOException {
        String resource = "mybatis-config.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

        //2、获取sqlSession实例，能直接执行已经映射的sql语句

        SqlSession openSession = sqlSessionFactory.openSession();
        try{
            Employee employee = openSession.selectOne("com.peiyang.mybatis.EmployeeMapper.selectEmp",1);
            System.out.println(employee);
        }finally{
            openSession.close();
        }
    }
}

```





## 根据接口(接口式编程)

1、接口式编程

原生：				Dao 			====> 			DaoImpl

mybatis：			Mapper			====> 			xxMapper.xml

2、SqlSession代表和数据库的一次会话，用完必须关闭。

3、SqlSession和connectuon一样都是非线程安全，每次使用都应该去获取新的对象

4、mapper接口没有实现类，但是mybatis会为这个接口生成一个代理对象

​			(将接口和xml进行绑定)

EmployeeMapper empMapper = sqlSession.getMapper(EmployeeMapper.class);

5、两个重要的配置文件，

​			mybatis的全局配置文件，包含数据库连接池信息，事务管理器信息等。。。系统运行信息等

​			sql映射文件，保存了每一个sql语句的映射信息，

​									将sql出去出来。

```java
package com.peiyang.mybatis.dao;

import com.peiyang.mybatis.bean.Employee;

public interface EmployeeMapper {
    public Employee getEmpById(Integer id);
}
```



```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.peiyang.mybatis.dao.EmployeeMapper">
    <!--
    namespace:名称空间
    id:唯一标识
    resultType:返回值类型
    -->
    <select id="getEmpById" resultType="com.peiyang.mybatis.bean.Employee">
        select id, last_name lastName, email, gender from tbl_employee where id = #{id}
    </select>
</mapper>
```



```java
package com.peiyang.mybatis.test;

import com.peiyang.mybatis.bean.Employee;
import com.peiyang.mybatis.dao.EmployeeMapper;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;

public class MyBatisTest2 {

    public SqlSessionFactory getSqlSessionFactory() throws IOException {
        String resource = "mybatis-config.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        return new SqlSessionFactoryBuilder().build(inputStream);
    }

    @Test
    public void test() throws IOException {
        //1、获取SqlSessionFactory对象
        SqlSessionFactory sqlSessionFactory = getSqlSessionFactory();

        //2、获取SqlSession对象
        SqlSession openSession = sqlSessionFactory.openSession();

        try {
            //3、获取接口的实现类对象
            //会为接口自动的创建一个代理对象，代理对象去执行增删改查
            EmployeeMapper mapper = openSession.getMapper(EmployeeMapper.class);
            Employee employee = mapper.getEmpById(1);
            System.out.println(employee);
        }finally{
            openSession.close();
        }
    }
}
```













