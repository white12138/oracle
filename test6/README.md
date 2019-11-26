



**一：用户及权限管理**

**创建第一个角色**
第1步：以system登录到pdborcl，创建角色PINK和用户PINK123 ，并授权和分配空间：

```
CREATE ROLE PINK;

GRANT connect,resource,CREATE VIEW TO PINK;

CREATE USER PINK123 IDENTIFIED BY 123 DEFAULT TABLESPACE users TEMPORARY TABLESPACE temp;

ALTER USER PINK123 QUOTA 50M ON users;

GRANT PINK TO PINK123 ;

 Exit

```
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019112412090618.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)
**创建第二个角色**
```
CREATE ROLE PINK2;

GRANT connect,resource TO PINK2;

CREATE USER PINK321 IDENTIFIED BY 123 DEFAULT TABLESPACE users TEMPORARY TABLESPACE temp;

ALTER USER PINK321 QUOTA 50M ON users;

GRANT PINK2 TO PINK321 ;

 Exit
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191124134101827.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)

**二：设计项目涉及的表及表空间使用方案。至少5张表和5万条数据，两个表空间。**


**创建表空间**

```
Create Tablespace U123
datafile
'/home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_U123_1.dbf'
  SIZE 100M AUTOEXTEND ON NEXT 256M MAXSIZE UNLIMITED,
'/home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_U123_2.dbf'
  SIZE 100M AUTOEXTEND ON NEXT 256M MAXSIZE UNLIMITED
EXTENT MANAGEMENT LOCAL SEGMENT SPACE MANAGEMENT AUTO;
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191124193257144.png)

```
Create Tablespace U1234
datafile
'/home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_U1234_1.dbf'
  SIZE 100M AUTOEXTEND ON NEXT 256M MAXSIZE UNLIMITED,
'/home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_U1234_2.dbf'
  SIZE 100M AUTOEXTEND ON NEXT 256M MAXSIZE UNLIMITED
EXTENT MANAGEMENT LOCAL SEGMENT SPACE MANAGEMENT AUTO;
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191124193411858.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)



**创建表1DEPARTMENTS**

```
CREATE TABLE DEPARTMENTS
(
  DEPARTMENT_ID NUMBER(6, 0) NOT NULL
, DEPARTMENT_NAME VARCHAR2(40 BYTE) NOT NULL
, CONSTRAINT DEPARTMENTS_PK PRIMARY KEY
  (
    DEPARTMENT_ID
  )
  USING INDEX
  (
      CREATE UNIQUE INDEX DEPARTMENTS_PK ON DEPARTMENTS (DEPARTMENT_ID ASC)
      NOLOGGING
      TABLESPACE USERS
      PCTFREE 10
      INITRANS 2
      STORAGE
      (
        INITIAL 65536
        NEXT 1048576
        MINEXTENTS 1
        MAXEXTENTS UNLIMITED
        BUFFER_POOL DEFAULT
      )
      NOPARALLEL
  )
  ENABLE
)
NOLOGGING
TABLESPACE USERS
PCTFREE 10
INITRANS 1
STORAGE
(
  INITIAL 65536
  NEXT 1048576
  MINEXTENTS 1
  MAXEXTENTS UNLIMITED
  BUFFER_POOL DEFAULT
)
NOCOMPRESS NO INMEMORY NOPARALLEL;



```
**创建表2EMPLOYEES**

```
CREATE TABLE EMPLOYEES
(
  EMPLOYEE_ID NUMBER(6, 0) NOT NULL
, NAME VARCHAR2(40 BYTE) NOT NULL
, EMAIL VARCHAR2(40 BYTE)
, PHONE_NUMBER VARCHAR2(40 BYTE)
, HIRE_DATE DATE NOT NULL
, SALARY NUMBER(8, 2)
, MANAGER_ID NUMBER(6, 0)
, DEPARTMENT_ID NUMBER(6, 0)
, PHOTO BLOB
, CONSTRAINT EMPLOYEES_PK PRIMARY KEY
  (
    EMPLOYEE_ID
  )
  USING INDEX
  (
      CREATE UNIQUE INDEX EMPLOYEES_PK ON EMPLOYEES (EMPLOYEE_ID ASC)
      NOLOGGING
      TABLESPACE USERS
      PCTFREE 10
      INITRANS 2
      STORAGE
      (
        INITIAL 65536
        NEXT 1048576
        MINEXTENTS 1
        MAXEXTENTS UNLIMITED
        BUFFER_POOL DEFAULT
      )
      NOPARALLEL
  )
  ENABLE
)
NOLOGGING
TABLESPACE USERS
PCTFREE 10
INITRANS 1
STORAGE
(
  INITIAL 65536
  NEXT 1048576
  MINEXTENTS 1
  MAXEXTENTS UNLIMITED
  BUFFER_POOL DEFAULT
)
NOCOMPRESS
NO INMEMORY
NOPARALLEL
LOB (PHOTO) STORE AS SYS_LOB0000092017C00009$$
(
  ENABLE STORAGE IN ROW
  CHUNK 8192
  NOCACHE
  NOLOGGING
  TABLESPACE USERS
  STORAGE
  (
    INITIAL 106496
    NEXT 1048576
    MINEXTENTS 1
    MAXEXTENTS UNLIMITED
    BUFFER_POOL DEFAULT
  )
);

```

**创建表3PRODUCTS**

```
CREATE TABLE PRODUCTS
(
  PRODUCT_NAME VARCHAR2(40 BYTE) NOT NULL
, PRODUCT_TYPE VARCHAR2(40 BYTE) NOT NULL
, CONSTRAINT PRODUCTS_PK PRIMARY KEY
  (
    PRODUCT_NAME
  )
  ENABLE
)
LOGGING
TABLESPACE "USERS"
PCTFREE 10
INITRANS 1
STORAGE
(
  INITIAL 65536
  NEXT 1048576
  MINEXTENTS 1
  MAXEXTENTS 2147483645
  BUFFER_POOL DEFAULT
);

ALTER TABLE PRODUCTS
ADD CONSTRAINT PRODUCTS_CHK1 CHECK
(PRODUCT_TYPE IN ('耗材', '手机', '电脑'))
ENABLE;


```

**创建表4ORDER_DETAILS**

```
CREATE TABLE ORDER_DETAILS
(
  ID NUMBER(10, 0) NOT NULL
, ORDER_ID NUMBER(10, 0) NOT NULL
, PRODUCT_NAME VARCHAR2(40 BYTE) NOT NULL
, PRODUCT_NUM NUMBER(8, 2) NOT NULL
, PRODUCT_PRICE NUMBER(8, 2) NOT NULL
, CONSTRAINT ORDER_DETAILS_FK1 FOREIGN KEY
  (
  ORDER_ID
  )
  REFERENCES ORDERS
  (
  ORDER_ID
  )
  ENABLE
)
TABLESPACE USERS
PCTFREE 10
INITRANS 1
STORAGE
(
  BUFFER_POOL DEFAULT
)
NOCOMPRESS
NOPARALLEL
PARTITION BY REFERENCE (ORDER_DETAILS_FK1)
(
  PARTITION PARTITION_BEFORE_2016
  NOLOGGING
  TABLESPACE USERS --必须指定表空间，否则会将分区存储在用户的默认表空间中
  PCTFREE 10
  INITRANS 1
  STORAGE
  (
    INITIAL 8388608
    NEXT 1048576
    MINEXTENTS 1
    MAXEXTENTS UNLIMITED
    BUFFER_POOL DEFAULT
  )
  NOCOMPRESS NO INMEMORY,
  PARTITION PARTITION_BEFORE_2017
  NOLOGGING
  TABLESPACE USERS02
  PCTFREE 10
  INITRANS 1
  STORAGE
  (
    INITIAL 8388608
    NEXT 1048576
    MINEXTENTS 1
    MAXEXTENTS UNLIMITED
    BUFFER_POOL DEFAULT
  )
  NOCOMPRESS NO INMEMORY
)
;


```

**创建表5ORDER_ID_TEMP**

```
CREATE GLOBAL TEMPORARY TABLE "ORDER_ID_TEMP"
   (	"ORDER_ID" NUMBER(10,0) NOT NULL ENABLE,
	 CONSTRAINT "ORDER_ID_TEMP_PK" PRIMARY KEY ("ORDER_ID") ENABLE
   ) ON COMMIT DELETE ROWS ;

   COMMENT ON TABLE "ORDER_ID_TEMP"  IS '用于触发器存储临时ORDER_ID';

```

**创建表6ORDERS**

```
CREATE TABLE ORDERS
(
  ORDER_ID NUMBER(10, 0) NOT NULL
, CUSTOMER_NAME VARCHAR2(40 BYTE) NOT NULL
, CUSTOMER_TEL VARCHAR2(40 BYTE) NOT NULL
, ORDER_DATE DATE NOT NULL
, EMPLOYEE_ID NUMBER(6, 0) NOT NULL
, DISCOUNT NUMBER(8, 2) DEFAULT 0
, TRADE_RECEIVABLE NUMBER(8, 2) DEFAULT 0
)
TABLESPACE USERS
PCTFREE 10
INITRANS 1
STORAGE
(
  BUFFER_POOL DEFAULT
)
NOCOMPRESS
NOPARALLEL
PARTITION BY RANGE (ORDER_DATE)
(
  PARTITION PARTITION_BEFORE_2016 VALUES LESS THAN (TO_DATE(' 2016-01-01 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 'NLS_CALENDAR=GREGORIAN'))
  NOLOGGING
  TABLESPACE USERS
  PCTFREE 10
  INITRANS 1
  STORAGE
  (
    INITIAL 8388608
    NEXT 1048576
    MINEXTENTS 1
    MAXEXTENTS UNLIMITED
    BUFFER_POOL DEFAULT
  )
  NOCOMPRESS NO INMEMORY
, PARTITION PARTITION_BEFORE_2017 VALUES LESS THAN (TO_DATE(' 2017-01-01 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 'NLS_CALENDAR=GREGORIAN'))
  NOLOGGING
  TABLESPACE USERS02
  PCTFREE 10
  INITRANS 1
  STORAGE
  (
    INITIAL 8388608
    NEXT 1048576
    MINEXTENTS 1
    MAXEXTENTS UNLIMITED
    BUFFER_POOL DEFAULT
  )
  NOCOMPRESS NO INMEMORY
);

```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191124193025947.png)

**插入数据**

```
declare
  dt date;
  m number(8,2);
  V_EMPLOYEE_ID NUMBER(6);
  v_order_id number(10);
  v_name varchar2(100);
  v_tel varchar2(100);
  v number(10,2);

begin
  for i in 1..10000
  loop
    if i mod 2 =0 then
      dt:=to_date('2015-3-2','yyyy-mm-dd')+(i mod 60);
    else
      dt:=to_date('2016-3-2','yyyy-mm-dd')+(i mod 60);
    end if;
    V_EMPLOYEE_ID:=CASE I MOD 6 WHEN 0 THEN 11 WHEN 1 THEN 111 WHEN 2 THEN 112
                                WHEN 3 THEN 12 WHEN 4 THEN 121 ELSE 122 END;
    --插入订单
    v_order_id:=SEQ_ORDER_ID.nextval; --应该将SEQ_ORDER_ID.nextval保存到变量中。
    v_name := 'aa'|| 'aa';
    v_name := 'zhang' || i;
    v_tel := '139888883' || i;
    insert /*+append*/ into ORDERS (ORDER_ID,CUSTOMER_NAME,CUSTOMER_TEL,ORDER_DATE,EMPLOYEE_ID,DISCOUNT)
      values (v_order_id,v_name,v_tel,dt,V_EMPLOYEE_ID,dbms_random.value(100,0));
    --插入订单y一个订单包括3个产品
    v:=dbms_random.value(10000,4000);
    v_name:='computer'|| (i mod 3 + 1);
    insert /*+append*/ into ORDER_DETAILS(ID,ORDER_ID,PRODUCT_NAME,PRODUCT_NUM,PRODUCT_PRICE)
      values (SEQ_ORDER_DETAILS_ID.NEXTVAL,v_order_id,v_name,2,v);
    v:=dbms_random.value(1000,50);
    v_name:='paper'|| (i mod 3 + 1);
    insert /*+append*/ into ORDER_DETAILS(ID,ORDER_ID,PRODUCT_NAME,PRODUCT_NUM,PRODUCT_PRICE)
      values (SEQ_ORDER_DETAILS_ID.NEXTVAL,v_order_id,v_name,3,v);
    v:=dbms_random.value(9000,2000);
    v_name:='phone'|| (i mod 3 + 1);
    insert /*+append*/ into ORDER_DETAILS(ID,ORDER_ID,PRODUCT_NAME,PRODUCT_NUM,PRODUCT_PRICE)
      values (SEQ_ORDER_DETAILS_ID.NEXTVAL,v_order_id,v_name,1,v);
    --在触发器关闭的情况下，需要手工计算每个订单的应收金额：
    select sum(PRODUCT_NUM*PRODUCT_PRICE) into m from ORDER_DETAILS where ORDER_ID=v_order_id;
    if m is null then
     m:=0;
    end if;
    UPDATE ORDERS SET TRADE_RECEIVABLE = m - discount WHERE ORDER_ID=v_order_id;
    IF I MOD 1000 =0 THEN
      commit; --每次提交会加快插入数据的速度
    END IF;
  end loop;
  --统计用户的所有表，所需时间很长：2千万行数据，需要1600秒，该语句可选
  --dbms_stats.gather_schema_stats(User,estimate_percent=>100,cascade=> TRUE); --estimate_percent采样行的百分比
end;
重复5次
```

查询数据量

```
SELECT count(*) from ORDERS
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191124193958619.png)

**三：在数据库中建立一个程序包，在包中用PL/SQL语言设计一些存储过程和函数，实现比较复杂的业务逻辑，用模拟数据进行执行计划分析。**


```
create or replace PACKAGE MyPack IS
  /*
  包MyPack中有：
  一个函数:Get_SaleAmount(V_DEPARTMENT_ID NUMBER)，
  一个过程:Get_Employees(V_EMPLOYEE_ID NUMBER)
  */
  FUNCTION Get_SaleAmount(V_DEPARTMENT_ID NUMBER) RETURN NUMBER;
  PROCEDURE Get_Employees(V_EMPLOYEE_ID NUMBER);
END MyPack;
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191124195845138.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)





```
create or replace PACKAGE BODY MyPack IS
  FUNCTION Get_SaleAmount(V_DEPARTMENT_ID NUMBER) RETURN NUMBER
  AS
    N NUMBER(20,2); --注意，订单ORDERS.TRADE_RECEIVABLE的类型是NUMBER(8,2),汇总之后，数据要大得多。
    BEGIN
      SELECT SUM(O.TRADE_RECEIVABLE) into N  FROM ORDERS O,EMPLOYEES E
      WHERE O.EMPLOYEE_ID=E.EMPLOYEE_ID AND E.DEPARTMENT_ID =V_DEPARTMENT_ID;
      RETURN N;
    END;

  PROCEDURE GET_EMPLOYEES(V_EMPLOYEE_ID NUMBER)
  AS
    LEFTSPACE VARCHAR(2000);
    begin
      --通过LEVEL判断递归的级别
      LEFTSPACE:=' ';
      --使用游标
      for v in
      (SELECT LEVEL,EMPLOYEE_ID,NAME,MANAGER_ID FROM employees
      START WITH EMPLOYEE_ID = V_EMPLOYEE_ID
      CONNECT BY PRIOR EMPLOYEE_ID = MANAGER_ID)
      LOOP
        DBMS_OUTPUT.PUT_LINE(LPAD(LEFTSPACE,(V.LEVEL-1)*4,' ')||
                             V.EMPLOYEE_ID||' '||v.NAME);
      END LOOP;
    END;
END MyPack;
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191124195949646.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)


**用模拟数据进行执行计划分析。**

```
select * from order_details od, orders ord,products pr where od.order_id=ord.ORDER_ID
and od.PRODUCT_NAME=pr.PRODUCT_NAME and ord.ORDER_DATE between to_date('2016-01-01','yyyy-mm-dd')
and to_date('2017-01-01','yyyy-mm-dd');
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191125105313190.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)

**Oracle 12c数据库全备份和全恢复实验**

**1. 开始全备份**
```
[oracle@oracle-pc ~]$ cat rman_level0.sh
#rman_level0.sh 
#!/bin/sh

export NLS_LANG='SIMPLIFIED CHINESE_CHINA.AL32UTF8'
export ORACLE_HOME=/home/oracle/app/oracle/product/12.1.0/dbhome_1  
export ORACLE_SID=orcl  
export PATH=$ORACLE_HOME/bin:$PATH  


rman target / nocatalog msglog=/home/oracle/rman_backup/lv0_`date +%Y%m%d-%H%M%S`_L0.log << EOF
run{
configure retention policy to redundancy 1;
configure controlfile autobackup on;
configure controlfile autobackup format for device type disk to '/home/oracle/rman_backup/%F';
configure default device type to disk;
crosscheck backup;
crosscheck archivelog all;
allocate channel c1 device type disk;
backup as compressed backupset incremental level 0 database format '/home/oracle/rman_backup/dblv0_%d_%T_%U.bak'
   plus archivelog format '/home/oracle/rman_backup/arclv0_%d_%T_%U.bak';
report obsolete;
delete noprompt obsolete;
delete noprompt expired backup;
delete noprompt expired archivelog all;
release channel c1;
}
EOF

exit
[oracle@oracle-pc ~]$ ./rman_level0.sh
RMAN> 2> 3> 4> 5> 6> 7> 8> 9> 10> 11> 12> 13> 14> 15> 16> RMAN> [oracle@oracle-p
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191124204534301.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)


查看备份文件
*.log是日志文件
dblv0*.bak是数据库的备份文件
arclv0*.bak是归档日期的备份文件
c-1392946895-20191124-02是控制文件和参数的备份

```
[oracle@oracle-pc rman_backup]$  ls
arclv0_ORCL_20191124_9guhmtu3_1_1.bak  dblv0_ORCL_20191124_9euhmtod_1_1.bak  lv0_20191124-203053_L0.log
c-1392946895-20191124-02               dblv0_ORCL_20191124_9fuhmtsv_1_1.bak  lv0_20191124-203217_L0.log
dblv0_ORCL_20191124_9duhmtl3_1_1.bak   lv0_20191111-003303_L0.log            lv1_20191111-003650_L0.log
[oracle@oracle-pc rman_backup]$ arclv0_ORCL_20191120_dauhb2fm_1_1.bak

```



**查看备份文件的内容**

```

[oracle@oracle-pc ~]$ rman target /

Recovery Manager: Release 12.1.0.2.0 - Production on 星期一 11月 25 13:07:05 2019

Copyright (c) 1982, 2014, Oracle and/or its affiliates.  All rights reserved.

connected to target database: ORCL (DBID=1392946895)

RMAN> list backup;

using target database control file instead of recovery catalog

List of Backup Sets
===================


BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
251     Incr 0  219.63M    DISK        00:03:01     25-11月-19    
        BP Key: 251   Status: AVAILABLE  Compressed: YES  Tag: TAG20191125T124528
        Piece Name: /home/oracle/rman_backup/dblv0_ORCL_20191125_9buhomj9_1_1.bak
  List of Datafiles in backup set 251
  Container ID: 3, PDB Name: PDBORCL
  File LV Type Ckp SCN    Ckp Time   Name
  ---- -- ---- ---------- ---------- ----
  8    0  Incr 47526430   25-11月-19 /home/oracle/app/oracle/oradata/orcl/pdborcl/system01.dbf
  9    0  Incr 47526430   25-11月-19 /home/oracle/app/oracle/oradata/orcl/pdborcl/sysaux01.dbf
  10   0  Incr 47526430   25-11月-19 /home/oracle/app/oracle/oradata/orcl/pdborcl/SAMPLE_SCHEMA_users01.dbf
  11   0  Incr 47526430   25-11月-19 /home/oracle/app/oracle/oradata/orcl/pdborcl/example01.dbf
  12   0  Incr 47526430   25-11月-19 /home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_users02_1.dbf
  13   0  Incr 47526430   25-11月-19 /home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_users02_2.dbf
  16   0  Incr 47526430   25-11月-19 /home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_users02_3.dbf
  17   0  Incr 47526430   25-11月-19 /home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_users02_4.dbf
  77   0  Incr 47526430   25-11月-19 /home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_users03_1.dbf
  78   0  Incr 47526430   25-11月-19 /home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_users03_2.dbf
  79   0  Incr 47526430   25-11月-19 /home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_U123_1.dbf
  80   0  Incr 47526430   25-11月-19 /home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_U123_2.dbf
  81   0  Incr 47526430   25-11月-19 /home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_U1234_1.dbf
  82   0  Incr 47526430   25-11月-19 /home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_U1234_2.dbf

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
252     Incr 0  369.98M    DISK        00:02:13     25-11月-19    
        BP Key: 252   Status: AVAILABLE  Compressed: YES  Tag: TAG20191125T124528
        Piece Name: /home/oracle/rman_backup/dblv0_ORCL_20191125_9cuhomp6_1_1.bak
  List of Datafiles in backup set 252
  File LV Type Ckp SCN    Ckp Time   Name
  ---- -- ---- ---------- ---------- ----
  1    0  Incr 47526499   25-11月-19 /home/oracle/app/oracle/oradata/orcl/system01.dbf
  3    0  Incr 47526499   25-11月-19 /home/oracle/app/oracle/oradata/orcl/sysaux01.dbf
  4    0  Incr 47526499   25-11月-19 /home/oracle/app/oracle/oradata/orcl/undotbs01.dbf
  6    0  Incr 47526499   25-11月-19 /home/oracle/app/oracle/oradata/orcl/users01.dbf

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
253     Incr 0  157.08M    DISK        00:00:45     25-11月-19    
        BP Key: 253   Status: AVAILABLE  Compressed: YES  Tag: TAG20191125T124528
        Piece Name: /home/oracle/rman_backup/dblv0_ORCL_20191125_9duhomti_1_1.bak
  List of Datafiles in backup set 253
  Container ID: 2, PDB Name: PDB$SEED
  File LV Type Ckp SCN    Ckp Time   Name
  ---- -- ---- ---------- ---------- ----
  5    0  Incr 1819408    01-12月-14 /home/oracle/app/oracle/oradata/orcl/pdbseed/system01.dbf
  7    0  Incr 1819408    01-12月-14 /home/oracle/app/oracle/oradata/orcl/pdbseed/sysaux01.dbf

BS Key  Size       Device Type Elapsed Time Completion Time
------- ---------- ----------- ------------ ---------------
254     66.50K     DISK        00:00:00     25-11月-19    
        BP Key: 254   Status: AVAILABLE  Compressed: YES  Tag: TAG20191125T125147
        Piece Name: /home/oracle/rman_backup/arclv0_ORCL_20191125_9euhomv4_1_1.bak

  List of Archived Logs in backup set 254
  Thrd Seq     Low SCN    Low Time   Next SCN   Next Time
  ---- ------- ---------- ---------- ---------- ---------
  1    1616    47526388   25-11月-19 47526592   25-11月-19

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
255     Full    17.77M     DISK        00:00:04     25-11月-19    
        BP Key: 255   Status: AVAILABLE  Compressed: NO  Tag: TAG20191125T125149
        Piece Name: /home/oracle/rman_backup/c-1392946895-20191125-00
  SPFILE Included: Modification time: 25-11月-19
  SPFILE db_unique_name: ORCL
  Control File Included: Ckp SCN: 47526605     Ckp time: 25-11月-19

```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191125131024789.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191125131119611.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191125131146976.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)




由上面的"list backup;" 输出可以看出，备份集中的文件内容是：
/home/oracle/rman_backup/dblv0_ORCL_20191120_d7uhb2ap_1_1.bak 是插接数据库PDBORCL的备份集
/home/oracle/rman_backup/dblv0_ORCL_20191120_d8uhb2c6_1_1.bak 是ORCL的备份集
/home/oracle/rman_backup/dblv0_ORCL_20191120_d9uhb2ei_1_1.bak是PDB$SEED的备份集
/home/oracle/rman_backup/arclv0_ORCL_20191120_dauhb2fm_1_1.bak是归档文件的备份集
/home/oracle/rman_backup/c-1392946895-20191120-01是参数文件(SPFILE)和控制文件(Control File)的备份集



**备份后修改数据**

```
[oracle@oracle-pc ~]$ sqlplus U_PINK/123@pdborcl

SQL*Plus: Release 12.1.0.2.0 Production on 星期一 11月 25 13:12:49 2019

Copyright (c) 1982, 2014, Oracle.  All rights reserved.

Last Successful login time: 星期日 11月 24 2019 19:01:47 +08:00

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options


Session altered.

SQL> create table t1 (id number,name varchar2(50);
create table t1 (id number,name varchar2(50)
                                           *
ERROR at line 1:
ORA-00907: 缺失右括号


SQL> create table t1 (id number,name varchar2(50));

Table created.

SQL> insert into t1 values(1,'zhang');

1 row created.

SQL>  commit;

Commit complete.

SQL> select * from t1;

        ID NAME
---------- --------------------------------------------------
         1 zhang

SQL> exit

```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191125214303411.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191125131609271.png)


**3. 删除数据库文件，模拟数据库文件损坏**

```
[oracle@oracle-pc ~]$ rm /home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_U123_1.dbf

```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191125105837545.png)



**删除数据库文件后修改数据**
删除数据文件后，仍然可以增加一条数据。这是因为增加的数据并没有写入数据文件，而是写到了日志文件中。如果增加的数据较多的时候，就会出问题了。


```
[oracle@oracle-pc ~]$ sqlplus U_PINK/123@pdborcl

SQL*Plus: Release 12.1.0.2.0 Production on 星期一 11月 25 21:05:20 2019

Copyright (c) 1982, 2014, Oracle.  All rights reserved.

Last Successful login time: 星期一 11月 25 2019 20:59:47 +08:00

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options


Session altered.

SQL> insert into t1 values(2,'wang');

1 row created.

SQL> commit;

Commit complete.

SQL> select * from t1;

        ID NAME
---------- --------------------------------------------------
         2 wang
         1 zhang

SQL> declare
  2  n number;
  3  begin
  4    for n in 1..10000 loop
  5   insert into t1 values(n,'name'||n);
  6   end loop;
  7  end;
  8  /
declare
*
ERROR at line 1:
ORA-01116: 打开数据库文件 79 时出错 ORA-01110:
数据文件 79: '/home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_U123_1.dbf'
ORA-27041: 无法打开文件
Linux-x86_64 Error: 2: No such file or directory
Additional information: 3
ORA-06512: 在 line 5


SQL> select * from t1;

        ID NAME
---------- --------------------------------------------------
         2 wang
         1 zhang

SQL> exit

```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191125211121235.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019112521114371.png)

 **4数据库完全恢复**
 **4.1 重启损坏的数据库到mount状态**
 通过shutdown immediate无法正常关闭数据库，只能通过shutdown abort强制关闭。然后将数据库启动到mount状态。

```
[oracle@oracle-pc ~]$  sqlplus / as sysdba

SQL*Plus: Release 12.1.0.2.0 Production on 星期一 11月 25 21:12:15 2019

Copyright (c) 1982, 2014, Oracle.  All rights reserved.


Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options


Session altered.

SQL> shutdown immediate
ORA-01116: 打开数据库文件 79 时出错
ORA-01110: 数据文件 79: '/home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_U123_1.dbf'
ORA-27041: 无法打开文件
Linux-x86_64 Error: 2: No such file or directory
Additional information: 3
SQL> shutdown abort
ORACLE instance shut down.
SQL> startup mount
ORACLE instance started.

Total System Global Area 1577058304 bytes
Fixed Size                  2924832 bytes
Variable Size             738201312 bytes
Database Buffers          654311424 bytes
Redo Buffers               13848576 bytes
In-Memory Area            167772160 bytes
Database mounted.
SQL> exit


```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191125211522262.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)



**4.2 开始恢复数据库**

```
[oracle@oracle-pc ~]$ rman target /

Recovery Manager: Release 12.1.0.2.0 - Production on 星期一 11月 25 21:13:56 2019

Copyright (c) 1982, 2014, Oracle and/or its affiliates.  All rights reserved.

connected to target database: ORCL (DBID=1392946895, not open)

RMAN> restore database ;

Starting restore at 25-11月-19
using target database control file instead of recovery catalog
allocated channel: ORA_DISK_1
channel ORA_DISK_1: SID=243 device type=DISK

skipping datafile 5; already restored to file /home/oracle/app/oracle/oradata/orcl/pdbseed/system01.dbf
skipping datafile 7; already restored to file /home/oracle/app/oracle/oradata/orcl/pdbseed/sysaux01.dbf
channel ORA_DISK_1: starting datafile backup set restore
channel ORA_DISK_1: specifying datafile(s) to restore from backup set
channel ORA_DISK_1: restoring datafile 00008 to /home/oracle/app/oracle/oradata/orcl/pdborcl/system01.dbf
channel ORA_DISK_1: restoring datafile 00009 to /home/oracle/app/oracle/oradata/orcl/pdborcl/sysaux01.dbf
channel ORA_DISK_1: restoring datafile 00010 to /home/oracle/app/oracle/oradata/orcl/pdborcl/SAMPLE_SCHEMA_users01.dbf
channel ORA_DISK_1: restoring datafile 00011 to /home/oracle/app/oracle/oradata/orcl/pdborcl/example01.dbf
channel ORA_DISK_1: restoring datafile 00012 to /home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_users02_1.dbf
channel ORA_DISK_1: restoring datafile 00013 to /home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_users02_2.dbf
channel ORA_DISK_1: restoring datafile 00016 to /home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_users02_3.dbf
channel ORA_DISK_1: restoring datafile 00017 to /home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_users02_4.dbf
channel ORA_DISK_1: restoring datafile 00077 to /home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_users03_1.dbf
channel ORA_DISK_1: restoring datafile 00078 to /home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_users03_2.dbf
channel ORA_DISK_1: restoring datafile 00079 to /home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_U123_1.dbf
channel ORA_DISK_1: restoring datafile 00080 to /home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_U123_2.dbf
channel ORA_DISK_1: restoring datafile 00081 to /home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_U1234_1.dbf
channel ORA_DISK_1: restoring datafile 00082 to /home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_U1234_2.dbf
channel ORA_DISK_1: reading from backup piece /home/oracle/rman_backup/dblv0_ORCL_20191125_9buhpj44_1_1.bak
channel ORA_DISK_1: piece handle=/home/oracle/rman_backup/dblv0_ORCL_20191125_9buhpj44_1_1.bak tag=TAG20191125T205219
channel ORA_DISK_1: restored backup piece 1
channel ORA_DISK_1: restore complete, elapsed time: 00:03:09
channel ORA_DISK_1: starting datafile backup set restore
channel ORA_DISK_1: specifying datafile(s) to restore from backup set
channel ORA_DISK_1: restoring datafile 00001 to /home/oracle/app/oracle/oradata/orcl/system01.dbf
channel ORA_DISK_1: restoring datafile 00003 to /home/oracle/app/oracle/oradata/orcl/sysaux01.dbf
channel ORA_DISK_1: restoring datafile 00004 to /home/oracle/app/oracle/oradata/orcl/undotbs01.dbf
channel ORA_DISK_1: restoring datafile 00006 to /home/oracle/app/oracle/oradata/orcl/users01.dbf
channel ORA_DISK_1: reading from backup piece /home/oracle/rman_backup/dblv0_ORCL_20191125_9cuhpj7i_1_1.bak
channel ORA_DISK_1: piece handle=/home/oracle/rman_backup/dblv0_ORCL_20191125_9cuhpj7i_1_1.bak tag=TAG20191125T205219
channel ORA_DISK_1: restored backup piece 1
channel ORA_DISK_1: restore complete, elapsed time: 00:05:01
Finished restore at 25-11月-19

RMAN> recover database;

Starting recover at 25-11月-19
using channel ORA_DISK_1

starting media recovery
media recovery complete, elapsed time: 00:00:06

Finished recover at 25-11月-19

RMAN> alter database open;

Statement processed

RMAN> exit

```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191125212702400.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019112521274535.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)



**5 查询数据是否恢复**
```
[oracle@oracle-pc ~]$ sqlplus U_PINK/123@pdborcl
SQL> select * from t1;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191125212851623.png)

**设计容灾方案。使用两台主机，通过DataGuard实现数据库整体的异地备份**

**第一步：备库**

```
mkdir -p /home/oracle/app/oracle/diag/orcl
mkdir -p /home/oracle/app/oracle/oradata/stdorcl/
mkdir -p /home/oracle/app/oracle/oradata/stdorcl/pdborcl
mkdir -p /home/oracle/arch
mkdir -p /home/oracle/rman
mkdir -p /home/oracle/app/oracle/oradata/stdorcl/pdbseed/
mkdir -p /home/oracle/app/oracle/oradata/stdorcl/pdb/
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191126125700936.png)
**删除原有数据库:**
```
[oracle@oracle-pc ~]$ sqlplus / as sysdba   

SQL*Plus: Release 12.1.0.2.0 Production on 星期二 11月 26 12:30:46 2019

Copyright (c) 1982, 2014, Oracle.  All rights reserved.


Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options


Session altered.

SQL> shutdown immediate; 
Database closed.
Database dismounted.
ORACLE instance shut down.
SQL> startup mount exclusive restrict;   
ORACLE instance started.

Total System Global Area 1577058304 bytes
Fixed Size                  2924832 bytes
Variable Size             738201312 bytes
Database Buffers          654311424 bytes
Redo Buffers               13848576 bytes
In-Memory Area            167772160 bytes
Database mounted.
SQL> drop database;    

Database dropped.

Disconnected from Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options
SQL> exit;  

```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191126130021860.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)

**启动到nomount7**
```
[oracle@oracle-pc ~]$ sqlplus / as sysdba

SQL*Plus: Release 12.1.0.2.0 Production on 星期二 11月 26 12:33:33 2019

Copyright (c) 1982, 2014, Oracle.  All rights reserved.

Connected to an idle instance.

alter session set NLS_DATE_FORMAT='YYYY-MM-DD HH24:MI:SS'
*
ERROR at line 1:
ORA-01034: ORACLE not available
进程 ID: 0
会话 ID: 0 序列号: 0


SQL> startup nomount 
ORACLE instance started.

Total System Global Area 1577058304 bytes
Fixed Size                  2924832 bytes
Variable Size             469765856 bytes
Database Buffers          922746880 bytes
Redo Buffers               13848576 bytes
In-Memory Area            167772160 bytes
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191126130416263.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)

**第二步：主库:**

```
[oracle@oracle-pc ~]$ sqlplus / as sysdba 

SQL*Plus: Release 12.1.0.2.0 Production on 星期二 11月 26 12:36:00 2019

Copyright (c) 1982, 2014, Oracle.  All rights reserved.


Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options


Session altered.
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191126130619570.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)

```
SQL> select group#,thread#,members,status from v$log;

    GROUP#    THREAD#    MEMBERS STATUS
---------- ---------- ---------- ----------------
         1          1          2 INACTIVE
         2          1          1 INACTIVE
         3          1          1 INACTIVE
         4          1          1 CURRENT
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191126130726632.png)

```

alter database add standby logfile  group 5 '/home/oracle/app/oracle/oradata/orcl/stdredo1.log' size 50m; 
alter database add standby logfile  group 6 '/home/oracle/app/oracle/oradata/orcl/stdredo2.log' size 50m; 
alter database add standby logfile  group 7 '/home/oracle/app/oracle/oradata/orcl/stdredo3.log' size 50m; 
alter database add standby logfile  group 8 '/home/oracle/app/oracle/oradata/orcl/stdredo4.log' size 50m; 

```
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019112613094537.png)

**主库环境开启强制归档**

```
ALTER DATABASE FORCE LOGGING; 
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191126131042980.png)

```

alter system set LOG_ARCHIVE_CONFIG='DG_CONFIG=(orcl,stdorcl)' scope=both sid='*';         
alter system set log_archive_dest_1='LOCATION=/home/oracle/arch VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=orcl' scope=spfile;
alter system set LOG_ARCHIVE_DEST_2='SERVICE=stdorcl LGWR ASYNC  VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE) DB_UNIQUE_NAME=stdorcl' scope=both sid='*';h sid='*';
alter system set fal_client='orcl' scope=both sid='*';    
alter system set FAL_SERVER='stdorcl' scope=both sid='*';  
alter system set standby_file_management=AUTO scope=both sid='*';
alter system set DB_FILE_NAME_CONVERT='/home/oracle/app/oracle/oradata/stdorcl/','/home/oracle/app/oracle/oradata/orcl/' scope=spfile sid='*';  
alter system set LOG_FILE_NAME_CONVERT='/home/oracle/app/oracle/oradata/stdorcl/','/home/oracle/app/oracle/oradata/orcl/' scope=spfile sid='*';
alter system set log_archive_format='%t_%s_%r.arc' scope=spfile sid='*';
alter system set remote_login_passwordfile='EXCLUSIVE' scope=spfile;
alter system set LOG_ARCHIVE_CONFIG='DG_CONFIG=(orcl,stdorcl)' scope=both sid='*';         

```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191126131511683.png)

**编辑主库以及备库的/home/oracle/app/oracle/product/12.1.0/dbhome_1/network/admin/tnsnames.ora文件**

```
gedit /home/oracle/app/oracle/product/12.1.0/dbhome_1/network/admin/tnsnames.ora
```
添加代码
```
ORCL =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = 192.168.199.231)(PORT = 1521))  //**
    )
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = orcl)
    )
  )

stdorcl =
  (DESCRIPTION =
    (ADDRESS = (PROTOCOL = TCP)(HOST = 192.168.199.159)(PORT = 1521))  //**
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SID = orcl)
    )
  )
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191126133349681.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)
**生成/home/oracle/app/oracle/product/12.1.0/dbhome_1/dbs/initorcl.ora**
```
SQL> create pfile from spfile;

File created.

SQL> exit;

```
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019112613354442.png)
**将主库的参数文件，密码文件拷贝到备库:**
```
[oracle@oracle-pc ~]$ scp /home/oracle/app/oracle/product/12.1.0/dbhome_1/dbs/initorcl.ora 192.168.199.159:/home/oracle/app/oracle/product/12.1.0/dbhome_1/dbs/
oracle@192.168.199.159's password: 
initorcl.ora                                                                            100% 2091     2.0KB/s   00:00    
[oracle@oracle-pc ~]$ scp /home/oracle/app/oracle/product/12.1.0/dbhome_1/dbs/orapworcl 192.168.199.159:/home/oracle/app/oracle/product/12.1.0/dbhome_1/dbs/
oracle@192.168.199.159's password: 
orapworcl                                                                               100% 7680     7.5KB/s   00:00    

```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191126201913616.png)

**将主库复制到备库**

```
$rman target sys/123@orcl auxiliary sys/123@stdorcl  
```

**执行duplicate:**

```

run{ 
allocate channel c1 type disk;
allocate channel c2 type disk;
allocate channel c3 type disk;
allocate AUXILIARY channel c4 type disk;
allocate AUXILIARY channel c5 type disk;
allocate AUXILIARY channel c6 type disk;
DUPLICATE TARGET DATABASE
  FOR STANDBY
  FROM ACTIVE DATABASE
  DORECOVER
  NOFILENAMECHECK;
release channel c1;
release channel c2;
release channel c3;
release channel c4;
release channel c5;
release channel c6;
}
```
**第三步：备库**

**在备库上更改参数文件**

```
[oracle@oracle-pc ~]$ gedit /home/oracle/app/oracle/product/12.1.0/dbhome_1/dbs/initorcl.ora

```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191126203852630.png)
替换文件内容

```
orcl.__data_transfer_cache_size=0
orcl.__db_cache_size=671088640
orcl.__java_pool_size=16777216
orcl.__large_pool_size=33554432
orcl.__oracle_base='/home/oracle/app/oracle'#ORACLE_BASE set from environment
orcl.__pga_aggregate_target=536870912
orcl.__sga_target=1258291200
orcl.__shared_io_pool_size=50331648
orcl.__shared_pool_size=301989888
orcl.__streams_pool_size=0
*._allow_resetlogs_corruption=TRUE
*._catalog_foreign_restore=FALSE
*.audit_file_dest='/home/oracle/app/oracle/admin/orcl/adump'
*.audit_trail='db'
*.compatible='12.1.0.2.0'
*.control_files='/home/oracle/app/oracle/oradata/orcl/control01.ctl','/home/oracle/app/oracle/fast_recovery_area/orcl/control02.ctl','/home/oracle/app/oracle/fast_recovery_area/orcl/control03.ctl'
*.db_block_size=8192
*.db_domain=''
*.db_file_name_convert='/home/oracle/app/oracle/oradata/orcl/','/home/oracle/app/oracle/oradata/stdorcl/'
*.db_name='orcl'
*.db_unique_name='stdorcl'
*.db_recovery_file_dest='/home/oracle/app/oracle/fast_recovery_area'
*.db_recovery_file_dest_size=4823449600
*.diagnostic_dest='/home/oracle/app/oracle'
*.dispatchers='(PROTOCOL=TCP)(dispatchers=1)(pool=on)(ticks=1)(connections=500)(sessions=1000)'
*.enable_pluggable_database=true
*.fal_client='stdorcl'
*.fal_server='orcl'
*.inmemory_max_populate_servers=2
*.inmemory_size=157286400
*.local_listener=''
*.log_archive_config='DG_CONFIG=(stdorcl,orcl)'
*.log_archive_dest_1='LOCATION=/home/oracle/arch VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=stdorcl'
*.log_archive_dest_2='SERVICE=orcl LGWR ASYNC  VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE) DB_UNIQUE_NAME=orcl'
*.log_archive_format='%t_%s_%r.arc'
*.log_file_name_convert='/home/oracle/app/oracle/oradata/orcl/','/home/oracle/app/oracle/oradata/stdorcl/'
*.max_dispatchers=5
*.max_shared_servers=20
*.open_cursors=400
*.parallel_execution_message_size=8192
*.pga_aggregate_target=511m
*.processes=300
*.recovery_parallelism=0
*.remote_login_passwordfile='EXCLUSIVE'
*.service_names='ORCL'
*.sga_max_size=1572864000
*.sga_target=1258291200
*.shared_server_sessions=200
*.standby_file_management='AUTO'
*.undo_tablespace='UNDOTBS1'

```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191126204018216.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)

**在备库增加静态监听**

```
gedit /home/oracle/app/oracle/product/12.1.0/dbhome_1/network/admin/listener.ora
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191126204120317.png)

```

SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (ORACLE_HOME = /home/oracle/app/oracle/product/12.1.0/db_1)
      (SID_NAME = orcl)
    )
  )
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191126204340779.png)

**重新启动,备库开启实时应用模式:：**

```
[oracle@oracle-pc ~]$ sqlplus / as sysdba

SQL*Plus: Release 12.1.0.2.0 Production on 星期二 11月 26 20:44:33 2019

Copyright (c) 1982, 2014, Oracle.  All rights reserved.


Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options


Session altered.

```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191126204756326.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)

```
shutdown immediate   /
startup              
alter database recover managed standby database disconnect; 
```
