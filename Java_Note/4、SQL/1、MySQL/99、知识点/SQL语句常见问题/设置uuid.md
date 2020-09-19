# mysql创建表时设置uuid

# 一、问题

oracel创建uuid为主键的表时可以直接指定default uuid();但是mysql不支持，可以通过触发器实现。

# 二、解决方法

## 1、sql语句直接创建触发器

### 1）格式

```sql
create 触发器名称
before/after(触发的时间)  insert/update/delete(触发事件)  on table(触发的表名)
for each ROW
BEGIN
	SET new.id=UUID();
END
```

### 2）示例

```sql
CREATE TRIGGER `product_before_insert` BEFORE INSERT ON `product` FOR EACH ROW 
BEGIN
IF new.id is NULL THEN
		SET new.id = UUID();
END IF; 
END;
```

### 3）注意事项

- sql语句结尾没加分号 ；（end if 和end后面）
2. 触发器名称和表名称没加 ``
- 设置id长度为32时,会报错 输入too long          要大于32      

## 2、使用navicat可视化界面对已创建的表添加触发器