# **BufferedOutputStream—字节输出缓冲流**

# 一、构造方法

1.BufferedOutputStream(OutputStream out) 创建一个新的缓冲输出流，以将数据写入指定的基础输出流。 

2.BufferedOutputStream(OutputStream out, int size) 创建一个新的缓冲输出流，以将具有指定缓冲区大小的数据写入指定的基础输出流。

# 二、代码演示

```
public static void main(String[] args) {
        //1、创建源
        File file=new File("D:io1.txt");
        // 2、选择流
        OutputStream out=null;
        BufferedOutputStream buf=null;
        try {
            out=new FileOutputStream(file);
            buf=new BufferedOutputStream(out);

            String data="好好学习,天天向上";
            buf.write(data.getBytes(),0,data.getBytes().length);//写入缓冲区
            buf.flush();//刷新缓冲区，即把内容写入
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            //4、释放资源
            try {
                if(buf!=null) {
                    buf.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
```

