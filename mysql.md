# MySQL

## MySQL登录以及创建数据库

### 登录MySQL

$ mysql -uroot -p

### 创建数据库

```sql
	CREATE {DATABASE|SCHEMA}[IF NOT EXISTS] db_name
	[DEFAULT] CHARCTER SET [=] charset_name
```

- 创建一个t1数据库

  ```mysql
  CREATE DATABASE t1;
  ```

- 查看所有数据库

  ```mysql
  SHOW DATABASES;
  ```

- IF NOT EXISTS

  ```mysql
  /*再次创建t1数据库*/
  CREATE DATABASE t1;
  /*ERROR 1007 (HY000): Can't create database 't1'; database exists*/
  CREATE DATABASE t1 IF NOT EXISTE;
  /*Query OK, 1 row affected, 1 warning (0.00 sec)*/
  ```

- 修改mysql编码

  ```mysql
  /*查看t1*/
  SHOW CREATE DATABSE t1;
  /*
  | t1 | CREATE DATABASE `t1` /*!40100 DEFAULT CHARACTER SET latin1 (拉丁语言，不支持中文) */
  /*修改默认MySQL默认编码*/
  /*找mysql安装路径，找到my.ini配置文件，修改client和server端的编码为utf8,之后创建的数据库都是以utf8为编码*/
  default-character-set=utf8
  character-set-server=utf8
  /*还必须重启mysql服务才起效*/
  * window下
  $ net stop mysql
  $ net start mysql
  ```

- 修改数据库

  ```mysql
  ALTER {DATABASE | SCHEMA} [db_name] [DETAULT] CHARACTER [=] chartset_name
  /*修改t1编码*/
  ALTER DATABASE t1 CHARACTER SET utf8;
  
  SHOW CREATE DATABASE t1; /* 编码就编程utf8 */
  ```

- 删除数据库

  ```mysql
  DROP {DATABASE | SCHEMA} [IF EXISTS] db_name
  
  /*删除t1*/
  DROP DATABASE IF EXISTS t1;
  
  /*再查看*/
  SHOW DATABASES
  ```

  

### 数据表

- 创建数据表

  ```mysql
  USE db_name /*先选择数据库*/
  CREATE TABLE [IF NOT EXITS] table_name (column_name data_type,....);
  
  /*查询当前在那一个数据库下*/
  SELECT DATABASE();
  
  /*建库*/
  CREATE DATABASE data1;
  use data1;
  /*建表t1*/
  CREATE TABLE t1(
  	id INT,
      name VARCHAR(20),
      age TINYINT UNSIGNED, /*UNSIGNED->无符号->都是正数*/
      salary FLOAT(10,2) UNSIGNED
  );
  ```

- 查看数据表命令

  ```MYSQL
  /*查看数据表列表*/
  SHOW TABLES [FROM db_name]
  /*查看数据表结构*/
  SHOW COLUMNS FROM tb_name
  
  /*查看data1下所有table*/
  SHOW TABLES FROM data1;
  /*查看t1的表结构*/
  SHOW COLUMNS FROM t1;
  ```

#### 数据表基本操作

- 插入记录

  ```MYSQL
  INSERT [INTO] tb_name [(col_name)] VALUES(val1,...);
  ```

- 给表t1添加数据

  ```mysql
  INSERT INTO t1 VALUES(1,'Jerry',27,1000.88);
  INSERT INTO t1 (id,name,salary) VALUES(2,'Tin',8888.8);
  ```

- 记录查找

  ```mysql
  SELECT expr,... 	FROM tb_name;
  
  /*查看t1表数据*/
  SELECT * FROM t1;
  ```

- 空值与非空

  ```mysql
  - NULL 字段可以为空
  - NOT NULL 字段禁止为空
  /*创建t2并设置字段空与非空属性*/
  CREATE TABLE t2(
  	id INT NOT NULL,
      name VARCHAR(20) NULL /*默认是NULL可以不写*/
  );
  /*插入数据*/
  INSERT t2(name) VALUES('张三丰'); /*居然可以插入，id默认为0*/ 
  
  ```

#### 约束

- 约束保证数据的完整性和一致性
- 分为表级约束和列级的约束
- 约束类型：
  * NOT	NULL (非空约束)
  * PRIMARY KEY (主键约束)
  * UNIQUE KEY (唯一约束)
  * DEFAULT (默认约束)
  * FOREIGN KEY (外键约束)

#### 主键---PRIMARY_KEY

- 主键约束
- 每张数据表只能存在一个主键
- 保证每条记录的唯一性
- 自动为NOT NULL

#### 自动编号---AUTO_INCREMENT

- 给所有记录自动编号，且必须与主键组合使用；
- 默认情况下，起始值为1，增量为1.

- 创建t3表，设主键且自增长

  ```mysql
  /*建表*/
  CREATE TABLE t3(
  	id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
      name VARCHAR(20)
  );
  /*插数据*/
  INSERT t3(name) VALUES('Tom');
  INSERT t3(name) VALUES('jACK');
  ```

#### 唯一约束、默认约束

- 唯一约束：
  * 保证数据的唯一性；
  * 一张表可以有多个唯一约束
  * 允许为空

- 默认约束
  * 当输入数据没有明确赋值，自动赋予默认值。

- 实践

  ```mysql
  /*唯一约束*/
  CREATE TABLE t4(
  	id INT AUTO_INCREMENT PRIMARY KEY,
      name VARCHAR(20) UNIQUE KEY
  );
  /*查看*/
  SHOW COLUMNS FROM t4;
  /*默认约束*/
  CREATE TABLE t5(
   id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(20) UNIQUE KEY,
    sex ENUM('1','2','3') DEFAULT '3' /*男、女、未知*/ 
  );
  /*查看*/
  SHOW COLUMNS FROM t5;
  /*插入数据*/
  INSERT t5(name,sex) VALUES('Ly','1');
  INSERT t5(name) VALUES('Ken');
  /*查看数据*/
  SELECT * FROM t5;
  
  ```

#### 外键约束

- FOREING KEY (字段) REFERENCES tbale(字段)

- 实战，创建一个商品分类表和一个商品表

  ```MYSQL
  /*创建C1表*/
  CREATE TABLE C1(
  	id 	INT AUTO_INCREMENT PRIMARY KEY,
      name VARCHAR(50)
  );
  /*创建商品表，并创建外键约束*/
  CREATE TABLE P1(
  	pid INT AUTO_INCREMENT PRIMARY KEY,
      pname VARCHAR(50),
      type INT,
      FOREIGN KEY(type) REFERENCES C1(id)
  );
  ```

##### 外键约束的基本操作

- CASCADE

  * 自动删除或者更新子表匹配的行

    ```mysql
    /*创建C1表*/
    CREATE TABLE c2(
    	id 	INT AUTO_INCREMENT PRIMARY KEY,
        name VARCHAR(50)
    );
    /*创建商品表，并创建外键约束*/
    CREATE TABLE p2(
    	pid INT AUTO_INCREMENT PRIMARY KEY,
        pname VARCHAR(50),
        type INT,
        FOREIGN KEY(type) REFERENCES c2(id) ON DELETE CASCADE
    );
    /*往c2插入三个分类数据*/
    INSERT INTO c2(name) VALUES('AA');
    INSERT INTO c2(name) VALUES('BB');
    INSERT INTO c2(name) VALUES('CC');
    /*往子表插入数据*/
    INSERT p2(pname,type) VALUES('牛奶',1);
    INSERT p2(pname,type) VALUES('苹果',3);
    INSERT p2(pname,type) VALUES('纯净水',2);
    /*查看两表*/
    SELECT * FROM c2;
    SELECT * FROM P2; 
    /*删除c2的AA分类*/
    DELETE FROM c2 WHERE id=1;
    /*查看p2*/
    SELECT * FROM p2; /*商品表（p2）外键type=1的商品也被删除了 */
    ```

    

- SET NULL

  * 从父表删除或者更新行，并设置子表中的外键列为NULL

    ```mysql
    /*使用方式*/
    ON DELETE SET NULL  /*当主表（父表）删除了，子表对应的外键设置为NULL*/
    ```

    

- RESTRICT

  * 拒绝对父表删除或更新操作

    ```mysql
    /*使用方式*/
    ON DELETE RESTRICT 
    ```

    

- NO ACTION

  * 标准的SQL的关键字，与RESTRICT相同

    ```mysql
    /*使用方式*/
    ON DELETE NO ACTION 
    ```

    