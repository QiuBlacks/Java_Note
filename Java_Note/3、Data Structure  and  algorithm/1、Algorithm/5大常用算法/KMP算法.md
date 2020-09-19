# KMP算法



## 一、介绍

KMP算法，又称作“看猫片”算法（误），是一种改进的字符串模式匹配算法，可以在O(n+m)的时间复杂度以内完成字符串的匹配操作，其核心思想在于：当一趟匹配过程中出现字符不匹配时，不需要回溯主串的指针，而是利用已经得到的“部分匹配”，将模式串尽可能多地向右“滑动”一段距离，然后继续比较。

## 二、最佳应用--字符串匹配问题

 

1) 有一个字符串 str1= "BBCABCDABABCDABCDABDE"，和一个子串 str2="ABCDABD"

 

2) 现在要判断 str1 是否含有 str2, 如果存在，就返回第一次出现的位置, 如果没有，则返回-1

 

## 三、思路分析图解

首先理解几个概念：

1、前缀，后缀

![image-20200513161538523](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200910181818.png)

“部分匹配值”就是”前缀”和”后缀”的最长的共有元素的长度。以”ABCDABD”为例，

－”A”的前缀和后缀都为空集，共有元素的长度为0；

－”AB”的前缀为[A]，后缀为[B]，共有元素的长度为0；

－”ABC”的前缀为[A, AB]，后缀为[BC, C]，共有元素的长度0；

－”ABCD”的前缀为[A, AB, ABC]，后缀为[BCD, CD, D]，共有元素的长度为0；

－”ABCDA”的前缀为[A, AB, ABC, ABCD]，后缀为[BCDA, CDA, DA, A]，共有元素为”A”，长度为1；

－”ABCDAB”的前缀为[A, AB, ABC, ABCD, ABCDA]，后缀为[BCDAB, CDAB, DAB, AB, B]，共有元素为”AB”，长度为2；

－”ABCDABD”的前缀为[A, AB, ABC, ABCD, ABCDA, ABCDAB]，后缀为[BCDABD, CDABD, DABD, ABD, BD, D]，共有元素的长度为0。

 

2、”部分匹配”

实质是，有时候，字符串头部和尾部会有重复。比如，”ABCDAB”之中有两个”AB”，那么它的”部分匹配值”就是2（”AB”的长度）。搜索词移动的时候，第一个”AB”向后移动 4 位（字符串长度-部分匹配值），就可以来到第二个”AB”的位置。

![image-20200513161551831](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200910181819.png)

3、思路分析

举例来说，有一个字符串 Str1 = “BBC ABCDAB ABCDABCDABDE”，判断，里面是否包含另一个字符串 Str2 = “ABCDABD”？

![image-20200513161617706](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200910181820.png)







## 四、代码实现

### 1、一维数组

参考：[有道云笔记](https://note.youdao.com/web/#/file/WEB8a52b35a855bfb1992e64962881a9eec/note/WEBc7670bd418995725c2a3af28d1ad88a7/)

```
	  @param str1 源字符串
	  @param str2 子串
	  @param next 部分匹配表, 是子串对应的部分匹配表
	  @return 如果是-1就是没有匹配到，否则返回第一个匹配的位置
	 /
	public static int kmpSearch(String str1, String str2, int[] next) {
		
		//遍历 
		for(int i = 0, j = 0; i < str1.length(); i++) {
			
			//需要处理 str1.charAt(i) ！= str2.charAt(j), 去调整j的大小
			//KMP算法核心点
                    //直接返回到共有元素后面，判断之后的字符串是否相等，即第11步
			while( j > 0 && str1.charAt(i) != str2.charAt(j)) {
				j = next[j-1];  //返回到共有元素部分
			}
			
			if(str1.charAt(i) == str2.charAt(j)) {
				j++;
			}			
			if(j == str2.length()) {//找到了 // j = 3 i 
				return i - j + 1;
			}
		}
		return  -1;
	}
```

部分匹配表算法

```
	//获取到一个字符串(子串) 的部分匹配值表
	public static  int[] kmpNext(String dest) {
		//创建一个next 数组保存部分匹配值
		int[] next = new int[dest.length()];
		next[0] = 0; //如果字符串是长度为1 部分匹配值就是0
		for(int i = 1, j = 0; i < dest.length(); i++) {
			//当dest.charAt(i) != dest.charAt(j) ，我们需要从next[j-1]获取新的j
			//直到我们发现 有  dest.charAt(i) == dest.charAt(j)成立才退出
			//这时kmp算法的核心点
			while(j > 0 && dest.charAt(i) != dest.charAt(j)) {
				j = next[j-1];
			}
			
			//当dest.charAt(i) == dest.charAt(j) 满足时，部分匹配值就是+1
			if(dest.charAt(i) == dest.charAt(j)) {
				j++;
			}
			next[i] = j;
		}
		return next;
	}
```

###  2、二维数组

参考：[动态规划KMP](https://mp.weixin.qq.com/s?__biz=Mzg2NzA4MTkxNQ==&mid=2247485979&idx=2&sn=56d4d0dd11951c29c9e6f94803d92e03&scene=21#wechat_redirect)

```
public class KMP {
    private int[][] dp;
    private String pat;

    public KMP(String pat) {
        this.pat = pat;
        int M = pat.length();
        // dp[状态][字符] = 下个状态
        dp = new int[M][256];
        // base case
        dp[0][pat.charAt(0)] = 1;
        // 影子状态 X 初始为 0
        int X = 0;
        // 构建状态转移图（稍改的更紧凑了）
        for (int j = 1; j < M; j++) {
            for (int c = 0; c < 256; c++) {
                dp[j][c] = dp[X][c];
            dp[j][pat.charAt(j)] = j + 1;
            // 更新影子状态
            X = dp[X][pat.charAt(j)];
        }
    }

    public int search(String txt) {
        int M = pat.length();
        int N = txt.length();
        // pat 的初始态为 0
        int j = 0;
        for (int i = 0; i < N; i++) {
            // 计算 pat 的下一个状态
            j = dp[j][txt.charAt(i)];
            // 到达终止态，返回结果
            if (j == M) return i - M + 1;
        }
        // 没到达终止态，匹配失败
        return -1;
    }
}
```

