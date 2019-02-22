# spring中的声明式的事务

![](/assets/spring-25-1.png)![](/assets/spring-25-2.png)![](/assets/spring-25-3.png)![](/assets/spring-25-4.png)

```
测试类：
import InterfacePack.BookShopDAO;
import InterfacePack.BookShopService;
import InterfacePack.checkout;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import javax.sql.DataSource;
import java.lang.reflect.Array;
import java.util.ArrayList;
import java.util.Arrays;

public class Junit {


    ApplicationContext context = null;
    checkout cashier = null;

    {
        context = new ClassPathXmlApplicationContext("applicationContext.xml");

        cashier = context.getBean(checkout.class);
    }

    @Test
    public void test() {
        cashier.check("kobe", Arrays.asList("1101", "1102"));

    }

}
```

各个类的简单的定义：

```
package DAO;

import BookException.BookStockException;
import InterfacePack.BookShopDAO;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Repository;

@Repository("bookShopDAO")
public class BookShopDAOImpl implements BookShopDAO {
    @Autowired
    private JdbcTemplate jdbcTemplate;

    @Override
    public int findBookPriceByIsbn(String isbn) {
        String sql = "select price from book where isbn=? ";
        return jdbcTemplate.queryForObject(sql, Integer.class, isbn);
    }

    @Override
    public void updateBookStod(String isbn) {
        String sql = "select stock from book_stock where isbn=?";
        int num = jdbcTemplate.queryForObject(sql, Integer.class, isbn);
        if (num == 0) {
            throw new BookStockException("库存不足");
        }
        sql = "update book_stock set stock =stock-1 where isbn =?";
        jdbcTemplate.update(sql, isbn);

    }


    @Override
    public void updateUserAccount(String username, int price) {
        String sql = "select balance from account where username = ?";
        int balance = jdbcTemplate.queryForObject(sql, Integer.class, username);

        if (balance < price) {
            throw new BookStockException("钱不够");
        }

        sql = "update account set balance = ? where username = ?";
        jdbcTemplate.update(sql, balance - price, username);
    }

}







package DAO;

import InterfacePack.BookShopDAO;
import InterfacePack.BookShopService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Propagation;
import org.springframework.transaction.annotation.Transactional;

@Service("bookShopService")
public class BookShopServiceImpl implements BookShopService {
    @Autowired
    private BookShopDAO bookShopDAO;

    @Override
    //事务的传递
    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void purchase(String username, String isbn) {
        int price = bookShopDAO.findBookPriceByIsbn(isbn);
        bookShopDAO.updateBookStod(isbn);
        bookShopDAO.updateUserAccount(username, price);
    }
}








package DAO;

import InterfacePack.BookShopService;
import InterfacePack.checkout;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Propagation;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;

@Service("checkout")
public class checkoutImpl implements checkout {

    @Autowired
    private BookShopService bookShopService;

    //事务的传递
    @Transactional
    @Override
    public void check(String username, List<String> isbns) {
        for (String isbn :
                isbns) {
            bookShopService.purchase(username, isbn);
        }
    }
}
```

application.xml的配置：

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context" xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/tx
http://www.springframework.org/schema/tx/spring-tx-2.5.xsd">



    <!--导入properties资源文件-->
    <context:property-placeholder location="classpath:db.properties"></context:property-placeholder>

    <!--扫描注入的Component-->
    <context:component-scan base-package="DAO"></context:component-scan>

    <!--配置C3P0数据源-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="user" value="${jdbc.user}"></property>
        <property name="password" value="${jdbc.password}"></property>
        <property name="jdbcUrl" value="${jdbc.jdbcUrl}"></property>
        <property name="driverClass" value="${jdbc.driverClass}"></property>


        <property name="initialPoolSize" value="${jdbc.initPoolSize}"></property>
        <property name="maxPoolSize" value="${jdbc.maxPoolSize}"></property>
    </bean>

    <!--配置spring的JDBCTemplate-->
    <bean id="nameParameterJdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <constructor-arg ref="dataSource"></constructor-arg>
    </bean>


    <!--添加事务管理器-->
    <bean id="dataSourceTransactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <constructor-arg name="dataSource" ref="dataSource"></constructor-arg>
    </bean>

    <tx:annotation-driven transaction-manager="dataSourceTransactionManager"></tx:annotation-driven>


</beans>
```



