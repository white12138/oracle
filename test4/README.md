PART1
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191030192715596.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)
PART2

![在这里插入图片描述](https://img-blog.csdnimg.cn/2019103019272858.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)
PART3 手工增加的一个表空间WHITE
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191030192739523.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)
PART4
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191030192746156.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)
PART5
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191030192753499.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)
PART6

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191030192812872.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)
PART7
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019103019282092.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)
PART8
--批量插入订单数据之前，禁用触发器![在这里插入图片描述](https://img-blog.csdnimg.cn/20191030192858533.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)

PART9--创建3个触发器
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191030192919419.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)

PART10
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191030192940215.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)
PART11

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191030192945802.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)

PART12--查询一个分区中的数据
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191030192951774.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)

PART13 --查看表空间的使用情况
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191030193000566.png)

PART14

```
select *FROM employees where employee_id='112';
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191030220154671.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)
PART15

```
WITH A (EMPLOYEE_ID,NAME,EMAIL,PHONE_NUMBER,HIRE_DATE,SALARY,MANAGER_ID,DEPARTMENT_ID) AS
  (SELECT EMPLOYEE_ID,NAME,EMAIL,PHONE_NUMBER,HIRE_DATE,SALARY,MANAGER_ID,DEPARTMENT_ID
    FROM employees WHERE employee_ID = 11
    UNION ALL
  SELECT B.EMPLOYEE_ID,B.NAME,B.EMAIL,B.PHONE_NUMBER,B.HIRE_DATE,B.SALARY,B.MANAGER_ID,B.DEPARTMENT_ID
    FROM A, employees B WHERE A.EMPLOYEE_ID = B.MANAGER_ID)
SELECT * FROM A;

```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191030220229241.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)
PART16

```
select o.customer_name,o.customer_tel, p.product_type AS 产品类型
FROM orders o,order_details d,products p
where o.order_id=d.order_id
and d.product_name=p.product_name;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191030220256763.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)



PART17

```
select * 
from orders
where order_id NOT in(SELECT o.order_id from orders o,order_details d WHERE o.order_id=d.order_id);

```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191030220501993.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)

PAET18

```
select d.*,e.name
from departments d,employees e
where d.department_id=e.department_id
and e.manager_id=d.department_id;
```



![在这里插入图片描述](https://img-blog.csdnimg.cn/20191030220559792.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)

PART19

```
select de.department_name,SUM(esum)
FROM (
select (d.product_num*d.product_price) esum
from order_details d,orders o,departments d,employees e
WHERE d.department_id=e.department_id
and o.employee_id = e.employee_id
and o.order_id=d.order_id
),departments de
group by de.department_name

```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191030220650241.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)
