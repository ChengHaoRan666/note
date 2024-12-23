## JDBC事务实现

>  一个事务最基本要求，必须是同一个连接对象 connection

注意事项：

1. 事务添加是在业务方法中
2. 利用try catch代码块，开启事务，提交事务，事务回滚
3. 将 connection 传入 dao 层，dao 只负责使用，不用close()



银行转账业务例子：

```java
import org.junit.Test;

import java.sql.*;

/**
 * @Author: 程浩然
 * @Create: 2024/7/9 - 21:27
 * @Description: 银行交易
 */
public class BankServiceTest {
    @Test
    public void text() throws Exception {
        BankService bankService = new BankService();
        // two 给 one 转帐500
        bankService.transfer("one", "two", 600);
    }
}


// 银行卡业务
class BankService {
    public void transfer(String addaccount, String subaccount, int money) throws Exception {
        bankDao bankDao = new bankDao();
        Class.forName("com.mysql.cj.jdbc.Driver");
        Connection connection = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/mysql_text_one", "root", "123");
        try {
            // 开启事务
            // 关闭事务提交
            connection.setAutoCommit(false);

            // 执行数据库动作
            // 注意，要一个连接，需要传进去
            bankDao.add(addaccount, money, connection);
            System.out.println("---------------");
            bankDao.sub(subaccount, money, connection);

            // 事务提交
            connection.commit();
        } catch (Exception e) {
            // 事务回滚
            connection.rollback();
            // 抛出异常
            throw e;
        } finally {
            // 关闭连接
            connection.close();
        }
    }
}

// 操作数据库的类
class bankDao {
    // 加钱的数据库操作方法
    public void add(String account, int money, Connection connection) throws Exception {
        String sql = "update t_bank set money=money+? where account=?";
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        preparedStatement.setObject(1, money);
        preparedStatement.setObject(2, account);
        int i = preparedStatement.executeUpdate();
        if (i < 0) throw new Exception("加钱错误");
        preparedStatement.close();
        System.out.println("加钱成功");
    }

    // 减钱的数据库操作方法
    public void sub(String account, int money, Connection connection) throws Exception {
        String sql = "update t_bank set money=money-? where account=?";
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        preparedStatement.setObject(1, money);
        preparedStatement.setObject(2, account);
        int i = preparedStatement.executeUpdate();
        if (i < 0) throw new Exception("减钱错误");
        preparedStatement.close();
        System.out.println("减钱成功");
    }

}

```

