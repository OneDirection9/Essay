### Content
1. [树状数组介绍](#树状数组介绍)
2. 树状数组应用
    1. [改点求段](#改点求段)
    2. [改段求点](#改段求点)
    3. [改段求段](#改段求段)

# 引言

本文主要介绍[树状数组](http://www.cnblogs.com/xudong-bupt/p/3484080.html)以及[树状数组的应用](http://www.cnblogs.com/zichi/p/4807072.html)。

> 树状数组的下标可以从任意一个数字开始。

# 树状数组介绍

## 核心思想

1. 树状数组中的每个元素是原数组中的一个或者多个连续元素的和。
2. 在进行连续求和操作 `a[1] + …… + a[n]` 时，只需要将数组中某几个元素求和即可。时间复杂度是**O(logn)**。
3. 在修改某个元素 `a[i]` 时，只需要修改树状数组中与之相关的某几个元素即可。时间复杂度是**O(logn)**。

下面是树状数组的示意图：

![Binary_Indexed_Tree](https://github.com/OneDirection9/Essay/blob/master/MarkdownImages/Binary_Indexed_Tree.png?raw=true)

## 详细解释

1. `a[]`: **保存原始数据的数组**。对该数组的操作有：1) 求其中连续多个数的和； 2) 任意修改其中一个元素。  
    `e[]`: **树状数组**，其中的任意一个元素 `e[i]` 可能是一个或者多个 `a` 数组中元素的和。
2. e[i]是a数组中几个元素的和？如果数字i的二进制表示中末尾有k个0，则e[i]是数组中2^k个元素的和。*(注意e数组的下标从1开始。)*
3. **后继**：可以理解为节点的父节点。**是离它最近的，且编号末尾连续0比它多的就是它的父亲**，如e[2]是e[1]的后继，e[4]是e[2]的后继。   

    后继主要用来计算e数组，将当前已经计算出的e[i]添加到他们后继中。  

    **前驱**：节点前驱的编号即为比自己小的，最近的，末尾连续0比自己多的节点。如e[7]的前驱是e[6]，e[6]的前驱是e[4]。
    
    前驱的主要作用是求解连续和时，避免重复添加元素。
    
    > 如 Sum(7) = a[1] + …… + a[7] = e[7] + e[6] + e[4]。

# 树状数组的应用

## 改点求段

对含有n个元素的数组，有下列两种操作
1. 求出第i个到第j个元素的和；
2. 修改其中某个元素的值。

**分析**：如果使用正常的解法，求和的时间复杂度是O(n)，修改值的时间复杂度是O(1)；如果使用数组累积和的方法，求和的时间复杂度是O(1)，修改值的时间复杂度是O(n)。

这里我们使用树状数组，平均时间复杂度是O(nlogn)。

以 [Leetcode 307.Range Sum Query - Mutable](https://leetcode.com/problems/range-sum-query-mutable/#/description) 为例写C++代码。

```cpp
#include <iostream>
#include <vector>
using namespace std;
/*
	Description:
		Given an integer array nums, find the sum of the elements between indices i and j (i ≤ j), inclusive.
		The update(i, val) function modifies nums by updating the element at index i to val.
*/
class NumArray {
public:
	NumArray(vector<int> nums) {
		initial = nums;
		tree.resize(nums.size() + 1, 0);

		for (int i = 1; i <= nums.size(); ++i) {
			tree[i] += nums[i - 1];

			int lowbit = i & (-i); // 计算二进制后面0的个数的，2次幂
			int post = i + lowbit; // 计算后继
			if (post <= nums.size()) {
				tree[post] += tree[i];
			}
		}
	}

	void update(int i, int val) {
		int dis = val - initial[i]; 
		initial[i] = val;

		i += 1; // 原数组的下标 和 tree 的下标差一个1
		while (i <= initial.size()) {
			tree[i] += dis;
			int lowbit = i & (-i); // 计算二进制后面0的个数的，2次幂
			i += lowbit;
		}

	}

	int getSum(int i) {
		i += 1;
		int sum(0);

		while (i > 0) {
			sum += tree[i];
			int lowbit = i & (-i);
			i -= lowbit; // 计算前驱
		}
		return sum;
	}

	int sumRange(int i, int j) {
		if (i == 0) {
			return getSum(j);
		}
		return getSum(j) - getSum(i - 1);
	}
private:
	vector<int> initial;
	vector<int> tree;
};
/**
* Your NumArray object will be instantiated and called as such:
* NumArray obj = new NumArray(nums);
* obj.update(i,val);
* int param_2 = obj.sumRange(i,j);
*/
```

## 改段求点

改段求点和改点求段恰好相反。比如给定一个数组，每次修改都是一段，比如让a[3] ~ a[5]中每个元素都加上10，求任意元素的值。

跟改点求段中树状数组tree[i]保存所辖子节点的和不同，**这里树状数组保存所辖子节点(代表原数组的节点)的增量**。

例如：对[i, j]进行增加时，会对[1, j]的所有父节点进行加增量，对[1, i - 1]的所有父节点减增量。查询某个数i的值时候，会从该节点累加到最终的父节点。

看例题[Color the ball](http://acm.hdu.edu.cn/showproblem.php?pid=1556)。

```cpp
#include <iostream>
#include <cstdio>
#include <vector>

using namespace std;

#define N 100005
int tree[N], n;

int lowbit(int x) { return x & (-x); }

void update(int index, int val){
    while(index){
        tree[index] += val;
        index -= lowbit(index);
    }
}

int query(int index){
    int ans(0);
    while(index <= n){
        ans += tree[index];
        index += lowbit(index);
    }
    
    return index;
}

int main(){
    int x, y;
    while(scanf("%d", &n) && n){
        for(int i = 1; i <= n; ++i){
            scanf("%d%d", &x, &y);
            update(y, 1);
            update(x - 1, -1);
        }
    }
    
    for(int i = 1; i < n; ++i){
        printf("%d ", query(i));
    }
    printf("%d\n", query(n));
    return 0;
}
```

## 改段求段

该类型的题目是 改点求段 和 改段求点的综合。*因为所有的区间求值都可以使用tree[m] - tree[n-1]来求解，所以只需要考虑前缀和。*

主要思想：

1. 利用两个树状数组：一个模拟改点求段的数组tree，一个模拟改段求点的数组add。
2. 当对[1, j]区间进行增量的时候分为两部分：
    1. 对改点求段的数组，看做在num[j]上改变了val*j的值，然后依次修改tree的后续节点。
    2. 对改段求点的数组，看做在num[1...j-1]上添加增量。从j-1开始往前缀节点添加增量。
3. 求解的时候，分别查询两个数组，对tree向前查询累加和；对add向后查找增量，增量乘以区间宽度就是区间增量和。两部分结果相加，就是区间总的和。
4. 
看例题[A Simple Problem with Integers](http://poj.org/problem?id=3468)：

```cpp
#include <iostream>
#include <cstdio>
#include <cstring>

using namespace std;

#define N 100005

long long tree[N], add[N];
int n;

int lowbit(int index){
    return index & (-index);
}

void UpdateBackward(int index, long long val){
    while(index <= n){
        tree[index] += val;
        index += lowbit(index);
    }
}

void UpdateForward(int index, long long val){
    while(index > 0){
        add[index] += val;
        index -= lowbit(index);
    }
}

void Update(int index, long long val){
    UpdateBackward(index, val * index);
    UpdateForward(index - 1, val);
}

long long QueryBackward(int index){
    long long sum(0);
    
    while(index <=n){
        sum += add[index];
        index += lowbit(index);
    }
    return sum;
}

long long QueryForward(int index){
    long long sum(0);
    
    while(index > 0){
        sum += tree[index];
        index -= lowbit(index);
    }
    return sum;
}

long long Query(int index){
    return QueryForward(index) + QueryBackward(index) * index;
}

int main(){
    int q, x, y;
    long long z;
    char str[2];
    memset(tree, 0, sizeof(tree));
    memset(add, 0, sizeof(add));
    
    scanf("%d%d", &n, &q);
    ++n; // 使用a[2] ... a[n + 1] 部分来存储数组
    for(int i = 1; i < n; ++i){
        scanf("%lld", &z);
        x = i + 1; y = i + 1;
        Update(y, z);
        Update(x - 1, -z); // 如果使用a[1] ... a[n]存储数组，会导致Update(0, -z)这样的调用
                           // 之后调用Update_backward(0, -z)的时候会造成死循环；
                           // 因为 0 & (-0) = 0。
                           // 虽然可以用判断语句来消除影响，但不如从下标2开始美观。
    }
    
    while(q--){
        scanf("%s", str);
        if(str[0] == 'C'){
            scanf("%d%d%lld", &x, &y, &z);
            ++x; ++y;
            Update(y, z);
            Update(x - 1, -z);
        }
        else{
            scanf("%d%d", &x, &y);
            ++x; ++y;
            printf("%lld\n", Query(y) - Query(x - 1));
        }
    }
    return 0;
}
```

> 这里有一点需要注意：一般的用数组来解的题，都是不用a[0]的，也就是元素是从a[1]~a[n]，因为 sum[n~m]=sum[m]-sum[n-1]，避免 n-1 为负数。而本题中的改段求段中的元素是从 a[2]~a[n+1] ，因为 Update()函数中的子函数 UpdateBackward() 函数中index为0的话会陷入死循环，所以参数最小是1。所以元素下标必须从 2 开始。
> 
> 也可以从1开始，但是加入一些判断语句，代码不美观。

***

Author: Zhipeng Han

Time: 2017/05/09
