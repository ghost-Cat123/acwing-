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
特点：每件物品最多用一次(选或不选)
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
### 多重背包问题
特点：每件物品数量不同
### 分组背包问题
特点：物品有n组，每组有若干种，每一组只能选一种
