---
title: CF Round 1133 Div. 2 题解
published: 2026-11-22
tags: [Codeforces, "Div. 2", 模拟赛]
category: Codeforces
draft: false
---

# 一，思路
## [A. Are They the Same?](https://codeforces.com/contest/2280/problem/A)
我们分析三种操作，操作一可以改变奇偶性为 $k$ 的奇偶性，其余操作均无法改变奇偶性。
所以，我们只需要统计我们需要多少个奇数和偶数即可，对于 $a_i$ 判断它能否成为奇数/偶数，并统计即可。
最后判断**能供给的奇数数量**是否大于等于**需要的奇数数量，**能供给的偶数数量**是否大于等于**需要的偶数数量**即可。
## [B. Secret String Patterns](https://codeforces.com/contest/2280/problem/B)
~~被交互题吓到的过来罚站。~~
我们注意到，唯一不满足条件的 01 串有这几种情况：全 0 串，全 1 串，或者一个前缀全是 1 的串。
于是，我们第一次询问整个串有多少个 1，即 `? 1 n`。
如果答案是 $0$ 或者 $n$，那么就输出 `NO`。
否则，我们只需要排除前缀 1 串，于是询问 `? 1 x`，看看是不是 $x$ 即可（$x$ 为第一次询问的答案）。
注意不要关流和 `fflush(stdout)`。
## [C. Reverse and Double](https://codeforces.com/contest/2280/problem/C)
题目要求最小化一个最大值，显然想到二分答案。
我们对于 `check(x)`，判断能否使得 ”gap value“ $\le x$。那么，我们要改的就是 $>x$ 的间隔。
我们注意到，对于一个操作 $[l,r]$，我们会导致这个区间内的所有间隔都 $\times 2$，所以我们这个”最大间隔“ $m$ 要满足 $2m \le x$。唯一可以有希望使得间隔减少的，只有 $|2a_{l}-a_{l-1}|$ 和 $|2a_{r}-a_{r+1}|$。
所以，我们设 $c$ 为满足”gap value“ $>x$ 的数量：
- $c=0$：直接满足，返回 `true`；
- $c=1$：我们可以直接 $O(2n)$ 枚举以当前数字为起点/终点的区间，并直接判断；
- $c=2$：此时我们唯一确定了要修改的区间，直接暴力修改并判断即可（不要像我一样不会 `reverse` 而 WA）；
- $c>2$：因为一次只能修改两个间隔，所以直接判断为不可能，返回 `false`。
于是就做完了，时间复杂度 $O(n \log n)$。
## [D. Balanced Subsets](https://codeforces.com/contest/2280/problem/D)
一个 $x$ 是否稳定，取决于 $x+1$，$x+2$，$x+3$ 这 $3$ 个数被选中的个数是否恰好等于 $2$，它告诉我们，判断只看后 $3$ 格，并且每个元素的稳定性判断是局部是，只和这 $3$ 个位置有关。
我们可以自然的想到，从右往左扫，这样子，我们可以理科判断，如果 $x$ 被选中，它是否稳定。不妨用三位二进制数 `mask` 表示 $x+1$，$x+2$，$x+3$ 是否被选，于是 $x$ 稳不稳定也就是 `popcount(mask)` 是否等于 $2$。
我们还需要记录有多少个不稳定元素，于是定义 DP 状态：$\text{dp}[x][S]$ 表示已经处理完 $i+1$ 及更大的数，当不稳定元素数为 $x$，$i+1$，$i+2$，$i+3$ 的选择情况为 $S$ 的方案数。初始显然 $\text{dp}[0][0] = 1$。
那么，对于 $i$，我们可以不选（永远允许）或者选（条件 $i \in A$）。
不选的话，窗口左移一格，并且最低位是 $0$。可以用简单的位运算 `(s << 1) & 7` 来实现，转移 `dp[x][(s << 1) & 7] += dp[x][s]`。
选的话，那么不稳定数有可能 $+1$，这里单独判断并且看下是否合法即可。
最后扫到了 $1$，把整个数组全部累加即可，但是这里包含一个~~滚木~~空集，最后 $-1$。
## [E. Anti-Productive](https://codeforces.com/contest/2280/problem/E)
首先，我们考虑排序，然后容易证明，$a_{n-1} \times a_n$ 一定是最危险的两个，也就是 $(a_ia_j)_{\max}$，所以，如果 $a_{n-1} \times a_n < \ell$，那么答案为 $0$。
我们考虑一个特殊情况，即 $\ell =1$，那么危险条件是 $a_ia_j \ge 1$。要让所有的数对都安全，显然最多只能有一个正数（两个整数一定是危险的），所以我们保留最大的正数，其余数字减小到 $0$ 即可。
然后，我们观察到一个关键的结构，我们设
$$
s=\max\{ y \ge 0:y^2 < \ell \}
$$
那么，如果两个数都 $\le s$，那么一定安全；反之，若两个数都 $>s$，那么一定危险（因为 $(s+1)^2 \ge \ell$）。于是，**最终数组中，只能有一个数 $>s$，其余数必须 $\le s$。**
我们应该保留原数组的最大值（~~这个想法自然吧？我们说，这是**包**自然的~~【朱老师上物理课 belike】），其余所有数都不能超过某个 $y$，其中 $0 \le y \le s$。
于是，我们固定一个 $y$，对于一个非最大元素 $a_i$，若 $a_i \le y$，不需要动；否则需要 $a_i-y$ 次。所以，对于一个 $y$ 的总代价为：
$$
C(y)=\sum_{i=1}^{n-1} \max(0,a_i-y)
$$
最大值原来为 $a_n$。它需要和其他数安全，其他数最大可以到 $y$，所以 $x' \times y < \ell$，其中 $x'$ 是 $x$ 减小后的最大值。由整数的离散性，即
$$
x' \le \lfloor \frac{\ell-1}{y} \rfloor=U(y)
$$
那么最大值要降到不超过 $U(y)$，这里的代价是 $\max(0,x-U(y))$。

总结一下：我们定义
$$
C(y)=\sum_{i=1}^{n-1} \max(0,a_i-y)
$$
以及
$$
D(y)=
\begin{cases}
0, & y=0,\\[6pt]
\max\left(0,\;a_n-\left\lfloor\dfrac{\ell-1}{y}\right\rfloor\right), & y>0.
\end{cases}
$$
求
$$
\min_{0\le y \le s}\{ C(y)+D(y) \}
$$
注意用增量的方式维护代价。
## [F. Meta Graph](https://codeforces.com/contest/2280/problem/F)
滚木滚木滚木。
~~这里什么也没有。~~
# 二，代码
## A
```cpp
#include<bits/stdc++.h>
#define ll long long
#define int long long
#define i128 __int128
#define INF 1e18
using namespace std;

void solve() {
	int n, k;
	cin >> n >> k;

	vector<int> a(n + 1), b(n + 1);
	for (int i = 1; i <= n; i++)
		cin >> a[i];
	for (int i = 1; i <= n; i++)
		cin >> b[i];	

	int cnt0a = 0, cnt1a = 0;
	int cnt0b = 0, cnt1b = 0;

	for (int i = 1; i <= n; i++) {
		if (b[i] & 1) cnt1b++;
		else cnt0b++;
	}

	for (int i = 1; i <= n; i++) {
		bool fg_odd = false, fg_even = false;

		if (a[i] & 1) fg_odd = true;
		else fg_even = true;

		if (k & 1) fg_odd = true;
		else fg_even = true;

		if (fg_odd) cnt1a++;
		if (fg_even) cnt0a++;
	}

	if (cnt1a >= cnt1b && cnt0a >= cnt0b) cout << "YES\n";
	else cout << "NO\n";
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
## B
```cpp
#include<bits/stdc++.h>
#define ll long long
#define int long long
#define i128 __int128
#define INF 1e18
using namespace std;

int ask() {
	int ans;
	scanf("%lld", &ans);
	return ans;
}

void solve() {
	int n;
	scanf("%lld", &n);

	printf("? 1 %lld\n", n);
	fflush(stdout);

	int cnt1 = ask();
	if (cnt1 == 0 || cnt1 == n) {
		printf("! NO\n");
		fflush(stdout);
	} else {
		printf("? 1 %lld\n", cnt1);
		fflush(stdout);

		int cnt2 = ask();
		if (cnt2 == cnt1) printf("! NO\n");			
		else printf("! YES\n");
		fflush(stdout);
	}
}

signed main() {
	int T;
	scanf("%lld", &T);
	while (T--) solve();
	return 0;
}
```
## C
```cpp
#include<bits/stdc++.h>
#define ll long long
#define int long long
#define i128 __int128
#define INF 1e18
using namespace std;

void solve() {
	int n;
	cin >> n;

	vector<int> a(n + 1);
	for (int i = 1; i <= n; i++)
		cin >> a[i];

	auto check = [&](int x)->bool{ // ans <= x
		vector<int> s;
		for (int i = 1; i < n; i++) {
			if (std::abs(a[i] - a[i + 1]) > x) {
				s.push_back(i);
			}
		}

		if (s.size() == 0) return true;
		else if (s.size() == 1) {
			bool fg = false;

			// l = [1, i], r = i
			int mv1 = -INF;
			for (int l = s[0], r = s[0]; l >= 1; l--) {
				if (l != r) mv1 = max(mv1, std::abs(a[l] - a[l + 1]));
				if (l == r && l == 1) {
					if (std::abs(a[r] * 2 - a[r + 1]) <= x) {
						fg = true;
						break;
					}
				} else if (l == r) {
					if (std::abs(a[r] * 2 - a[r + 1]) <= x && std::abs(a[l] * 2 - a[l - 1]) <= x) {
						fg = true;
						break;
					}
				} else if (l != r && l == 1) {
					if (std::abs(a[l] * 2 - a[r + 1]) <= x && mv1 * 2 <= x) {
						fg = true;
						break;
					}	
				} else {
					if (std::abs(a[l] * 2 - a[r + 1]) <= x && std::abs(a[r] * 2 - a[l - 1]) <= x && mv1 * 2 <= x) {
						fg = true;
						break;
					}						
				}
			}

			// l = i + 1, r = [i + 1, n]
			int mv2 = -INF;
			for (int l = s[0] + 1, r = s[0] + 1; r <= n; r++) {
				if (l != r) mv2 = max(mv2, std::abs(a[r] - a[r - 1]));
				if (l == r && r == n) {
					if (std::abs(a[l] * 2 - a[l - 1]) <= x) {
						fg = true;
						break;
					}
				} else if (l == r) {
					if (std::abs(a[r] * 2 - a[r + 1]) <= x && std::abs(a[l] * 2 - a[l - 1]) <= x) {
						fg = true;
						break;
					}
				} else if (l != r && r == n) {
					if (std::abs(a[r] * 2 - a[l - 1]) <= x && mv2 * 2 <= x) {
						fg = true;
						break;
					}						
				} else {
					if (std::abs(a[l] * 2 - a[r + 1]) <= x && std::abs(a[r] * 2 - a[l - 1]) <= x && mv2 * 2 <= x) {
						fg = true;
						break;
					}					
				}
			}

			if (fg) return true;
			else return false;
		} else if (s.size() == 2) {
			vector<int> c = a;
			for (int i = s[0] + 1; i <= s[1]; i++) {
				c[i] = c[i] * 2;
			}
			reverse(c.begin() + s[0] + 1, c.begin() + s[1] + 1);

			bool fg = true;
			for (int i = 1; i < n; i++) {
				if (std::abs(c[i] - c[i + 1]) > x) {
					fg = false;
					break;
				}
			}

			if (fg) return true;
			else return false;
		} else return false;
	};

	int l = 0, r = 1e6, ans = -1;
	while (l <= r) {
		int mid = (l + r) >> 1;
		// cout << mid << " " << check(mid) << "\n";
		if (check(mid)) {
			ans = mid;
			r = mid - 1;
		} else {
			l = mid + 1;
		}
	}

	cout << ans << "\n";
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
## D
```cpp
#include<bits/stdc++.h>
#define ll long long
#define int long long
#define i128 __int128
#define INF 1e18
using namespace std;

const int mod = 998244353;

void solve() {
	int n;
	cin >> n;

	vector<int> a(n + 1);
	vector<int> vis(3 * n + 1, 0);
	int max_val = 0;
	for (int i = 1; i <= n; i++) {
		cin >> a[i];
		max_val = max(max_val, a[i]);
		vis[a[i]] = 1;
	}

	vector<int> fg(8, 0);
	for (int s = 0; s < 8; s++) {
		fg[s] = (__builtin_popcount(s) != 2);
	}

	vector<vector<int>> dp(3, vector<int>(8, 0));
	dp[0][0] = 1;	

	for (int i = max_val; i >= 1; i--) {
		vector<vector<int>> ndp(3, vector<int>(8, 0));

		for (int x = 0; x <= 2; x++) {
			for (int s = 0; s < 8; s++) {
				int shift = (s << 1) & 7;
				ndp[x][shift] = (ndp[x][shift] + dp[x][s]) % mod;

				int nx = x + fg[s];
				if (vis[i] && nx <= 2) {
					ndp[nx][shift | 1] = (ndp[nx][shift | 1] + dp[x][s]) % mod;
				}			
			}
		}

		dp = ndp;
	}

	int ans = 0;
	for (int x = 0; x <= 2; x++) {
		for (int s = 0; s < 8; s++) {
			ans = (ans + dp[x][s]) % mod;
		}
	}

	cout << (ans - 1 + mod) % mod << "\n";
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
## E
```cpp
#include<bits/stdc++.h>
#define ll long long
#define int long long
#define i128 __int128
#define INF 1e18
using namespace std;

void solve() {
	int n, l;
	cin >> n >> l;	

	vector<int> a(n + 1), pre(n + 1);
	for (int i = 1; i <= n; i++)
		cin >> a[i];

	sort(a.begin() + 1, a.end());

	// pre[i] = sum(i~n)
	pre[n] = a[n];
	for (int i = n - 1; i >= 1; i--)
		pre[i] = pre[i + 1] + a[i];

	int ans = INF;

	// y = 0
	int res = 0;
	for (int i = 1; i <= n - 1; i++)
		res += a[i];
	
	ans = min(ans, res);

	// y = 1~s
	int s = sqrt(l);
	int pos = 1; // The first positive a[i] - y
	for (int y = 1; y <= s; y++) {
		while (pos <= n && a[pos] - y <= 0) pos++;
		res = pre[pos] - a[n] - (n - pos) * y + max(0LL, a[n] - (l - 1) / y);
		ans = min(ans, res);
	}

	cout << ans << "\n";
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
