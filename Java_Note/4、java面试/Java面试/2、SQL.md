# SQL

---

## 一、什么是事务,为何用事务
保证数据的一致性和完整性



## 二、sql语句，怎样查找不重复的字段

### 1、单字段使用Distinct关键字
  SELECT DISTINCT [字段名] FROM table

注意：这里DISTINCT后面只能加一个字段名

###  2、多字段使用GROUP BY 分组