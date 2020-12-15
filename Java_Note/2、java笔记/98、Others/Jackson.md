# Jackson

# 一、基础知识

## 1、基本介绍

Jackson可以轻松的将Java对象转换成json对象和xml文档，同样也可以将json、xml转换成Java对象

 **ObjectMapper**类是Jackson库的主要类。它称为ObjectMapper的原因是因为它将JSON映射到Java对象（反序列化），或将Java对象映射到JSON（序列化）。它使用JsonParser和JsonGenerator的实例实现JSON实际的读/写。







# 二、转Java对象



# 三、转Json

ObjectMapper write有三个方法

- writeValue()
- writeValueAsString()
- writeValueAsBytes()

