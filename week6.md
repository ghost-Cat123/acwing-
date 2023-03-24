## 背包问题（在背包不超过背包容量的情况下，装入物品价值最大）
### 01背包问题
Dp问题分析方法：
Dp
* 状态表示f(i,j)
  * 集合
    * 所有选法
    * 条件
      1. 只从前i个物品中选
      2. 总体积<=j
  * 属性 
    1. 最大值(最大价值)
    2. 最小值(重量最小)
    3. 数量
* 状态计算
  * 集合划分
  * 将集合分成若干类**不重不漏表示**上述集合
    1. 不包含i f(i,j)=f(i-1,j)
    2. 包含i f(i,j)=f(i-1,j-vi)+wi
  * 问题的解就为**Max(f(i-1,j),f(i-1,j-vi)+wi)**
#### 01背包问题
特点：每件物品最多用一次(选或不选)
朴素二维数组做法：
```C++
#include <iostream>
using namespace std;

const int N = 1010;

int n, m;
int v[N], w[N]; // v体积 w价值
int f[N][N];

int main() {
    cin >> n >> m;
    
    for (int i = 1; i <= n; i ++) cin >> v[i] >> w[i];
    
    // f[0][0~m] = 0 表示从0件物品中选 重量不超过0~m 全局变量不用初始化
    for (int i = 1; i <= n; i ++) // 枚举价值
        for (int j = 0; j <= m; j ++) { // 枚举重量
            f[i][j] = f[i - 1][j]; // 不选第i件物品
            if (j >= v[i]) f[i][j] = max(f[i][j], f[i - 1][j - v[i]] + w[i]);
        }
        
    cout << f[n][m] << endl;
    
    return 0;
}
```
一维数组优化版：
```C++
#include <iostream>
using namespace std;

const int N = 1010;

int n, m;
int v[N], w[N]; // v体积 w价值
int f[N];

int main() {
    cin >> n >> m;
    
    for (int i = 1; i <= n; i ++) cin >> v[i] >> w[i];
    
    // f[0][0~m] = 0 表示从0件物品中选 重量不超过0~m 全局变量不用初始化
    for (int i = 1; i <= n; i ++) {
        for (int j = m; j >= v[i]; j --) // 枚举重量
            f[j] = max(f[j], f[j - v[i]] + w[i]);
            // 如果从小到大枚举计算的是 f[i][j] = max(f[i][j], f[i][j - v[i]] + w[i]);
    }
    
    cout << f[m] << endl;
    
    return 0;
}
```

### 完全背包问题
特点：每件物品可以用多次
分析方法同01背包问题
**状态计算上的区别**
01背包的集合被分成2份，而完全背包被分成了k份 

*曲线救国*
1. 去掉k个物品i
2. 求Max, f(i-1, j-k*vi)
3. 再加回来k个物品i
最终状态转移方程为：`f(i,j)=f(i-1,j-k*vi+wi*k)`
朴素做法(会TLE)
```C++
#include <iostream>
using namespace std;

const int N = 1010;

int n, m;
int v[N], w[N];
int f[N][N];

int main() {
    cin >> n >> m;
    
    for (int i = 1; i <= n; i ++) cin >> v[i] >> w[i];
    
    for (int i = 1; i <= n; i ++) 
        for (int j = 0; j <= m; j ++) 
            for (int k = 0; k * v[i] <= j; k ++) 
                f[i][j] = max(f[i][j], f[i - 1][j - k * v[i]] + w[i] * k);
                
    cout << f[n][m] << endl;
    
    return 0;
}
```
**完全背包问题优化**

`f(i,j)=Max(f(i-1, j), f(i-1, j-v)+w, f(i-1, j-2v)+2w, f(i-1, j-3v)+3w, ……) `

`f(i,j-v)=Max(         f(i-1, j-v),   f(i-1, j-2v)+w,  f(i-1, j-3v)+2w, ……)` 

Max(f(i-1, j-v)+w, f(i-1, j-2v)+2w, f(i-1, j-3v)+3w, ……) = Max(f(i-1, j-v),f(i-1, j-2v)+w,f(i-1, j-3v)+2w, ……)+w = f(i,j-v)+w 

则状态转移方程可优化为：**f(i,j) = Max(f(i-1, j), f(i, j-v)+w)**
优化到两重循环：
```C++
#include <iostream>
using namespace std;

const int N = 1010;

int n, m;
int v[N], w[N];
int f[N][N];

int main() {
    cin >> n >> m;
    
    for (int i = 1; i <= n; i ++) cin >> v[i] >> w[i];
    
    for (int i = 1; i <= n; i ++) 
        for (int j = 0; j <= m; j ++) {
            f[i][j] = f[i - 1][j];
            if (j >= v[i])
                f[i][j] = max(f[i][j], f[i][j - v[i]] + w[i]);
        }
                
    cout << f[n][m] << endl;
    
    return 0;
}
```
优化到一维数组：
```C++
#include <iostream>
using namespace std;

const int N = 1010;

int n, m;
int v[N], w[N];
int f[N];

int main() {
    cin >> n >> m;
    
    for (int i = 1; i <= n; i ++) cin >> v[i] >> w[i];
    
    for (int i = 1; i <= n; i ++) 
        for (int j = v[i]; j <= m; j ++) {
            f[j] = max(f[j], f[j - v[i]] + w[i]); // 这里就不会出现01背包的问题
        }
                
    cout << f[m] << endl;
    
    return 0;
}
```
例题：
整数拆分：
该问题可以转化为完全背包问题
整数n就为背包容量
没一个2的次幂就是一个物品，数值就是体积，每个物品都有无限个
2^0 v=1
2^1 v=2
2^2 v=4
……  ……
集合：从前i个物品中选，且总体积恰好等于j的所有方案的集合

二维版本(会MLE)
```C++
#include <iostream>
using namespace std;

const int N = 21, M = 1000010, MOD = 1e9;

int m; // 体积
int f[N][N];

int main() {
    scanf("%d", &m);
    
    f[0][0] = 1;
    
    int n = 0; // 定义物品数量
    
    for (int i = 1, v = 1; v <= m; i ++, v *= 2) { // v为当前体积
        n ++;
        for (int j = 0; j <= m; j ++) {
            f[i][j] = f[i - 1][j]; // 不选第i个物品
            if (j >= v) f[i][j] = (f[i][j] + f[i][j - v]) % MOD;
        } 
    }
    
    printf ("%d", f[n][m]);
    
    return 0;
}
```

一维优化版本：
```C++
#include <iostream>
using namespace std;

const int N = 21, M = 1000010, MOD = 1e9;

int m; // 体积
int f[M];

int main() {
    scanf("%d", &m);
    
    f[0] = 1;
    
    for (int i = 1; i <= m; i *= 2)  // v为当前体积
        for (int j = i; j <= m; j ++) f[j] = (f[j] + f[j - i]) % MOD;
    
    printf ("%d\n", f[m]);
    
    return 0;
}
```
注意：
一维：
` for (int i = 1, v = 1; v <= m; i ++, v *= 2) { // v为当前体积
        n ++;
        for (int j = v; j <= m; j ++) f[j] = (f[j] + f[j - v]) % MOD;
    }
 `
 二维:
 `
 for (int j = 0; j <= m; j ++) {
            f[i][j] = f[i - 1][j]; // 不选第i个物品
            if (j >= v) f[i][j] = (f[i][j] + f[i][j - v]) % MOD;
        } 
`
由于在算`f[j] = (f[j] + f[j - v]) % MOD;`时右边的f\[j\]还没有被算过所以还是上一层的f\[i-1\]\[j\]，
f\[j - v\]由于是从小到大枚举j所以f\[j - v\]在f\[j\]之前以及被算过所以算的是f\[i\]\[j\]符合状态转移方程

### 多重背包问题
特点：每件物品数量不同
状态转移方程(和完全背包问题类似)：

`f(i, j) = Max(f(i - 1, j), f(i - 1, j - k * v[i]) + k * w[i]) k = 0、1、2……s[i]`

朴素做法O(n^3)(数据大于1000会TLE)：
```C++
#include <iostream>
using namespace std;

const int N = 110;

int n, m;
int v[N], w[N], s[N];
int f[N][N];

int main() {
    cin >> n >> m;
    
    for (int i = 1; i <= n; i ++) cin >> v[i] >> w[i] >> s[i];
    
    for (int i = 1; i <= n; i ++)
        for (int j = 0; j <= m; j ++)
            for (int k = 0; k <= s[i] && k * v[i] <= j; k ++) { // 枚举每件物品的个数
                f[i][j] = max(f[i][j], f[i - 1][j - k * v[i]] + w[i] * k);
            }
            
    cout << f[n][m];
    
    return 0;
}
```
二进制优化O(n * m * logn) **将s件物品拆分成logs组物品，每一组选或不选**:
```C++
#include <iostream>
using namespace std;

const int N = 25000; // 一共2000件物品，每种物品拆分成logn组，一共25000个物品
int n, m;
int v[N], w[N], s[N];
int f[N];

int main() {
    cin >> n >> m;
    
    int cnt = 0;
    for (int i = 1; i <= n; i ++) {
        int a, b, s;
        cin >> a >> b >> s;
        
        // 开始拆分
        int k = 1;
        while (k <= s) { // k = 1、2、4、6、8、16、2^n;
            cnt ++;
            v[cnt] = k * a; // 每一组的体积
            w[cnt] = k * b; // 每一组的价值
            s -= k;
            k *= 2;
        }
        if (s > 0) { // 还没有分完 将剩下的全部作为1组
            cnt ++;
            v[cnt] = s * a;
            w[cnt] = s * b;
        }
    }
    
    n = cnt; // 更新物品数量为组的数量
    
    // 多重背包变为了01背包问题 每一组选或不选
    // 一维数组优化
    for (int i = 1; i <= n; i ++)
        for (int j = m; j >= v[i]; j --)
            f[j] = max(f[j], f[j - v[i]] + w[i]);
            
    cout << f[m] << endl;
    
    return 0;
}
```

### 分组背包问题
特点：物品有n组，每组有若干种，每一组只能选一种

集合划分：
不选第i组的物品：f(i-1, j)
选第i组中的第k个物品：f(i-1, j-v\[i,k\]+w\[i,k\])

代码：
```C++
#include <iostream>
using namespace std;

const int N = 110;

int n, m;
int v[N][N], w[N][N], s[N];
int f[N];

int main() {
    cin >> n >> m;
    
    for (int i = 1; i <= n; i ++) {
        cin >> s[i];
        for (int j = 0; j < s[i]; j ++) {
            cin >> v[i][j] >> w[i][j];
        }
    }
    
    for (int i = 1; i <= n; i ++)
        for (int j = m; j >= 0; j --)  // 需要用到上一层 j从大到小枚举 
            for (int k = 0; k < s[i]; k ++)
                if (v[i][k] <= j) // 第i组物品中第k个物品的体积 小于当前背包容量
                    f[j] = max(f[j], f[j - v[i][k]] + w[i][k]); // 同01背包问题
    
    cout << f[m] <<endl;
    
    return 0;
}
```
## 线性DP
### 数字三角形
```C++
#include <iostream>
using namespace std;

const int N = 510, INF = 1e9;

int n;
int a[N][N];
int f[N][N]; // f[i][j] 表示从起点开始到a[i][j]的路径的集合

int main() {
    scanf ("%d", &n);
    
    for (int i = 1; i <= n; i ++)
        for (int j = 1; j <= i; j ++)
            scanf ("%d", &a[i][j]);
            
    // 初始化集合
    for (int i = 0; i <= n; i ++)
        for (int j = 0; j <= i + 1; j ++) 
            f[i][j] = -INF;
            
    f[1][1] = a[1][1]; // 第1个点到起点的路径就是他自己
    
    // 状态计算
    for (int i = 2; i <= n; i ++) // f[1][1]已经算过 从f[2][j]开始计算
        for (int j = 1; j <= i; j ++) 
            f[i][j] = max(f[i - 1][j - 1] + a[i][j], f[i - 1][j] + a[i][j]);
    
    int res = -INF;
    
    for (int i = 1; i <= n; i ++) res = max(res, f[n][i]);
    
    printf ("%d", res);
    
    return 0;
}
```

