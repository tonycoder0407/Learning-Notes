# 组合数学的突击复习2333

正如题目所言，为了突击一下所以开个文档

第四章不考，所以应该有

**第1章 排列与组合 第2章 递推关系与母函数 第3章 容斥原理与鸽巢原理**

## 第一章 排列与组合

> 狭义的组合数学是研究满足一定条件的组态（也称组合模型）的存在、计数、分析和优化的一门学科。
>
> 组合数学的主要内容有组合计数、组合设计、组合矩阵、组合优化等。
>
> 离散数学(Discrete mathematics)是数学的几个分支的总称，以研究离散量的结构和相互间的关系为主要目标，其研究对象一般是有限个或可数无穷个元素；因此它充分描述了计算机科学离散性的特点。
>
> 离散数学通常研究的领域包括：数理逻辑、集合论、关系论、函数论、组合学、代数系统与图论。

### 重要词汇

#### Permutation 排列 

> We denote **P(n, r)** the number of r-permutations of an n-element set.
>
> If **r>n**, then **P(n, r)=0**. Clearly **P(n,1)=n** for each positive integer n. An n-permutation of an n-element set S will be more simply called **a permutation of S** or **a permutation of n elements**.
>
> Thus, a permutation of a set S can be thought of as **a listing of the elements of S** in some order. 

$$
P(n, r)=n\times(n - 1) \times \dots \times (n – r + 1)
$$


$$
P(n,r) =\frac {n!}{(n-r)!}
$$
所以有
$$
P(n,r)=0,(r>n)
$$

$$
P(n,1) = n, (n \geq 1)
$$

$$
P(n,r)=n \times\ (n-1) \times \dots \times\ (n-r+1) = \frac {n!}{(n-r)!},  0!=1
$$
当r=n时有，
$$
P(n,n) = n \times (n-1) \times \dots \times 2 \times 1 = n!
$$
从n个对象中取r个沿一圆周排列的排列数用**Q(n,r)**表示，则有

> The number of circular r-permutations of a set of n elements is given by

$$
Q(n,r) = \frac {P(n,r)} {r} = \frac {n!}{r \times\ (n-r)!}
$$

特别的，
$$
Q(n,n) = \frac {P(n,n)} {n} = \frac{n!}{n} = (n-1)!
$$




#### Combination 组合

> Let S be a set of n elements. A combination of a set S is a term usually used to denote an unordered selection of the elements of S. The result of such a selection is a subset A of the elements of S: A ⊆ S. Thus a combination of S is a choice of a subset of S. As a result, the terms combination and subset are essentially interchangeable, and we shall generally use the more familiar subset rather than perhaps the more awkward combination, unless we want to emphasize the selection process.
>
> Now let r be a nonnegative integer. By **an r-combination of a set S of n elements**, we understand an unordered selection of r of the n objects of S. The result of an r-combination is an r-subset of S, a subset of S consisting of r of the n objects of S. 
>
> We denote by C(n, r) the number of r-subset of an n-element set .  

$$
For \ 0 \leq r \leq n, P(n,r) = r! \times C(n,r) \\ C(n,r) \ also \ denoted \ by \ C_{n}^{r}
$$


$$
For \ 0 \leq r \leq n, C_{n}^{r} = C_{n}^{n-r}
$$

$$
For\ all\ integers\ n\ and \ k\ with\ 1\leq k \leq n-1, C_{n}^{k} = C_{n-1}^{k} + C_{n-1}^{k-1}
$$
证明：区分S中的一个特殊元素x，S的k项子集分为两种：包含x与不包含x。

所以包含x的子集个数为C(n-1,k-1)，不包含x的子集个数为C(n-1,k)
$$
For\ n \geq 0, C_{n}^{0}+C_{n}^{1}+C_{n}^{2}+\dots+C_{n}^{n} = 2^n
$$
在n个不同的元素中取r个进行组合，若允许重复，则组合数为           
$$
C_{n+r-1}^{r}
$$
从**A={1,2,…,n}**中取**r**个作不相邻的的组合，其组合数为
$$
C_{n-r+1}^{r}
$$
性质：
$$
C_{n}^{l} \times\ C_{l}^{r} = C_{n}^{r} \times\ C_{n-r}^{l-r}
$$

#### Permutations of Multisets 多重集合的排列

> Let S be a multiset with objects of k different types, where each object has an infinite repetition number. Then the number of r-permutations of S is


$$
k^r
$$


> Let S be a multiset with objects of k different types with finite repetition numbers n<sub>1</sub>,n<sub>2</sub>,…,n<sub>k</sub> , respectively. Let the size of S be n= n<sub>1</sub>+n<sub>2</sub>+…+n<sub>k</sub>. Then the number of permutations of S equals

$$
\frac{n!}{n_1!\times\ n_2!\times \dots \times\ n_k!}
$$

> Let n be a positive integer and let n<sub>1</sub>,n<sub>2</sub>,…,n<sub>k</sub> be positive integers with n= n<sub>1</sub>+n<sub>2</sub>+…+n<sub>k</sub>. The number of ways to partition a set of n objects into k labeled boxes in which Box 1 contains n<sub>1</sub> objects, Box 2 contains n<sub>2</sub> objects, …, Box k contains n<sub>k</sub> objects equals

$$
\frac{n!}{n_1!\times\ n_2!\times \dots \times\ n_k!}
$$

> There are n rooks of k colors with n<sub>1</sub> rooks of the first color, n<sub>2</sub> rooks of the second color, …, and n<sub>k</sub> rooks of the kth color. The number of ways to arrange these rooks on an n-by-n board so that no rook can attack another equals

$$
n!\frac{n!}{n_1!\times\ n_2!\times \dots \times\ n_k!} = \frac{(n!)^2}{n_1!\times\ n_2!\times \dots \times\ n_k!}
$$

#### **Combinations of** Multisets 多重集合的组合

> If S is a multiset, then an r-combination of S is an unordered selection of r of the objects of S. Thus, an r-combination of S (more precisely, the result of the selection) is itself a multiset, a sub-multiset of S of size r, or an r-sub-multiset. If S has n objects, then there is only one n-combination of S, namely, S itself. If S contains objects of k different types, then there are k  1-combinations of S. We generally use combination rather than sub-multiset.
>
> Let S be s multiset with objects of k types, each with an infinite repetition number. Then the number of r-combinations of S equals

$$
C_{r+k-1}^{r} = C_{r+k-1}^{k-1}
$$

## 第二章 递推关系与生成函数

### Intro: Fibonacci Sequences

> The Fibonacci numbers satisfy the formula

$$
f_n = \frac {1}{\sqrt {5}}(\frac {1+\sqrt {5}}{2})^n - \frac {1}{\sqrt {5}}(\frac {1-\sqrt {5}}{2})^n,(n\geq 0)
$$

### Generating Functions

> Let    h<sub>0</sub>, h<sub>1</sub>,h<sub>2</sub>,…,h<sub>n</sub>,…  (2.9)
>
> be an infinite sequence of numbers. Its generating function is defined to be the infinite series
>
> g(x)= h<sub>0</sub>+h<sub>1</sub>x<sub>1</sub>+h<sub>2</sub>x<sub>2</sub>+...+h<sub>n</sub>x<sub>n</sub>+...
>
> The coefficient of x<sub>n</sub> in g(x) is the nth term h<sub>n</sub> of (2.9); thus, x<sub>n</sub> acts as a placeholder for h<sub>n</sub>.

> A finite sequence
>
> ​		h<sub>0</sub>, h<sub>1</sub>,h<sub>2</sub>,…,h<sub>n</sub>,…
>
> can be regarded as the infinite sequence
>
> ​		h<sub>0</sub>, h<sub>1</sub>,h<sub>2</sub>,…,h<sub>n</sub>,0,0,…
>
> in which all but a finite number of terms equal 0. Hence, every finite sequence has a generating function
>
> ​		g(x)=h<sub>0</sub>+h<sub>1</sub>x<sub>1</sub>+h<sub>2</sub>x<sub>2</sub>+...+h<sub>n</sub>x<sub>n</sub>,
>
> which is a polynomial.

> The generating function of the infinite sequence 1,1,1,…,1,…, each of whose terms equals 1, is 
>
> ​             g(x)=1+x+x<sub>2</sub>+…+x<sub>n</sub>+….
>
> This generating function g(x) is the sum of a geometric series with value

$$
g(x) = \frac{1}{1-x}\ \ \ \ \ \ \ \ \ \ \ 									(2.10)
$$

> The formula (2.10) holds the information about the infinite sequence of all 1s in exceedingly compact form.

> Let m be a positive integer. The generating function for the binomial coefficients

$$
C_{m}^{0},C_{m}^{1},C_{m}^{2},C_{m}^{3},\dots,C_{m}^{m},
$$

> is

$$
g_m(x)=C_{m}^{0}+C_{m}^{1}x+C_{m}^{2}x^2+C_{m}^{3}x^3+\dots+C_{m}^{m}x^m
$$

> By the binomial theorem,  g<sub>m</sub>(x)=(1+x)<sup>m</sup>,
>
> which also displays the information about the sequence of binomial coefficients in compact form.

### Exponential Generating Functions

> These monomials arise in the Taylor series

$$
e^x = \sum_{n=0}^{\infty} \frac{x^n}{n!} = 1+x+\frac{x^2}{2!}+\dots+\frac{x^n}{n!}+\dots
$$

> Generating functions considered with respect to the monomials (2.17) are called exponential generating functions. The exponential generating functions for the sequence h<sub>0</sub>,h<sub>1</sub>,h<sub>2</sub>,…,h<sub>n</sub>,…is defined to be 

$$
g^{(e)}(x) = \sum_{n=0}^{\infty} h_n\frac{x^n}{n!} = h_0+h_1x+h_2\frac{x^2}{2!}+\dots+h_n\frac{x^n}{n!}+\dots
$$

> Let n be a positive integer. Determine the exponential generating function for the sequence of numbers
>
> ​         P(n,0),P(n,1),P(n,2),…,P(n,n),
>
> where P(n, k) denotes the number of k-permutations of an n-element set, and thus has the value n!/(n-k)! for k=0,1,…,n. The exponential generating function is 

$$
g^{(e)}(x)=P(n,0)+P(n,1)x+\frac{P(n,2)x^2}{2!}+\dots+\frac{P(n,n)x^n}{n!} 
\\  =1+nx+\frac{n!}{2!\times (n-2)!} x^2+\dots+\frac{n!}{n!\times 0!}x^n
\\    =(1+x)^n
$$

> The exponential generating function for the sequence 1,1,1,…,1,…
>
> is

$$
g^{(e)}(x)= \sum_{n=0}^{\infty} \frac{x^n}{n!} = e^x
$$

> More generally, if a is any real number, the exponential generating function for the sequence 
>
> ​       a<sub>0</sub>=1,a,a<sub>2</sub>,…,a<sub>n</sub>,…
>
> is

$$
g^{(e)}(x)= \sum_{n=0}^{\infty} a^n \frac{x^n}{n!}= \sum_{n=0}^{\infty}  \frac{(ax)^n}{n!} = e^{ax}
$$

### 递推关系参考高等数学以及线性代数内容，这里不单独展示。

### Catalan Numbers

> The *Catalan sequence* is the sequence C<sub>0</sub>,C<sub>1</sub>,…,C<sub>n</sub>,…, where  

$$
C_{n} = \frac{1}{n+1}C_{n}^{2n},(n=0,1,2,\dots)
$$

> C0=1   C1=1   C2=2   C3=5   C4=14
>
> C5=42  C6=132  C7=429 C8=1430 C9=4862

### Difference Sequences

> Let  h<sub>0</sub>,h<sub>1</sub>,…,h<sub>n</sub>,,…                   (2.61)
>
>   be a sequence of numbers. We define a new sequence
>
>      △h<sub>0</sub>,△h<sub>1</sub>,△h<sub>2</sub>,…,△h<sub>n</sub>,…,    (2.62)
>
>  called the (first-order) difference sequence of (2.61), by
>
>      △h<sub>n</sub>= h<sub>n+1</sub>-h<sub>n</sub>, (n ≥ 0).
>
> The terms of the difference sequence (2.62) are the differences of consecutive terms of the sequence (2.61). We can form the difference sequence of (2.62) and obtain the second-order difference sequence
>
>     △<sup>2</sup>h<sub>0</sub>,△<sup>2</sup>h<sub>1</sub>,△<sup>2</sup>h<sub>2</sub>,…,△<sup>2</sup>h<sub>n</sub>,….

