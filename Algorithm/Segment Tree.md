### Content
1. [线段树](#线段树)
2. [从一个例子理解线段树](#从一个例子理解线段树)
3. [线段树的应用](#线段树的应用)

# 引言

本文主要介绍[线段树](http://www.cnblogs.com/TenosDoIt/p/3453089.html)。

感觉线段树能解决的问题，使用树状数组都可以解决。


# 线段树

对于线段树我们可以选择和普通二叉树一样的链式结构。由于线段树是完全二叉树，我们也可以用数组来存储。可以通过对节点加入不同的信息来达到不同的目的。

线段树，类似区间树，它在各个节点保存一条线段（数组中的一段子数组），主要用于高效解决连续区间的动态查询问题，由于二叉结构的特性，它基本能保持每个操作的复杂度为O(longn)。

线段树的**性质**：线段树的每个节点表示一个区间，子节点分别表示父节点的左右半区间。例如父节点的区间是[a, b]，那么左儿子的区间是\[a, c\](c = a + (b - a) / 2)，右儿子的区间是[c + 1, b]。

# 从一个例子理解线段树

下面我们从一个经典的例子来了解线段树，**问题描述**如下:从数组arr[0...n-1]中查找某个数组某个区间内的最小值，其中数组大小固定，但是数组中的元素的值可以随时更新。

> 对这个问题一个简单的解法是：遍历数组区间找到最小值，时间复杂度是O(n),额外的空间复杂度O(1)。当数据量特别大，而查询操作很频繁的时候，耗时可能会不满足需求。
> 
> 另一种解法：使用一个二维数组来保存提前计算好的区间[i,j]内的最小值，那么预处理时间为O(n^2)，查询耗时O(1), 但是需要额外的O(n^2)空间，当数据量很大时，这个空间消耗是庞大的，而且当改变了数组中的某一个值时，更新二维数组中的最小值也很麻烦。

我们可以用线段树来解决这个问题：预处理耗时O(n)，查询、更新操作**O(logn)**，需要额外的空间O(n)。根据这个问题我们构造如下的二叉树：

1. 叶子节点是原始组数arr中的元素
2. 非叶子节点代表它的所有子孙叶子节点所在区间的最小值

例如对于数组[2, 5, 1, 4, 9, 3]可以构造如下的二叉树（背景为白色表示叶子节点，非叶子节点的值是其对应数组区间内的最小值，例如根节点表示数组区间arr[0...5]内的最小值是1）：

![Segment_Tree](https://github.com/OneDirection9/Essay/blob/master/MarkdownImages/Segment_Tree.jpg?raw=true)

由于线段树的父节点区间是平均分割到左右子树，因此线段树是完全二叉树，对于包含n个叶子节点的完全二叉树，它一定有n-1个非叶节点，总共2n-1个节点，因此存储线段是需要的空间复杂度是O(n)。

## 创建线段树

下面的讨论及代码都是数组来存储线段树（注意到用数组存储时，有效空间为2n-1,实际空间确不止这么多，比如上面的线段树中叶子节点1、3虽然没有左右子树，但是的确占用了数组空间，实际空间是满二叉树的节点数目： 2^n - 1，n  是树的高度，但是这个空间复杂度也是O(n)的 ）。

```cpp
const int MAXIMUM = 1000;
struct SegTreeNode{
    int val;
}segTree[MAXIMUM]; // 定义线段树

/*
功能：构建线段树
root：当前线段树的根节点下标
arr：用来构造线段树的数组
istart：数组的起始位置
iend：数组的结束位置
*/
void Build(int root, int arr[], int istart, int iend){
    if(istart == iend){ // 叶子节点
        segTree[root].val = arr[istart];
    }
    else{
        int mid = istart + (iend - istart) / 2;
        Build(root * 2 + 1, arr, istart, mid); // 递归构造左子树
        Build(root * 2 + 2, arr, mid + 1, iend); // 递归构造右子树
        
        segTree[root] = min(segTree[root * 2 + 1], segTree[root * 2 + 2]);
    }
}
```

## 查询线段树

已经构建好了线段树，那么怎样在它上面查找某个区间的最小值呢？查询的思想是选出一些区间，使他们相连后恰好涵盖整个查询区间，因此线段树适合解决“相邻的区间的信息可以被合并成两个区间的并区间的信息”的问题。代码如下，具体见代码解释：

```cpp
/*
功能：查询线段树
root：当前线段树的根节点下标
[nstart, nend]：当前节点所表示的区间
[qstart, qend]：需要查询的区间
*/


int query(int root, int nstart, int nend, int qstart, int qend){
    // 查询区间和当前节点区间没有交集
    if(qstart > nend || qend < nstart){
        return INT_MAX;
    }
    
    // 当前节点区间包含在查询区间内
    if(qstart <= nstart && qend >= nend){
        return segTree[root].val;
    }
    
    int mid = nstart + (nend - nstart) / 2;
    return min(query(root * 2 + 1, nstart, mid, qstart, qend), query(root * 2 + 2, mid + 1, nend, qstart, qend));
}
```

> 个人觉得里面根据节点区间包含在查询区间内就返回值的方式不太好理解，
> 可以在递归查询的时候修改需要查询的区间（修改为mid之类的，而不是固定不变的原本的查询区间），这样最终就是判断两个区间相等。

举例说明（对照上面的二叉树）：

1. 当我们要查询区间[0,2]的最小值时，从根节点开始，要分别查询左右子树，查询左子树时节点区间[0,2]包含在查询区间[0,2]内，返回当前节点的值1，查询右子树时，节点区间[3,5]和查询区间[0,2]没有交集，返回正无穷INFINITE，查询结果取两子树查询结果的较小值1，因此结果是1.

2. 查询区间[0,3]时，从根节点开始，查询左子树的节点区间[0,2]包含在区间[0,3]内，返回当前节点的值1；查询右子树时，继续递归查询右子树的左右子树，查询到非叶节点4时，又要继续递归查询：叶子节点4的节点区间[3,3]包含在查询区间[0,3]内，返回4，叶子节点9的节点区间[4,4]和[0,3]没有交集，返回INT\_MAX,因此非叶节点4返回的是min(4, INT\_MAX) = 4，叶子节点3的节点区间[5,5]和[0,3]没有交集，返回INT\_MAX,因此非叶节点3返回min(4, INT\_MAX) = 4, 因此根节点返回 min(1,4) = 1。

##  单节点更新

单节点更新是指只更新线段树的某个叶子节点的值，但是更新叶子节点会对其父节点的值产生影响，因此更新子节点后，要回溯更新其父节点的值。

```cpp
/*
功能：更新线段树中某个叶子节点的值
root：当前线段树的根节点下标
[nstart, nend]：当前节点表示的区间
index：待更新节点在原数组中的下标
addval：更新的值（原来的值加上addVal）
*/
void UpdateOne(int root, int nstart, int nend, int index, int addVal){
    if(nstart == nend){
        if(index == nstart){ // 找到对应节点，更新
            segTree[root] += val;
        }
        return;
    }
    
    int mid = nstart + (nend - nstart) / 2;
    if(index <= mid){ // 在左子树中更新
        UpdateOne(root * 2 + 1, nstart, mid, index, addVal);
    }
    else{ // 在右子树中更新
        UpdateOne(root * 2 + 2, mid + 1, nend, index, addVal);
    }
    
    segTree[root].val = min(segTree[root * 2 + 1].val, segTree[root * 2 + 2].val);
}
```

## 区间更新

详细见上面的参考链接。

# 线段树的应用

## 改点求段

对含有n个元素的数组，有下列两种操作
1. 求出第i个到第j个元素的和；
2. 修改其中某个元素的值。

**分析**：如果使用正常的解法，求和的时间复杂度是O(n)，修改值的时间复杂度是O(1)；如果使用数组累积和的方法，求和的时间复杂度是O(1)，修改值的时间复杂度是O(n)。

这里我们使用线段树，平均时间复杂度是O(nlogn)。*与上面不同，这里使用链式结构来存储线段树。*

以 [Leetcode 307.Range Sum Query - Mutable](https://leetcode.com/problems/range-sum-query-mutable/#/description) 为例写C++代码。

```cpp
struct segmentNode {
	int start, end, sum;
	segmentNode* left;
	segmentNode* right;
	segmentNode(int a, int b) : start(a), end(b), sum(0), left(NULL), right(NULL) {}
};
class NumArray {
public:
	NumArray(vector<int> nums) {
		root = BuildTree(nums, 0, nums.size() - 1);
	}

	void update(int i, int val) {
		modifyTree(i, val, root);
	}

	int sumRange(int i, int j) {
		return query(i, j, root);
	}

private:
	segmentNode* root;
	segmentNode* BuildTree(vector<int> &nums, int start, int end) {
		if (start > end) {
			return NULL;
		}
		segmentNode* root = new segmentNode(start, end);

		if (start == end) {
			root->sum = nums[start];
			return root;
		}
		int mid = start + (end - start) / 2;
		root->left = BuildTree(nums, start, mid);
		root->right = BuildTree(nums, mid + 1, end);
		root->sum = root->left->sum + root->right->sum;

		return root;
	}

	int modifyTree(int i, int val, segmentNode* root) {
		if (root == NULL) {
			return 0;
		}

		if (root->start == root->end) {
			if (root->start == i) {
				int diff = val - root->sum;
				root->sum = val;
				return diff;
			}
		}
		else {
			int diff(0);
			int mid = root->start + (root->end - root->start) / 2;
			if (i > mid) {
				diff = modifyTree(i, val, root->right);
			}
			else {
				diff = modifyTree(i, val, root->left);
			}
			root->sum += diff;
			return diff;
		}
	}

	int query(int start, int end, segmentNode* root) {
		if (root == NULL) {
			return 0;
		}

		if (root->start == start && root->end == end) {
			return root->sum;
		}

		int mid = root->start + (root->end - root->start) / 2;
		if (start > mid) {
			return query(start, end, root->right);
		}
		else if (end < mid) {
			return query(start, end, root->left);
		}
		return query(start, mid, root->left) + query(mid + 1, end, root->right);
	}
};

```

## 线段投影的长度

解题思路参考[这里](http://www.cnblogs.com/shuaiwhu/archive/2012/04/22/2464583.html)。

参考链接：
http://blog.csdn.net/metalseed/article/details/8039326

***

Author: Zhipeng Han

Time: 2017/05/10
