# springmvc注意点

## 1、解决中文乱码问题

配置filter时要注意位置，不然会报错，没事，可以正常运行

如果依旧中文乱码，则修改tomcat的  添加：      -Dfile.encoding=UTF-8

过滤器filter的路径要加/ *   ，如果是 / 的话他不会过滤jsp文件