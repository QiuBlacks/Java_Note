# JVM工作流程

---

## 定义一个类
```
public class Math {

    public static final Integer  constant=666;

    public  int compute(){
            int a=1;
            int b=2;
            int c=(a+b)*10;
            return c;
    }

    public static void main(String[] args) {
        Math math=new Math();
        math.compute();
    }
}
```
## JVM工作流程图：

![image](WEBRESOURCEc656f419404457663b0f778261577277)


可参考其字节码文件
查看Java类字节码文件   找到某个类的输出文件test.class，.打开终端，javap -c test.class

```
Compiled from "Math.java"
public class Math {
  public static final java.lang.Integer constant;

  public Math();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public int compute();
    Code:
       0: iconst_1         //常量1进入操作数栈
       1: istore_1         //赋值1给局部变量a
       2: iconst_2
       3: istore_2
       4: iload_1          //
       5: iload_2
       6: iadd
       7: bipush        10
       9: imul
      10: istore_3
      11: iload_3
      12: ireturn

  public static void main(java.lang.String[]);
    Code:
       0: new           #2                  // class Math
       3: dup
       4: invokespecial #3                  // Method "<init>":()V
       7: astore_1
       8: aload_1
       9: invokevirtual #4                  // Method compute:()I
      12: pop
      13: return

  static {};
    Code:
       0: sipush        666
       3: invokestatic  #5                  // Method java/lang/Integer.valueOf:(I)Ljava/lang/Integer;
       6: putstatic     #6                  // Field constant:Ljava/lang/Integer;
       9: return
}
```
