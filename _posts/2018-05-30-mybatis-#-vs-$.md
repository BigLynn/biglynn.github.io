---
layout: post
title: "mybatis # vs. $"
date: 2018-05-30
---
## #和$的区别
### #的用法举例
 * SELECT * FROM PERSON WHERE ID = #{id}
 * This statement is called selectPerson, takes a parameter of type int (or Integer), and returns a HashMap keyed by column names mapped to row values.
 * #{id} This tells MyBatis to create a PreparedStatement parameter. With JDBC, such a parameter would be identified by a "?" in SQL passed to a new PreparedStatement, something like this:
 ```
    String selectPerson = "SELECT * FROM PERSON WHERE ID=?";
    PreparedStatement ps = conn.prepareStatement(selectPerson);
    ps.setInt(1,id);
 ```
  * 说明：默认情况下，#{}会产生PreparedStatement properties and set the values safely against the PreparedStatement parameter (e.g. ?).
  
### $ 用法举例
      ORDER BY ${columnName}
      $ MyBatis不会columnName做任何修改。 
      
      
### 总结：
  * #要更安全，更快，是首要的推荐。能够阻止SQL注入。
  * $不安全，要慎用。合理使用非常有用：如果你传的参数，要作为select 的表名，或者不作修改的注入string到SQL statement。
