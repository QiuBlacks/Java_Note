# 序列化（Serialization）

# 一、基本介绍
## 1、背景介绍
Java平台允许我们在内存中创建可复用的Java对象，但一般情况下，只有当JVM处于运行时，这些对象才可能存在，即，这些对象的生命周期不会比JVM的生命周期更长。但在现实应用中，就可能要求在JVM停止运行之后能够保存(持久化)指定的对象，并在将来重新读取被保存的对象。Java对象序列化就能够帮助我们实现该功能。
## 2、基本概念
- Serialization（序列化）是一种将一个对象表示为一个字节序列的过程；
- deserialization(反序列化)是一种将这些字节序列重建成一个对象的过程。

 &emsp;&emsp;该字节序列包括该对象的数据、有关对象的类型的信息和存储在对象中数据的类型。<br>
 &emsp;&emsp;如果去掉继承Serialization会报：==java.io.NotSerializableException==



## 3、注意：

- 对象序列化保存的是对象的”状态”，即它的成员变量。由此可知，对象序列化==不会保存类中的静态变量==。

- 整个过程都是 Java 虚拟机（JVM）独立的，也就是说，在一个平台上序列化的对象可以在另一个完全不同的平台上反序列化该对象。

- 要想将父类对象也序列化，就需要让父类也实现Serializable 接口。

- ==Transient== 关键字的作用是控制变量的序列化，在变量声明前加上该关键字，可以==阻止该变量被序列化==到文件中，在被反序列化后，transient 变量的值被设为初始值，如 int 型的是 0，对象型的是 null。

- 服务器端给客户端发送序列化对象数据，对象中有一些数据是敏感的，比如密码字符串等，希望对该密码字段在序列化时，进行加密，而客户端如果拥有解密的密钥，只有在客户端进行反序列化时，才可以对密码进行读取，这样可以一定程度保证序列化对象的数据安全

  

## 4、使用场景
- 当你想把的内存中的对象保存到一个文件中或者数据库中时候(数据持久化)；
- 利用序列化实现远程通信，即在网络上传送对象的字节序列；




# 二、实现序列化和反序列化
##   1、实现方法
- 1）使一个类实现**java.io.Serializable**接口，然后通过==ObjectOutputStream==和==ObjectInputStream==对对象进行序列化及反序列化<br>

- 2）实现Externalizable接口


## 2、Serializable实现例子
&emsp;&emsp;定义一个序列化类
```java
public class User1 implements Serializable {

    private String name;
    private int age;

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}

```
进行序列化和反序列化

```java
public class SerializableDemo1 {

    public static void main(String[] args) {
        //Initializes The Object
        User1 user = new User1();
        user.setName("hollis");
        user.setAge(23);
        System.out.println(user);

        //Write Obj to File序列化
        ObjectOutputStream oos = null;
        try {
            oos = new ObjectOutputStream(new FileOutputStream("tempFile"));
            oos.writeObject(user);
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            IOUtils.closeQuietly(oos);
        }

        //Read Obj from File反序列化
        File file = new File("tempFile");
        ObjectInputStream ois = null;
        try {
            ois = new ObjectInputStream(new FileInputStream(file));
            User1 newUser = (User1) ois.readObject();
            System.out.println(newUser);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } finally {
            IOUtils.closeQuietly(ois);
            try {
                FileUtils.forceDelete(file);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

    }
}

//OutPut:
//User{name='hollis', age=23}
//User{name='hollis', age=23}

```



# 三、自定义序列化

## 1、实现方式
&emsp;&emsp;通过在被序列化的类中增加==writeObject（） 和 readObject==方法

> 在序列化过程中，如果被序列化的类中定义了writeObject 和 readObject 方法，虚拟机会试图调用对象类里的 writeObject 和 readObject 方法，进行用户自定义的序列化和反序列化。
>
> 如果没有这样的方法，则默认调用是 ObjectOutputStream 的 defaultWriteObject 方法以及 ObjectInputStream 的 defaultReadObject 方法。
> 
> 用户自定义的 writeObject 和 readObject 方法可以允许用户控制序列化的过程，比如可以在序列化的过程中动态改变序列化的数值。
## 2、ArrayList的序列化



# 四、Serializable和Externalizable的区别
&emsp;&emsp;Externalizable继承了Serializable，该接口中定义了两个抽象方法：writeExternal()与readExternal()。当使用Externalizable接口来进行序列化与反序列化的时候需要开发人员==重写writeExternal()与readExternal()方法==。由于上面的代码中，并没有在这两个方法中定义序列化实现细节，所以输出的内容为空。

&emsp;&emsp;还有一点值得注意：在使用Externalizable进行序列化的时候，在读取对象时，会调用被序列化类的==无参构造器==去创建一个新的对象，然后再将被保存对象的字段的值分别填充到新对象中。所以，实现Externalizable接口的类必须要提供一个public的无参的构造器。

&emsp;&emsp;如果Externalizable类中没有无参数的构造函数，在运行时会抛出异常：==java.io.InvalidClassException==

---



## 参考文献：

http://www.hollischuang.com/archives/1140#What%20Serializable%20Did<br>

http://hollischuang.gitee.io/tobetopjavaer/#/basics/java-basic/serialize-in-java