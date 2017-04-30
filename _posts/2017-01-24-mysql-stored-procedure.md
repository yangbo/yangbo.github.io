---
layout: post
title:  "MySQL存储过程开发方法"
date:   2017-01-24 14:00:00
categories: database mysql
---
# Cursor 游标

mysql 存储过程中的游标是 `只读`、`不可滚动`和`敏感`的。
`变动敏感型`游标直接指向实际数据，其他链接修改了数据后，`变动敏感型`游标能立刻反应出来。

声明游标的语法

    DECLARE cursor_name CURSOR FOR SELECT_statement;
    
声明游标的语句必须在所有声明变量的语句后，否则mysql会报错。

然后打开游标

    OPEN cursor_name;
    
之后就可以获取一行数据

    FETCH cursor_name INTO variables list;
    
最后关闭游标

    CLOSE cursor_name;
    
用游标时需要声明一个`NOT FOUND`处理器，类似下面的语句

    DECLARE CONTINUE HANDLER FOR NOT FOUND SET finished = 1;
    
`NOT FOUND`处理器声明必须放到变量和游标声明语句后面，否则mysql报错。

下面的图说明了一个完整的游标使用过程

http://www.mysqltutorial.org/wp-content/uploads/2009/12/mysql-cursor.png

# 游标的示例

    DELIMITER $$
     
    CREATE PROCEDURE build_email_list (INOUT email_list varchar(4000))
    BEGIN
     
     DECLARE v_finished INTEGER DEFAULT 0;
     DECLARE v_email varchar(100) DEFAULT "";
     
     -- declare cursor for employee email
     DEClARE email_cursor CURSOR FOR 
     SELECT email FROM employees;
     
     -- declare NOT FOUND handler
     DECLARE CONTINUE HANDLER 
            FOR NOT FOUND SET v_finished = 1;
     
     OPEN email_cursor;
     
     get_email: LOOP
     
     FETCH email_cursor INTO v_email;
     
     IF v_finished = 1 THEN 
     LEAVE get_email;
     END IF;
     
     -- build email list
     SET email_list = CONCAT(v_email,";",email_list);
     
     END LOOP get_email;
     
     CLOSE email_cursor;
     
    END$$
     
    DELIMITER ;
    
调用的语句：

    SET @email_list = "";
    CALL build_email_list(@email_list);
    SELECT @email_list;
    
# 参考

- http://www.mysqltutorial.org/mysql-cursor/
- http://www.mysqltutorial.org/stored-procedures-loop.aspx