---
layout: default
title: 拓展欧几里得算法
type: cipher
description: 拓展欧几里得算法可用于求一个数倒数的模，在RSA等密码学中有重要应用
---
####应用背景
作为一只码农每当学习个新知识尤其是数学知识时。我觉得最好得搞清楚它是为了解决一个什么问题。欧几里得算法是为了求两个数的最大公约数**Greatest Common Divisor**后文都以**gcd**简称，而拓展欧几里得算法则可以帮助我们求出倒数的模$$a^{-1} \equiv 1 \;(\bmod\; n) $$ 如果对这个写法不太熟悉，我们换一种表示就是——已知两个正整数a和n，我们想求一个数e使得a与e的乘积除以n的余数为1。而在大名鼎鼎的RSA算法中就有这样一步需要求倒数的模，并且还多加了一个限制条件即a与n互质。
####欧几里得算法
拓展欧几里得(简称EEA) 人如其名是基于欧几里得算法(EA) 的，那么我们来回顾下小学所学怎么求两个数m,n的最小公约数$$gcd(m,n) = gcd(n,m\bmod\ n) $$ 如果这个公式还没勾起你的回忆，那么我们来一段计算过程吧。这里我们求39和69的最大公约数
$$
\begin{gather}
69 = 39 \times 1 + 30 \\
39 = 30 \times 1 + 9 \\
30 = 9 \times 3 + 3 \\
9 = 3 \times 3 + 0 
\end{gather}
$$
实话说，小时候学只是机械式地记得这个操作。但当看到EA的表达式后发现写作$gcd(69,39) = gcd(39,30) = gcd(30,9) = gcd(9,3) = 3$能够更好地理解它的本质。
####拓展欧几里得
一句话为了概括EEA就是求两个数$\alpha$和$\beta$使得
$$ \alpha\times A + \beta\times B = gcd(A,B)$$
在具体讲怎么求$\alpha$,$\beta$之前我想先介绍为什么它能让我们求得倒数的模。如前面提到的A，B两数互质的情况下我们有$gcd(A,B)=1$而我们要求的是$B^{-1} \equiv 1 \;(\bmod\; A) $所以对等式两边对A取余
$$
\begin{align}
\alpha\times A + \beta\times B\;(\bmod A)=& 1 \bmod A\\
\beta\times B \bmod A=& 1\\
\beta \equiv& B^{-1} \;(\bmod\; n) 
\end{align}
$$
下面我们来讲讲怎么一步一步地来求$\alpha$,$\beta$。改写前面求39和69公约数的式子并令$r_0=69,r_1=39$
$$
\begin{align}
r_2 =& 30 = 69 - 39\\
r_3 =& 9 = 39 - 30= 39 - (69-39)=-69+2\times39\\
r_4 =& gcd(69,39) = 3 = 30 - 9=(69-39)-(-69+2\times39)=2\times69 - 3\times39
\end{align}
$$
归纳总结，为了求$\alpha_i$我们需要$\alpha_{i-1}$和$\alpha_{i-2}$
$$
\begin{align}
r_{i} =& r_{i-2} - q_{i-1}r_1\\
=&(\alpha_{i-2}r_0 + \beta_{i-2}r_1) - q_{i-1}(\alpha_{i-1}r_0 + \beta_{i-1}r_1)\\
=&(\alpha_{i-2}-\alpha_{i-1}q_{i-1})r_0 + (\beta_{i-2}-\beta_{i-1}q_{i-1})r_1\\
\end{align}
$$
所以我们有
$$
\begin{align}
\alpha_{i} =& \alpha_{i-2}- \alpha_{i-1}q_{i-1}\\
\beta_{i} =& \beta_{i-2} - \beta_{i-1}q_{i-1}
\end{align}
$$
其中初始的$\alpha_{0} = 1,\alpha_1 = 0,\beta_{0} = 0,\beta_{1}=1$这点我们可以当$i=2$时$r_2 = r_0 + q_1r_1$来验证
####JS代码实现
```JS
/*
    扩展欧几里得算法，其中s为alpha,t为beta
    s_i = s_i-2 - s_i-1 * q_i-1
    t_i = t_i-2 - t_i-1 * q_i-t
    初始值
    s_0 = 1    t_0 = 0
    s_1 = 0    t_1 = 1  
*/
const extendedEA = function(a,b){
    if (a < b){
        console.log('Error! a < b');
        return;
    }
    let r = a%b, q = parseInt(a/b);
    let s0 = 1, s1 = 0, t0 = 0, t1 = 1;
    while(r !== 0){
        let s2 = s0 - q*s1, t2 = t0 - q*t1;
        s0 = s1;s1 = s2;
        t0 = t1;t1 = t2;
        a = b;b = r;
        r = a%b;
        q = parseInt(a/b);
    }
    console.log(`gcd: ${b} s: ${s1} t: ${t1}`);
}
```