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
SELECT tablespace_name,FILE_NAME,BYTES/1024/1024 MB,MAXBYTES/1024/1024 MAX_MB,autoextensible FROM dba_data_files  WHERE  tablespace_name='USERS';

SELECT a.tablespace_name "表空间名",Total/1024/1024 "大小MB",
 free/1024/1024 "剩余MB",( total - free )/1024/1024 "使用MB",
 Round(( total - free )/ total,4)* 100 "使用率%"
 from (SELECT tablespace_name,Sum(bytes)free
        FROM   dba_free_space group  BY tablespace_name)a,
       (SELECT tablespace_name,Sum(bytes)total FROM dba_data_files
        group  BY tablespace_name)b
 where  a.tablespace_name = b.tablespace_name;
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191014115011487.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)


PART5

```
UPDATE students set "ORACLE用户名"='WHITE' where "学号"='201710414227'
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/20191012182014392.png)


PART6

```
UPDATE students set "ORACLE表名"='mytable' where "学号"='201710414227'
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2019101218112083.png)



PART7

结果


![在这里插入图片描述](https://img-blog.csdnimg.cn/20191012181836234.png)
