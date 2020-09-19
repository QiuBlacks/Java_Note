# 整合Spring和SpringMVC

## 一、在web.xml文件中配置监听器实现启动服务创建Spring容器

注意：配置 spring 提供的监听器，用于启动服务时加载容器 。

又因为该监听器只能加载 WEB-INF 目录中名称为 applicationContext.xml 的配置文件 ，所以要配置好applicationContext.xml的路径使之变成在/resources路径下

```xml
<listener>
      <listener-class>
        org.springframework.web.context.ContextLoaderListener
      </listener-class>
</listener>
<!-- 手动指定 spring 配置文件位置 -->
<context-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:applicationContext.xml</param-value>
</context-param>
```

