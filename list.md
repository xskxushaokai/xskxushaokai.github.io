# 链表
## 引入
链表的特点是：用一组任意的存储单元存储线性表的数据元素（这组存储单元可以是连续的，也可以不连续）。通过如链条一般的指针来连接元素。它的特点是插入与删除数据十分方便，但寻找与读取数据的表现欠佳。常用的链表操作有：初始化、添加、遍历、插入、删除、查找、排序、释放等。链表可分为**单向链表**和**双向链表**，如图所示：

![输入图片说明](/imgs/2025-10-29/7crkDek0ajYRGhJh.svg+xml)
（a）单向链表
![输入图片说明](/imgs/2025-10-29/T79Lx0sJ8LJEdt3O.png)
（b）双向链表

链表一般是循环的，各节点首尾相接，最后的next指针指向第1个节点，第1个pre指针指向最后一个节点。单向链表只有一个遍历方向，双向链表有两个遍历方向，比单向链表的访问更方便一些，也快一些。在需要频繁访问前后几个节点的场景可以使用双向链表。

使用链表时，可以直接用STL list，也可以自己写链表。如果自己写代码实现链表，有两种编码实现方法：**动态链表** 和 **静态链表**。在算法竞赛中为加快编码速度，一般使用静态链表或STL list。而在工程应中，为进行高效的空间管理，通常使用动态链表。

下面用例题洛谷 P1996，给出动态链表、静态链表、STL 链表等 4 种实现方案。

## 与数组的区别

链表和数组都可用于存储数据。与链表不同，数组将所有元素按次序依次存储。不同的存储结构令它们有了不同的优势：

链表因其链状的结构，能方便地删除、插入数据，操作次数是 𝑂(1)。但也因为这样，寻找、读取数据的效率不如数组高，在随机访问数据中的操作次数是 𝑂(𝑛)

数组可以方便地寻找并读取数据，在随机访问中操作次数是 𝑂(1)但删除、插入的操作次数是 𝑂(𝑛)次。

---
## 例题  约瑟夫问题（洛谷P1996）
**题目描述**
$n$ 个人围成一圈，从第一个人开始报数,数到 $m$ 的人出列，再由下一个人重新从 $1$ 开始报数，数到 $m$ 的人再出圈，依次类推，直到所有的人都出圈，请输出依次出圈人的编号。



**输入格式**：输入两个整数 $n,m$。
**输出格式**：输出一行 $n$ 个整数，按顺序输出每个出圈人的编号。

**输入样例**
```
10 3
```

**输出样例**
```
3 6 9 2 7 1 8 5 10 4
```

**说明/提示**
$1 \le m, n \le 100$

---


## 动态链表
动态链表需要临时分配链表节点、使用完毕后释放链表节点。这样做，优点是能及时释放空间，不使用多余内存。缺点是很容易出错。动态链表是“教科书式”的标准做法。
以下代码用动态单向链表实现 P1996
```C++
#include <bits/stdc++.h>

struct node{
	//链表结构
	int data;
	node *next;
};

int main(){
	int n,m;
	scanf("%d %d",&n,&m);
	node *head,*p,*now,*prev;
	//定义变量
	head = new node; head->data = 1; head->next=NULL; //分配第一个节点，数据置为 1
	now = head; //当前指针是头
	for(int i=2;i<=n;i++){
		p = new node;
		p->data = i; p->next = NULL;//p 是新节点
		now->next = p;//把申请的新节点连到前面的链表上
		now = p;//尾指针后移一个
	}
	now->next = head;//尾指针指向头：循环链表建立完成

	// 模拟题目流程
	now = head, prev=head;  //从第 1 个开始数
	while((n--) ){
		for(int i=1;i<m;i++){    //数到 m，停下
			prev = now;    //记录上一个位置，用于下面跳过第 m 个节点
			now = now->next;
		}
		printf("%d ", now->data);    //输出第 m 节点，带空格
		prev->next = now->next;    //跳过这个节点
		delete now;    //释放节点
		now = prev->next;    //新的一轮
	}

	return 0;
}
```


## 静态链表
**动态链表虽然标准，但是竞赛中一般不用。算法竞赛对内存管理要求不严格，为加快编码速度，一般就在题目允许的存储空间内静态分配内存，省去了动态分配内存和释放的麻烦。**

静态链表使用预先分配的一段连续空间存储链表。具体做法为：定义链表结构体数组，和动态链表的结构差不多。以下给出两个示例，分别是：用结构体数组实现单向静态链表、用结构体数组实现双向静态链表。

1.**用结构体数组实现单向静态链表**，注意静态分配尽量定义在全局，而不要定义在函数内部。
```C++
#include <bits/stdc++.h>

const int maxn = 105;//定义静态链表的空间大小

struct node{
	//单向链表
	int data;  // 数据域
	int nextid;  // 指针域
}nodes[maxn];

int main(){
	int n, m;
	scanf("%d%d", &n, &m);
	
	// 链表初始化
	for(int i = 1; i <= n; i++){
		nodes[i].data = i;
		nodes[i].nextid = i + 1;
	}
	nodes[n].nextid = 1;//循环链表：尾指向头

	int now = 1, prev = 1;//从第 1 个开始报数
	while(n--){
		for(int i = 1; i < m; i++){//数到 m，停下
			prev = now;
			now = nodes[now].nextid;
		}
		printf("%d ", nodes[now].data);
		nodes[prev].nextid = nodes[now].nextid;//跳过节点 now，即删除 now
		now = nodes[prev].nextid;//新的 now
	}

	return 0;

}
```

2.**用结构体数组实现双向静态链表**
```C++
#include <bits/stdc++.h>

const int maxn = 105;

struct node{
	//双向链表
	int data; // 数据域
	int preid;// 指针域，前一个节点
	int nextid;// 指针域，后一个节点
}nodes[maxn];

int main(){
	int n, m;
	scanf("%d%d", &n, &m);

	//建立链表
	for(int i = 1; i <= n; i++){
		nodes[i].data = i;
		nodes[i].preid = i-1;//前节点
		nodes[i].nextid = i+1;//后节点
	}
	nodes[n].nextid = 1;//循环链表：尾指向头
	nodes[1].preid = n;//循环链表：头指向尾

	int now = 1;//从第 1 个开始
	while(n--){
		for(int i = 1; i < m; i++)//数到 m，停下
			now = nodes[now].nextid;
		printf("%d ", nodes[now].data);//打印
		
		// 删除now节点
		int prev = nodes[now].preid;  // 先将now节点的前、后两个节点取出
		int next = nodes[now].nextid;
		nodes[prev].nextid = nodes[now].nextid;//删除 now
		nodes[next].preid = nodes[now].preid;
		
		now = next;//新的开始
	}
	
	return 0;

}
```

---

## STL list
竞赛或工程中，常常使用 C++ STL list。list 是双向链表，它的内存空间可以是不连续的，通过指针来进行数据的访问，它能高效率地在任意地方删除和插入，插入和删除操作是常数时间的。
有关STL list的具体用法，可查看[https://www.cnblogs.com/linuxAndMcu/p/10260627.html](https://www.cnblogs.com/linuxAndMcu/p/10260627.html)

下面是使用list实现 P1996
```c++
#include <bits/stdc++.h>

using namespace std;

int main(){
	int n, m;
	cin>>n>>m;

	list<int>node;
	for(int i=1;i<=n;i++)
		//建立链表
		node.push_back(i);
		
	list<int>::iterator it = node.begin();
	while(node.size()){    //list 的大小由 STL 自己管理
		for(int i=1;i<m;i++){    //数到 m
			it++;
			if(it == node.end()) //手动循环链表，end()是 list 末端下一位置
				it = node.begin();
		}

		cout << *it <<' ';
		// 先取出下一个节点
		list<int>::iterator next = ++it;
		if(next==node.end())
			next=node.begin();    //手动循环链表
		// 删除报m的节点，node.size()自动减 1
		node.erase(--it);
		// 下一轮报数从下一个节点开始
		it = next;

	}
	
	return 0;

}
```

---
## 附 动态链表常用的实现

### 双向链表
```c++
struct Node {
  int value;
  Node *left;
  Node *right;
};
```

### 向双向循环链表中插入（写入）数据
在向双向循环链表插入数据时，除了要判断给定链表是否为空外，还要同时修改左、右两个指针。

大致流程如下：
1.  初始化待插入的数据 `node`；
2.  判断给定链表 `p` 是否为空；
3.  若为空，则将 `node` 的 `left` 和 `right` 指针，以及 `p` 都指向自己；
4.  否则，将 `node` 的 `left` 指针指向 `p`;
5.  将 `node` 的 `right` 指针指向 `p` 的右结点；
6.  将 `p` 右结点的 `left` 指针指向 `node`；
7.  将 `p` 的 `right` 指针指向 `node`。
```c++
void insertNode(int i, Node *p) {
	Node *node = new Node;
	node->value = i;
	if (p == NULL) {
		p = node;
		node->left = node;
		node->right = node;
	} 
	else {
		node->left = p;
		node->right = p->right;
		p->right->left = node;
		p->right = node;
	}
}
```


### 从双向循环链表中删除数据
流程大致如下：

1.  将 `p` 左结点的右指针指向 `p` 的右节点；
2.  将 `p` 右结点的左指针指向 `p` 的左节点；
3.  新建一个临时结点 `t` 存放 `p` 的地址；
4.  将 `p` 的右节点地址赋给 `p`，以避免 `p` 变成悬垂指针；
5.  删除 `t`。

```c++
void deleteNode(Node *&p) {
	p->left->right = p->right;
	p->right->left = p->left;
	Node *t = p;
	p = p->right;
	delete t;
}
```



