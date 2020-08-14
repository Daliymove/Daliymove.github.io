---
title: Finally之未解之谜
top: 
categories: 面试
tags:
- java
- finally
date: 2020-07-08 11:20:23 
top_img: https://cdn.jsdelivr.net/gh/daliymove/tuchuang/img/019.jpg
cover: https://cdn.jsdelivr.net/gh/daliymove/tuchuang/img/019.jpg
description: "Finally之面试相关"
toc_number:
auto_open:
copyright:
mathjax: 
katex:
aplayer:
highlight_shrink: false
---

# Finally 关键字

---

## Finally 是什么？
有一些代码，可能会希望try块中的异常无论是否抛出，都需要执行某一些语句，这个时候，我们就用到了finally;

```
try块语法：
try{

}catch(异常类 形参){

}finally{

}

try块 catch和finally可任选一个，一般两个都写，不可以两个都不写；
```

## Finally常见问题
### 1. try语句中有异常，finally 语句块会执行吗？

这个答案是肯定的。finally语句的优先级等级很高，只要不涉及一些关键字眼，如**JVM异常关闭**,**系统异常崩溃**等，finally中的语句总是会执行的，不管是否有异常抛出。

### 2. finally 与 continue,break之间的关系
```java
continue与break 代表着跳出本次循环、结束本次循环，finally遇上这两个代码会怎么样呢？

请看以下代码：

public class FinallyLearn {

public static void main(String[] args) {

    try {
        for (int i = 0; i < 5; i++) {
            System.out.println(i);
            if (i == 3) {
                break;//continue也一样
            }
        }
    }catch(Exception e) {
        System.out.println("有异常，请检查");
    }finally {
        System.out.println("一定会执行");
        }

    }
}

输出：
0
1
2
3
一定会执行

可以看到即使跳出了循环，finally语句中的内容依然会执行
```

### 3. finally与return的关系

```java
return的执行逻辑：

    1. 复制一份return后的表达式至操作数栈顶，计算表达式的值，将表达式的值保存至操作数栈顶
    2. 检查后续代码有无finally
    3. 若有，则优先执行finally内的代码，再返回操作数栈顶的值
    4. 若没有，则直接返回操作数栈顶的值


观察下面代码：

public class FinallyLearn {

public static void main(String[] args){

	    System.out.println(say());

}

public static int say() {

        int a =0;
        try {
            a = 10;
            return a;
        }finally {
            a = 20;
        }

	}
}

想一想输出的值是什么？
```

![思考](https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=2328241580,270776270&fm=26&gp=0.jpg)
```




答案为：10

通过上面的4步，很容易判断，操作数栈顶的保存的值为10，虽然后面通过finally中的语句使a的值变为了20，但return返回的值仍是10；
```
看看网上的一个面试题：

![mianshi.png](https://i.loli.net/2020/06/26/BQDrToY9O5mX8q4.png)

```
想一想答案是什么？

下面我们通过画图来演示一下
```
![tupian](https://i.loli.net/2020/06/26/BXoIRxgl6yrwpKE.png)

```
所以第一行打印Test1()的时候，先打印try语句中的'A',然后将'A'保存至操作数栈，后面打印finally语句中的'B',将Label的值变为'B',Test1()的返回值为'A',Label的值为'B';

输出的值为： A***;
```
**升华一下**
```java
我们来变一下，倘若在finally后面加上return语句，结果会不会不一样呢？

代码如下：
public class ReturnLearn {
	
	static char Label;

	public static void main(String[] args){
		System.out.println(say());
		System.out.println(Label);
	}

	public static char say() {
		try {
			System.out.println('A');
			return Label = 'A';
		}
		finally {
			System.out.println('B');
			return Label = 'B';
		}
	}
}

这下返回的结果会是什么样呢？
```
![a](https://tse3-mm.cn.bing.net/th/id/OIP.hoS8C8Tvn6ahI_XKFt2NwwHaHV?pid=Api&rs=1)

```
对没错,输出的就是A***

解释如下：

第一个return,是将'A'储存进了操作数栈顶，然后在执行finally语句的时候，又一次的将'B'保存在了操作数栈顶，所以最终返回值跟Label的值都为'B';
```
**再升华一下**
```java
大家是不是对于finally与return的关系掌握得更具体了呢，再升华一点，如果在try 块后面加上return 'A';try 块内添加catch(Expection e)语句，结果会怎么样？

代码如下：
public class ReturnLearn {
	
	static char Label;

	public static void main(String[] args){
		System.out.println(say());
		System.out.println(Label);
	}

	public static char say() {
		try {
			System.out.println('A');
			return Label = 'A';
		}catch(Exception e) {
		}
		finally {
			System.out.println('B');
			Label = 'B';
		}
		return 'C';
	}
}

想一想结果会怎样？

大脑一片空白··········



















emmm  问师傅 我也不知道！


此处省略一万字。。。。
```



![aa.jpeg](https://i.loli.net/2020/06/26/VtdEGlsar4be2WU.jpg)


## 参考文献
https://blog.csdn.net/sinat_22594643/article/details/80509266
https://blog.csdn.net/WYpersist/article/details/80710352
https://www.jianshu.com/p/144a4496575a

仅仅代表一些很基础的个人观点，希望大家能积极指出不足，我一定好好修改的，希望我能跟小伙伴们一起进步！