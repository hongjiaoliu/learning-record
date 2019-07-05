
> [LeetCode基础算法题第141篇：爬楼梯问题](https://www.toutiao.com/i6709313633471431176/?tt_from=weixin&utm_campaign=client_share&wxshare_count=1&timestamp=1562313707&app=news_article&utm_source=weixin&utm_medium=toutiao_ios&req_id=201907051601460100210430257909AFE&group_id=6709313633471431176)

LeetCode 70. 爬楼梯（Climbing Stairs）

### 问题描述

正在爬楼梯。它需要走n个台阶才能达到终点。每次你可以迈上1个台阶或2个台阶。您可以有多少种不同的方式到达终点？

注:

+ 给定n将是一个正整数。

### 示例

1. Example a :

Input 2
Output 2
```
 + 1 step + 1 step
 + 2 steps
```

2. Example b :

Input 3
Output 3
```
  + 1 step + 1 step + 1 step
  + 1 step + 2 step
  + 2 step + 1 step
```


### 思路

 + 若我们先到达第x-1台阶我们只需再迈1个台阶就到达第x台阶；如果以这种方式，所有到达x层的方式就等于所有到达x-1层的方式。

 + 若我们先到达第x-2台阶我们只需再一次迈2个台阶就到达第x台阶；如果以这种方式，所有到达第x台阶的方式就等于所有到达第x-2台阶的方式。

此外我们还要考虑到n的取值。

由于n值是正整数，且当n=1是，结果是1，所以可以转化为求Fib(n+1)的值的问题。

**递归的方案有可能会导致超时**

```java
/**
 * LeetCode 70 爬楼梯问题
 *
 * @author liuhongjao
 * @date 2019/7/5 16:34
 */

public class ClimbingStairs {

	public static int solutionWithArray(int n){
		if(n == 1){
			return 1;
		}
		if(n == 2){
			return 2;
		}
		int[] result= new int[n];
		result[0] = 1;
		result[1] = 2;
		for (int i = 2; i < n; i++) {
			result[i] = result[i-1] + result[i-2];
		}
		return result[n-1];
	}

	public static void main(String[] args) {
		System.out.println(ClimbingStairs.solutionWithArray(6));
	}
}
````
