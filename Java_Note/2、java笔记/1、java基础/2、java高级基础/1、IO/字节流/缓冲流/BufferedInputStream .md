# BufferedInputStream 字节输入缓冲流

## 一、构造方法

1、BufferedInputStream(InputStream in) 创建 BufferedInputStream 并保存其参数，即输入流 in，以便将来使用。 

2、BufferedInputStream(InputStream in, int size) 创建具有指定缓冲区大小的BufferedInputStream，并保存其参数，即输入流 in，以便将来使用。

## 二、代码演示

```
public static void main(String[] args) {
             //1、创建源
            File file=new File("D:io1.txt");
            // 2、选择流
            InputStream in=null;
            BufferedInputStream buf=null;
          
        try {
            in=new FileInputStream(file);
            buf=new BufferedInputStream(in);

            byte[]bytes=new byte[1024];
            //数据读取
            int len=-1;
            StringBuffer  sb=new StringBuffer();
            while((len=buf.read(bytes))!=-1)
            {
                sb.append(new String(bytes,0,len));
            }
            System.out.println("内容为："+sb);

        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            //4、释放资源
            try {
                if(null!=in) {
                    buf.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```

