---
title: DP 状态设计
published: 2026-11-07
tags: [动态规划, 题单]
category: 动态规划
draft: false
---

# 一，状态设计原则
1. **无后效性**：即一旦当前状态决定，后续的状态只需要通过当前的状态就可以正确的转移；
2. 最优子结构：一个问题的最优解，可以由其子问题的最优解构造出来；
3. 状态空间：不能有状态遗漏，否则最优解会出现问题；
4. 简洁性：保留尽可能少的状态。
# 二，DP 与 DAG
DP 与 DAG 存在对应关系如下：
- **DP 状态** 即 **DAG 的节点**；
- **状态转移** 即 **DAG 的边**；
- **无后效性** 即 **DAG 无环性质**；
- **最优解** 即 **路径权值和**；
- **DP 顺序** 即 **DAG 的拓扑排序**。
# 三，例题
## 1. P11793 橙子装箱 / Oranges
$\text{dp}[i]$ 表示将前 $i$ 个橙子装箱的最小总代价。容易得到状态转移：

$$
\text{dp}[i]=\min_{l=\max(1,i-m+1)}^i \{ \text{dp}[l-1] + k + (i-l+1) \times (\max a[l\dots i]-\min a[l\dots i])\}
$$
时间复杂度 $O(nm)$。
## 2. P1373 小 a 和 uim 之大逃离
显然我们的状态中要用 $(i,j)$。
然后题目要求两个人的竖直相等，也就是差值等于 $0$，所以多定义一个状态 $d$。
又我们还需要知道当前是那个人在吸收，于是再定义一维 $0/1$。（设 $0$ 为这一步小 a 走，$1$ 为这一步是小 uim 走）
于是，状态定义为：$\text{dp}[i][j][d][0/1]$ 有多少种方案。
首先考虑初始化，显然 $\text{dp}[i][j][a_{i,j}][0]=1$。
状态转移比较显然，这里列出一种情况：

$$
\text{dp}[i][j][k][0]=\text{dp}[i][j][k][0]+\text{dp}[i-1][j][(k-a_{i,j})\bmod (k+1)][1]
$$
其余情况同理，时间复杂度 $O(nmk)$。
## 3. P1622 释放囚犯
我们不难发现，释放一个人，相当于“切点”，这样子左右两边的区间就没有关联了。
所以，我们不妨先把释放名单排序，增加两个哨兵 $0$ 和 $p+1$。这样子 $a_i$ 与 $a_j$ 之间的人数为 $a_i-a_j-1$。
于是，我们不难想到，$\text{dp}[i][j]$ 表示释放 $[i,j]$ 区间的人，最少送肉人次。
我们不妨枚举最后一个释放的人 $k$，此时 $(a_{i-1},a_{j+1})$ 是一个完整的连通块（这里假设端点已经被释放/是边界的哨兵），这里面所有人都要吃肉。我们释放 $k$，那么 $a_i$ 到 $a_{k-1}$，$a_{k+1}$ 到 $a_j$ 也空了，且两段是独立的。所以最终转移：

$$
\text{dp}[i][j]=\min_{k=i}^j \{ \text{dp}[i][k-1]+\text{dp}[k+1][j]+a_{j+1}-a_{i-1}-2 \}
$$
## 4. P3572 PTA-Little Bird
首先显然对每只鸟进行单独的 DP。
很容易得到转移方程：

$$
\text{dp}[i]=\min_{j=i-k}^{i-1}(\text{dp}[j]+[d_i \ge d_j])
$$
但这样的时间复杂度是 $O(n^2)$ 的，考虑进行优化。
我们比较两个决策点 $j_1$ 和 $j_2$。当 $\text{dp}[j_1] \ne \text{dp}[j_2]$ 的时候，显然 $\text{dp}[j]$ 越小越好；当 $\text{dp}[j_1]=\text{dp}[j_2]$，我们要使 $[d_i \ge d_j]$ 更难触发，显然需要 $d_j$ 更大。所以对于一个状态，我们先按照 $\text{dp}[i]$ 升序排序，然后按照 $d_i$ 降序排序。
此时显然可以用单调队列进行优化，注意要记录 $(\text{dp}[i],d_i,i)$ 三个维度。
## 5. P14460 【MX-S10-T1】『FeOI-4』寻雾启示
显然，如果 $t_1 \le t_2$，那么跑步不划算，直接等 $d$ 块铁，然后搭过去即可。
否则，我们考虑 DP。我们考虑一个 $l$，说明 $0,1, \dots ,l$ 已经全部铺好了羊毛。
此时，我们选择一个 $i$（$i>l$），那么我们分这几步：
- 等铁刷够：第 $i$ 块铁在 $i \times k$ 的时候刷，你跑回 $0$ 需要 $f(l)+l \times t_2$ 的时间（$f(l)$ 即 DP 数组，到 $l$ 的最短时间），两者取最大值；
- 回到 $l$，搭上最后一段路：需要 $l \times t_2 + (i-l) \times t_1$。
首先我们不难发现 $f(l)+l \times t_2$ 的单调不减的，因为若 $l$ 越大，那么 $f(l)$ 肯定越大，$l \times t_2$ 也肯定越大；我们又发现，$i$ 越大，那么铁刷的更晚，又前面 $f(l)+l \times t_2$ 递增，所以 $l$ 也随 $i$ 递增。

于是，我们对于每个目标距离 $i$，找最后回程点 $l$，这里不妨用双指针，合法条件为：

$$
f(l)+l\times t_2 \le i \times k
$$
> 解释：我们希望等的时间越少，如果满足条件，也就是人到了，铁没有到，那么我们就浪费了时间，显然要让浪费的时间越少越好，所以我们要寻找这个最大的 $l$。

注意这里 $l+1$ 也有可能是更好的。
最后，我们计算时间：

$$
f(i)=\max(i\times k, f(l)+l \times t_2)+l\times t_2+(i-l)\times t_1
$$
其中这个对于 $l$ 和 $l+1$ 分别处理一下即可。
## 6. P4544 Buying Feed G
我们发现当前的运费只和车上有多少吨有关。所以我们不难想到，把商店按照坐标 $x_i$ 排序，定义 $\text{dp}[i][j]$ 表示处理完前 $i$ 个商店，车上有 $j$ 吨饲料的最小花费。
从 $i-1$ 到 $i$，距离为 $x_{i}-x_{i-1}$，假设到达第 $i$ 个商店前，有 $p$ 吨饲料，那么这一段费用为 $(x_i-x_{i-1}) \times p^2$。
在第 $i$ 个商店要买到 $j$ 吨饲料，花费为 $c_i \times (j-p)$。
于是不难列出转移方程：
$$
\text{dp}[i][j]=\min_{p}\{ \text{dp}[i-1][p]+(x_i-x_{i-1}) \times p^2+c_i \times (j-p)\}
$$
其中 $p$ 应满足：$p \le j$，$j-p \le f_i$（即 $p \ge j-f_i$）。
即 $\max(0,j-f_i) \le p \le j$。

注意到时间复杂度是 $O(EK^2)$，显然时间复杂度超时。
我们先把式子拆开：
$$
\text{dp}[i][j]=c_i \times j + \min_{p}\{ \text{dp}[i-1][p]+(x_i-x_{i-1}) \times p^2-c_i \times p\}
$$
令 
$$
f[p]=\text{dp}[i-1][p]+(x_i-x_{i-1}\times p^2 - c_i \times p)
$$
那么原式即
$$
\text{dp}[i][j]=c_i \times j + \min_{p} f[p]
$$
又由于 $p$ 有一个限制 $\max(0,j-f_i) \le p \le j$，于是显然可以用单调队列优化 DP。
## 7. P10755 Netrpeljivost
我们首先考虑一个 $O(n^4)$ 的做法。
我们定义一个递归函数 `solve(l, m)` 表示这个子树的覆盖叶子为 $[l,l+m)$。此时 `dp[i][j]` 表示头为全局 $l+i$，尾为全局 $l+j$ 时，子树的最小代价。
然后如果 $m=2$，显然 `dp[0][1] = w[l][l+1], dp[1][0] = w[l+1][l]`。
其余情况考虑进行子树的合并。我们定义 `lh`（左子树的头），`lt`（左子树的尾），`rh`（左子树的头），`rt`（左子树的尾）。我们四层循环枚举这 $4$ 个变量，并分交换和不交换两种情况：
- 不交换：先枚举 `lh`，`rt`，然后枚举 `lt` 与 `rh` 这里的条件是 `L[lh][lt]` 和 `R[rh][rt]` 都合法，那么贡献很容易计算：`L[lh][lt] + w[l + lt][l + s + rh] + R[rh][rt]`；
- 交换：开头为 `rh`，结尾为 `lt`，然后枚举 `rt` 与 `lh`，条件一样，然后贡献同理。
这显然是会超时的，这里我们可以发现对于“不交换”的情况的计算，`rh` 只会出现在后两项，于是不妨先固定 `lt` 与 `rt`，然后枚举 `rh`，储存后两项（`w[l + lt][l + s + rh] + R[rh][rt]`）的最小值，然后 DP 的时候枚举 `lh`，`rt`，`lt` 即可。交换的情况完全同理。
这个代码的时间复杂度被优化至 $O(n^2)$，证明略。
## 8. CF21D Traveling Graph
对于一个闭合的路径，每个点走的次数一定是偶数。但是原图中有些点的度可能是奇数，这些的经过次数变成偶数就需要重复走的边。我们不妨将这些点两两配对，重复走，那么新的代价就是路径长度，也就是我们要跑最短路，用 Floyd 跑一遍全源最短路。
然后收集所有的奇数点，跑一个最小权完美匹配即可，用状压 DP。
注意图连通，从 $1$ 开始要能走到所有度数 $>0$ 的点。
## 9. P4170 涂色
~~水蓝~~。如果 $s_l=s_r$，那么可以在由 $[l+1,r]$ 和 $[l,r-1]$ 转移而来相当于重新涂色一遍；否则直接枚举断点，两边分别涂色。
## 10. AT_arc074_c RGB Sequence
我不会。qwq。
# 四，代码
## 1. P11793
```cpp
#include<bits/stdc++.h>
#define ll long long
#define int long long
#define i128 __int128
#define INF 1e18
using namespace std;

const int N = 2e4 + 10;

int n, m, k, a[N], dp[N];

void solve() {
	cin >> n >> m >> k;

	for (int i = 1; i <= n; i++)
		cin >> a[i];	

	memset(dp, 0x3f3f3f3f, sizeof(dp));
	
	dp[0] = 0;

	for (int i = 1; i <= n; i++) {
		int max_val = -INF, min_val = INF;
		for (int l = i; l >= max(1LL, i - m + 1); l--) {
			max_val = max(max_val, a[l]);
			min_val = min(min_val, a[l]);
			dp[i] = min(dp[i], dp[l - 1] + k + (i - l + 1) * (max_val - min_val));
		}
	}

	cout << dp[n] << "\n";
}

signed main() {
    ios::sync_with_stdio(0);
    cin.tie(0), cout.tie(0);
    int T;
    // cin >> T;
    T = 1;
    while (T--) solve();
    return 0;
}
```
## 2. P1373
```cpp
#include<bits/stdc++.h>
#define i128 __int128
#define INF 1e18
using namespace std;

const int N = 810;
const int M = 20;
const int mod = 1e9 + 7;

int n, m, K, a[N][N], dp[N][N][M][2];

void solve() {
    cin >> n >> m >> K;

    for (int i = 1; i <= n; i++) {
    	for (int j = 1; j <= m; j++) {
    		cin >> a[i][j];
    		dp[i][j][a[i][j]][0] = 1;
    	}
    }

    for (int i = 1; i <= n; i++) {
    	for (int j = 1; j <= m; j++) {
    		for (int k = 0; k <= K; k++) {
    			dp[i][j][k][0] = (dp[i][j][k][0] + dp[i - 1][j][(k - a[i][j] + K + 1) % (K + 1)][1]) % mod;
    			dp[i][j][k][0] = (dp[i][j][k][0] + dp[i][j - 1][(k - a[i][j] + K + 1) % (K + 1)][1]) % mod;
    			dp[i][j][k][1] = (dp[i][j][k][1] + dp[i - 1][j][(k + a[i][j] + K + 1) % (K + 1)][0]) % mod;
    			dp[i][j][k][1] = (dp[i][j][k][1] + dp[i][j - 1][(k + a[i][j] + K + 1) % (K + 1)][0]) % mod;    		
    		}
    	}
    }

    int ans = 0;
    for (int i = 1; i <= n; i++) {
    	for (int j = 1; j <= m; j++) {
    		ans = (ans + dp[i][j][0][1]) % mod;
    	}
    }

    cout << ans << "\n";
}

signed main() {
    ios::sync_with_stdio(0);
    cin.tie(0), cout.tie(0);
    int T;
    // cin >> T;
    T = 1;
    while (T--) solve();
    return 0;
}
```
## 3. P1622
```cpp
#include<bits/stdc++.h>
#define ll long long
#define int long long
#define i128 __int128
#define INF 1e18
using namespace std;

const int N = 110;

int n, m, a[N], dp[N][N];

void solve() {
	cin >> n >> m;

	for (int i = 1; i <= m; i++) 
		cin >> a[i];

	a[0] = 0;
	a[m + 1] = n + 1;

	for (int len = 1; len <= m; len++) {
		for (int l = 1, r = len; r <= m; l++, r++) {
			dp[l][r] = INF;
			for (int k = l; k <= r; k++) {
				dp[l][r] = min(dp[l][r], dp[l][k - 1] + dp[k + 1][r] + a[r + 1] - a[l - 1] - 2);
			}			
		}
	}

	cout << dp[1][m] << "\n";
}

signed main() {
    ios::sync_with_stdio(0);
    cin.tie(0), cout.tie(0);
    int T;
    // cin >> T;
    T = 1;
    while (T--) solve();
    return 0;
}
```
## 4. P3572
```cpp
#include<bits/stdc++.h>
#define ll long long
#define int long long
#define i128 __int128
#define INF 1e18
using namespace std;

const int N = 1e6 + 10;

int n, q, d[N], dp[N];

struct State { int val, h, id; };

void solve() {
    cin >> n;
    for (int i = 1; i <= n; i++)
    	cin >> d[i];

    cin >> q;
    while (q--) {
    	int k; cin >> k;
		memset(dp, 0x3f3f3f3f, sizeof(dp));

		deque<State> q;
		dp[1] = 0;
		q.push_back({dp[1], d[1], 1});

		for (int i = 2; i <= n; i++) {
			while (!q.empty() && q.front().id < i - k) q.pop_front();
			dp[i] = q.front().val + (d[i] >= q.front().h);
			while (!q.empty()) {
				if ((q.back().val > dp[i]) ||
					(q.back().val == dp[i] && q.back().h <= d[i]))
					q.pop_back();
				else break;
			}
			q.push_back({dp[i], d[i], i});			
		}

		cout << dp[n] << "\n";
    }
}

signed main() {
    ios::sync_with_stdio(0);
    cin.tie(0), cout.tie(0);
    int T;
    // cin >> T;
    T = 1;
    while (T--) solve();
    return 0;
}
```
## 5. P14460
```cpp
#include<bits/stdc++.h>
#define ll long long
#define int long long
#define i128 __int128
#define INF 1e18
using namespace std;

const int N = 1e5 + 10;

int m, k, t1, t2, dp[N];

void solve() {
	cin >> m >> k >> t1 >> t2;

	if (t1 <= t2) {
		for (int i = 1; i <= m; i++) {
			cout << (k + t1) * i << " ";
		}
		cout << "\n";
		return;
	}

	memset(dp, 0x3f3f3f3f, sizeof(dp));

	int l = 0;
	dp[0] = 0;

	for (int i = 1; i <= m; i++) {
		while (l < i && dp[l] + l * t2 <= i * k) l++;

		int best = INF;

		if (l >= 1) 
			best = min(best, max(i * k, dp[l - 1] + (l - 1) * t2) + (l - 1) * t2 + (i - l + 1) * t1);
		if (l < i)
			best = min(best, max(i * k, dp[l] + l * t2) + l * t2 + (i - l) * t1);

		dp[i] = best;
	}

	for (int i = 1; i <= m; i++)
		cout << dp[i] << " ";
	cout << "\n";
}

signed main() {
	ios::sync_with_stdio(0);
	cin.tie(0), cout.tie(0);
	int T;
	cin >> T;
	while (T--) solve();
	return 0;
}
```
