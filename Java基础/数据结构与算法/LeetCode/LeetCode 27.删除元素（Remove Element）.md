
> [LeetCode基础算法题第136篇：删除数组中值为val的所有元素](https://www.toutiao.com/i6703404701917905421/?tt_from=weixin&utm_campaign=client_share&wxshare_count=1&timestamp=1562650960&app=news_article&utm_source=weixin&utm_medium=toutiao_ios&req_id=201907091342400100230621495779515&group_id=6703404701917905421)

LeetCode 27. 删除元素（Remove Element）

### 问题描述：

给定一个数组nums和一个数值val，直接删除nums中所有值等于val的元素，然后返回nums新的长度。要求，不额外分配空间，空间复杂度要满足O(1)。对元素的顺序没有要求，顺序可以更改。不关心超过nums新长度以外的内容。

### 示例

```java

 Given nums = [3,2,2,3]

 Your solution should return length = 2


```

```java
public class RemoveElement {

	public static int operate(int[] nums,int val){
		int i = 0;
		for (int j = 0; j < nums.length; j++) {
			if(nums[j] != val){
				nums[i++] = nums[j];
			}
		}
		return i;
	}

	public static void main(String[] args) {
		int[] arr = new int[]{3,2,2,3};
		int operate = RemoveElement.operate(arr, 3);
		System.out.println(operate);
	}
}
```
