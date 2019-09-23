查询语句1
```
set autotrace on

SELECT d.department_name,count(e.job_id)as "部门总人数"，
avg(e.salary)as "平均工资"
from hr.departments d,hr.employees e
where d.department_id = e.department_id
and d.department_name in ('IT','Sales')
GROUP BY d.department_name;
```
Oracle优化指导
查询结果 
通过创建一个或多个索引可以改进此语句的执行计划。
建议
考虑运行可以改进物理方案设计的访问指导或创建推荐的索引
原理
创建推荐的索引可以显著地改进此语句的执行计划。 但是，使用典型的SQL工作量运行“访问指导”可能比单个语句更可取。通过这种方法可以获得全面的索引建议案，包括计算索引维护的开销和附加空间的消耗。![在这里插入图片描述](https://img-blog.csdnimg.cn/20190923110134634.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)

查询语句2

```
set autotrace on

SELECT d.department_name,count(e.job_id)as "部门总人数",
avg(e.salary)as "平均工资"
FROM hr.departments d,hr.employees e
WHERE d.department_id = e.department_id
GROUP BY d.department_name
HAVING d.department_name in ('IT','Sales');
```
查询语句
无
建议
无
原理
无
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190923110340122.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAwNTEzMg==,size_16,color_FFFFFF,t_70)
