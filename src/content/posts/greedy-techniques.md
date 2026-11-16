---
title: 贪心技巧
published: 2026-11-15
tags: [题单, 贪心]
category: 贪心
draft: false
---

# 一，贪心的思想
1. 贪心的选择是趋优的，局部的最优可以导向全局的最优，这是解决问题的关键。
2. 总最优解和局部最优解应存在包含的关系。
# 二，贪心与 DP 的对比
1. 最优性：贪心只看眼前的决策，局部最优反而可能把全局最优带偏；DP 一定是全局最优的，因为他会穷举所有的可能状态。
2. 决策：贪心绝对不会反悔撤销决策；而 DP 是根据历史状态来决定当前的决策，相当于是“可撤销”的。
3. 复杂度：贪心的复杂度一般是极低的，大概 $O(n \log n)$ 或 $O(n)$；DP 的复杂度通常是多项式复杂度，较高，如 $O(n^2)$ 或 $O(nm)$。
4. 证明：贪心的证明是比较复杂的，证明方法如下；DP 的证明是比较简单的，因为其本身就是数学归纳法或者分类讨论的体现。
# 三，贪心的证明技巧
1. 邻项交换法：一般用于确定排序的策略，我们考虑将两个决策交换，看看哪个更优，从而得到排序的方法；
2. 数学归纳法：证明每一步最优，对数学能力要求很高；
3. 反证法：我们假设存在一个更优解，推出矛盾即可。
# 四，例题
## 1. P1986 元旦晚会
即有 $m$ 条线段，你要选择若干个点，使得每一条线段上都至少包含 $c_i$ 个点，求最少点数。
我们可以先考虑这个问题的弱化版，即 $c_i=1$，显然我们要尽可能的往右放，维护一个当前最后放的位置即可。
但是在本题中，一个区间可能要多个点，我们不妨用一个数据结构来维护话筒数。
先按照右端点排序，我们对于当前区间 $[a_i,b_i]$，需求为 $c_i$，我们先用数据结构查一下当前已经有了几个点，记为 $x$。
若 $x<c_i$，我们从 $b_i$ 开始，往左倒着扫描，有空位就放话筒。
~~看了题解后发现这题似乎不需要数据结构，直接暴力吧。~~
## 2. AT_abc103_d Islands War
即选择点问题，在 $[a_i,b_i-1]$ 范围内必须选择一个点，求最少选择几个点。选点问题弱化版。
## 3. P2107 小 Z 的 AK 计划
我们注意到走路的时间是不可避免的，于是我们反悔只能反悔 AK 的时间。所以我们先按照顺序去机房，然后先默认 AK，如果时间超了，我们就把当前 $t_i$ 最大的给反悔掉（因为每个 $t_i$ 的收益其实都是一样的），然后就好了。
## 4. P2587 泡泡堂
维护我方/对方的最强/最弱的值，按照这个顺序进行操作：
- 我方最强可以打对方最强：直接打；
- 我方最弱可以打对方最弱：直接打；
- 我方最强小于对方最强，我方最弱小于对方最弱：用我方最弱打对方最强。这里才判断平手。
我方最坏，将两个数组交换即可，即 $2n - \text{对方最好得分}$。
> 注意：不要用我方最强打对方最弱！可以构造出反例，而且从直觉上也是错的，浪费了强者的价值，杀鸡焉用牛刀。

扩展：最大化净胜分也是不变！310 赛制还是不变！证明可用邻项交换法。
## 5. P4801 饥饿的狐狸
对于最小值，我们进行分类讨论：
- $W<t_1$：那么从 $W$ 升序吃，即 $t_n-W$；
- $W>t_n$：那么从 $W$ 降序吃，即 $W-t_1$；
- $t_1 \le W \le t_n$：那么从 $t_1$ 吃到 $t_n$，即 $t_n-t_1$。
或者也可以模拟着吃，把 $W$ 加入后排序，从小到大吃。
对于最大值，有 $1 \rightarrow n  \rightarrow 2 \rightarrow n-1 \dots$  和 $n  \rightarrow 1 \rightarrow n-1 \rightarrow 2 \dots$ 两种情况，分别尝试即可，然后每一次取直接吃和喝水后吃两种情况的最大值。
## 6. P3076 Taxi G
首先我们发现，总距离分为载客距离和空车距离。载客距离是固定的，即 $\sum |a_i-b_i|$。
于是我们要最小化空车距离。我们发现空车距离有这些点：
- A：即我们要赶过去的点，终点 $m$，我们去接牛的起点 $a_i$；
- B：即从这一刻起，车就空了，我们要赶到 A，起点 $0$，我们牛下车后的目的地 $b_i$。

于是，我们一次空车的运行，就是从 A 到 B，我们最小化这个距离。这是一个很简单的贪心，我们收集所有的点，分成两组分别排序，一一相减最后求和即可。
## 7. P5521 梅深不见冬
我们定义 $f_u$ 为：为了在 $u$ 的整颗子树内完成所有操作，且最终在 $u$ 上种 $w_u$ 朵花，你出门最少要带的”总花朵数“。
假设你现在在 $u$，有 $R$ 朵花，你要依次处理每一个儿子 $v$：
- 去儿子：要进 $v$ 的子树，你手里至少得有 $f_v$ 朵花，这样子才能解决这个子树；
- 从儿子出来：我们可以把儿子的子树的花全都拔掉，这样子你只少了 $w_v$ 朵花。
如果我们已经处理完了 $u$ 的若干儿子，累计”冻结“在它们身上的花总数为 $s$，你现在还有 $R-s$ 朵花，那么处理下一个儿子（$f_v$ 朵），你至少要有 $s+f_v$ 朵花。（$R-s \ge f_v$）
那么对于一个 $u$，至多要把所有的 $w_v$ 相加最后加上 $w_u$（这样子才能把 $w_u$ 放上去），与上式取最大值即可。
然后我们考虑贪心顺序，假设我们有 $x$ 和 $y$：
- 先 $x$ 后 $y$：即 $\max(s+f_x,s+w_x+f_y)$（处理 $x$ 要 $s+f_x$，$y$ 的时候 $s$ 变成了 $s+w_x$，下同）。
- 先 $y$ 后 $x$：即 $\max(s+f_y,s+w_y+f_x)$。
即：
$$
\max(f_x,w_x+f_y) \le \max(f_y,w_y+f_x)
$$
我们不妨同时减去 $w_x+w_y$,，并令 $d_x=f_x-w_x$，$d_y=f_y-w_y$，可以得到：
$$
\max(d_x-w_y,d_y) \le \max(d_y-w_x,d_x)
$$
我们依靠~~直觉~~分类讨论可以得到，按照 $d_x$ 降序排序即可。
> 直觉：假如我们要拼积木，每个积木有”展开面积“ $f$，”收拢面积“ $w$，那么我们显然先去做展开面积大，收拢面积小的积木，因为它可以给桌子留出更多的空间。
## 8. CF798D Mike and distribution
首先，我们显然直接拉满，令 $k=\lfloor n \div 2 \rfloor+1$。
两个“超过一半”的条件很难满足，我们考虑先满足一个。
如果我们把所有下标按照 A 从大到小排序，然后选择 $a_1$，后面的两两分组，即 $(a_2,a_3),(a_4,a_5),\dots$，然后每一只选一个，可以证明此时可以满足 A 的条件。
> 也就是我们考虑最劣情况，选择 $a_1,a_3,a_5,a_7,\dots$，若 $n=2m+1$，则两式的差值为 $2(a_1+a_3+\dots+a_{2m+1})-(a_1+a_2+\dots+a_{2m+1})=(a_1-a_2)+(a_3-a_4)+\dots+(a_{2m-1}-a_{2m})+a_{2m+1}$，此式显然 $>0$；若 $n=2m$，证明方法类似。

此时我们 A 条件已经满足，我们考虑满足 B 条件。对于一组，我们只选一个下标，显然选较大的值即可，可以证明一定满足条件，因为 $2\max(b_i,b_{i+1})>b_i+b_{i+1}$，但是有一个问题，我们是否需要比较 $b_1$？显然不需要，因为 $2b_1>b_1$。于是题目做完了。这道题目的思维量较大，但是代码极短。
注意输出的是原下标而不是排序后的。
## 9. P3620 数据备份
~~水蓝~~。首先，我们不难想到，最优解里每一对都可以看成相邻的点在配对，也就是我们只需要在相邻的点选边。我们定义相邻的距离 $d_i=s_{i+1}-s_i$，那么即在 $d_1,d_2,\dots,d_{n-1}$ 中选 $k$ 个数，使权值和最小。
这可以转化为一道经典的反悔贪心题目 [P1484 种树 - 洛谷](https://www.luogu.com.cn/problem/P1484)，唯一区别在于“至多”和“恰好”，所以在我们的代码中删去 `if (val <= 0) break;` 即可。但是其实此题没有负权值，所以不删也没事。
注意我们求的是最小代价，所以小根堆改成大根堆，并且把原来的 `a[0] = a[n + 1] = -INF` 改成 `INF`，因为我们不想让他连这两个点，设为极大值即可。
## 10. AT_agc018_c Coins
~~紫题依旧来凑数。我能会做吗。qwq~~
# 五，代码
## 1. P1986
```cpp
#include<bits/stdc++.h>
#define ll long long
#define int long long
#define i128 __int128
#define INF 1e18
using namespace std;

const int N = 30010;

int n, m, p[N];

struct Node {
	int a, b, c;
	bool operator<(const Node &other) const {
		return b < other.b;
	}
} x[N];

void solve() {
    cin >> n >> m;
    for (int i = 1; i <= m; i++) 
    	cin >> x[i].a >> x[i].b >> x[i].c;

	sort(x + 1, x + m + 1);

	int ans = 0;

	for (int i = 1; i <= m; i++) {
		int cnt = 0;
		for (int j = x[i].a; j <= x[i].b; j++) 
			cnt += p[j];

		if (cnt >= x[i].c) continue;

		for (int j = x[i].b; j >= x[i].a; j--) {
			if (p[j]) continue;
			p[j] = 1;
			ans++;
			cnt++;
			if (cnt == x[i].c) break;
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
## 2. AT_abc103_d
```cpp
#include<bits/stdc++.h>
#define ll long long
#define int long long
#define i128 __int128
#define INF 1e18
using namespace std;

const int N = 1e5 + 10;

int n, m;

struct Node {
	int a, b;
	bool operator<(const Node &other) const {
		return b < other.b;
	}
} x[N];

void solve() {
    cin >> n >> m;
    for (int i = 1; i <= m; i++) 
    	cin >> x[i].a >> x[i].b;

    sort(x + 1, x + m + 1);

    int ans = 0;
    int lst = 0;

    for (int i = 1; i <= m; i++) {
    	if (lst >= x[i].a && lst <= x[i].b - 1) continue;
		ans++;
		lst = x[i].b - 1;    	
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
## 3. P2107
```cpp
#include<bits/stdc++.h>
#define ll long long
#define int long long
#define i128 __int128
#define INF 1e18
using namespace std;

const int N = 1e5 + 10;

int n, m;

struct Node {
	int x, t;
	bool operator<(const Node &other) const {
		return x < other.x;
	}
} a[N];

void solve() {
    cin >> n >> m;
    for (int i = 1; i <= n; i++)
    	cin >> a[i].x >> a[i].t;

	sort(a + 1, a + n + 1);

	priority_queue<int> q;
	int sum = 0;

	for (int i = 1; i <= n; i++) {
		sum += a[i].x - a[i - 1].x;
		sum += a[i].t;
		q.push(a[i].t);
		if (sum > m) {
			sum -= q.top();
			q.pop();
		}
	}

	cout << q.size() << "\n";
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
## 4. P2587
```cpp
#include<bits/stdc++.h>
#define ll long long
#define int long long
#define i128 __int128
#define INF 1e18
using namespace std;

const int N = 1e5 + 10;

int n, a[N], b[N];

void solve() {
    cin >> n;
    for (int i = 1; i <= n; i++)
    	cin >> a[i];
    for (int i = 1; i <= n; i++)
    	cin >> b[i];

	sort(a + 1, a + n + 1);
	sort(b + 1, b + n + 1);

	int l1 = 1, r1 = n; // 我方最弱，我方最强
	int l2 = 1, r2 = n;	// 对方最弱，对方最强
	int ans = 0;

	while (l1 <= r1 && l2 <= r2) {
		if (a[r1] > b[r2]) {
			ans += 2;
			r1--;
			r2--;
		} else if (a[l1] > b[l2]) {
			ans += 2;
			l1++;
			l2++;
		} else {
			if (a[l1] < b[r2]) {
				l1++;
				r2--;
			} else if (a[l1] == b[r2]) {
				ans++;
				l1++;
				r2--;
			}
		}
	}

	cout << ans << " ";

	for (int i = 1; i <= n; i++)
		swap(a[i], b[i]);

	l1 = 1, r1 = n;
	l2 = 1, r2 = n;
	ans = 0;	

	while (l1 <= r1 && l2 <= r2) {
		if (a[r1] > b[r2]) {
			ans += 2;
			r1--;
			r2--;
		} else if (a[l1] > b[l2]) {
			ans += 2;
			l1++;
			l2++;
		} else {
			if (a[l1] < b[r2]) {
				l1++;
				r2--;
			} else if (a[l1] == b[r2]) {
				ans++;
				l1++;
				r2--;
			}
		}
	}

	cout << 2 * n - ans << "\n";
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
## 5. P4801
```cpp
#include<bits/stdc++.h>
#define ll long long
#define int long long
#define i128 __int128
#define INF 1e18
using namespace std;

const int N = 1e5 + 10;

int n, w, a[N];

void solve() {
    cin >> n >> w;
    for (int i = 1; i <= n; i++)
    	cin >> a[i];

    sort(a + 1, a + n + 1);

    if (w < a[1]) cout << a[n] - w << " ";
    else if (w > a[n]) cout << w - a[1] << " ";
    else cout << a[n] - a[1] << " ";

    auto calc = [&](bool st1)->int{
    	int l = 1, r = n;
    	int lst = w, sum = 0;
    	for (int i = 1; i <= n; i++) {
    		int idx = ((st1 == (i & 1)) ? l++ : r--);
    		sum += max(std::abs(a[idx] - w), std::abs(a[idx] - lst));
    		lst = a[idx];
    	}
    	return sum;
    };

    cout << max(calc(false), calc(true)) << "\n";
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
## 6. P3076
```cpp
#include<bits/stdc++.h>
#define ll long long
#define int long long
#define i128 __int128
#define INF 1e18
using namespace std;

const int N = 1e5 + 10;

int n, m, a[N], b[N];
int x[N], y[N];

void solve() {
    cin >> n >> m;
    for (int i = 1; i <= n; i++)
    	cin >> a[i] >> b[i];

    int ans = 0;

    for (int i = 1; i <= n; i++)
    	ans += std::abs(a[i] - b[i]);

    x[1] = m, y[1] = 0;
    for (int i = 1; i <= n; i++) {
    	x[i + 1] = a[i];
    	y[i + 1] = b[i];
    }

    sort(x + 1, x + n + 2);
    sort(y + 1, y + n + 2);

    for (int i = 1; i <= n + 1; i++) {
    	ans += std::abs(x[i] - y[i]);
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
## 7. P5521
```cpp
#include<bits/stdc++.h>
#define ll long long
#define int long long
#define i128 __int128
#define INF 1e18
using namespace std;

const int N = 100010;

int n, w[N], f[N];
vector<int> g[N];

void dfs(int u) {
	for (int v : g[u]) dfs(v);

	sort(g[u].begin(), g[u].end(), [&](int A, int B)->bool{
		return f[A] - w[A] > f[B] - w[B];
	});

	int sum = 0, mxv = 0;
	
	for (int v : g[u]) {
		mxv = max(mxv, sum + f[v]);
		sum += w[v];
	}

	f[u] = max(mxv, sum + w[u]);
}

void solve() {
    cin >> n;
    for (int i = 2; i <= n; i++) {
    	int p; cin >> p;
    	g[p].push_back(i);
    }
    for (int i = 1; i <= n; i++)
    	cin >> w[i];

    dfs(1);

    for (int i = 1; i <= n; i++)
    	cout << f[i] << " ";
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
## 8. CF798D
```cpp
#include<bits/stdc++.h>
#define ll long long
#define int long long
#define i128 __int128
#define INF 1e18
using namespace std;

const int N = 1e5 + 10;

int n;

struct Node {
	int a, b, id;
	bool operator<(const Node &other) const {
		return a > other.a;
	}
} x[N];

void solve() {
    cin >> n;
    for (int i = 1; i <= n; i++)
    	cin >> x[i].a;
    for (int i = 1; i <= n; i++)
    	cin >> x[i].b;
    for (int i = 1; i <= n; i++)
        x[i].id = i;

    sort(x + 1, x + n + 1);

    vector<int> ans;
    ans.push_back(1);

    for (int i = 2; i <= n - 1; i += 2) {
    	if (x[i].b > x[i + 1].b) ans.push_back(i);
    	else ans.push_back(i + 1);
    }

    if (!(n & 1)) ans.push_back(n);

    cout << ans.size() << "\n";
    for (int idx : ans) cout << x[idx].id << " ";
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
## 9. P3620
```cpp
#include<bits/stdc++.h>
#define ll long long
#define int long long
#define i128 __int128
#define INF 1e18
using namespace std;

const int N = 300010;

int n, k, s[N], a[N];
int l[N], r[N], ver[N], del[N];

void solve() {
    cin >> n >> k;
    for (int i = 1; i <= n; i++)
    	cin >> s[i];

    for (int i = 1; i < n; i++)
    	a[i] = s[i + 1] - s[i];
    n--;

    a[0] = a[n + 1] = INF;

    // 初始化链表
    for (int i = 1; i <= n; i++) {
    	l[i] = i - 1;
    	r[i] = i + 1;
    }
    l[0] = 0, r[0] = 1;
    l[n + 1] = n, r[n + 1] = n + 1;

    priority_queue<tuple<int, int, int>, vector<tuple<int, int, int>>, greater<tuple<int, int, int>>> q;
	for (int i = 1; i <= n; i++) {
		q.push({a[i], i, ver[i]});
	}    

	int ans = 0;

	while (k--) {
		while (!q.empty()) {
			auto [val, id, vsn] = q.top();
			if (del[id] || vsn != ver[id]) {
				q.pop();
				continue;
			}
			break;
		}

		auto [val, id, vsn] = q.top();
		q.pop();

		ans += val;

		int left = l[id], right = r[id];

		a[id] = a[left] + a[right] - a[id];
		++ver[id];

		del[left] = 1;
		r[l[left]] = r[left];
		l[r[left]] = l[left];

		del[right] = 1;
		r[l[right]] = r[right];
		l[r[right]] = l[right];

		q.push({a[id], id, ver[id]});
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
