
本文的创作参考了[《背包九讲》](https://github.com/tianyicui/pack){target="_blank"}([原文pdf](./%E8%83%8C%E5%8C%85%E4%B9%9D%E8%AE%B2.pdf){target=_blank})，使用 C/C++ 实现了文中的伪代码，并对文章中的思路给出自己的理解。


## **1.01背包**

### **问题**

[01背包模板](https://www.luogu.com.cn/problem/P2871){target="_blank"}
有 $N$ 个物品，第 $i$ 个物品的费用为 $c_i$ ，价值为 $w_i$，有一个容量为 $V$ 的背包，求怎样选择能获得价值最大的物品。

### **思路**

我们设 $dp[i][j]$ 代表着从前 $i$ 个物品中选取物品装到容量为 $j$ 的背包中所能获得的最大价值，根据这样的定义，我们很容易能写出下面的递推公式：

$$
dp[i][j]=max\{dp[i-1][j],dp[i-1][j-c_i]+w_i \}
$$

**"** 将前 $i$ 件物品放入容量为 $v$ 的背包中 **"** 这个子问题，若只考虑第 $i$ 件物品的策略（放或不放），那么就可以转化为一个只牵扯前 $i-1$ 件物品的问题。如果不放第 $i$ 件物品，那么问题就转化为 “前 $i-1$ 件物品放入容量为 $v$ 的背包中” ；如果放第 $i$ 件物品，那么问题就转化为 “前 $i-1$ 件物品放入剩下的容量为 $v-c_i$ 的背包中” ， $dp[i-1][j-c_i]$  再加上通过放入第 $i$ 件物品获得的价值 $w_i$ 。

### **一维数组优化空间**

我们可以发现再递推时，我们只用到了 $i-1$ 的状态，所以我们可以用一维数组只记录 $i-1$ 时的状态，即可。但是遍历背包的顺序要改变。因为我们要保证 $dp[i][j]$ 是从 $dp[i-1][j-c_i]$ 推来，用一维数组表示 $dp[i-1][j-c_i]$ 就是 $dp[j-c_i]$ ，我们要保证在推 $dp[j]$ 时， $dp[j-c_i]$ 是还未更新的值，所以要从后往前遍历。

```cpp
for (int i = 0; i < N; ++i) {
    for (int j = V ; j >= c[i]; --j) {
        dp[j] = max(dp[j], dp[j - c[i]] + w[i]);
    }
}

```

## **2.完全背包**

### **问题**

题意基本与 $01$ 背包相同，只是每个物品可以取无限次。

### **思路**

因为每个物品可以拿无限次，那么就不能单纯的考虑拿或不拿。如果我们还是用 $dp[i][j]$ 表示前 $i$ 件物品放入背包容量为 $j$ 所能获得的最大值的话，很容易写出下面的递推式：

$$
dp[i][j]=max\{dp[i-1][j-kc_i]+kw_i,0\le kc_i\le j \}
$$

那么时间复杂度就会很高，这样显然不行。

在滚动数组优化 $01$ 背包时，我们要倒着遍历背包大小，这样做的目的是在推 $dp[j]$ 时 $dp[j-c_i]$ 的值还未更新，而我们思考一下，更新其实代表着选取该物品，那如果我们正着遍历背包容量，这代表着每当能装下一个该物品我们就选取，就达到了我们要多次选取的目的。那么递推公式就和 $01$ 背包相同，只需要改一下遍历顺序即可。

```cpp
for (int i = 0; i < N; ++i) {
    for (int j = c[i]; j <= V; ++j) {
        dp[j] = max(dp[j], dp[j - c[i]] + w[i]);
    }
}

```

## **3.多重背包**

### **问题**

[多重背包模板](https://www.luogu.com.cn/problem/P1776){target="_blank"}
题意还是和 $01$ 背包相同，只是每个物品只有 $n_i$ 件可用。

### **思路**

暴力的思路是，我们将 $n_i$ 个物品拆成 $01$ 背包中的 $n_i$ 个不同的物品。


```cpp
for (int i = 0; i < N; ++i) {
    for (int j = V; j >= 0; --j) {
        for (int k = 0; k <= n[i]; ++k) {
            if (j >= k * c[i])
                dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - k * c[i]] + k * w[i]);
            else dp[i][j] = dp[i - 1][j];
        }
    }
}
```

这里也可用一维数组对其空间复杂度进行优化

```cpp
for (int i = 0; i < N; ++i) {
    for (int j = V ; j > 0; --j) {
        for (int k = 0; k <= n[i]; ++k) {
            if (k * c[i] <= j)
                dp[j] = max(dp[j], dp[j - k * c[i]] + k * w[i]);
        }
    }
}
```

时间复杂是 $O(V\cdot \sum n_i)$ ，这样的复杂度不是很让人满意。

### **二进制拆分优化**

这里可以用二进制拆解的方法进行优化。我们要将 $n_i$ 拆成若干个数，并且这些数可也组成 $[0,n_i]$ 之间的所有数。可以将 $n_i$ 拆为 $1,2,4,\dots,2^{k-1},n_i-2^k+1$ ，且 $k$ 是满足 $n_i-2^k+1>0$ 的最大整数。拆成这样的序列，我们一定可以表示 $[1,2^k]$ 的所有数组，剩下 $(2^k,n_i]$ 的数字，可以通过加上 $n_i-2^k+1$ 来表示。

```cpp
for (int i = 0; i < N; ++i) {
    int nn = n[i], no;
    for (int k = 0; nn; ++k) {
        no = min(nn, (1 << k));
        nn = max(0, nn - (1 << k));
        for (int j = V; j >= no*c[i]; --j)
                dp[j] = max(dp[j], dp[j - no * c[i]] + no * w[i]);
    }
}
```

这样时间复杂度就优化到 $O(V\cdot \sum (\log n_i))$ 。

### **单调队列优化**

这里还可以用单调队列进行优化。

我们很容易可以发现 $dp[j]$ 是从 $max\{dp[j-c_i],dp[j-2c_i],dp[j-3c_i],\cdots,j-n_ic_i\}$ 推导得来的，而这些背包容量满足 $j\equiv j-c_i\equiv j-2c_i\equiv \cdots \equiv j-n_ic_i(mod\ c_i)$ ，所以我们可以将背包的容量 $j$ 划分为 $c_i$ 类，即 $\{0,1,2,\cdots,c_i-1\}$ ，其中每类中的状态只能由同类中的状态推导的来。

我们将每一类状态单独拿出来进行推导。我们可以得到递推式：

$$
dp[j]=max\{dp[k]+\frac{w_i(j-k)}{c_i}\mid k = j - xc_i,0 \leq x < \frac{j}{c_i}\}
$$

有递推式可知，我们要求得就是 $(j-n_ic_i,j]$ ，之间上式的最大值，这不就是维护一个滑动窗口求区间最大值嘛，我们就可以用单调队列来维护。那么问题又来了，这递推式中有 $j$ 呀，区间元素是个变量，这怎么用单调队列来维护？
很简单，我们将上式中 $j$ 从 $max$ 中提出来即可:

$$
dp[j]=max\{dp[k]-\frac{w_ik}{c_i}\mid k = j - xc_i,0 \leq x < \frac{j}{c_i}\}+\frac{w_ij}{c_i}
$$

这样单调队列维护上式中 $max$ 表达式中的值， $dp[j]$ 的值 $O(1)$ 时间复杂度就可以计算出来。

实现过程中，如果要用一维数组优化空间，我们就要使用滚动数组，而不能像 $01$ 背包那样倒着遍历，因为我们要使用单调队列，倒着遍历的话单调队列不好实现。

```cpp
for (int i = 0; i < N; ++i) {
    cin >> w >> c >> n;
    //滚动数组拷贝
    memcpy(dp2, dp1, sizeof dp2);
    for (int k = 0; k < c; ++k) {
        //维护一个单调队列
        int hh = 0, tt = -1;
        for (int j = k; j <= V; j += c) {
            if (hh <= tt && j - q[hh] > n * c)++hh;
            if (hh <= tt)dp1[j] = max(dp2[j], dp2[q[hh]] + (j - q[hh]) * w / c);
            while (hh <= tt && dp2[j] - j  * w / c > dp2[q[tt]] - q[tt] * w / c)--tt;
            q[++tt] = j;
        }
    }
}
```

这样优化后时间复杂度为 $O(NV)$ 。

## **4.混合背包**

### **问题**

混合背包，顾名思义，给定的物品中有的只能取一次（ $01$ 背包），有的可以取无限次（完全背包），有的能取 $n_i$ 次（多重背包）。

### **思路**

这样的问题也很简单，对于 $01$ 背包的物品就用 $01$ 背包的代码，对于完全背包的物品就用完全背包的代码，对于多重背包的物品就用多重背包的代码。这里就不给出示例代码了。

时间复杂度就是 $O(NV)$ 

## **5.二维费用背包**

### **问题**

该问题中，对于每一件物品，具有两种不同的费用，选择这件物品必须同时付出这两种代价；对于每种代价都有一个可付出的最大值（背包容量）。问怎样选择物品可以得到最大的价值。两种可付出的代价最大值为 $V_1,V_2$ 

### **思路**

#### **01背包**

[模板题](https://www.luogu.com.cn/problem/T129162){target="_blank"}
费用加了一个，那么我们就直接将递推的式子也加一维 $dp[i][j][k]$ ， $i$ 是物品下标， $j、k$ 是两个费用，很容易写出类似 $01$ 背包的递推式：

$$
dp[i][j][k]=max\{dp[i-1][j][k],dp[i-1][j-c_{1i}][k-c_{2i}]+w_i\}
$$

```cpp
for (int i = 0; i < N; ++i) 
    for (int j = c1[i]; j <= V1; ++j) 
        for (int k = c2[i]; k <= V2; ++k) 
            dp[i][j][k] = max(dp[i - 1][j][k], dp[i - 1][j - c1[i]][k - c2[i]] + w[i]);
```

可以只维护最近的状态，进而将数组降一维

```cpp
for (int i = 0; i < N; ++i)
    for (int j = V1; j >= c1[i]; --j) 
        for (int k = V2; k >= c2[i]; --k) 
            dp[j][k] = max(dp[j][k], dp[j - c1[i]][k - c2[i]] + w[i]);
```

#### **完全背包**

对于二维费用的多重背包，和一维的一样，只需要改变 $01$ 背包的的遍历顺序即可


```cpp
for (int i = 0; i < N; ++i) 
    for (int j = c1[i]; j <= V1; ++j) 
        for (int k = c2[i]; k <= V2; ++k) 
            dp[j][k] = max(dp[j][k], dp[j - c1[i]][k - c2[i]] + w[i]);
```

#### **多重背包**

和一维的类似，暴力的做法就是转化为 $01$ 背包

```cpp
for (int i = 0; i < N; ++i) 
    for (int f = 1; f <= n[i]; ++f) 
        for (int j = V1; j >= c1[i]; --j) 
            for (int k = V2; k >= c2[i]; --k) 
                dp[j][k] = max(dp[j][k], dp[j - c1[i]][k - c2[i]] + w[i]);
```

也可以将其进行二进制拆解

```cpp
for (int i = 0; i < N; ++i) {
    int nn = n[i], no;
    for (int f = 0; nn; ++f) {
        no = min((1 << f), nn);
        nn = max(0, nn - (1 << f));

        for (int j = V1; j >= no * c1[i]; --j)
            for (int k = V2; k >= no * c2[i]; --k) 
                dp[j][k] = max(dp[j][k], 
                        dp[j - no * c1[i]][k - no * c2[i]] + no * w[i]);
    }
}
```

## **6.分组的背包问题**

[分组背包模板](https://www.luogu.com.cn/problem/P1757){target="_blank"}

### **问题**

有 $N$ 件物品和一个容量为 $V$ 的背包。第 $i$ 件物品的费用是 $c_i$ ，价值 $w_i$ 是。这些物品被划分为若干组，每组中的物品互相冲突，最多选一件。求解将哪些物品装入背包可使这些物品的用总和不超过背包容量，且价值总和最大。

### **思路**

这个问题变成了每组物品有若干种策略：是选择本组的某一件，还是一件都不选。由此我们可以写出递推式：

$$
dp[k][j]=max\{dp[k-1][j],dp[k-1][j-c[i]]+w[i]\},物品i属于第k组
$$

由递推式可知，我们要保证递推时第 $k$ 组的值是由 $k-1$ 组推来的。所以要先枚举背包，再枚举第 $k$ 组的物品


```cpp
typedef vector<int> vi;
int dp[MAX], N, V, w[MAX], p, c[MAX];
vi kk[101]; // 存储每一组的物品
for (int k = 0; k <= 100; ++k) {
	for (int j = V; j > 0; --j) {
		for (int i = 0; i < int(kk[k].size()); ++i) {
			if (j >= c[kk[k][i]])
				dp[j] = max(dp[j], dp[j - c[kk[k][i]]] + w[kk[k][i]]);
		}
	}
}
```

## **7.有依赖的背包**

[金明的预算方案](https://www.luogu.com.cn/problem/P1064){target="_blank"}

### **问题**

这种背包问题的物品间存在某种“依赖”的关系。也就是说， $i$ 依赖于 $j$  ，表示若选物品 $i$  ，则必须选物品  $j$ 。为了简化起见，我们先设没有某个物品既依赖于别的物品，又被别的物品所依赖；另外，没有某件物品同时赖多件物品。

### **思路**

对于上面的例题的，每个主件的附件最多有两个，我们完全可也将所有的附件的组合都看成一个单独的物品，一个主件和其附件集合就对应分组背包问题中的一个物品组。我们利用分组背包算法进行计算即可。

#### **一般的问题**

考虑一种更普遍的情况，每个主件有 $n$ 个附件，那么附件的组合种类就有 $2^n+1$ 种，如果把所有的附件组合都看成一个物品的话时间复杂度和空间复杂度都会超。

首先一个很明显的结论：对于两个费用相同的物品，我们的选择一定是价值更高的那一个。所以我们对于相同费用的物品组合，我们只保留那个价值最高的一个，这样也不会影响最后的结果。所以我们可以对主件 $i$ 的附件集合进行一次 $01$ 背包，得到费用依次为 $[0,V-c_i]$ 这些值相对应的最大价值 $f'[v],v\in[0,V-c_i]$ 。我们将主件 $i$ 和 $V-c_i+1$ 个附件组合看作一个物品组，应用分组背包算法即可。核心代码如下：

```cpp
// kk 是物品分组
// tmp 是用来进行 01 背包的临时数组
for (int i = 1; i <= tot; ++i) {
    // 固定每个物品组合都要选主件
    for (int jj = 0; jj <= n - kk[i][0].first; ++jj)
        tmp[jj] = dp[jj] + kk[i][0].second;

    // 应用01背包
    for (int ii = 1; ii < (int)kk[i].size(); ++ii)
        for (int jj = n - kk[i][0].first; jj >= kk[i][ii].first; --jj)
            tmp[jj] = max(tmp[jj], tmp[jj - kk[i][ii].first] + kk[i][ii].second);
            
    // 考虑是否选择该物品组中的物品
    for (int j = kk[i][0].first; j <= n; ++j)
        dp[j] = max(dp[j], tmp[j - kk[i][0].first]);
}
```

#### **更一般的问题**

主件的附件仍然可能有自己的附件，但限制每个附件只能有一个主件，且无循环依赖的关系。这里的依赖关系类似图论里的森林。

如果在选择某个附件时，该附件也有附件集合，那么就先要将它的附件集合转化为物品组，然后应用分组背包算法。该过程实际上就是树形 $dp$ ，在求解父节点的最优解时，先要对其子节点应用动态规划求值。

## **8.泛化物品**

泛化物品的思想是要我们把每个物品不看做一个物品而是一个函数，即我们输入消耗 $c$ 从而得到价值 $h(c)$ 。对于 $01$ 背包，这个函数就是下面这样的：

$$
h(c)=\begin{cases}
 w_i&c=c_i\\
0& c\ne c_i
\end{cases}
$$

只有当花费等于对应物品的花费时，函数值为对应价值。
对应完全背包，这个函数是当且仅当 $c$ 被 $c_i$ 整除时有 $h(c)=\frac{c}{c_i}\times w_i$ ，其它函数值均为 $0$ 。其他类型的背包问题也是类似的，这里就不举例了。

## **9.背包问题问法的变换**
