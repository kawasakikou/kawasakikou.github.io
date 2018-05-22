---
layout: math 
title:  "Differential of softmax with loss func of cnn"
date:   2017-09-26 14:38:14 +0900
---

-----
# 出力層について

$$
\begin{align*}
  x_i = \sum w_ji y_i + b_j \\
  p_j = \frac{e^{x_i}}{\sum_{k=1}^{n} e^{x_k}}
\end{align*}
$$


($$x_i \rightarrow \infty$$の場合もありうるので注意)
であり、

d_iを1,正しいカテゴリ 2,その他とすると、Cは

$$
\begin{align*}
  C = -\sum_{i=1}^{n}d_i log p_i (サンプル数1の場合) \\
  C = -\sum_{s=1}^{m} \sum_{i=1}^{n} d_i^s log p_i^s
(サンプル数mの場合)
\end{align*}
$$

よって、

$$
\begin{align*}
  \frac{\partial C}{\partial w_ji} =
  \frac{\partial C}{\partial p_j}
  \frac{\partial p_j}{\partial x_j} 
  \frac{\partial x_j}{\partial w_ji}
\end{align*}
$$

また、

$$
\begin{align*}
  \frac{\partial C}{\partial b_j} =
  \frac{\partial C}{\partial p_j}
  \frac{\partial p_j}{\partial x_j} 
  \frac{\partial x_j}{\partial b_j}
\end{align*}
$$


サンプル数1の場合

$$
\begin{align*}
  \frac{\partial C}{\partial p_j} =
  \frac{d_j}{p_j}
\end{align*}
$$

サンプル数mの場合

$$
\begin{align*}
  \frac{\partial C}{\partial p_j} =
  \frac{d_j^s}{p_j^s}
\end{align*}
$$

$$ \frac{\partial p_j}{\partial w_j}$$を求めるために、
各要素を微分で求める。


(i) $$\frac{\partial p_i}{\partial x_i}$$

(i=jの時)

$$
\begin{align*}
  \frac{\partial p_i}{\partial x_i} &=

  \left( \frac{e^{x_i}}{\sum_{k=1}^{n} e^{x_k}} \right)' \\

  & =\frac{e^{x_i}}{\sum_{k=1}^{n} e^{x_k}} -
  \frac{e^{2x_i}}{(\sum_{k=1}^{n} e^{x_k})^2} \\

  & = \frac{e^{x_i}}{\sum_{k=1}^{n} e^{x_k}} 
  \left( 1 - \frac{e^{x_i}}{(\sum_{k=1}^{n} e^{x_k})} \right) \\

  & = p_i(1-p_i) (sigmoidの微分と同じ形)
\end{align*}
$$


($$i \ne j$$の時)

$$
\begin{align*}
  \frac{\partial p_i}{\partial x_j} &=

  \frac{-e^{x_i}e^{x_j}}{(\sum_{k=1}^{n} e^{x_k})^2}  \\

  & = -p_i p_j
\end{align*}
$$

(ii) 

$$ \frac{\partial x_j}{\partial w_ji} = y_i $$

(iii)

$$ \frac{\partial x_j}{\partial b_j} = 1$$

以上より、

$$
\begin{align*}
  \frac{\partial C}{\partial w_ji} &=
  \frac{\partial C}{\partial p_j}
  \frac{\partial p_j}{\partial x_j} 
  \frac{\partial x_j}{\partial w_ji} \\

  &= -\sum_{s=1}^{m} \left(
      \frac{d_j^s}{p_j^s}
      \cdot p_i^s(1-p_i^s)
      \cdot y_i^s
      \right)

\end{align*}
$$

$$
\begin{align*}
  \frac{\partial C}{\partial b_j} &=
  \frac{\partial C}{\partial p_j}
  \frac{\partial p_j}{\partial x_j} 
  \frac{\partial x_j}{\partial b_j} \\

  &= -\sum_{s=1}^{m} \left(
      \frac{d_j^s}{p_j^s}
      \cdot p_i^s(1-p_i^s)
      \cdot 1
      \right)

\end{align*}
$$

w_ji: j番目の出力ユニットのi番目の入力

$$
\begin{align*}
\varDelta w_{ji}^t = 
  -\epsilon
  \frac{\partial C}{\partial w_{ji}^t}
  + \alpha \varDelta w_{ji}^{t-1}
  - \epsilon \lambda w_{ji}^t

\end{align*}
$$

$$
\begin{align*}
\varDelta b_{j}^t = 
  -\epsilon
  \frac{\partial C}{\partial b_j}
  + \varDelta b_j^{t-1}
  - \epsilon \lambda b_i^t

\end{align*}
$$

------

# pooling layerについて

* 学習しない
* 入力チャネルごとに独立して処理をする

誤差情報を入力側の畳み込み層に逆伝播する必要がある。

入出力

k: チャネル
P_{ij}: 小領域


平均プーリング
$$
\begin{align*}
  y_{ijk} = \frac{1}{|P_{ij}|} 
  \sum_{(p,q) \in P_{ij}} y_{pqk}
\end{align*}
$$

maxプーリング
$$
\begin{align*}
  y_{ijk} = max_{(p,q) \in P_{ij}} y_{pqk}
\end{align*}
$$

Lpプーリング
$$
\begin{align*}
  y_{ijk} = 
  \left(
  \frac{1}{|P_{ij}|} 
  \sum_{(p,q) \in P_{ij}} y_{pqk}^p
  \right)
  ^ \frac{1}{p}
\end{align*}
$$

  (a)出力層からの逆伝播


  $$y_{ijk}^s$$ : P layerの出力

  $$y_{abk}^s$$ : P layerの入力

  k :チャネル 


  $$
  \begin{align*}
  \frac{\partial C}{\partial y_{abk}} =
  \sum_{s=1}^{m} \sum_{p=1}^{n}
  \frac{\partial C}{\partial x_p^s}
  \frac{\partial x_p^s}{\partial y_{ijk}^s} 
  \frac{\partial y_{ijk}^s}{\partial y_{abk}^s}
  \end{align*}
  $$

  上式の偏微分について
  $$
  \begin{align*}
  \frac{\partial x_{pql}}{\partial y_{ijl}} = w_{ijl}
  \end{align*}
  $$



  ここで、

  $$
  \begin{align*}
  \sum_{s=1}^{m} \sum_{p=1}^{n}
  \frac{\partial C}{\partial x_p^s}
  \end{align*}
  $$
  の部分は出力層での計算結果を使用する。


  (b)convolution層からの逆伝播

  $$x_{ijl}$$: Clayerのチャネルlの位置p,qの入力の荷重和バイアス

  N: Clayerのチャネル数

  $$P_{ij}$$: P layerの出力が対応するClayerのユニット

$$
\begin{align*}
  \frac{\partial C}{\partial y_abk} =
  \sum_{s=1}^{m}
  \left(
  \sum_{p=1}^{n} \sum_{p,q \in P_ij}
  \frac{\partial C}{\partial x_{ijl}^s}
  \frac{\partial x_{ijl}^s}{\partial y_{ijk}^s} 
  \right)
  \frac{\partial y_{ijk}^s}{\partial y_{abk}^s}
\end{align*}
$$

上式の偏微分について
$$
\begin{align*}
  \frac{\partial x_{pql}}{\partial y_{ijl}} =
\end{align*}
$$
C layerのフィルタに対応する位置の値
(アルゴリズムをあとで考える)

$$ \frac{\partial x_{ijl}^s}{\partial y_{ijk}^s} $$については
次の逆伝播のClayerで計算すべきなので、この層では計算しない

平均プーリング微分
$$
\begin{align*}
  \frac{\partial y_{ijk}}{\partial y_{abk}} =
  \frac{1}{|P_{ij}|} 
\end{align*}
$$

maxプーリング微分
$$
\begin{eqnarray}
\frac{\partial y_{ijk}}{\partial y_{abk}} =
  \begin{cases}
    1 :(P_{ij}小領域最大値の位置) \\
    0 :その他
  \end{cases}
\end{eqnarray}
$$

Lpプーリング微分
$$
\begin{align*}
  \frac{\partial y_{ijk}}{\partial y_{abk}} =
  \frac{1}{P}
  \left(
  \frac{1}{|P_{ij}|} 
  \sum_{(p,q) \in P_{ij}} y_{pqk}^p
  \right)
  ^{\frac{1}{p} - 1}
\end{align*}
$$


------
# Convolution layerについて

入出力

$$
\begin{align*}
  x_{ijl} =
  \sum_{k=1}^{N}
  \left[
  \sum_{p=1}^w \sum_{q=1}^w
  y_{i+p, j+q, k} h_{pqk}
  \right]
  + b_{ijl}
\end{align*}
$$

$$b_{ijl}$$はユニット毎に変更する

$$l$$: c layerのチャネル

ij: c layerのユニットの位置

$$y_{ijl} = \frac{1}{1+e^x_{ijl}}$$

プーリング層では畳み込み層のチャネルと1対1対応だが、
**フィルタが入力チャネル一つに一つ**なので

$$Q_{pql}$$: フィルタの位置pqが荷重として働いている位置

$$
\begin{align*}
  \frac{\partial C}{\partial h_{pqk}} =
  \sum_{r,s \in Q_{pq}}
  \frac{\partial C}{\partial x_{rsl}}
  \frac{\partial x_{rsl}}{\partial h_{ijl}^s}
\end{align*}
$$


$$ \frac{\partial C}{\partial h_{pqk}}$$を求めるために、
各要素を微分で求める。

(i) $$ \frac{\partial C}{\partial x_{rsl}} $$

$$y_{pql}^s$$: p layerの出力
$$y_{rsl}^s$$: 保留

$$P_{ij}$$: 現在計算しているユニットからの入力を受けるp layerのユニット

$$
\begin{align*}
  \frac{\partial C}{\partial x_{rsl}} =
  \sum_{s=1}^{m}
  \sum_{r,s \in Q_{pq}}
  \frac{\partial C}{\partial y_{pql}^s}
  \frac{\partial y_{pql}^s}{\partial y_{rsl}^s}
  \frac{\partial y_{rsl}^s}{\partial x_{rsl}^s}
\end{align*}
$$

ここで、

$$
\begin{align*}
  \sum_{s=1}^{m}
  \sum_{r,s \in Q_{pq}}
  \frac{\partial C}{\partial y_{pql}^s}
\end{align*}
$$

は、pooling layerでの計算結果を使う。

(ii) $$ \frac{\partial y_{rsl}}{\partial x_{rsl}} $$

$$
\begin{align*}
  \frac{\partial y_{rsl}}{\partial x_{rsl}} &=
  \left(
  \frac{1}{1 + e^{-x_{rsl}}}
  \right)' \\
  &= y_{rsl} (1 - y_{rsl})
\end{align*}
$$

(ii)$$ \frac{\partial y_{rsl}}{\partial x_{rsl}} = $$対応する入力$$y_{ijk}$$


$$
\begin{align*}
  \frac{\partial C}{\partial b_{ijk}} =
  \sum_{s=1}^{m}
  \sum_{r,s \in Q_{pq}}
  \frac{\partial C}{\partial y_{pql}^s}
  \frac{\partial y_{pql}^s}{\partial y_{ijl}^s}
  \frac{\partial y_{ijl}^s}{\partial x_{ijl}^s}
  \cdot
  \frac{\partial x_{ijl}^s}{\partial b_{ij}}
\end{align*}
$$

ここで、
$$ \frac{\partial x_{ijl}^s}{\partial b_{ij}} = 1$$


