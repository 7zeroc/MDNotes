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

#### 添加和删除数据列

- 添加单列

  ```mysql
  ALTER TABLE tb_name ADD[COLUMN] col_name column_definition [FIRST | AFTER col_name]
  -- 例子
  CREATE DATABASE db_product;
  USE db_product;
  CREATE TABLE t1(
      id INT AUTO_INCREMENT PRIMARY KEY,
      name VARCHAR(20) NOT NULL
  );
  SHOW COLUMNS FROM t1;
  -- 在 id 和 name 之间添加一个age
  ALTER TABLE t1 ADD age SMALLINT NOT NULL AFTER id;
  SHOW COLUMNS FROM t1;
  -- 添加一列并放在第一列
  ALTER TABLE t1 ADD a INT FIRST;
  ```

- 添加多列

  ```mysql
  ALTER TABLE tb_name ADD [COlUMN] (col_name column_definition,...)
  -- 例子
  -- 往t1添加多列，添加多列是无法调整位置的，默认排最后
  ALTER TABLE t1 ADD (salary FLOAT(8,2), number INT);
  ```

- 删除列

  ```mysql
  ALTER TABLE tb_name DROP [column] col_name
  -- 例子
  -- 删除t1的 a 列
  ALTER TABLE t1 DROP a;
  -- 删除t1的age和number
  ALTER TABLE t1 DROP age, DROP number;
  
  ```

#### 添加删除约束

- 添加主键约束

  ```mysql
  ALTER TABLE tb_name ADD [约束名[唯一的名字]] PRIMARY KEY [index_type](index_col_name,...)
  
  -- 创建t2
  CREATE TABLE t2(
  id INT,
  NAME VARCHAR(20),
  age SMALLINT,
  number INT
  );
  -- 添加主键约束
  ALTER TABLE t2 ADD PRIMARY KEY (id);
  SHOW COLUMNS FROM t2;
  ```

- 删除主键约束

  ```mysql
  AlTER TABLE tb_name DROP PRIMARY KEY;
  -- 例子
  -- 删除t2主键
  ALTER TABLE t2 DROP PRIMARY KEY;
  SHOW COLUMNS FROM t2;
  ```

- 添加唯一约束

  ```mysql
  -- 例子给t2的name添加唯一约束
  ALTER TABLE t2 ADD UNIQUE KEY (name)
  SHOW COLUMNS FROM t2;
  ```

- 删除唯一约束

  ```mysql
  -- 例子
  ALTER TABLE t2 DROP KEY name;
  SHOW COLUMNS FROM t2;
  ```

- 添加外键约束

  ```mysql
  -- 给t2的number列添加外键参照t1的id
  ALTER TABLE t2 ADD FOREIGN KEY(number) REFERENCES t1(id);
  SHOW COLUMNS FROM t2;
  ```

- 删除外键约束

  ```mysql
  -- 例子
  -- 1. 现在查找t2外键的名字
  SHOW CREATE TABLE t2;
  /* 根据结果得到外键名字是t2_ibfk_1
  CREATE TABLE `t2` ( `id` int(11) NOT NULL DEFAULT '0', `name` varchar(20) DEFAULT NULL, `age` smallint(6) DEFAULT NULL, `number` int(11) DEFAULT NULL, KEY `number` (`number`), CONSTRAINT `t2_ibfk_1` FOREIGN KEY (`number`) REFERENCES `t1` (`id`) ) ENGINE=InnoDB DEFAULT CHARSET=utf8
  */
  -- 删除外键
  ALTER TABLE t2 DROP FOREIGN KEY t2_ibfk_1;
  ```

- 添加默认默认约束

  ```mysql
  -- 给t2的age设置默认值为18
  ALTER TABLE t2 ALTER age SET DEFAULT 18;
  SHOW COLUMNS FROM t2;
  ```

- 删除默认约束

  ```mysql
  -- 例子 删除t2age的默认值
  ALTER TABLE t2 ALTER age DROP DEFAULT;
  SHOW COLUMNS FROM t2;
  ```

#### 修改列定义/列名称

- 修改列定义

  ```mysql
  ALTER TABLE tb_name MODIFY [COLUMN] col_name col_definition [FIRST | AFTER col_name]
  -- 创建t3
  create table t3(
      id int not null,
      name varchar(30),
      number tinyint unsigned not null,
      p_age int
  );
  show columns from t3;
  -- 修改 p_age 列为 smallint unsigned(没符合，正数)并放在name下
  ALTER TABLE t3 MODIFY p_age SMALLINT UNSIGNED NOT NULL AFTER NAME;
  SHOW COLUMNS FROM t3;
  ```

  

- 修改列名称

```mysql
ALTER TABLE tb_name CHANGE [COLUMN] old_col_name new_col_name colunm_definition [FIRST | AFTER col_name]
-- 例子 把t3的number列修改为salary,float(8,2) 没符号位，放在name下
ALTER TABLE t3 CHANGE number salary FLOAT(8,2) UNSIGNED NOT NULL AFTER NAME;
SHOW COLUMNS FROM t3;
```

#### 修改数据表

- 数据据表更名

  ```mysql
  -- 第一种
  ALTER TABLE tb_name RENAME [TO|AS] new_table_name
  -- 同时修改多表名
  RENAME TABLE tb_name TO new_tb_name [,tb_name2 TO tb_new_name,...]
  
  -- 例子
  ALTER TABLE t3 RENAME t4;
  SHOW TABLES;
  ```

  