## 做题思路

```C++
for(int i = 0; i < PAT.size(); ++i) {
    独立思考题目的解题思路(这一步是提升个人编程能力和代码思维的关键)
    if(个人思路可以AC ) {
        if(书中代码和自己思路一致)
		continue;
         else 
        要记录一下个人思路，学习一下作者思路(一 般书上思路比较精炼，还是很值得学习的) ;
	}
    else {
        记录个人思路，参并对比答案思路寻找异同，不看具体代码和其使用的数据结构;
		参考答案思路，个人独立编程;
        if(独立编程AC)
			对比个人代码与答案代
        else
            学习答案思路和答案代码
    }
    对本专题进行总结(这一步会有很大的收获)
}
```



### 前缀和

#### 一维前缀和

代码模板

重要公式：

1. 初始化前缀和数组 `s[i] = s[i - 1] + a[i];`
2. 查询数组中任意两个下标之间数的和： `s[r] - s[l - 1]`

```c++
#include <iostream>

using namespace std;

const int N = 100010;
int a[N]; // 输入数组
int s[N]; // 前缀和数组(前n个数的和)

int main() {
    int n, m;
    scanf("%d%d", &n, &m);
    
    // for (int i = 1; i <= n; i++) scanf("%d", &a[i]); 
    // s[i] = s[i - 1] + a[i];
    
    for (int i = 1; i <= n; i++) {
        scanf("%d", &s[i]);
        s[i] += s[i - 1]; // 一边读入一边初始化前缀和数组
    }    
    while (m--) {
        int l, r;
        scanf("%d%d", &l, &r);
        printf("%d\n", s[r] - s[l - 1]); // 询问前缀和数组
    }
    return 0;
}
```

#### 截断数组

```c++
#include <iostream>
using namespace std;

const int N = 100010;

int s[N];

typedef long long LL;

int main () {
    int n;
    scanf("%d", &n);
    for(int i = 1; i <= n; i++) {
        scanf("%d", &s[i]);
        s[i] +=s[i - 1]; // 初始化前缀和数组
    }
    
    if (s[n] % 3 != 0) { // 如果该数组所有元素之和本身不能被正好分为3份则无解
        puts("0");
        return 0;
    }
    
    LL res = 0; // 存放结果
    LL count = 0;
    for (int i = 3; i <= n; i++) { //至少要给前一个分界点留够位置，保证每一段不为空
        if(s[i - 2] == s[n] / 3) count++;
        if(s[n] - s[i - 1] == s[n] / 3) res += count;
    }
    printf("%lld", res);
    
    return 0;
}
```

#### 二维前缀和

求解前缀和公式：`s[i][j] = s[i - 1][j] + s[i][j - 1] - s[i - 1][j - 1] + a[i][j]`

求解任意两坐标(x1, y1) ~ (x2, y2)之间的元素之和：`s[x2][y2] - s[x1 - 1][y2] - s[x2][y1 - 1] + s[x1 - 1][y1 - 1]`

```C++
#include <iostream>
using namespace std;

const int N = 1010;

int a[N][N]; // 原数组
int s[N][N]; // 前缀和数组
int n, m, q;

int main() {
    scanf ("%d%d%d", &n, &m, &q);
    for (int i = 1; i <= n; i ++)
        for (int j = 1; j <= m; j ++) {
            scanf ("%d", &a[i][j]);
            s[i][j] = s[i - 1][j] + s[i][j - 1] - s[i - 1][j - 1] + a[i][j]; // 求前缀和数组
        }
    while (q --) {
        int x1, y1, x2, y2;
        scanf ("%d%d%d%d", &x1, &y1, &x2, &y2);
        printf ("%d\n", s[x2][y2] - s[x2][y1 - 1] - s[x1 - 1][y2] + s[x1 - 1][y1 - 1]); // 求解指定区间元素
    }
}
```

### 差分

s[N]为我们的原数组,a[N]是我们要求的差分数组

1.高中学过等差数列就应该知道一个公式，s[n] - s[n - 1] = a[n];
2.前缀和的公式是 s[n] = s[n - 1] + a[n];

第一个公式变形可以得到第二个公式，他们都是相通的
可以从第一个公式变形为 a[n] = s[n] - s[n - 1];
也可以从第二个公式，变形为 a[n] = s[n] - s[n - 1];

```C++
#include<iostream>
using namespace std;

const int N = 100010;
int a[N]; // 原数组(差分数组的前缀和数组)
int b[N]; // 差分数组

void insert(int l, int r, int c) { // 插入操作 在r到l之间每个元素都加c
    b[l] += c; // l到n全部加c
    b[r + 1] -= c; // r+1到n再减c
}

int main() {
    int n, m;
    scanf("%d%d", &n, &m);
    
    for(int i = 1; i <= n; i++) scanf("%d", &a[i]);
    
    for(int i = 1; i <= n; i++) insert(i, i, a[i]); // 输入元素相当于在i到i插入元素
    
    while (m--) {
        int l, r, c;
        scanf("%d%d%d", &l, &r, &c);
        insert(l, r, c); // 在l到r之间插入元素
    }
    
    for(int i = 1; i <= n; i++) b[i] += b[i - 1]; // 求差分数组前缀和 还原原数组
    
    for(int i = 1; i <= n; i++) printf("%d ", b[i]);
    
    return 0;
}
```

#### 改变数组元素

始终只有一种操作—**插入**

输入元素也看成是插入，则差分数组自动构建

差分数组的前缀和数组=原数组

```C++
#include <iostream>
#include <cstring>

using namespace std;

const int N = 200010;

int b[N]; // 差分数组
int n;

int main() {
    int T;
    scanf("%d", &T);
    while (T --) {
        scanf("%d", &n);
        memset(b, 0, (n + 1) * 4); // 每次读入数据都要初始化差分数组 需要多少空间就初始化多少空间 减少时间开销
        for (int i = 1; i <= n; i++) {
        	int x;
        	scanf("%d", &x);
        	x = min(i, x); // 数组总共i个元素,当ai大于数组元素个数，相当于对数组中的每个元素都进行一次操作
            // 对i-x+1到i一共x个数进行一次操作
        	int l = i - x + 1;
        	int r = i;
        	// 差分数组的基本操作对l到r之间的所有元素+1
            b[l] ++;
            b[r + 1] --;
        }
       	// 输出差分数组的原数组 即差分数组的前缀和数组
       	for (int i = 1; i <= n; i++) b[i] += b[i - 1];
       	
       	// 输出操作后的数组 如果操作次数大于等于1则元素为1，如果小于1元素为0
       	for (int i = 1; i <= n; i++) printf("%d ", !!b[i]);
      	
      	printf("\n");
    }
    return 0;
}
```

### 二分

有两种模板

**当让r=mid时mid=(l+r)/2**

**当让l=mid时mid=(l+r)/2+1**

```C++
// 整数二分算法模板

bool check(int x) {/* ... */} // 检查x是否满足某种性质

// 区间[l, r]被划分成[l, mid]和[mid + 1, r]时使用：
int bsearch_1(int l, int r)
{
    while (l < r)
    {
        int mid = l + r >> 1;
        if (check(mid)) r = mid;    // check()判断mid是否满足性质
        else l = mid + 1;
    }
    return l;
}
// 区间[l, r]被划分成[l, mid - 1]和[mid, r]时使用：
int bsearch_2(int l, int r)
{
    while (l < r)
    {
        int mid = l + r + 1 >> 1;
        if (check(mid)) l = mid;
        else r = mid - 1;
    }
    return l;
}
```

#### 我在哪

使用哈希表去重再进行二分

```C++
#include <iostream>
#include <unordered_set>

using namespace std;

int n;
string str;

bool check(int mid) {
    unordered_set<string> hash; // 创建一个哈希表
    
    for (int i = 0; i + mid - 1 < n; i++) { 
        string s = str.substr(i, mid); // 取出每一个子串
        if(hash.count(s)) return false; // 判断字串中是否存在过该子串 有相同字串则返回false
        hash.insert(s); // 否则将该子串加入到哈希表中
    }
    
    return true;
}

int main() {
    cin >> n >> str;
    
    // 二分模板
    int l = 1;
    int r = n;
    while (l < r) {
        int mid = (r - l) / 2 + l;
        if(check(mid)) r = mid;
        else l = mid + 1;
    }
    cout << l << endl;
}
```

### 双指针

#### 最长连续不重复子序列

始终让两个指针之间是合法的即元素不重复

为了判断两指针之间是否有元素重复，则应该使用另一数组记录每一个元素出现的次数

```c++
#include <iostream>
using namespace std;

const int N = 100010;

int a[N]; // 原数组
int s[N]; // 记录区间中的元素是否重复
int n;

int main() {
    scanf("%d", &n);
    for (int i = 0; i < n; i ++) scanf("%d", &a[i]);
    
    int res = 0;
    // i、j始终满足之间元素不重复的最长序列
    for (int i = 0, j = 0; i < n; i ++) { // i为快指针 j为慢指针
        s[a[i]] ++; // 记录该元素出现的次数
        while (s[a[i]] > 1) { // 有重复元素
            s[a[j]] --; // 将j指向元素剔出
            j++; // 将j指针向后移
        }
        res = max(res, i - j + 1); // 将结果更新
    }
    printf("%d", res);
}
```

#### 字符串删减

在最优解中，一定不会删除x以外的字符（反证法)

并且每一段连续x序列都是独立的（之间互不影响）

要使序列都小于2则

当k<3时删除0个（不用删除）

当k>=3时删除k-2个（k-x=2,x=k-2）

```C++
#include <iostream>
using namespace std;

string str;

int main() {
    int n;
    cin >> n >> str;
    int res = 0;
    for (int i = 0; i < n; i ++) {
        if (str[i] == 'x') { // 遇到x
            int j = i; // 创建第二个指针
            while (j < n && str[j] == 'x') j ++; // j指针一直走知道遇到不等于x的位置
            if (j - i >= 3) res += j - i - 2; // 该连续x区间的长度为j-i为使其长度小于3则应该删除长度-2个x
            i = j - 1; // 再去遍历下一个区间
        }
    }
    cout << res << endl; // 输出答案
}
```

#### 数组元素的目标和

双指针算法中要使两指针之间的元素满足题目要求，不满足要求则指针移动

先写出暴力算法，再看有无单调性，可以利用单调性将问题的时间复杂度优化

```c++
#include <iostream>
using namespace std;

const int N = 100010;

int a[N], b[N];
int n, m, x;

int main() {
    scanf("%d%d%d", &n, &m, &x);
    for (int i = 0; i < n; i ++) scanf("%d", &a[i]);
    for (int i = 0; i < m; i ++) scanf("%d", &b[i]);
    
    int i = 0, j = m - 1; // 双指针，将两个指针分别指向两数组开头
    
    while (a[i] + b[j] != x) {
        if (a[i] + b[j] > x) j --;
        else i ++;
    }
    
    printf("%d %d", i, j);
    
    return 0;
}
```

### 递推

#### 砖块

两个性质：

最终的结果要么全黑，要么全白

非黑即白 ，同一个位置最多能够操作两次

n个砖块一共操作n-1次操作

```C++
#include <iostream>
#include <vector>
using namespace std;

string str;
int n;

void update(char& c) {
    if (c == 'B') c = 'W';
    else c = 'B';
}

bool check(char c) {
    vector<int> res; // 存储需要修改的下标
    string s = str; // 每次操作不改变原串
    for (int i = 0; i + 1 < n; i ++) {
        if (s[i] != c) {
            update(s[i]); // 反转颜色
            update(s[i + 1]);
            // 将位置记录下来
            res.push_back(i);
        }
    }
    
    if (s.back() != c) return false; // 前n - 1个元素反转后，最后一个元素位置固定如果没有归位则无解
    
    cout << res.size() << endl;
    for (int x: res) cout << x + 1 << ' '; // 输出位置，下标从1开始
    if(res.size()) cout << endl; // 如果元素个数不为0则输出完元素后应加上回车
    return true;
}

int main() {
    int T;
    cin >> T;
    while(T --) {
        cin >> n >> str;
        if (!check('B') && !check('W')) cout << -1 << endl;
    }
    return 0;
}
```
