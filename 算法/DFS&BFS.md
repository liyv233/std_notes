# vector

- vector不定长数组
- 添加头文件`#include <vector>`

**初始化：**

~~~c++
//定义具有10个整型元素的向量（尖括号为元素类型名，它可以是任何合法的数据类型），不具有初值，其值不确定
vector<int>a(10);
//定义具有10个整型元素的向量，且给出的每个元素初值为1
vector<int>a(10,1);
//用向量b给向量a赋值，a的值完全等价于b的值
vector<int>a(b);
//将向量b中从0-2（共三个）的元素赋值给a，a的类型为int型
vector<int>a(b.begin(),b.begin+3);
 //从数组中获得初值
int b[7]={1,2,3,4,5,6,7};
vector<int> a(b,b+7）;
~~~



**vector对象的常用内置函数使用**

~~~c++
#include<vector>
vector<int> a,b;
//b为向量，将b的0-2个元素赋值给向量a
a.assign(b.begin(),b.begin()+3);
//a含有4个值为2的元素
a.assign(4,2);
//返回a的最后一个元素
a.back();
//返回a的第一个元素
a.front();
//返回a的第i元素,当且仅当a存在
a[i];
//清空a中的元素
a.clear();
//判断a是否为空，空则返回true，非空则返回false
a.empty();
//删除a向量的最后一个元素
a.pop_back();
//删除a中第一个（从第0个算起）到第二个元素，也就是说删除的元素从a.begin()+1算起（包括它）一直到a.begin()+3（不包括它）结束
a.erase(a.begin()+1,a.begin()+3);
//在a的最后一个向量后插入一个元素，其值为5
a.push_back(5);
//在a的第一个元素（从第0个算起）位置插入数值5,
a.insert(a.begin()+1,5);
//在a的第一个元素（从第0个算起）位置插入3个数，其值都为5
a.insert(a.begin()+1,3,5);
//b为数组，在a的第一个元素（从第0个元素算起）的位置插入b的第三个元素到第5个元素（不包括b+6）
a.insert(a.begin()+1,b+3,b+6);
//返回a中元素的个数
a.size();
//返回a在内存中总共可以容纳的元素个数
a.capacity();
//将a的现有元素个数调整至10个，多则删，少则补，其值随机
a.resize(10);
//将a的现有元素个数调整至10个，多则删，少则补，其值为2
a.resize(10,2);
//将a的容量扩充至100，
a.reserve(100);
//b为向量，将a中的元素和b中的元素整体交换
a.swap(b);
//b为向量，向量的比较操作还有 != >= > <= <
a==b;
~~~



**顺序访问 vector 的几种方式：**

~~~cpp
// 向向量a中添加元素
vector<int>a;
for(int i=0;i<10;++i){a.push_back(i);}

//从现有向量中选择元素向向量中添加
int a[6]={1,2,3,4,5,6};
vector<int>b;
vector<int>c(a,a+4);
for(vector<int>::iterator it=c.begin();it<c.end();++it)
{
	b.push_back(*it);
}

//通过下标方式获取元素
int a[6]={1,2,3,4,5,6};
vector<int>b(a,a+4);
for(int i=0;i<=b.size()-1;++i){cout<<b[i]<<endl;}

// 通过迭代器方式读取元素
 int a[6]={1,2,3,4,5,6};
 vector<int>b(a,a+4);
 for(vector<int>::iterator it=b.begin();it!=b.end();it++){cout<<*it<<"  ";}

~~~



**常用算法：**

~~~cpp
 #include<algorithm>
 //对a中的从a.begin()（包括它）到a.end()（不包括它）的元素进行从小到大排列
 sort(a.begin(),a.end());
 //对a中的从a.begin()（包括它）到a.end()（不包括它）的元素倒置，但不排列，如a中元素为1,3,2,4,倒置后为4,2,3,1
 reverse(a.begin(),a.end());
  //把a中的从a.begin()（包括它）到a.end()（不包括它）的元素复制到b中，从b.begin()+1的位置（包括它）开始复制，覆盖掉原有元素
 copy(a.begin(),a.end(),b.begin()+1);
 //在a中的从a.begin()（包括它）到a.end()（不包括它）的元素中查找10，若存在返回其在向量中的位置
  find(a.begin(),a.end(),10);
~~~





# 栈

**特点：**

- 先进后出
- 限定只能在栈顶进行插入和删除操作

**相关概念：**

- 栈顶与栈底：允许元素插入与删除的一端称为栈顶，另一端称为栈底。
- 压栈：栈的插入操作，叫做进栈，也称压栈、入栈
- 弹栈：栈的删除操作，也叫做出栈

**常用操作：**

~~~cpp
导入头文件
#include< stack.h > 
 
stack<int> s;

s.empty();         //如果栈为空则返回true, 否则返回false;
s.size();          //返回栈中元素的个数
s.top();           //返回栈顶元素, 但不删除该元素
s.pop();           //弹出栈顶元素, 但不返回其值
s.push();          //将元素压入栈顶
~~~



# 队列

**特点：**

- 先进先出
- 只能在容器末尾添加新元素，在容器头部删除元素

~~~cpp
#include <iostream>
#include <queue>
using namespace std;

// queue 队列生成
std::queue<int> numbers;

// 在队列队尾添加一个元素 push
numbers.push(1);
// 获取第一个元素 front() ,front()：返回 queue 中第一个元素的引用
numbers.front()
// 获取最后一个元素 back() ,返回 queue 中最后一个元素的引用
 numbers.back()
// size()：返回 queue 中元素的个数
numbers.size()
// pop(): 删除 queue 中的第一个元素
numbers.pop();
// empty()：如果 queue 中没有元素的话，返回 true
numbers.empty()

// 交换两个队列
// swap()函数用于交换两个队列的内容，但是队列的类型必须相同，尽管大小可能有所不同
numbers.swap(nus);

// 遍历队列
// queue 也没有迭代器。访问元素的唯一方式是遍历容器内容，并移除访问过的每一个元素。
while (!nus.empty()){
    std::cout << nus.front() << std::endl;
    nus.pop();
}
~~~



# 二叉树



## 深度优先遍历





# DFS

深度优先搜索，保存本次的位置并深入搜索，都搜索完便回溯回来，搜下一个位置，直到把所有最深位置都搜一遍，记录走过的位置，标记完后可能要改回来。



**DFS用来搜索全部的解。**

在记录路径的时候会简单一点，只需要把每一次找的点，放进去答案中就好；并且，相对而言dfs在做很多题目可以用上，比如分治、数位dp，其实就是递归，而dfs用的就比较少。



dfs和bfs大都运用在图的处理上：如迷宫，八皇后，n皇后，油田，连通块，数独等，在这些类型的题上几乎全需要dfs或bfs来解，或者说有些难度更大的需要bfs+dfs或bfs+bfs才能完成。

模板：

~~~
DFS(dep,...) {
	if(找到解 | 走不下去) {
		...
		return
	}
	枚举下一种情况：DFS(dep+1,...)
}
~~~

~~~js
你有一个用于表示一片土地的整数矩阵land，该矩阵中每个点的值代表对应地点的海拔高度。
若值为 0 则表示水域 。 由垂直，水平或对角线的水域为池塘。池塘的大小是指相连接的水域个数。
编写一个方法来计算矩阵中所有池塘的大小，返回值需要从小到大排序。

示例：
输入 ：
[ [0,2,1,0],[0,1,0,1],[1,1,0,1],[0,1,0,1] ]
输出：
[1,2,4]

class Solution {
public:
    vector<int> pondSizes(vector<vector<int>>& land) {
        
        vector<int> res;
        for(int i = 0; i < land.size(); i++){
            for(int j = 0; j < land[0].size(); j++){
                int count = 0;
                if(land[i][j] == 0){
                    dfs(i, j, land, count);
                    if(count != 0)
                        res.push_back(count);
                }
            }
        }
        sort(res.begin(), res.end());
        return res;
 
    }
 
    void dfs(int x, int y, vector<vector<int>> &land, int &count){
        land[x][y] = -1;
        count++;
        int dirx[8] = {0,0,1,1,1,-1,-1,-1};
        int diry[8] = {1,-1,1,-1,0,1,-1,0};
        for(int i = 0; i < 8; i++){
            int x1 = x + dirx[i];
            int y1 = y + diry[i];
            if(x1 >= 0 && x1 < land.size() && y1 >= 0 && y1 < land[0].size() && land[x1][y1] == 0){
                dfs(x1, y1, land, count);
            }
        }
    }
 
};

~~~





# BFS

它的思想是从一个被选定的点出发；然后从这个点的所有方向每次只走一步的走到底

运用队列和函数内循环构造



**BFS用来搜索最短路径的解法是比较合适的。**

如求最少步数的解，最少交换次数的解，最快走出迷宫等等，因为bfs搜索过程中遇到的第一个解一定是离最初位置最近的，所以遇到第一个解，一定就是最优解，此时搜索算法可以终止。

模板：

~~~
通常用队列(先进先出，FIFO)实现
初始化队列Q
Q = {起点s}，标记s为已经访问
while(Q非空) {
	取Q队首元素u，u出列
	if(u == 目标状态) { ... }
	所有与 u 相邻且未被访问的点进入队列
	标记 u 为已访问
}
~~~



~~~js

int visit[N][N]//用来记录走过的位置
int dir[4][2]={0,-1,0,1,-1,0,1,0};
struct node
{
	int x,y,bits;//一般是点，还有步数，也可以存其他的
};
queue<node>v;
void bfs1(node p)
{
	node t,tt;
	v.push(p);
	while(!v.empty())
	{
		t=v.front();//取出最前面的
		v.pop();//删除
		if(找到符合条件的)
		{
			做记录；
			while(!v.empty()) v.pop();//如果后面还需要用，随手清空队列
			return;
		}
		visit[t.x][t.y]=1;//走过的进行标记，以免重复
		rep(i,0,4)//做多次查找
		{
			tt=t;
			tt.x+=dir[i][0];tt.y+=dir[i][1];//这里的例子是向上下左右查找的
			if(如果这个位置符合条件)
			{
				tt.bits++;//步数加一
				v.push(tt); //把它推入队列，在后面的时候就可以用了
			}
		}
	}


~~~



# 动态规划

给定一个问题，我们把它拆成一个个子问题，直到子问题可以直接解决。然后把子问题答案保存起来，以减少重复计算。再根据子问题答案反推，得出原问题解的一种方法。

一般这些子问题很相似，可以通过函数关系式递推出来。动态规划就致力于解决每个子问题一次，减少重复计算

如斐波那契数列就可以看做入门级的经典动态规划问题



**基本步骤：**

- 1，定义**数组元素的含义**，我们常会用一个数组，来保存历史数组，假设用一维数组 dp[] 。这个时候有一个非常非常重要的点，就是规定你这个数组元素的含义，例如你的 dp[i] 是代表什么意思？
- 2，找出**数组元素之间的关系式**，我觉得动态规划，还是有一点类似于我们高中学习时的**归纳法**的，当我们要计算 dp[n] 时，是可以利用 dp[n-1]，dp[n-2]…..dp[1]，来推出 dp[n] 的，也就是可以利用**历史数据**来推出新的元素值，所以我们要找出数组元素之间的关系式，例如 dp[n] = dp[n-1] + dp[n-2]，这个就是他们的关系式了。
- 3，找出**初始值**。学过**数学归纳法**的都知道，虽然我们知道了数组元素之间的关系式，例如 dp[n] = dp[n-1] + dp[n-2]，我们可以通过 dp[n-1] 和 dp[n-2] 来计算 dp[n]，但是，我们得知道初始值啊，例如一直推下去的话，会由 dp[3] = dp[2] + dp[1]。而 dp[2] 和 dp[1] 是不能再分解的了，所以我们必须要能够直接获得 dp[2] 和 dp[1] 的值，而这，就是**所谓的初始值**。





## 简单的一维 DP

问题描述：一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级的台阶总共有多少种跳法



- 1：定义**数组元素的含义** ：，我们的问题是要求青蛙跳上 n 级的台阶总共由多少种跳法，那我们就定义 dp[i] 的含义为：**跳上一个 i 级的台阶总共有 dp[i] 种跳法**。这样，如果我们能够算出 dp[n]，不就是我们要求的答案吗？
- 2：**找出数组元素间的关系式**：dp[n] = dp[n-1] + dp[n-2]。（跳一级+跳两级）
- 3：**找出初始条件**：dp[1] = 1 ， dp[0] = 0. ，dp[2] = 2



~~~js
int f( int n ){
    if(n <= 1)
    return n;
    // 先创建一个数组来保存历史数据
    int[] dp = new int[n+1];
    // 给出初始值
    dp[0] = 0;
    dp[1] = 1;
    dp[2] = 2 ；
    // 通过关系式来计算出 dp[n]
    for(int i = 3; i <= n; i++){
        dp[i] = dp[i-1] + dp[i-2];
    }
    // 把最终结果返回
    return dp[n];
}
~~~



## 二维数组的 DP

**问题描述：**

一个机器人位于一个 m x n 网格的左上角 。（m<100,n<100)

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角

问总共有多少条不同的路径？



- **定义数组元素的含义:**  当机器人从左上角走到(i, j) 这个位置时，一共有 dp[i] [j] 种路径。那么，dp[m-1] [n-1] 就是我们要的答案了。

- **找出关系数组元素间的关系式**:  dp[i] [j] = dp[i-1] [j] + dp[i] [j-1]。(向下走+向右走)

- **找出初始值：**

  dp[0] [0….n-1] = 1; // 相当于最上面一行，机器人只能一直往左走

  dp[0…m-1] [0] = 1; // 相当于最左面一列，机器人只能一直往下走



~~~js
public static int uniquePaths(int m, int n) {
    if (m <= 0 || n <= 0) {
        return 0;
    }
 
    int[][] dp = new int[m][n]; // 
      // 初始化
      for(int i = 0; i < m; i++){
      dp[i][0] = 1;
    }
      for(int i = 0; i < n; i++){
      dp[0][i] = 1;
    }
        // 推导出 dp[m-1][n-1]
    for (int i = 1; i < m; i++) {
        for (int j = 1; j < n; j++) {
            dp[i][j] = dp[i-1][j] + dp[i][j-1];
        }
    }
    return dp[m-1][n-1];
}
~~~





**问题描述：**

给定一个包含非负整数的 *m* x *n* 网格，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。

每次只能向下或者向右移动一步。



## 01背包问题

有 N 件物品和一个容量是 V 的背包。每件物品只能使用一次。

第 i 件物品的体积是 vi，价值是 wi。

求解将哪些物品装入背包，可使这些物品的总体积不超过背包容量，且总价值最大。
输出最大价值。

**输入格式**:

第一行两个整数，N，V，用空格隔开，分别表示物品数量和背包容积。

接下来有 N 行，每行两个整数 vi,wi，用空格隔开，分别表示第 i 件物品的体积和价值。

**输出格式**:

输出一个整数，表示最大价值。

**数据范围:**

0<N,V≤10000
0<vi,wi≤10000

**输入样例:**

```
4 5
1 2
2 4
3 4
4 5
```

**输出样例：**

```
8
```

法一：暴力二维dp

- f [i] [j] 表示只看前 i 个物品，总体积是 j 的情况下 ， 总价值最大是多少
  - res = max{f[n] [0~V]}
- 第 i 个物品可能的状态：（递推公式/状态转换）
  - 1：不选第 i 个物品，f[i] [j] =  f[i-1] [j]
  - 2:  选第 i 个物品： f[i] [j] = f[i-1] [j - v[i]] 
- f [i] [j] = max{情况1，情况2} 

- 初始化：f[0] [0] = 0

~~~cpp
#include<iostream>
#include<cstring>
#include<algorithm>

using namespace std ;

const int N = 1010 ;

int n,m;
int f[N][N] ;
int v[N],w[N] ;

int main() 
{
    cin>> n >> m ;
    for(int i = 1 ; i <= n ;i++) cin>> v[i] >> w[i] ;
    for(int i = 1 ; i <= n ;i++)
    {
        for(int j = 0 ; j <= m ;j++) 
        {
            f[i] [j] =  f[i-1] [j] // 不选
                if(j >= v[i])
                     f[i] [j] = max(f[i] [j] , f[i-1] [j - v[i]] + w[i])
        }
    }
    
    int res = 0 ;
    for(int i = 0 ; i <= V ; i++) res = max(res,f[n][i]) ;
    cout<<res<<endl ;
    return 0 ;
}

~~~



**优化代码：一维数组表示**

~~~cpp
#include<iostream>
#include<cstring>
#include<algorithm>

using namespace std ;

const int N = 1010 ;

int n,m;
int f[N] ; //表示体积是 j 的情况下，最大价值是多少
int v[N],w[N] ;

int main() 
{
    cin>> n >> m ;
    for(int i = 1 ; i <= n ;i++) cin>> v[i] >> w[i] ;
    
    for(int i = 1 ; i <= n ;i++)
    {
        for(int j = m ; j >= v[i] ;j--) 
        {
            f[j] = max( f[j] , f[j - v[i]] + w[i])
        }
    }
    
    cout<<f[m]<<endl ;
    return 0 ;
}
~~~



## 完全背包问题：

有 N 件物品和一个容量是 V 的背包。每种物品都有无限件可用。

第 i 件物品的体积是 vi，价值是 wi。

求解将哪些物品装入背包，可使这些物品的总体积不超过背包容量，且总价值最大。
输出最大价值。

**输入格式**:

第一行两个整数，N，V，用空格隔开，分别表示物品数量和背包容积。

接下来有 N 行，每行两个整数 vi,wi，用空格隔开，分别表示第 i 件物品的体积和价值。

**输出格式**:

输出一个整数，表示最大价值。

**数据范围:**

0<N,V≤10000
0<vi,wi≤10000

**输入样例:**

```
4 5
1 2
2 4
3 4
4 5
```

**输出样例：**

```
10
```



- f[i] [j] 表示只看前 i 个物品，总体积是 j 的情况下 ， 总价值最大是多少
- 对第 i 个物体 状态转移：
  - 1：不选 ：f[i] [j] = f[i-1] [j] 
  - 2：选：f[i] [j] = f[i-1] [j-v[i]] + w[i] 
- 初始化：f[N] [N] = 0







































































