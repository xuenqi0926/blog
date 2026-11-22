---
title: ABC 484 题解
published: 2026-11-22
tags: [ABC, Atcoder, 模拟赛]
category: AtCoder
draft: false
---

# 一，思路
## [A - Chocolate Bar Count](https://atcoder.jp/contests/abc484/tasks/abc484_a)
略。
## [B - Nineball](https://atcoder.jp/contests/abc484/tasks/abc484_b)
略。
## [C - Move One Card](https://atcoder.jp/contests/abc484/tasks/abc484_c)
一次操作等价于，从 $A$ 中取出一个元素，并插入到任意位置。若 $A=B$，直接输出 `Yes`；否则记录 $L$ 为第一个不同的位置，$R$ 为最后一个不同的位置。那么，肯定只能是，$L$ 移动到后面或者 $R$ 移动到前面。于是，我们只需要检查：
- 删除 $A_L$ 和 $B_R$ 后是否相同，并且 $A_L=B_R$；
- 删除 $A_R$ 和 $B_L$ 后是否相同，并且 $A_R=B_L$。
## [D - Move One Card Left](https://atcoder.jp/contests/abc484/tasks/abc484_d)
我们发现，如果有一段是一样的话（如 `4 4 4 ...`），那么这一段就可以直接用一个数来计算整段数。
那么对于一个数 $a_i$，它可以插入到前面 $i-1$ 个位置（$i$ 不能插，因为这样子就是原数组），但是如果前面有和 $a_i$ 一样的位置，依旧是不能插（会重复），所以减去 $a_i$ 在之前出现的次数就是答案。
记得用 `map` 统计次数；记忆加上一个原串的情况。
## [E - Subsegment Sum Finder](https://atcoder.jp/contests/abc484/tasks/abc484_e)
很容易想到对 $a$ 进行前缀和。那么对于一个 $s_i$，我们要找 $j$ 使得 $l \le s_i-s_j \le r$，容易得到 $s_i-r \le s_j \le s_i-l$。于是，我们用值域线段树维护即可。
## [F - Make them monochrome](https://atcoder.jp/contests/abc484/tasks/abc484_f)
一道非常简单的线段树水题。“区间内数相等”，即最大值等于最小值，那么我们用线段树维护即可。对于~~滚木~~ `-1` ，我们在最大值和最小值线段树中分别设为极小值和极大值即可。注意查询的时候特判一下全是 `-1` 的情况。
## [G - Wildcard Construction](https://atcoder.jp/contests/abc484/tasks/abc484_g)
前 6 题这么水，这题应该很难吧。QWQ。
# 二，代码
## A
```cpp
#include<bits/stdc++.h>
#define ll long long
#define int long long
#define i128 __int128
#define INF 1e18
using namespace std;

int b, r;

void solve() {
	cin >> b >> r;
	cout << (30 - b) * (30 - r) << "\n";    
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
## B
```cpp
#include<bits/stdc++.h>
#define ll long long
#define int long long
#define i128 __int128
#define INF 1e18
using namespace std;

int k, a[10];

void solve() {
 	cin >> k;
 	for (int i = 1; i <= k; i++)
 		cin >> a[i];

 	bool has_nine = false;
 	int ans = INF;

 	for (int i = 1; i <= k; i++) {
 		if (a[i] == 9) has_nine = true;
 		ans = min(ans, a[i]);
 	}   

	if (!has_nine) cout << -1 << "\n";
	else cout << ans << "\n"; 	
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

    vector<int> a(n), b(n);
    for (int i = 0; i < n; i++)
        cin >> a[i];
    for (int i = 0; i < n; i++)
        cin >> b[i];

    int l = INF, r = -INF;
    for (int i = 0; i < n; i++) {
        if (a[i] != b[i]) {
            l = min(l, i);
            r = max(r, i);
        }
    }

    if (l == INF && r == -INF) {
        cout << "Yes\n";
        return;
    }

    {
        vector<int> c = a;
        c.erase(c.begin() + l);
        c.insert(c.begin() + r, a[l]);
        if (c == b) {
            cout << "Yes\n";
            return;
        }
    }

    {
        vector<int> c = a;
        c.erase(c.begin() + r);
        c.insert(c.begin() + l, a[r]);
        if (c == b) {
            cout << "Yes\n";
            return;
        }
    }

    cout << "No\n";
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
## D
```cpp
#include<bits/stdc++.h>
#define ll long long
#define int long long
#define i128 __int128
#define INF 1e18
using namespace std;

const int N = 2e5 + 10;

int n, a[N];
unordered_map<int, int> mp;

void solve() {
    cin >> n;
    for (int i = 1; i <= n; i++)
        cin >> a[i];

    int ans = 1;
    for (int i = 1; i <= n; i++) {
        if (i > 1 && a[i] != a[i - 1])
            ans += i - 1 - mp[a[i]];
        mp[a[i]]++;
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
## E
```cpp
#include<bits/stdc++.h>
#define ll long long
#define int long long
#define i128 __int128
#define INF 1e18
using namespace std;

const int N = 3e5 + 10;
const int M = N * 60;
const int K = INF + 10;

int n, l, r, a[N], s[N];

int ls[M], rs[M], val[M], tot = 0;

void push_up(int p) {
    val[p] = val[ls[p]] + val[rs[p]];
}

void update(int &p, int l, int r, int pos, int v) {
    if (!p) p = ++tot;
    if (l == r) {
        val[p] += v;
        return;
    }
    int mid = (l + r) >> 1;
    if (pos <= mid) update(ls[p], l, mid, pos, v);
    else update(rs[p], mid + 1, r, pos, v);
    push_up(p);
}

int query(int p, int l, int r, int ql, int qr) {
    if (!p) return 0;
    if (ql <= l && r <= qr) return val[p];
    int mid = (l + r) >> 1, res = 0;
    if (ql <= mid) res += query(ls[p], l, mid, ql, qr);
    if (qr > mid) res += query(rs[p], mid + 1, r, ql, qr);
    return res;
}

void solve() {
    cin >> n >> l >> r;	
    for (int i = 1; i <= n; i++)
    	cin >> a[i];

    for (int i = 1; i <= n; i++)
    	s[i] = s[i - 1] + a[i];

	int ans = 0;
	int rt = 0;
	update(rt, -K, K, 0, 1);

    for (int i = 1; i <= n; i++) {
    	ans += query(rt, -K, K, s[i] - r, s[i] - l);
    	update(rt, -K, K, s[i], 1);
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
## F
```cpp
#include<bits/stdc++.h>
#define ll long long
#define int long long
#define i128 __int128
#define INF 1e18
using namespace std;

const int N = 3e5 + 10;

int n, q, a[N];

int mx[N << 2], mn[N << 2];

void push_up(int x) {
	mx[x] = max(mx[x << 1], mx[x << 1 | 1]);
	mn[x] = min(mn[x << 1], mn[x << 1 | 1]);	
}

void build(int x, int l, int r) {
	if (l == r) {
		if (a[l] == -1) {
			mx[x] = -INF;
			mn[x] = INF;
		} else {
			mx[x] = a[l];
			mn[x] = a[l];
		}
		return;
	}
	int mid = (l + r) >> 1;
	build(x << 1, l, mid);
	build(x << 1 | 1, mid + 1, r);
	push_up(x);
}

void update(int x, int l, int r, int pos, int v) {
	if (l == r) {
		if (v == -1) {
			mx[x] = -INF;
			mn[x] = INF;
		} else {
			mx[x] = v;
			mn[x] = v;
		}
		return;
	}
	int mid = (l + r) >> 1;
	if (pos <= mid) update(x << 1, l, mid, pos, v);
	else update(x << 1 | 1, mid + 1, r, pos, v);
	push_up(x);
}

int query_max(int x, int l, int r, int ql, int qr) {
	if (ql <= l && r <= qr) return mx[x];
	int mid = (l + r) >> 1, res = -INF;
	if (ql <= mid) res = max(res, query_max(x << 1, l, mid, ql, qr));
	if (mid < qr) res = max(res, query_max(x << 1 | 1, mid + 1, r, ql, qr));
	return res;	
}

int query_min(int x, int l, int r, int ql, int qr) {1
	if (ql <= l && r <= qr) return mn[x];
	int mid = (l + r) >> 1, res = INF;
	if (ql <= mid) res = min(res, query_min(x << 1, l, mid, ql, qr));
	if (mid < qr) res = min(res, query_min(x << 1 | 1, mid + 1, r, ql, qr));
	return res;	
}

void solve() {
    cin >> n >> q;
    for (int i = 1; i <= n; i++)
    	cin >> a[i];

    build(1, 1, n);

    while (q--) {
    	int op; cin >> op;
    	if (op == 1) {
    		int i, x; cin >> i >> x;
    		update(1, 1, n, i, x);
    	} else {
    		int l, r; cin >> l >> r;
    		int a1 = query_max(1, 1, n, l, r);
    		int a2 = query_min(1, 1, n, l, r);
    		// cout << a1 << " " << a2 << "\n";
    		if (a1 == a2) {
    			cout << "Yes\n";
    		} else {
    			if (a1 == -INF && a2 == INF) {
    				cout << "Yes\n";
    			} else {
    				cout << "No\n";
    			}
    		}
    	}
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
