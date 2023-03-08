### Trie树：

高效的**存储**或**查找**字符串集合的数据结构

```c++
#include <iostream>
using namespace std;

const int N = 100010;

int son[N][26]; // 表示每个节点并且孩子节点最多只有26个 每个节点的所有儿子
int index; // 表示当前节点移动到哪一个位置
int cnt[N]; // 表示一共有多少个以该单词结尾的字符串
char str[N];

void insert (char str[]) {
    int p = 0;
    for (int i  = 0; str[i]; i ++) {
        int u = str[i] - 'a'; // 先将要插入的字母映射成数字
        if (!son[p][u]) son[p][u] = ++ index; // 如果没有该点 则创建该点
        p = son[p][u]; // 将当前节点变为新创建的节点
    }
    
    cnt[p] ++; // 以p这个单词结尾的字符串的个数++
}

int query(char str[]) { // 查询操作
    int p = 0;
    for (int i = 0; str[i]; i ++) {
        int u = str[i] - 'a';
        if (!son[p][u]) return 0;
        p = son[p][u];
    }
    
    return cnt[p]; // 返回字符串在集合中的个数
}

int main() {
    char op[2];
    int n;
    scanf("%d", &n);
    while (n --) {
        scanf ("%s%s", op, str);
        if (op[0] == 'I') insert(str);
        else printf ("%d\n", query(str));
    }
    return 0;
}
```

#### 最大异或对

**贪心**+**trie树**

```C++
#include <iostream>
using namespace std;

const int N = 100010, M = 31 * N; // 100000个数每个数31位

int n;
int son[M][2]; // 最多存储M位，每一位一共有两种孩子(二进制0或1)
int index; // 表示当前移动到哪一个位置
int a[N]; // 存储每一个数

void insert(int x) {
    int p = 0;
    for (int i = 30; i >= 0; i --) { // 从大到小枚举每一位
        int u = x >> i & 1; // 取出十进制数的每一个二进制位
        if(!son[p][u]) son[p][u] = ++ index;
        p = son[p][u];
    }
}

int query(int x) {
    int p = 0, res = 0; 
    
    for (int i = 30; i >= 0; i --) { // 越高位异或越大 从后往前枚举
        int u = x >> i & 1; 
        // 二进制 每一个分支要么是0要么是1
        if (son[p][!u]) {
            p = son[p][!u]; // 如果有不同的分支 走到不同分支
            res = res * 2 + !u;
        } else { // 如果没有不同分支，就走相同分支
            p = son[p][u];
            res = res * 2 + u;
        }
    }
    return res;
}

int main() {
    scanf ("%d", &n);
    
    for (int i = 0; i < n; i ++) scanf ("%d", &a[i]);
    
    int res = 0;
    for (int i = 0; i < n; i ++) {
        insert(a[i]); // 先插入再查询，避免特判空树
        
        int t = query(a[i]);
        
        res = max(res, a[i] ^ t);
    }
    
    printf ("%d", res);
    
    return 0;
}
```

#### 最大异或和

**前缀和**+**贪心**+**滑动窗口**+**trie树**

前缀和也可结合位运算使用（只要是和的关系）

总结：只要题目中出现一段数的和则可以考虑前缀和

```C++
#include <iostream>
using namespace std;

const int N = 3100010;

int son[N][2]; // trie数
int index; // 表示当前下标
int s[N]; // 前缀和数组
int cnt[N]; // 以这个根为子树里有多少个点 用于删除和判断节点是否存在
int n, m;

void insert(int x, int v) {
    int p = 0; // 根节点
    for (int i = 30; i >= 0; i --) { // 枚举每一位
        
        int u = x >> i & 1; // 将x的每一位二进制取出
        
        if (!son[p][u]) son[p][u] = ++ index;
        
        p = son[p][u]; // 当前节点往前走
        cnt[p] += v; // 以p为根节点的子树中孩子个数+v
    }
}

int query(int x) { // 查询trie中与x异或最大的数
    int p = 0, res = 0; // res记录的是异或和
    for (int i = 30; i >= 0; i --) { 
        
        int u = x >> i & 1;
        
        if (cnt[son[p][!u]]) { // 如果不同的子树存在 则走向不同的那棵子树
            p = son[p][!u];
            res = res * 2 + 1; // 不同异或和为1
        } else {
            p = son[p][u];
            res *= 2;  // 相同异或和为0
        }
    }
    return res;
}

int main() {
    scanf ("%d%d", &n, &m);
    
    for (int i = 1; i <= n; i ++) { // 初始化前缀和数组
        scanf ("%d", &s[i]);
        s[i] ^= s[i - 1]; // 前缀和的意义在于 将一个区间上的异或和 转化为两个点之间的异或和
    }
    
    int res = 0; // 初始化结果 空数组的结果
    insert(s[0], 1); // 将前缀和第一个数插入trie树
    for (int i = 1; i <= n; i ++) { // 枚举r的值
        if (i - m - 1 >= 0) insert (s[i - m - 1], -1); // l的值为r-m-1 加入一个数就删除一个数 滑动窗口
        // 先查询再插入
        res = max(res, query(s[i])); // 更新异或和
        insert(s[i], 1); // 将s[i] 右端点的数加入trie树
    }
    printf ("%d", res);
    
    return 0;
}
```

### BFS与DFS

| 搜索方法 | 所用数据结构 | 空间复杂度 |      最短路      |
| :------: | :----------: | :--------: | :--------------: |
|   DFS    |    stack     |    O(h)    | 不具有最短路性质 |
|   BFS    |    queue     |   O(2^h)   |  具有最短路性质  |

### DFS

#### 全排列问题

```c++
#include <iostream>
using namespace std;

const int N = 10;

int n; 
int path[N]; // 记录当前要枚举的位置
bool st[N]; // 记录当前数字是否可选

void bfs(int u) { // 深搜枚举每一位 u 为当前枚举第几位
    
    if (u == n) { // 如果已经枚举了最后一位
        for (int i = 0; i < n; i ++) cout << path[i] << " "; // 输出全排列
        cout << endl;
        return;
    }
    
    for (int i = 1; i <= n; i ++) { // 从1开始枚举
        if (!st[i]) { // 如果这一位没有被枚举过
            path[u] = i; // 枚举这一位
            st[i] = true; // 将这意味数标记为已选
            
            bfs (u + 1); // 开始枚举下一位
            
            // 枚举完成后 回溯 恢复现场
            st[i] = false; 
        }
    }
}

int main() {
    cin >> n;
    
    bfs(0); // 从第0个位置开始枚举每一位
    
    return 0;
}
```

#### n皇后问题

##### 第一种枚举方式

按行或者按列枚举

使用3个bool变量进行不可行解的判断 (剪枝)

```C++
#include <iostream>
using namespace std;

const int N = 20; // 对角线个数是2n-1

char g[N][N]; // 表示棋盘
int n;
bool col[N], dg[N], udg[N]; // 按行枚举 则需要标记每一列，每一主对角线，副对角线是否可以放置皇后

void dfs(int u) {
    
    if (u == n) { // 说明已经枚举完最后一个皇后
        for (int i = 0; i < n; i ++) cout << g[i] << endl; // 对于每一个u都枚举一遍对应的行
        
        cout << endl;
        return;
    }
    
    for (int i = 0; i < n; i ++) { // 枚举每一行
        if (!col[i] && !dg[i + u] && !udg[n + i - u]) {
            g[u][i] = 'Q';
            col[i] = dg[i + u] = udg[n + i - u] = true;
            
            dfs (u + 1); // 枚举下一个皇后、
            
            col[i] = dg[i + u] = udg[n + i - u] = false;
            g[u][i] = '.';
        }
    }
}

int main() {
    cin >> n;
    
    // 初始化棋盘
    for (int i = 0; i < n; i ++) 
        for (int j = 0; j < n; j ++) 
            g[i][j] = '.';
    
    dfs (0); // 从第0个皇后开始枚举
    
    return 0;
}
```

##### 第二种枚举方式

对每一个格子进行枚举

```C++
#include <iostream>
using namespace std;

const int N = 20; // 对角线个数是2n-1

char g[N][N]; // 表示棋盘
int n;
bool row[N], col[N], dg[N], udg[N]; // 按行枚举 则需要标记每一列，每一主对角线，副对角线是否可以放置皇后

void dfs(int x, int y, int s) {
    if (y == n) y = 0, x ++; // 如果y超出数组范围，将y移到第一列，将x移动到下一行
    
    if (x == n) { // 说明枚举结束
        if (s == n) {// 如果用掉n个皇后说明是一组可行解
            for (int i = 0; i < n; i ++) cout << g[i] << endl;
            cout << endl;
        }
        return;
    }
    
    // 如果没有枚举完 此时有两种情况
    // 不放皇后
    dfs (x, y + 1, s);
    
    // 放皇后
    if (!row[x] && !col[y] && !dg[x + y] && !udg[x - y + n]) { // 如果每一行 每一列 每一个主对角线 每一个副对角线 都没有皇后
        g[x][y] = 'Q';
        row[x] = col[y] = dg[x + y] = udg[x - y + n] = true;
        
        dfs(x, y + 1, s + 1); // 枚举下一个位置
        
        // 恢复现场
        g[x][y] = '.';
        row[x] = col[y] = dg[x + y] = udg[x - y + n] = false;
    }
}

int main() {
    cin >> n;
    
    // 初始化棋盘
    for (int i = 0; i < n; i ++) 
        for (int j = 0; j < n; j ++) 
            g[i][j] = '.';
    
    dfs (0, 0, 0); // 从第0行第0列第0个皇后开始枚举
    
    return 0;
}
```

#### 不同路径数

```C++
#include <iostream>
#include <unordered_set>
using namespace std;

const int N = 10;

int n, m, k; 
int g[N][N]; // 存储二维矩阵
int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, 1, 0, -1}; // 记录 上右下左 偏移量 
unordered_set<int> S; // 创建哈希表 记录已经出现过的三位数

void dfs(int x, int y, int u, int num) {
    
    
    if (u == k) { // 说明走完了
        S.insert (num);
    } else {
        for (int i = 0; i < 4; i ++) { // 枚举上下左右四个方向
            int a = x + dx[i], b = y + dy[i];
            if (a >= 0 && a < n && b >= 0  && b < m) {
                dfs (a, b, u  + 1, num * 10 + g[a][b]);
            }
        }
    }
}

int main() {
    cin >> n >> m >> k;
    for (int i = 0; i < n; i ++) 
        for (int j = 0; j < m; j ++) {
            cin >> g[i][j];
        }
    
    for (int i = 0; i < n; i ++) 
        for (int j = 0; j < m; j ++) {
            dfs(i, j, 0, g[i][j]); // 现在的横坐标 现在的纵坐标 当前已经走了多少位 当前的数
        }
    
    cout << S.size() <<endl;
    
    return 0;
}
```

#### 小猫爬山

```c++
#include <iostream>
#include <algorithm>
using namespace std;

const int N = 20;

int n, m;
int cat[N], sum[N];
int ans = N; // 最多需要20辆车

void dfs (int u, int k) { // 第几只猫 当前可用几辆车
    if (k >= ans) return; // 如果大于20辆 直接返回
    
    if (u == n) { // 遍历到最后一只猫
        ans = k; // 更新答案
        return;
    }
    
    for (int i = 0; i < k; i ++) 
        if (cat[u] + sum[i] <= m) {
            sum[i] += cat[u];
            
            dfs (u + 1, k);
            
            // 恢复现场
            sum[i] -= cat[u];
        }
        
    // 新增一辆车 让装不下的猫单独坐这辆车
    sum[k] = cat[u];
    
    dfs (u + 1, k + 1);
    
    // 恢复现场
    sum[k] = 0;
}

int main() {
    cin >> n >> m;
    
    for (int i = 0; i < n; i ++) cin >> cat[i];
     
    // 从大到小排序 剪枝
    sort (cat, cat + n);
    reverse (cat, cat + n);
    
    dfs (0, 0);
    
    cout << ans << endl;
    
    return 0;
}
```

### BFS

#### 走迷宫

```C++
#include <iostream>
#include <algorithm>
#include <cstring>
using namespace std;

const int N = 110;

typedef pair<int, int> PII;

int n, m;
int g[N][N]; // 记录迷宫的数组
int d[N][N]; // 记录每个点到起点的距离 距离为-1表示这个点没有走过
PII q[N * N];

int bfs() {
    int hh = 0, tt = 0; //　hh队头 tt队尾
    q[0] = {0, 0}; //初始化队列
    
    memset(d, -1, sizeof d);
    d[0][0] = 0; // 表示队头走过
    
    int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, -1, 0, 1};
    
    while (hh <= tt) { // 队列不为空
        auto t = q[hh ++]; // 取出队头
        
        for (int i = 0; i < 4; i ++) { // 遍历四个方向
            int x = t.first + dx[i], y = t.second + dy[i]; 
            if (x >= 0 && x < n && y >= 0 && y < m && g[x][y] == 0 && d[x][y] == -1) {
                d[x][y] = d[t.first][t.second] + 1; // 到起点的距离+1
                q[++ tt] = {x, y}; // 将该点入队
            }
        }
    }
    
    return d[n - 1][m - 1]; // 返回右下角到起点的距离 即为到达终点的最短路径
}

int main() {
    cin >> n >> m;
    
    for(int i = 0; i < n; i ++)
        for(int j = 0; j < m; j ++)
            cin >> g[i][j];
    
    cout << bfs() << endl;
    
    return 0;
}
```
#### 八数码

一维坐标转二维坐标 ：`X = x / 矩阵阶数 Y = y / 矩阵阶数` ***（x,y是一维下标， X,Y是二维下标）***
```C++
#include <iostream>
#include <algorithm>
#include <unordered_map>
#include <queue>

using namespace std;

int bfs(string start) {
    queue<string> q; // bfs队列
    unordered_map<string, int> d;  // 记录当前点与起点的距离
    
    string end = "12345678x"; // 定义结束状态
    
    // 初始化队列与list
    q.push(start); // 将起点压入队列中
    d[start] = 0; // 起点距离起点距离为0
    
    while (!q.empty()) { // 队列不为空时
        auto t = q.front(); // 取出队头
        q.pop(); // 队头弹出
        
        int distance = d[t]; // 取出当前对头的距离
        if (t == end) return distance; // 队头为结束状态 直接返回
        
        // 状态转移
        int k = t.find("x"); // x在一维数组中的位置
        
        int x = k / 3, y = k % 3; // 转化为二维数组中的下标
        
        int dx[4] = {1, 0, -1, 0}, dy[4] = {0, -1, 0, 1};
        
        for (int i = 0; i < 4; i ++) { // 遍历上下左右四个方向
            int a = x + dx[i], b = y + dy[i];
            
            if (a >= 0 && a < 3 && b >= 0 && b < 3) { // 没有越界
                swap(t[k], t[3 * a + b]); // 交换两个数的位置 
                
                if (!d.count(t)) { // 如果这个点之前没有被搜到
                    d[t] = distance + 1; // 交换后t这个字符串进行的操作次数+1
                    q.push(t); // 将新字符串加入队列
                }
                
                swap(t[k], t[3 * a + b]); // 将两个数的位置交换回来
            }
        }
    }
    
    return -1; // 不存在解决方案
}

int main() {
    string start; // 初始状态
    for (int i = 0; i < 9; i ++) {
        char c;
        cin >> c;
        start += c;
    }
    
    cout <<bfs(start) <<endl;
    
    return 0;
}
```
### 图论

树可以看作是一种特殊的图， 有向无环图

图有两种存储方式：**邻接表**和**邻接矩阵**

##### 图的搜索与建立模板
``` C++
#include <iostream>
#include <cstring>
#include <algorithm>
using namepase std;

const int N = 100010, M = 2 * N;

int h[N]; // 存储n个链表的链表头
int e[M]; // 表示每个节点的值
int ne[M]; // 表示每个节点的next指针
int idx;
bool st[N]; // 记录是否被搜索过

void bfs(int u) {
    st[u] = true; // 标记u已经被搜索过了
    
    for (int i = h[u]; i != -1; i = ne[i]) { // 没有到空节点 就继续遍历
        int j = e[i]; // 记录该点的值
        if (!st[j]) bfs(j); // 递归搜索该点
    }
}

int add(int a, int b) { // 插入一条a到b的边
    // 先赋值
    e[idx] = b;
    // 再连接
    ne[idx] = h[a];
    h[a] = idx ++;
}

int main() {
    // 邻接表的初始化
    memset(h, -1, sizeof h); // 将每个头节点都指向-1
    
    bfs(1); 
}
```

##### 树的重心
```C++
#include <iostream>
#include <cstring>
#include <algorithm>
using namespace std;

const int N = 100010, M = 2 * N;

int n;
int h[N]; // 存储n个链表的链表头
int e[M]; // 表示每个节点的值
int ne[M]; // 表示每个节点的next指针
int idx;
bool st[N]; // 记录是否被搜索过
int ans = N; // 存储最终答案 最小的最大值

// 返回以u为根的子树中节点的数量
int bfs(int u) {
    st[u] = true; // 标记u已经被搜索过了
    
    int sum = 1; // 当前子树的大小 包括自己
    int res = 0; // 删除该点后每一个连通块中节点数量的最大值
    
    for (int i = h[u]; i != -1; i = ne[i]) { // 没有到空节点 就继续遍历
        int j = e[i]; // 记录该点的值
        if (!st[j]) {
            int s = bfs(j); // 递归搜索该点 s为以j为根节点子树的大小
            res = max(res, s);
            sum += s;
        }
    }
    
    // 与剩下的连通块数量取最大值
    res = max(res, n - sum);
    
    // 和答案取最小值
    ans = min(ans, res);
    
    return sum;
}

void add(int a, int b) { // 插入一条a到b的边
    // 先赋值
    e[idx] = b;
    // 再连接
    ne[idx] = h[a];
    h[a] = idx ++;
}

int main() {
    cin >> n;

    // 邻接表的初始化
    memset(h, -1, sizeof h); // 将每个头节点都指向-1
    
    for (int i = 0; i < n - 1; i ++) {
        int a, b;
        cin >> a >> b;
        add(a, b), add(b, a); // 无向图要加两条想向的边
    }
    
    bfs(1); 
    
    cout << ans <<endl;
    
    return 0;
}
````
##### 图中点的层次（图的BFS）
```C++
#include <iostream>
#include <algorithm>
#include <cstring>

using namespace std;

const int N = 100010;

int n, m;

int h[N], e[N], ne[N], idx;

int d[N], q[N];

int add(int a, int b) {
    e[idx] = b, ne[idx] = h[a], h[a] = idx ++;
}

int bfs() {
    int hh = 0, tt = 0;
    q[0] = 1;
    
    memset(d, -1, sizeof d);
    d[1] = 0;
    
    while (hh <= tt) {
        int t = q[hh ++];
        
        for (int i = h[t]; i != -1; i = ne[i]) {
            int j = e[i];
            if (d[j] == -1) {
                d[j] = d[t] + 1;
                q[++ tt] = j;
            }
        }
    }
    
    return d[n];
}

int main() {
    memset(h, -1, sizeof h);
    cin >> n >> m;
    while (m --) {
        int a, b;
        cin >> a >> b;
        add(a, b);
    }
    
    cout << bfs() << endl;
    
    return 0;
}
```
##### BFS框架 （数组模拟队列写法）
```C++
#include <iostream>
#include <algorithm>
#include <cstring>

using namespace std;

const int N = 100010;

int n, m;

int d[N]; // 每个节点到起点的距离
int q[N]; // 数组模拟的队列

int bfs() { // 深搜框架 返回起点到某个点的最短距离
    int hh = 0; // 队头指针
   	int tt = 0; // 队尾指针
    
    q[0] = 1; // 队列初始化 最开始只有起点
    memset(d, -1, sizeof d); // 距离数组初始化 每一个点到起点的距离最开始都为-1 也就是还不可到达
    d[1] = 0; // 初始化起点到起点的最短距离为0
    
    while (hh <= tt) { // 当队列不为空
        int t = q[hh ++]; // 取出对头元素
        
        for (int i = h[t]; i != -1; i = ne[i]) { // 可以一直往下走 这里为图的遍历
            int j = e[i]; // 取出当前遍历到的节点
            if (d[j] == -1) { // 如果当前节点还没有被遍历到
                d[j] = d[t] + 1; // 就访问这个节点 更新该点到起点的距离
                q[++ tt] = j; // 将该点入队
            }
        }
    }
    return d[n]; // 返回n点到起点的距离
}

int main() {
    cout << bfs() << endl;
    return 0;
}
```
#### 拓扑排序

**拓扑序列：对于每条边起点都在终点的前面**

存在**环**就一定不存在拓扑序

*有向无环图*一定存在一个拓扑序列, 所以有向无环图也被成为**拓扑图**

求拓扑序列：一个有向无环图，则至少存在一个入度为0的点
```C++
// 伪代码
将所有入度为0的点入队
while (队不空) {
	t = 对头;
    枚举t的所有出边 j;
    删掉t->j 的这条边;
    j的入度 - 1;
    if (j的入度 == 0) {
        将j入队;
    }
}
```
一个有向无环图的拓扑序不唯一

##### 有向图的拓扑序列
```C++
#include <iostream>
#include <algorithm>
#include <cstring>

using namespace std;

const int N =100010;

int n, m;

int h[N], e[N], ne[N], idx;

int q[N], d[N]; // d保存入度

void add(int a, int b) {
    e[idx] = b, ne[idx] = h[a], h[a] = idx ++;
}

bool topsort() {
    int hh = 0, tt = -1;
    
    // 将所有入度为0的点入队
    for (int i = 1; i <= n; i ++) { // 编号从1~n
        if(!d[i])
            q[++ tt] = i;
    }
    
    while (hh <= tt) {
        int t = q[hh ++];  // 取对头
        
        for (int i = h[t]; i != -1; i = ne[i]) { // 遍历出边
            int j = e[i]; // 取每一条边
            d[j] -- ; // 删掉t->j的边 将j的入度-1
            if (d[j] == 0) q[++ t] = j; // 如果j的入度为0 将j入队
        }
    }
    
    return tt == n - 1; // 如果所有边都入队 则存在拓扑序列
}

int main () {
    cin >> n >> m;
    
    memset(h, -1, sizeof h);
    
    while (m --) {
        int a, b;
        cin >>a >> b;
        add (a, b);
        d[b] + 1; // 每次插入一条a到b的边 b的入读都+1
    }
    
    if (topsort()) {
        for (int i = 0; i < n; i ++) printf ("%d ", q[i]);
        puts("");
    } else {
        puts ("-1");
    }
    
    return 0;
}
```
### 基础数据结构

链表 (算法题中一般用静态链表)

数组模拟链表 以及链表基本操作（头插法、插入元素、删除元素）
```C++
#include <iostream>
using namespace std;

const int N = 100010;

// int head; // 表示头节点的下标 指向头节点
// int e[i]; // 表示节点i的值
// int ne[i]; // 表示节点i的下一个节点的下标
// int idx; // 存储当前已经用到的点
int head, e[N], ne[N], idx;

void init() {
    head = -1; // 头节点指向空
    idx = 0; // 表示可以从0号点开始分配
}

// 将x插入到头节点 头插法
void add_to_head(int x) {
    e[idx] = x;
    ne[idx] = head;
    head = idx;
    idx ++;
}

// 将x插到下标为k的点的下一个点
void add(int k, int x) {
    e[idx] = x;
    ne[idx] = ne[k];
    ne[k] = idx;
    idx ++;
}

// 将下标是k的点后面一个点删除
void remove(int k) {
    ne[k] = ne[ne[k]]; // 将k指向k下一个节点的下一个节点
}
```
##### 单链表
```C++
#include <iostream>
using namespace std;

const int N = 100010;

// int head; // 表示头节点的下标 指向头节点
// int e[i]; // 表示节点i的值
// int ne[i]; // 表示节点i的下一个节点的下标
// int idx; // 存储当前已经用到的点
int head, e[N], ne[N], idx;

int m;

void init() {
    head = -1; // 头节点指向空
    idx = 0; // 表示可以从0号点开始分配
}

// 将x插入到头节点 头插法
void add_to_head(int x) {
    e[idx] = x;
    ne[idx] = head;
    head = idx ++;
}

// 将x插到下标为k的点的下一个点
void add(int k, int x) {
    e[idx] = x;
    ne[idx] = ne[k];
    ne[k] = idx ++;
}

// 将下标是k的点后面一个点删除
void remove(int k) {
    ne[k] = ne[ne[k]]; // 将k指向k下一个节点的下一个节点
}

int main() {
    cin >> m;
    int x, k; // 题目中的k为位序
    char op;
    
    init();
    
    while (m --) {
        cin >> op;
        
        if (op == 'H') {
            cin >> x;
            add_to_head(x);
            
        } else if (op == 'I') {
            cin >> k >> x;
            add(k - 1, x);
            
        } else {
            cin >> k;
            if (!k) head = ne[head];
            remove(k - 1);
            
        }
    }
    
    for (int i = head; i != -1; i = ne[i]) cout << e[i] << " ";
    cout << endl;
    return 0;
}
```

