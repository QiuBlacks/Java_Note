

刷题网址 ：https://www.nowcoder.com/ta/sql



## SQL1：查找最晚入职员工

### 1、题目描述

查找最晚入职员工的所有信息，为了减轻入门难度，目前所有的数据里员工入职的日期都不是同一天

```mysql
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));
```

### 2、代码

```mysql
#运行时间：17ms ,占用内存：4756k
select * from employees
    order by hire_date desc
    limit 1;
    
#运行时间：12ms  占用内存：3324k
SELECT s1.emp_no , s1.birth_date, s1.first_name,s1.last_name,s1.gender,s1.hire_date
FROM employees AS s1 WHERE s1.hire_date = (SELECT MAX(hire_date) FROM employees);    
```



## SQL2：查找倒数第三晚的入职员工

### 1、题目描述

在SQL1的基础，查找入职员工时间排名倒数第三的员工所有信息

### 2、代码

```mysql
select * from employees
    order by hire_date desc
    LIMIT 1 offset 2;
    
SELECT s1.emp_no , s1.birth_date, s1.first_name,s1.last_name,s1.gender,s1.hire_date
FROM employees AS s1 WHERE s1.hire_date = (SELECT MAX(hire_date) FROM employees);
```













