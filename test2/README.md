PART1
```
CREATE ROLE WHITE12138;

GRANT connect,resource,CREATE VIEW TO WHITE12138:

SQL> CREATE USER WHITE IDENTIFIED BY 123 DEFAULT TABLESPACE users TEMPORARY TABLESPACE temp;

SQL> ALTER USER WHITE QUOTA 50M ON users;

 GRANT WHITE12138TO WHITE ;

 exit
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191012180539194.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)

PART2
```
show user;
CREATE TABLE mytable (id number,name varchar(50));
INSERT INTO mytable(id,name)VALUES(1,'zhang');
INSERT INTO mytable(id,name)VALUES (2,'wang');
CREATE VIEW myview AS SELECT name FROM mytable;
SELECT * FROM myview;
GRANT SELECT ON myview TO hr;
exit
```
![`在这里插入图片描述`](https://img-blog.csdnimg.cn/20191012180649760.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)


PART3
```
SELECT * FROM white.myview;
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191012180745747.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)
PART4

```
UPDATE students set "ORACLE用户名"='WHITE' where "学号"='201710414227'
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191012182014392.png)

PART5
```
UPDATE students set "ORACLE表名"='mytable' where "学号"='201710414227'
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019101218112083.png)



PART6
结果


![在这里插入图片描述](https://img-blog.csdnimg.cn/20191012181836234.png)
