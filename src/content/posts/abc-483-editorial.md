---
title: ABC 483 题解
published: 2026-11-08
tags: [ABC, AtCoder, 模拟赛]
category: AtCoder
draft: false
---

# 一，思路
## [A - Double-Sided Printing](https://atcoder.jp/contests/abc483/tasks/abc483_a)
略。
## [B - Flip Card Stack](https://atcoder.jp/contests/abc483/tasks/abc483_b)
略。
## [C - Second Choice Election](https://atcoder.jp/contests/abc483/tasks/abc483_c)
显然如果某个人的第一志愿退出了，那么这个人就会去选择他的第二志愿。
由于只有 $m$ 个人，所以只有 $m$ 次需要修改统计情况。
查询，我们只需要先预处理一下 `Max[i]`，`Id[i]` 为除了 $i$ 之外原来的最大值和最大值编号。
那么对于修改的人，我们只需要把修改后的结果与其进行比较即可。
## [D - Glitchy Robot](https://atcoder.jp/contests/abc483/tasks/abc483_d)
我们不妨记录 `pre[i]` 为从 $1$ 到 $i$ 执行最后的区间以及最终位置；`suf[i]` 为从 $i$ 到 $n$ 执行最后的区间以及最终位置。
然后对于 `pre[i]` 我们可以采用递推的形式；`suf[i]` 我们可以来偏移合并区间：即先执行第 $i$ 步，得到一个区间 $[l,r]$，然后对于后面的结果 `suf[i + 1]`，即把他偏移 $x$ 个单位，也就是 $[L,R]$ 变成了 $[L+x,R+x]$，同理最终位置也会被偏移。
然后我们考虑依次枚举我们要变动的 $a_i$，也就是把前面的 `pre[i - 1]` 执行完毕，然后执行一次 $-a_i$，然后再执行 `suf[i + 1]`，这里偏移逻辑和上面一样。
一些细节问题：先考虑一次都不改动的情况；$i=1$ 和 $i=n$ 的情况单独处理；$n=1$ 直接输出 $|a_1|+1$。
## [E - Typo Pairs](https://atcoder.jp/contests/abc483/tasks/abc483_e)
汉明距离 $\le 2$，我们把字符串分为 $3$ 类，即：$P_0$ 为距离为 $0$ 的对数，$P_1$ 为距离为 $1$ 的对数，$P_2$ 为距离为 $2$ 的对数。最终答案即 $P_0+P_1+P_2$。
对于 $P_0$，我们只需要用桶求出每个字符串的出现次数即可。若某个字符串出现了 $c$ 次，那么它对 $P_0$ 的贡献为 $\binom{c}{2}$，所以：
$$
P_0=\sum\binom{c}{2}
$$
对于 $P_1$，我们考虑枚举哪一位 $p$ 是不同的，那么我们把这一位删掉后得到一个长度为 $m-1$ 的字符串，然后再分桶。然后求出对于所有的 $p=1,2,\dots ,m$ 此时的桶内总点对数之和，记为 $T$。那么对于距离为 $0$ 的，出现了 $m$ 次；距离为 $1$ 的，出现了 $1$ 次。所以有 $T=m\times P_0+P_1$，故：
$$
P_1=T-m \times P_0
$$
对于 $P_2$，我们同样去枚举删除哪两个位置 $\{p,q\}$。此时我们得到一个长度为 $m-2$ 的字符串，然后再分桶。同样求出对于所有的 $\{p,q\}$ 来说，此时的桶内总点对数之和，记为 $S$。那么对于距离为 $0$ 的，会出现 $\binom{m}{2}$ 次；对于距离为 $1$ 的，会出现 $m-1$ 次（说明：令不同的位置为 $r$，那么 $\{p,q\}=\{r,x\}$，其中这个 $x$ 显然有 $m-1$ 种不同的选择）；对于距离为 $2$ 的，会出现 $1$ 次。所以有：
$$
S=\binom{m}{2} \times P_0+(m-1) \times P_1 + P_2
$$
于是：
$$
P_2=S-\binom{m}{2} \times P_0-(m-1) \times P_1
$$
最终算出 $P_0+P_1+P_2$ 即可。
## [F - Sum of Mod of Linear](https://atcoder.jp/contests/abc483/tasks/abc483_f)
我们可以观察当 $x$ 增加 $1$ 的时候答案怎么变，从而递推出所有的答案。
即，我们要找 $\text{ans}[x]-\text{ans}[x-1]$。
对于 $x$，我们记
$$
f_i(x)=(x+a_i) \bmod b_i
$$
因为 $x$ 增加了 $1$，所以 $f_i(x)$ 通常也只增加 $1$。
但是有一种例外情况，即 $b_i \mid x+a_i$，那么从 $b_i-1$ 跳回 $0$，即 $f_i(x)=0$，$f_i(x-1)=b_i-1$。此时，$f_i(x)-f_i(x-1)=-(b_i-1)=1-b_i$，与正常情况额外少了 $b_i$。于是写成：
$$
f_i(x)-f_i(x-1)=1-b_1 \times [b_i \mid (x+a_i)]
$$
然后对所有元素求和，拆开得：
$$
\text{ans}[x]-\text{ans}[x-1]=n-\sum_{i:p(i)} b_i
$$
其中 $p(i)=[b_1 \mid (x+a_i)]$。
不难发现（题目保证 $a_i<b_i$）
$$
\text{ans}[0]=\sum_{i=1}^n a_i
$$
所以，我们只需要找出对于一个 $x$，所有的满足 $p(i)$ 的 $i$。
即 $x+a_i \equiv 0 (\bmod b_i)$，等价于 $a \equiv -a_i(\bmod b_i)$。于是，$x=kb_i-a_i$，对于每一个 $i$，枚举这个 $x$，并累加 $b_i$。于是定义
$$
\text{sub}[x]=\sum_{i:p(i)} b_i
$$
所以递推式即
$$
\text{ans}[x]=\text{ans}[x-1]+n-\text{sub}[x]
$$
最后可以分组优化，对于相同的 $(a_i,b_i)$，不必重复枚举。
时间复杂度最多是 $O(m\sqrt{2n})$。
## [G - Qualification](https://atcoder.jp/contests/abc483/tasks/abc483_g)
我不会 qwq。不想补了。
# 二，代码
> 注：代码经过 AI 的风格优化，逻辑相同，改动的仅有变量名。
## A
```cpp
#include<bits/stdc++.h>
#define ll long long
#define int long long
#define i128 __int128
#define INF 1e18
using namespace std;

int n;

void solve() {
    cin >> n;
    cout << (n + 1) / 2 << '\n';
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

const int N = 110;

int n, q;
int p[N], s[N];

void solve() {
    cin >> n >> q;

    for (int i = 1; i <= n; i++) {
        p[i] = i;
        s[i] = 1;
    }

    while (q--) {
        int k;
        cin >> k;

        for (int i = 1; i <= k / 2; i++)
            swap(p[i], p[k - i + 1]);

        for (int i = 1; i <= k; i++)
            s[p[i]] = -s[p[i]];
    }

    for (int i = 1; i <= n; i++)
        cout << p[i] * s[p[i]] << '\n';
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

const int M = 2e5 + 10;

int n, m;
int a[M], b[M], cnt[M];
int mx[M], win[M];
set<int> st[M];

void solve() {
    cin >> n >> m;

    for (int i = 1; i <= m; i++) {
        cin >> a[i] >> b[i];
        st[a[i]].insert(i);
        cnt[a[i]]++;
    }

    int mx1 = 0;
    for (int i = 1; i <= n; i++)
        mx1 = max(mx1, cnt[i]);

    int id1 = -1;
    for (int i = 1; i <= n; i++)
        if (cnt[i] == mx1) { id1 = i; break; }

    int id1_ = -1;
    for (int i = 1; i <= n; i++) {
        if (i == id1) continue;
        if (cnt[i] == mx1) { id1_ = i; break; }
    }

    int mx2 = 0;
    for (int i = 1; i <= n; i++) {
        if (cnt[i] == mx1) continue;
        mx2 = max(mx2, cnt[i]);
    }

    int id2 = -1;
    for (int i = 1; i <= n; i++)
        if (cnt[i] == mx2) { id2 = i; break; }

    for (int i = 1; i <= n; i++) {
        if (i == id1) {
            if (id1_ != -1) { mx[i] = mx1; win[i] = id1_; }
            else { mx[i] = mx2; win[i] = id2;  }
        } else {
            mx[i] = mx1; win[i] = id1;
        }
    }

    for (int i = 1; i <= n; i++) {
        int cur = mx[i];
        unordered_map<int, int> add;
        for (int x : st[i]) add[b[x]] = cnt[b[x]];
        for (int x : st[i]) add[b[x]]++;

        int w = win[i];
        for (auto [c, v] : add) {
            if (v > cur) { cur = v; w = c; }
            else if (v == cur && c < w) { w = c; }
        }
        cout << w << '\n';
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
vector<tuple<int, int, int>> pre, suf;

void solve() {
    cin >> n;

    for (int i = 1; i <= n; i++)
        cin >> a[i];

    if (n == 1) {
        cout << std::llabs(a[1]) + 1 << "\n";
        return;
    }

    pre.resize(n + 1);
    suf.resize(n + 1);

    int pl = 0, pr = 0, px = 0;
    for (int i = 1; i <= n; i++) {
        px = px + a[i];
        pl = min(pl, px);
        pr = max(pr, px);
        pre[i] = {pl, pr, px};
    }

    suf[n] = {min(0LL, a[n]), max(0LL, a[n]), a[n]};

    for (int i = n - 1; i >= 1; i--) {
        int sl = 0, sr = 0, sx = 0;
        sx = sx + a[i];
        sl = min(sl, sx);
        sr = max(sr, sx);
        auto [tl, tr, tx] = suf[i + 1];
        suf[i] = {min(sl, tl + a[i]), max(sr, tr + a[i]), tx + sx};
    }

    int l = 0, r = 0, x = 0;
    int ans = 0;

    auto [tl0, tr0, tx0] = pre[n];
    ans = max(ans, tr0 - tl0 + 1);

    // i = 1
    x = x + (-a[1]);
    l = min(l, x);
    r = max(r, x);
    auto [tl1, tr1, tx1] = suf[2];
    l = min(l, x + tl1);
    r = max(r, x + tr1);
    ans = max(ans, r - l + 1);

    // i = n
    auto [tl2, tr2, tx2] = pre[n - 1];
    x = tx2 + (-a[n]);
    l = min(x, tl2);
    r = max(x, tr2);
    ans = max(ans, r - l + 1);

    for (int i = 2; i < n; i++) {
        int cl = 0, cr = 0, cx = 0;
        auto [tl1, tr1, tx1] = pre[i - 1];
        auto [tl2, tr2, tx2] = suf[i + 1];
        cx = tx1 + (-a[i]);
        cl = min(tl1, cx);
        cr = max(tr1, cx);
        cl = min(cl, tl2 + cx);
        cr = max(cr, tr2 + cx);
        ans = max(ans, cr - cl + 1);
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

const int N = 2e5 + 10;
const int M = 10;

int n, m;
char s[N][M];
unordered_map<string, int> mp0, mp1, mp2;
int p0 = 0, p1 = 0, p2 = 0;
int sum1 = 0, sum2 = 0;

string delete0(int i) {
    string t;
    for (int j = 1; j <= m; j++)
        t += s[i][j];
    return t;
}

string delete1(int i, int p) {
    string t;
    for (int j = 1; j <= m; j++) {
        if (j == p) continue;
        t += s[i][j];
    }
    return t;
}

string delete2(int i, int p, int q) {
    string t;
    for (int j = 1; j <= m; j++) {
        if (j == p || j == q) continue;
        t += s[i][j];
    }
    return t;
}

void solve() {
    cin >> n >> m;

    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= m; j++) {
            cin >> s[i][j];
        }
    }

    // p0
    for (int i = 1; i <= n; i++)
        mp0[delete0(i)]++;

    for (auto [x, y] : mp0)
        p0 += y * (y - 1) / 2;

    // p1
    for (int p = 1; p <= m; p++) {
        mp1.clear();
        for (int i = 1; i <= n; i++)
            mp1[delete1(i, p)]++;
        for (auto [x, y] : mp1)
            sum1 += y * (y - 1) / 2;
    }

    p1 = sum1 - m * p0;

    // p2
    for (int p = 1; p <= m; p++) {
        for (int q = p + 1; q <= m; q++) {
            mp2.clear();
            for (int i = 1; i <= n; i++)
                mp2[delete2(i, p, q)]++;
            for (auto [x, y] : mp2)
                sum2 += y * (y - 1) / 2;
        }
    }

    p2 = sum2 - p0 * m * (m - 1) / 2 - (m - 1) * p1;

    cout << p0 + p1 + p2 << '\n';
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

const int N = 5e5 + 10;

int n, q, a[N], b[N];
int ans[N], suf[N];
map<pair<int, int>, int> mp;

void solve() {
    cin >> n;

    for (int i = 1; i <= n; i++) {
        cin >> a[i] >> b[i];
        mp[{a[i], b[i]}]++;
    }

    for (auto [key, cnt] : mp) {
        auto [x, y] = key;
        for (int k = 1; k * y - x < N; k++) {
            suf[k * y - x] += y * cnt;
        }
    }

    for (int i = 1; i <= n; i++)
        ans[0] += a[i];

    for (int i = 1; i < N; i++)
        ans[i] = ans[i - 1] + n - suf[i];

    cin >> q;
    while (q--) {
        int x;
        cin >> x;
        cout << ans[x] << '\n';
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
