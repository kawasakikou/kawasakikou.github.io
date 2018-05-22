---
layout: math 
title:  "Euclidean Algorithm"
date:   2017-02-13 10:30:20 +0900
categories: math 
---

# 最大公約数(greatest common divisor)


>Def(最大公約数)
>
>$$a_1,...,a_n \in \Bbb Z$$の最大公約数とは
>
>$$a_1,...,a_n$$を
>
>$$
>\begin{align*}
>  a_j = \varepsilon_j \prod_{p\ prime} p^ {e_p(j)} \quad (e_p(j) \ge 0, \varepsilon_j = \pm 1)
>\end{align*}
>$$
>
>と素因数分解したとき
>
>$$
>\begin{align*}
>  gcd(a_1,...,a_n) = \prod_{p\ prime} p^ {\min(e_p(1),...,e_p(n))}
>\end{align*}
>$$
>
>である。

------
# ユークリッドの互除法(Euclidean Algorithm)

Thm(Euclidean Algorithm)

$$
\begin{align*}
 \forall a, b \in \Bbb N, 
 a=bq+rの時,
\end{align*}
$$

$$ gcd(a,b) = gcd(b,r)$$

------
proof1

方針: gcd(a,b)とgcd(b,r)の関係性を考える。公約数が最大公約数以下であることを用いて等式に持っていく

$$gcd(a,b)= g, \\ gcd(b,r) = h$$

とする。

(公約数の定義を思い出すと倍数でかけることを思いつく)   
a,bはgの倍数であるので、

$$a = a'g, b = b'g (\exists a' b' \in N) \tag{1}$$
と表すことができる   

(どうにかgとhを絡められないかを考える。  
$$gcd(a,b)$$がrで割れて,$$gcd(b,r)$$がaで割れるという関係性を考えようとする)

$$a = qb + r \tag{2}$$

より

$$r = a - qb$$

これに(1)を代入して

$$
\begin{align*}
  r &= a'g - qb'g \\
  & = (a' - qb')g

\end{align*}
$$


**これよりrはgを約数として持つため、gはbとrの公約数である。**
  
**公約数は最大公約数以下なので$$g \le h$$**


また、b,rはhの倍数なので、
$$b = c'h, r = r'h (\exists c' h' \in N) \tag{3}$$

(2)に(3)を代入して、

$$
\begin{align*}
  a &= qb + r   \\
  &= qc'h + r'h \\
  & = (qc' + r)h

\end{align*}
$$

**よってaはhを約数として持つため、hはaとbの公約数である。**
  
**公約数は最大公約数以下なので$$g \ge h$$**

$$g \le h$$ かつ $$g \ge h$$なので g=h $$\Box$$

-----
proof2
(1)まで同様
$$a = ga' , b = gb' \tag{1}$$と表せる。

(1)の時、
$$gcd(a', b') = 1　\tag{2}$$となることを示す。
(互いに素)

-----


$$
\begin{align*}

\end{align*}
$$


# 参考文献
[Wikipedia:ユークリッドの互除法](https://ja.wikipedia.org/wiki/%E3%83%A6%E3%83%BC%E3%82%AF%E3%83%AA%E3%83%83%E3%83%89%E3%81%AE%E4%BA%92%E9%99%A4%E6%B3%95)  
  * define greatest common divisor

[書籍:ガロア理論の頂を踏む](https://www.beret.co.jp/books/detail/487)
  * proof1
