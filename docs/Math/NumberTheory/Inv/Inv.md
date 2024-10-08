# 乘法逆元

## **定义**
如果一个线性同余方程 $ax \equiv 1 \pmod b$，则 $x$ 称为 $a \bmod b$ 的逆元，记作 $a^{-1}$。

逆元存在的前提是$gcd(a,b)=1$，即$a、b$互质

乘法逆元其实就是模$p$意义下的倒数。

乘法逆元不会具体作为一个考点出题目，只会在一些答案输出需要取模时，我们的求解过程中遇到除法就要用乘法逆元来替换，因为模意义下的除法是不能直接进行计算。

例如我们下面的除法就要替换成乘法逆元：

$$
a / b(mod\space p)\rightarrow a\times inv(b) (mod\space p)
$$

即在模$p$的情况下$a$除$b$要替换成$a$乘$b$的逆元


## **扩展欧几里德**
[[NOIP2012 提高组] 同余方程](https://www.luogu.com.cn/problem/P1082)

$ax \equiv 1 \pmod b$其实就是等价于$ax+by=1$，因为$a、b$互质，$gcd(a,b)=1$，式子就是$ax+by=gcd(a,b)$，可以用扩展欧几里得算法，计算$x，y$的解即可。
 
```cpp
typedef long long ll;
ll exgcd(ll a,ll b,ll& x,ll& y){
    if(!b){
        x=1,y=0;
        return a;
    }
    int d=exgcd(b,a%b,x,y),x0=x,y0=y;
    x=y0;
    y=x0-a/b*y0;
    return d;
}
ll inv(ll a,ll p){
    ll x,y;
    exgcd(a,p,x,y);
    return (x%p+p)%p;
}
```
时间复杂度位：$O(\log n)$，适用于任何数

## **费马小定理**

定理内容：如果 $p$ 是质数，而整数 $a$ 不是 $p$ 的倍数，则有 $a^{p-1}\equiv 1(mod\space p)$ ，将式左边拆开就能得到 $a\cdot a^{p-2}\equiv 1(mod\space p)$ ，根据逆元定义可以知道 $a^{p-2}$ 就是 $a$ 的逆元，直接利用快速幂计算即可。

```cpp
ll qpow(ll a,ll b, ll p){
    ll res = 1;
    while(b > 0){
        if(b & 1) res = res * a % p;
        a = a * a % p;
        b >>= 1;
    }
    return res;
}
ll inv(ll a,ll p){
    return qpow(a,p-2);
}
```
时间复杂度位：$O(\log n)$，只使用于质数

## **递推打表求n个数的逆元**

上面两种方法只适用于对单个数进行逆元求解，如果对$1\dots n$，的所有数求关于$p$的逆元，用上面的方法肯会超时。下面介绍一种$O(n)$的方法。

首先我们知道$1\times 1\equiv 1(mod\space p)$，所以在模$p$的情况下，$1$的逆元就是$1$。

我们再来看其他情况：设我们要求$i$的逆元$i^{-1}$，我们令$k=\left \lfloor \frac{p}{i}\right \rfloor$，$j=p\space mod\space i$，有$p=ki+j$。我们会得到$ki+j\equiv 0(mod\space p)$，我们左右同时乘以$i^{-1}\times j^{-1}$，就可已得到：

$$
kj^{-1}+i^{-1}\equiv 0(mod\space p)\\
~\\
i^{-1}\equiv -kj^{-1}(mod\space p)
$$

我们再将$j=p\ mod\space i$和$k=\left \lfloor \frac{p}{i}\right \rfloor$带入，得到：

$$
i^{-1}\equiv -\left \lfloor \frac{p}{i}\right \rfloor (p\space mod\space i)^{-1}(mod \space p)
$$

因为 $p\space mod\space i$ 一定是小于 $i$ 的，所以我们可以从以前算过的逆元推导得 $i$ 的逆元，然后将其存入到一个数组里，使用时直接查询即可。

为了防止出现负数，计算时可以加上一个 $p$ 再模 $p$ 。
```cpp
ll inv[MAX];//记录逆元
void init(){
    inv[1]=1;
    for(int i=2;i<=n;++i)
        inv[i]=(ll)(p-p/i)*inv[p%i]%p;
}
```
[【模板】乘法逆元](https://www.luogu.com.cn/problem/P3811)
[【模板】乘法逆元 2](https://www.luogu.com.cn/problem/P5431)  这题卡常，用快读才能过

## **递推求阶乘逆元**

记$n!$的逆元为$inv[n!]$，$(n - 1)!$的逆元为$inv[(n - 1)!]$

有$(n)! \times inv[n!] \equiv 1(mod\space p)$

所以$(n - 1)! \times n\times inv[n!] \equiv 1(mod\space p)$

根据这个同余方程不难看出$n\times inv[n!]$是$(n - 1)!$的一个逆元

因此可以先用快速幂求出$n!$的逆元，再反向递推$1~n$所有数阶乘的逆元

```cpp
ll P[MAX], inv[MAX];

// qpow为上文快速幂

// n取题目的数据范围，比如2e5
void init(ll n, ll p) {
    P[0] = 1;
    for(int i = 1; i <= n; i ++ ) P[i] = (i * P[i - 1]) % p;
    inv[n] = qpow(P[n], p - 2, p); // 费马小定理求n!逆元
    for(int i = n - 1; i >= 0; i -- ) inv[i] = (inv[i + 1] * (i + 1)) % p;
    // 递推求阶乘逆元
}
```

**阶乘逆元的应用——求模p意义下的组合数**

[Codeforces Round 964 (Div. 4) E](https://codeforces.com/contest/1999/problem/F)

观察组合数计算公式 $C_n^k = \frac{n!}{m!(n - m)!}$，如果要求模p意义下的$C_n^k$，无法通过除法取模来得到正确的结果，所以必须转化为求乘法逆元再对乘法取模。这里就需要用到阶乘的逆元来求带模数组合数。

```cpp
// 在主函数中优先调用上文init()函数初始化
inline ll comb(ll n, ll m, ll p) {
    if(n < m) return 0 % mod;
    return ((P[n] * inv[m]) % p * inv[n - m]) % p;
}
```
**注意：**这里求带模数的组合数的方法必须在题目给定的p为质数的前提下才能使用，否则无法肯定$C_n^k$的逆元存在。

-----------------
参考文章：
[Oi-Wiki 乘法逆元](https://oi-wiki.org/math/number-theory/inverse/)
