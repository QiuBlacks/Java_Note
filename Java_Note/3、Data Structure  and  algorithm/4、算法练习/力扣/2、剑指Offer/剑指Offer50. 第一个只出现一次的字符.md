## 剑指Offer50. 第一个只出现一次的字符



## 一、题目描述

在字符串 s 中找出第一个只出现一次的字符。如果没有，返回一个单空格。 s 只包含小写字母



## 二、解题方法

### 1、自制哈希表

```Java
   public static char firstUniqChar(String s) {
        char[] c=s.toCharArray();
        HashMap<Character,Integer> map=new HashMap<>();
        for(int i=0;i<s.length();i++){
            if(!map.containsKey(c[i])) {
                map.put(c[i],1);  
            }else{
                map.put(c[i],map.get(c[i])+1); //直接使用map.put  c[i]会空指针异常
            }
        }
        for(int i=0;i<s.length();i++){
            if(map.get(c[i])==1) return c[i];
        }

        return ' ';
    }
```

### 2、大佬哈希表

```Java
class Solution {
    public char firstUniqChar(String s) {
        HashMap<Character, Boolean> dic = new HashMap<>();
        char[] sc = s.toCharArray();
        for(char c : sc)
            dic.put(c, !dic.containsKey(c)); //使用boolean值代替
        for(char c : sc)
            if(dic.get(c)) return c;
        return ' ';
    }
}
```

### 3、大佬有序哈希表：LinkedHashMap

```Java
class Solution {
    public char firstUniqChar(String s) {
        Map<Character, Boolean> dic = new LinkedHashMap<>();
        char[] sc = s.toCharArray();
        for(char c : sc)
            dic.put(c, !dic.containsKey(c));
        for(Map.Entry<Character, Boolean> d : dic.entrySet()){
           if(d.getValue()) return d.getKey();
        }
        return ' ';
    }
}
```











