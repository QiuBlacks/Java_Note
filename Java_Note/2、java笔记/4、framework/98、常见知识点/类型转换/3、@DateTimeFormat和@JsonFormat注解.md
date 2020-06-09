# **@DateTimeFormat和@JsonFormat注解**

## **@DateTimeFormat：前端转后台**

## **@JsonFormat注解:后台转前端**

```
@DateTimeFormat(pattern = "yyyy-MM-dd")
@JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss",timezone="GMT+8")
private Date symstarttime;
```

