# 总论和思想

主要是leetcode中相关的一些用法，也兼记录各种C++的神奇特性



# 具体技巧

## 基础的排序查找

**冒泡**

```cpp
#include <iostream>
using namespace std;
template<typename T> //整数或浮点数皆可使用,若要使用类(class)或结构体(struct)时必须重载大于(>)运算符
void bubble_sort(T arr[], int len) {
        int i, j;
        for (i = 0; i < len - 1; i++) //忽略已经排序好的最后一个元素
                for (j = 0; j < len - 1 - i; j++)
                        if (arr[j] > arr[j + 1])
                                swap(arr[j], arr[j + 1]);
}
int main() {
        int arr[] = { 61, 17, 29, 22, 34, 60, 72, 21, 50, 1, 62 };
        int len = (int) sizeof(arr) / sizeof(*arr);
        bubble_sort(arr, len);
        for (int i = 0; i < len; i++)
                cout << arr[i] << ' ';
        cout << endl;
        float arrf[] = { 17.5, 19.1, 0.6, 1.9, 10.5, 12.4, 3.8, 19.7, 1.5, 25.4, 28.6, 4.4, 23.8, 5.4 };
        len = (float) sizeof(arrf) / sizeof(*arrf);
        bubble_sort(arrf, len);
        for (int i = 0; i < len; i++)
                cout << arrf[i] << ' '<<endl;
        return 0;
}

```
**选择**

```cpp

template<typename T> //整數或浮點數皆可使用，若要使用物件（class）時必須設定大於（>）的運算子功能
void selection_sort(std::vector<T>& arr) {
        for (int i = 0; i < arr.size() - 1; i++) {
                int min = i;
                for (int j = i + 1; j < arr.size(); j++)
                        if (arr[j] < arr[min])
                                min = j;
                std::swap(arr[i], arr[min]);
        }
}
```

**插入**

```cpp
void insertion_sort(int arr[],int len){
        for(int i=1;i<len;i++){
                int key=arr[i];
                int j=i-1;
                while((j>=0) && (key<arr[j])){
                        arr[j+1]=arr[j];
                        j--;
                }
                arr[j+1]=key;
        }
}
```

**二分**


**自定义查找**
自己定义查找规则，用sort，传入begin、end和函数比较器
```cpp
bool cmp(pair<int,int> a, pair<int,int> b) {
    return a.second < b.second;
}
sort(v.begin(), v.end(), cmp);
```

**快排**

```cpp
// 快速排序函数（Hoare 分区方案实现）
void quickSort1(vector<int>& nums, int l, int r) {
    // 1. 递归终止条件：当子数组长度为1或空时，无需排序
    if (l >= r) return;

    // 2. 初始化分区参数：
    // - i 初始在左边界左侧（l-1）
    // - j 初始在右边界右侧（r+1）
    // - 基准值 x 选中间元素（避免极端情况如全排序数组导致最坏时间复杂度）
    int i = l - 1, j = r + 1;
    int x = nums[(l + r) >> 1]; // 位运算代替 (l + r) / 2，等价且更高效

    // 3. Hoare 分区循环：双指针从两端向中间移动
    while (i < j) {
        // 3.1 移动左指针 i：跳过所有小于 x 的元素，直到找到 >=x 的元素
        do i++; while (nums[i] < x);

        // 3.2 移动右指针 j：跳过所有大于 x 的元素，直到找到 <=x 的元素
        do j--; while (nums[j] > x);

        // 3.3 如果指针未交错，交换左右指针的元素（确保左边 <=x，右边 >=x）
        if (i < j) swap(nums[i], nums[j]);
    }

    // 4. 递归排序左右子数组：
    // - 分区点为 j（因为当 i >= j 时，j 是左半部分的最右端）
    // - 左子数组：[l, j]（所有元素 <=x）
    // - 右子数组：[j+1, r]（所有元素 >=x）
    quickSort1(nums, l, j);
    quickSort1(nums, j + 1, r);
}
```

## 递归算法思路

递归算法三要素：
1. 确定递归函数参数和返回值：核心就是哪些参数在递归中要处理
2. 确定中止条件：何时停止递归
3. 确定单层递归的逻辑：每一层递归需要处理什么信息


## 二叉树迭代

前序：根->左->右
中序：左->根->右
后序：左->右->根


### 二叉树递归前中后序遍历的写法
```cpp
class Solution {
public:
    void traversal(TreeNode* cur, vector<int>& vec) {
        if (cur == NULL) return;
        vec.push_back(cur->val);    // 中
        traversal(cur->left, vec);  // 左
        traversal(cur->right, vec); // 右
    }
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> result;
        traversal(root, result);
        return result;
    }
};

```

### 二叉树统一风格前中后序遍历

```cpp
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> result;
        stack<TreeNode*> st;
        if (root != NULL) st.push(root);
        while (!st.empty()) {
            TreeNode* node = st.top();
            if (node != NULL) {
                st.pop(); // 将该节点弹出，避免重复操作，下面再将右中左节点添加到栈中
                if (node->right) st.push(node->right);  // 添加右节点（空节点不入栈）

                st.push(node);                          // 添加中节点
                st.push(NULL); // 中节点访问过，但是还没有处理，加入空节点做为标记。

                if (node->left) st.push(node->left);    // 添加左节点（空节点不入栈）
            } else { // 只有遇到空节点的时候，才将下一个节点放进结果集
                st.pop();           // 将空节点弹出
                node = st.top();    // 重新取出栈中元素
                st.pop();
                result.push_back(node->val); // 加入到结果集
            }
        }
        return result;
    }
};


```

### 二叉树层序遍历
```cpp  

class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        queue<TreeNode*> que;
        if (root != NULL) que.push(root);
        vector<vector<int>> result;
        while (!que.empty()) {
            int size = que.size();
            vector<int> vec;
            // 这里一定要使用固定大小size，不要使用que.size()，因为que.size是不断变化的
            for (int i = 0; i < size; i++) {
                TreeNode* node = que.front();
                que.pop();
                vec.push_back(node->val);
                if (node->left) que.push(node->left);
                if (node->right) que.push(node->right);
            }
            result.push_back(vec);
        }
        return result;
    }
};
```







## 回溯法

**处理模板**
```cpp
void backtracking(参数) {
    if (终止条件) {
        存放结果;
        return;
    }
    for (选择：本层集合中元素（树中节点孩子的数量就是集合的大小）) {
        处理节点;
        backtracking(路径，选择列表); // 递归
        回溯，撤销处理结果
    }
}
```


## DFS（深度优先搜索）

**处理模板**
```cpp
void dfs(参数) {
    if (终止条件) {
        存放结果;
        return;
    }

    for (选择：本节点所连接的其他节点) {
        处理节点;
        dfs(图，选择的节点); // 递归
        回溯，撤销处理结果
    }
}
```

## BFS（广度优先搜索）

**处理模板（四方格地图）**
```cpp
int dir[4][2] = {0, 1, 1, 0, -1, 0, 0, -1}; // 表示四个方向
// grid 是地图，也就是一个二维数组
// visited标记访问过的节点，不要重复访问
// x,y 表示开始搜索节点的下标
void bfs(vector<vector<char>>& grid, vector<vector<bool>>& visited, int x, int y) {
    queue<pair<int, int>> que; // 定义队列
    que.push({x, y}); // 起始节点加入队列
    visited[x][y] = true; // 只要加入队列，立刻标记为访问过的节点
    while(!que.empty()) { // 开始遍历队列里的元素
        pair<int ,int> cur = que.front(); que.pop(); // 从队列取元素
        int curx = cur.first;
        int cury = cur.second; // 当前节点坐标
        for (int i = 0; i < 4; i++) { // 开始想当前节点的四个方向左右上下去遍历
            int nextx = curx + dir[i][0];
            int nexty = cury + dir[i][1]; // 获取周边四个方向的坐标
            if (nextx < 0 || nextx >= grid.size() || nexty < 0 || nexty >= grid[0].size()) continue;  // 坐标越界了，直接跳过
            if (!visited[nextx][nexty]) { // 如果节点没被访问过
                que.push({nextx, nexty});  // 队列添加该节点为下一轮要遍历的节点
                visited[nextx][nexty] = true; // 只要加入队列立刻标记，避免重复访问
            }
        }
    }

}

```

## 图论


### dijkstra
- 第一步，选源点到哪个节点近且该节点未被访问过
- 第二步，该最近节点被标记访问过
- 第三步，更新非访问节点到源点的距离（即更新minDist数组）

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;
int main() {
    int n, m, p1, p2, val;
    cin >> n >> m;

    vector<vector<int>> grid(n + 1, vector<int>(n + 1, INT_MAX/2));
    for(int i = 0; i < m; i++){
        cin >> p1 >> p2 >> val;
        grid[p1][p2] = val;
    }

    int start = 1;
    int end = n;

    // 存储从源点到每个节点的最短距离
    std::vector<int> minDist(n + 1, INT_MAX/2);

    // 记录顶点是否被访问过
    std::vector<bool> visited(n + 1, false);

    minDist[start] = 0;  // 起始点到自身的距离为0

    for (int i = 1; i <= n; i++) { // 遍历所有节点

        int minVal = INT_MAX/2;
        int cur = 1;

        // 1、选距离源点最近且未访问过的节点
        for (int v = 1; v <= n; ++v) {
            if (!visited[v] && minDist[v] < minVal) {
                minVal = minDist[v];
                cur = v;
            }
        }

        visited[cur] = true;  // 2、标记该节点已被访问

        // 3、第三步，更新非访问节点到源点的距离（即更新minDist数组）
        for (int v = 1; v <= n; v++) {
            if (!visited[v] && grid[cur][v] != INT_MAX/2 && minDist[cur] + grid[cur][v] < minDist[v]) {
                minDist[v] = minDist[cur] + grid[cur][v];
            }
        }

    }

    if (minDist[end] == INT_MAX/2) cout << -1 << endl; // 不能到达终点
    else cout << minDist[end] << endl; // 到达终点最短路径

}

```

### 并查集

### 最小生成树


### 拓扑排序

一共就两步，不断循环
1. 找到入度为0 的节点，加入结果集
2. 将该节点从图中移除

- 判断是否有有向环：
结果集元素个数 不等于 图中节点个数，我们就可以认定图中一定有 有向环

- 如何移除节点：本质是要将 该节点作为出发点所连接的节点的 入度 减一 就可以了，这样好能根据入度找下一个节点，不用真在图里把这个节点删掉



## 双指针
核心思路就是通过双指针，将

还有先排序，再双指针

## 栈和队列
- stack:
- 队列queue
- 双端队列deque



## 滑动窗口

## 哈希表
- C++中的实现：map、multimap(都是用红黑树实现) 和 unordered_map（用哈希实现）


## 前缀和

## 二分查找


## 贪心

从局部最优找到整体最优，一般也不需要证明，但要确保这是成立的

## 动态规划

动态规划的过程是由前一个状态推导出来的

基本步骤：
- 确定dp数组（dp table）以及下标的含义
- 确定递推公式（dp数组的递推计算方法）
- dp数组如何初始化
- 确定遍历顺序
- 举例推导dp数组


二维背包问题：
即dp[i][j] 表示从下标为[0-i]的物品里任意取，放进容量为j的背包，价值总和最大是多少


初始化：重量为0时价值都是0，然后根据0号物品的重量对dp数组的第一行进行初始化。其余的值都初始化为零。




## 数组相关

- 首先考虑能不能排序，是否排序后会简单一些
- 考虑用hash map来进行查找或者储存



# 细节和基本语法使用




## ACM模式

[ACM模式](/C:/Users/CY/Downloads/卡码网-ACM输入输出模板(C++,Java,Python,go,JS).pdf)


### 输入
**cin**
```cpp
#include <iostream>
int n;
cin >> n;
// 输出读入的整数
cout << n << endl;
```

**getline（很重要）**
```cpp
#include <string>
#include <iostream>
string s;
getline(cin, s);
// 输出读入的字符串
cout << s << endl;

```
**getchar**
```cpp
char ch;
ch = getchar();
// 输出读入的字符
cout << ch << endl;
```

**一维输入**
```cpp
int n;
cin >> n; // 读入3，说明数组的大小是3
vector<int> nums(n); // 创建大小为3的vector<int>
for(int i = 0; i < n; i++) {
	cin >> nums[i];
}

```

**二维输入**
```cpp
int m; // 接收行数
int n; // 接收列数

cin >> m >> n;
getchar();
vector<vector<int>> matrix(m, vector<int>(n));

for(int i = 0; i < m; i++) {
	for(int j = 0; j < n; j++) {
		cin >> matrix[i][j];
	}
}
```

**二维带逗号的整数输入**
```cpp
int m; // 接收行数
int n; // 接收列数

cin >> m >> n;
getchar();
vector<vector<int>> matrix(m);

for(int i = 0; i < m; i++) {
    // 读入字符串
	string s;
	getline(cin, s);
	
	// 将读入的字符串按照逗号分隔为vector<int>
	vector<int> vec;
	int p = 0;
	for(int q = 0; q < s.size(); q++) {
		p = q;
		while(s[p] != ',' && p < s.size()) {
			p++;
		}
		string tmp = s.substr(q, p - q);
		vec.push_back(stoi(tmp));
		q = p;
	}
	
	//写入matrix
	matrix[i] = vec;
	vec.clear();
}

```

**不固定输入**
```cpp
//一维不固定
vector<int> nums;
int num;
while(cin >> num) {
	nums.push_back(num);
	// 读到换行符，终止循环
	if(getchar() == '\n') {
		break;
	}
}
```
**多个字符串输入**
```cpp
vector<string> strings;
string str;
while(cin >> str) {
	strings.push_back(str);
	// 读到换行符，终止循环
	if(getchar() == '\n') {
		break;
	}
}
```

**char\*字符串输入**
```cpp
char str[100];
cin.getline(str, 100); // 读入字符串，最大长度为99（最后一个字符留给'\0'）
```


**字符串转整数数组**

方法一：
```cpp
vector<int> vec;

// 读入字符串
string s;
getline(cin, s);

// 将读入的字符串按照逗号分隔为vector<int>
	int p = 0;
	for(int q = 0; q < s.size(); q++) {
		p = q;
		while(s[p] != ',' && p < s.size()) {
			p++;
		}
		string tmp = s.substr(q, p - q);
		vec.push_back(stoi(tmp));
		q = p;
	}
```
方法二：
```cpp
#include <sstream>
std::vector<std::string> result;
std::string ip_str;
std::stringstream ss(ip_str); // 用 IP 字符串初始化 stringstream
std::string segment;

// 使用 getline 按 '.' 分割字符串
// 第三个参数是分隔符
while (std::getline(ss, segment, '.')) {
    result.push_back(segment); // 将分割出的部分添加到 vector
}
```

**十进制转二进制数**
```cpp
string decimal;
while (decimal > 0) 
{
    binary = (decimal % 2 == 0 ? "0" : "1") + binary; //相当于从低位求到高位，然后把新的位数加到旧的位数的前面
    decimal /= 2;
}
```

## 各种小细节


### 头文件
```cpp
#include<algorithm>
#include<vector>
#include<map>
#include<unordered_map>
#include<string>
#include<set>
```
### 语法特性和用法


#### 迭代器遍历和其他遍历

```cpp
// iterator 遍历
for (vector<int>::iterator it = nums.begin(); it != nums.end(); ++it) {
    cout << *it << endl;
}

string s = "hello";
while( int c: s)
{
    cout << c << endl;
}

```

#### vector用法


```cpp
vector<vector<int>> grid(n, vector<int>(m, 0));
//创建一个 n 行 m 列的二维数组，初始值为 0

grid.push_back({1, 2, 3}); // 添加一行
grid[0].push_back(4); // 在第一行添加一个元素


```

#### pair用法
```cpp
pair<int,double> graph;
pair<int, double> p1(1, 2.5); // 创建一个 pair 对象
graph.first = 1;
graph.second = 2.5; // 访问 pair 的元素

```

#### unorderedd_map

unorderedd_map用法：
```cpp
// 或者直接用类似hash map
unordered_map<int, string> myMap = {{5, "zhangsan"},{6, "lisi"}};  // 使用{}赋值

unordered_map<int, int> hashtable; //键值对，key-value，其中key是唯一的，value可以重复
for(int i = 0; i < nums.size(); i ++)
{
    auto it = hashtable.find(target - nums[i]);
    if(it != hashtable.end())
    {
        return {it->second, i};
    }
    hashtable[nums[i]] = i;
}


int main() {
    // 声明无向图的邻接表
    unordered_map<int, vector<int>> umap;

    // 插入边（无向图）
    auto addEdge = [&](int u, int v) {
        umap[u].push_back(v);
        umap[v].push_back(u); // 若是有向图，删除这一行即可
    };

    addEdge(1, 2);
    addEdge(1, 3);
    addEdge(2, 4);
    addEdge(3, 4);
    addEdge(4, 5);

    // 遍历图：打印每个点及其邻接点
    for (const auto& pair : umap) {
        int node = pair.first;
        const vector<int>& neighbors = pair.second;

        cout << "Node " << node << " is connected to: ";
        for (int neighbor : neighbors) {
            cout << neighbor << " ";
        }
        cout << endl;
    }

    return 0;
}
```



#### map
```cpp
#include <iostream>
#include <map>
#include <string>

int main() {
    // 创建一个 map 容器，存储员工的姓名和年龄
    std::map<std::string, int> employees;

    // 插入员工信息
    employees["Alice"] = 30;
    employees["Bob"] = 25;
    employees["Charlie"] = 35;

    // 遍历 map 并打印员工信息
    for (std::map<std::string, int>::iterator it = employees.begin(); it != employees.end(); ++it) {
        std::cout << it->first << " is " << it->second << " years old." << std::endl;
    }

    return 0;
}

if (myMap.find(key) != myMap.end()) {
    // 键存在
}

myMap.erase(key);
myMap.size();
```
#### stack
```cpp
#include <stack>
stack<int> myStack;
myStack.push(1);
myStack.pop();
myStack.top();
myStack.empty();
myStack.size();

```



#### queue
```cpp
#include <queue>
queue<int> myQueue;
myQueue.push(1);
myQueue.pop();
myQueue.front();
myQueue.back();
myQueue.empty();
myQueue.size();

```

#### priority_queue以及最小堆
```cpp

//最大堆
priority_queue<int> pq;
pq.push(1);
pq.pop();
pq.top();


//最小堆
std::priority_queue<int, std::vector<int>, std::greater<int>> minHeap;

struct compare {
    bool operator()(int a, int b) {
        return a > b; // 定义最小堆
    }
};

int main() {
    // 创建一个自定义类型的优先队列，使用最小堆
    std::priority_queue<int, std::vector<int>, compare> pq_min;

    // 向优先队列中添加元素
    pq_min.push(30);
    pq_min.push(10);
    pq_min.push(50);
    pq_min.push(20);

    // 输出队列中的元素
    std::cout << "最小堆中的元素：" << std::endl;
    while (!pq_min.empty()) {
        std::cout << pq_min.top() << std::endl;
        pq_min.pop();
    }

    return 0;
}

```


#### deque
```cpp
#include <deque>
deque<int> myDeque;
myDeque.push_back(1);
myDeque.push_front(2);
myDeque.pop_back();
myDeque.pop_front();
myDeque.front();
myDeque.back();
myDeque.empty();
myDeque.size();


```

引用之后就不能修改引用指向了（也就是引用指向a之后不能改为指向b）




#### 指针
指针的所占内存在32位为4个字节，64为8个字节

注意：
- 当将指针作为参数传递时，实际上是将实参的一个拷贝传递给形参，两者指向的地址相同，但不是同一个变量。在函数中改变指针的指向不影响实参。而引用作为参数传递时，修改引用会影响实参

修改指针指向的方法：
```cpp
void modifyPointer(int*& ptr) { // 引用传递指针本身
    ptr = new int(100); // 直接修改外部指针的指向
}

int main() {
    int* p = nullptr;
    modifyPointer(p);
    // 此时 p 指向新分配的 int(100)
}

//或者

void modifyPointer(int** ptr) { // 传递指针的指针
    *ptr = new int(100); // 通过解引用修改外部指针的指向
}

int main() {
    int* p = nullptr;
    modifyPointer(&p); // 传递指针的地址
    // 此时 p 指向新分配的 int(100)
}

```


动态分配二维数组：
```cpp
// 分配一个 3x4 的二维数组
int rows = 3, cols = 4;
int** matrix = new int*[rows];  // 分配行指针数组
for (int i = 0; i < rows; i++) {
    matrix[i] = new int[cols]; // 为每行分配列空间
}

// 释放内存
for (int i = 0; i < rows; i++) {
    delete[] matrix[i];
}
delete[] matrix;

```


**二级指针**
用二级指针代替指针函数
```cpp
#include<stdio.h>
#include<stdlib.h>
 
void getPose(int stu,int (*pscore)[4], int **ppos) 
{
	*ppos = (int *)(pscore + stu);  
}
 
int main()
{
	int score[3][4] = {{98,99,97,100},      //每行代表一个学生的四项成绩
		               {56,58,59,59},
					   {85,89,87,88}};
	int *pstu;
	int stu;
	
	printf("请输入你要查询的学生序号：0、1、2\n");
	scanf("%d",&stu);
 
	getPose(stu,score,&pstu);  
	for(int i=0;i<4;i++){
		printf("%d ",*pstu++);
	}
	return 0;
}
```

**指针数组**
指针数组是一个数组，且数组中的元素都是指针类型。 每个元素指向一个独立的对象。可以通过索引访问每个指针元素，进而操作对应的对象
```cpp
int* ptrArray[5];  // 声明一个包含5个指针元素的指针数组

int main() {
    int a = 1, b = 2, c = 3, d = 4, e = 5;

    ptrArray[0] = &a;  // 指针数组的第一个元素指向变量a
    ptrArray[1] = &b;
    ptrArray[2] = &c;
    ptrArray[3] = &d;
    ptrArray[4] = &e;

    for (int i = 0; i < 5; i++) {
        std::cout << *ptrArray[i] << " ";  // 输出每个指针元素所指向的值
    }
    std::cout << std::endl;

    return 0;
}
```


**数组指针**


数组指针是指针，是指指向数组的指针，它指向整个数组而不是数组的单个元素。可以通过指针的偏移来访问数组中的不同元素
```cpp
int arr[5] = {1, 2, 3, 4, 5};  // 声明一个包含5个整数的数组

int (*ptr)[5];  // 声明一个指向包含5个整数的数组的指针

int main() {
    ptr = &arr;  // 数组指针指向数组的首地址

    for (int i = 0; i < 5; i++) {
        std::cout << (*ptr)[i] << " ";  // 通过指针和偏移来访问数组元素
    }
    std::cout << std::endl;

    return 0;
}
```

**指针常量**
指针常量是指指针本身是一个常量，即指针的值不可修改，但可以通过该指针间接地修改所指向的对象
```cpp
int value = 10;
int* const ptr = &value;  // 声明一个指针常量，指向整数value

*ptr = 20;  // 通过指针间接修改value的值

// ptr = nullptr;  // 错误，无法修改指针的值
```

**常量指针**
常量指针是指指针指向的对象是一个常量，即所指向的对象的值不可修改，但可以修改指针本身
```cpp
int value = 10;
const int* ptr = &value;  // 声明一个常量指针，指向整数value

// *ptr = 20;  // 错误，无法通过常量指针修改value的值

ptr = nullptr;  // 可以修改指针的值
```

#### 字符串处理和使用
字符串的处理

```cpp
string temp = "Hello, World!"; // 初始化字符串
string part = temp.substr(0, 5); // 截取字符串的前5个字符
```

string的使用：

```cpp
#include <iostream>
#include <string>
string s;
s.insert(0, "Hello"); // 在字符串开头插入"Hello"
s.erase(0, 5); // 删除字符串开头的5个字符
string temp = to_string(123); // 将整数转换为字符串

int temp = atoi("123"); // 将字符串转换为整数
int temp2 = stoi("456"); // 将字符串转换为整数
reverse(s.begin(), s.end()); // 反转字符串

```
#### 地址、指针、引用相关的处理和使用

**引用**
格式：
```cpp
int a = 10;
int &b = a; // b是a的引用
```

在C++传参时，比如把图传入函数时，通常会用引用来避免拷贝开销
```cpp
void processGraph(const unordered_map<int, vector<int>>& graph) {
    // 处理图的逻辑
    for (const auto& pair : graph) {
        int node = pair.first;
        const vector<int>& neighbors = pair.second;
        // ...
    }
}
```



### 调试输出

#### 初始化的问题

- 零输入、各种结果初始化的处理（尤其考虑dp数组、一些计数值的默认为0还是1的问题）
#### 溢出的问题

- int大数溢出的处理（在初始化时比如INT_MAX/2,这样如果把两个数加起来也不会溢出）

#### 打印结果和调试输出