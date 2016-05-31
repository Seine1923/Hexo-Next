title: Java随机数
date: 2015-10-23 00:00:00
categories: Java
tags: [Java, Random]
description: java.lang.Math.Random和java.util.Random的随机函数都可以生成指定范围的不重复随机数，但从效率来讲，由于java.lang.Math.Random还需要浮点数乘法，以及强制类型转换，因此，显然java.util.Random效率更高。<br><br>而在java.util.Random中，一旦指定了同样的种子，那么无论什么时候运行，无论生成多长的随机数，都完全一致。通常按照默认指定种子为当前时间可避免这个问题，但是两个生成器之间尽量要有一定时间间隔，否则两者时间会一致，导致依然一样。
---

## 1. java.lang.Math.Random
### 1.1 基础
	
调用这个Math.Random()函数能够返回带正号的double值，该值大于等于0.0且小于1.0，即取值范围是[0.0,1.0)的左闭右开区间，返回值是一个伪随机选择的数，在该范围内（近似）均匀分布。

实验代码：
	
	for(int i = 0; i < 10; ++i){
		System.out.println(Math.random());
	}

实验结果：
	
	0.6672404351456087
	0.003421426855892973
	0.24443794443889943
	0.49547249792952275
	0.9262167456092021
	0.06891844395293878
	0.4625487756961171
	0.6038531957482409
	0.5725574475956846
	0.13679770063093388
	
### 1.2 扩展
		
从0－6中生成6个不重复的随机数

实验代码：

	ArrayList<Integer> randNum = new ArrayList<Integer>();
	while(randNum.size() < 6){
		int num = (int)(Math.random() * 6);
		if(!randNum.contains(num))
			randNum.add(num);
	}
	System.out.println("RandomNum:");
	for(int i = 0; i < 6; ++i){
		System.out.print(randNum.get(i) + "\t");
	}
			
实验结果：

	RandomNum:
	4	0	1	5	3	2

## 2. java.util.Random

### 2.1 基础

1. java.util.Random类中实现的随机算法是伪随机，也就是有规则的随机，所谓有规则的就是在给定种(seed)的区间内随机生成数字；
2. 相同种子数的Random对象，相同次数生成的随机数字是完全相同的；
3. Random类中各方法生成的随机数字都是均匀分布的，也就是说区间内部的数字生成的几率均等；
		
实验代码：
	
	System.out.println("r1:");
	Random r1 = new Random(1);
	for(int i = 0; i < 5; ++i){
		System.out.print(r1.nextInt(5) + "\t");
	}
	System.out.println("\nr2:");
	Random r2 = new Random();
	for(int i = 0; i < 5; ++i){
		System.out.print(r2.nextInt(5) + "\t");
	}
	System.out.println("\nr3:");
	Random r3 = new Random(1);
	for(int i = 0; i < 5; ++i){
		System.out.print(r3.nextInt(5) + "\t");
	}

实验结果，第一次运行：
	
	r1:
	0	3	2	3	4	
	r2:
	1	0	1	0	3	
	r3:
	0	3	2	3	4
	
实验结果，第二次运行：
	
	r1:
	0	3	2	3	4	
	r2:
	1	4	3	1	4	
	r3:
	0	3	2	3	4

### 2.2 扩展
	
从0－6中生成6个不重复的随机数

实验代码：

	ArrayList<Integer> randNumR1 = new ArrayList<Integer>();
	Random r1 = new Random(1);
	while(randNumR1.size() < 6){
		int num = r1.nextInt(6);
		if(!randNumR1.contains(num))
			randNumR1.add(num);
	}
	System.out.println("RandomNum R1:");
	for(int i = 0; i < 6; ++i){
		System.out.print(randNumR1.get(i) + "\t");
	}
	
	ArrayList<Integer> randNumR2 = new ArrayList<Integer>();
	Random r2 = new Random();
	while(randNumR2.size() < 6){
		int num = r2.nextInt(6);
		if(!randNumR2.contains(num))
			randNumR2.add(num);
	}
	System.out.println("\nRandomNum R2:");
	for(int i = 0; i < 6; ++i){
		System.out.print(randNumR2.get(i) + "\t");
	}
	
	ArrayList<Integer> randNumR3 = new ArrayList<Integer>();
	Random r3 = new Random();
	while(randNumR3.size() < 6){
		int num = r3.nextInt(6);
		if(!randNumR3.contains(num))
			randNumR3.add(num);
	}
	System.out.println("\nRandomNum R3:");
	for(int i = 0; i < 6; ++i){
		System.out.print(randNumR3.get(i) + "\t");
	}

实验结果，第一次运行：

	RandomNum R1:
	3	4	1	2	0	5	
	RandomNum R2:
	1	2	4	3	0	5	
	RandomNum R3:
	3	4	1	0	5	2

实验结果，第二次运行：
		
	RandomNum R1:
	3	4	1	2	0	5	
	RandomNum R2:
	1	5	3	0	4	2	
	RandomNum R3:
	4	1	5	3	2	0

## 3. 总结

1. 两者都可以生成指定范围的不重复随机数，但从效率来讲，由于java.lang.Math.Random还需要浮点数乘法，以及强制类型转换，因此，显然java.util.Random效率更高。

2. 在java.util.Random中，一旦指定了同样的种子，那么无论什么时候运行，无论生成多长的随机数，都完全一致。通常按照默认指定种子为当前时间可避免这个问题，但是两个生成器之间尽量要有一定时间间隔，否则两者时间会一致，导致依然一样。

	
