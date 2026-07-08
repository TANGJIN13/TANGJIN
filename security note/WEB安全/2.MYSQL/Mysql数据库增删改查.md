### 1、mysql数据库增操作
![Pasted image 20241029212320.png](http://gzxingyu.cloud/wp-content/uploads/2025/01/Pasted-image-20241029212320-1.png)
#### （1）新增命令create
1、create database 库名;  创建数据库

2、Create database 库名 character set utf8;  指定编码数据库 utf-8
![Pasted image 20241029212743.png](http://gzxingyu.cloud/wp-content/uploads/2025/01/Pasted-image-20241029212743-1.png)

3、Use 库名;  指定数据库
![Pasted image 20241029212809.png](http://gzxingyu.cloud/wp-content/uploads/2025/01/Pasted-image-20241029212809-1.png)

4、创建表

Create table 表名(

字段名1  数据类型(字节数),  如    id  int(11),

字段名2  数据类型(字节数),  username  verchar(50),

...............

);
![Pasted image 20241029212957.png](http://gzxingyu.cloud/wp-content/uploads/2025/01/Pasted-image-20241029212957-1.png)

5、插入数据

  Insert into 表名(字段1,字段2，字段3.......) values(值1,值2,.........)  注意字符型要打’’这个符号
  ![Pasted image 20241029213347.png](http://gzxingyu.cloud/wp-content/uploads/2025/01/Pasted-image-20241029213347-1.png)

第二种写法

  Insert into 表名 set 字段1=值1, 字段2=值2  注意字符型
  ![Pasted image 20241029213508.png](http://gzxingyu.cloud/wp-content/uploads/2025/01/Pasted-image-20241029213508-1.png)

同时插入多条数据

  Insert into 表名(字段1,字段2，字段3.......) values(值1,值2,.........),(值1,值2,.........),(值1,值2,.........);
  ![Pasted image 20241029213644.png](http://gzxingyu.cloud/wp-content/uploads/2025/01/Pasted-image-20241029213644-1.png)
####  （2）删除命令Drop
1、Drop database 库名;  删除数据库
![Pasted image 20241029213936.png](http://gzxingyu.cloud/wp-content/uploads/2025/01/Pasted-image-20241029213936-1.png)

2、drop table 表名;  删除表


3、delete from 表名 where 字段 = 值;        删除数据
![Pasted image 20241029214056.png](http://gzxingyu.cloud/wp-content/uploads/2025/01/Pasted-image-20241029214056-1.png)
where（判断）
![Pasted image 20241029214324.png](http://gzxingyu.cloud/wp-content/uploads/2025/01/Pasted-image-20241029214324-1.png)
delete from user 删库
#### （3）查询命令select
1、show databases;  查看存在的数据库

2、Use 数据库名;    指定数据库

3、查询语句

Select 字段1，字段2，字段3 from 表名  where 条件;

  如 select username form abc where id =1   这个的意思是  查询abc表中  id =1 的 username 数据

Select * from 表名;  代表查询表里所有数据
4、查询起别名

Select username a from 表名 where 条件;  a就是username的别名


查询函数 函数是指一段可以直接被另一段程序或代码引用的程序或代码。

5、Select current_user();    当前用户名

6、select session_user();  连接数据库的用户名

7、select @@basedir;    mysql安装路径

8、select @@datadir;  数据库路径

9、select @@version_compile_os;  操作系统版本

##### 字符串连接函数

concat(),concat_ws(),group_concat()

10、select concat(id,'|',username,'|',password) from users;

11、select concat_ws('|',id,username,password) from users;

12、select group_concat('\n',username) from users;
##### 字符串截取函数
13、select substr((select username from users limit 0,1),1,2);

  //代表从users中username第一条数据从第一位开始截取，截取两位数

14、select mid('martin',2,3);    //代表借助MID函数从字符串‘martin’中从第二位开始，提取长度为5的字符串。

15、select left('martin',2);

  //代表从左数第二位

16、select right('martin',2);

  //代表从右数第二位

17、select locate('security','1234security'); 

  //返回字符串security中第一次出现子字符串的位置