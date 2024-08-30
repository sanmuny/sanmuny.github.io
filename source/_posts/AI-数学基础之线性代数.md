---
title: 'AI-数学基础之线性代数【草稿】'
tags:
  - AI
categories: AI
published: true
hideInList: false
isTop: false
cover: /images/ai.jpg
date: 2024-08-23 16:06:44
feature:
---


### 线性方程组

\begin{cases}
c_{11}x_1 + c_{12}x_2 +\cdots + c_{1n}x_n = d_1\\\\
c_{21}x_1 + c_{22}x_2 +\cdots + c_{2n}x_n = d_2\\\\
\vdots\\\\
c_{m1}x_1 + c_{m2}x_2 +\cdots + c_{mn}x_n = d_m
\end{cases}

#### 用矩阵表示线性方程组

* 系数矩阵：去掉未知数和常数形成的矩阵
\begin{pmatrix}
c_{11} & c_{12} &\cdots & c_{1n}\\\\
c_{21} & c_{22} &\cdots & c_{2n}\\\\
\vdots &\vdots &\ddots &\vdots\\\\
c_{m1} & c_{m2} &\cdots & c_{mn}
\end{pmatrix}
* 增广矩阵：系数矩阵加上常数
\begin{pmatrix}
c_{11} & c_{12} &\cdots & c_{1n} &\vert& d_1\\\\
c_{21} & c_{22} &\cdots & c_{2n} &\vert& d_2\\\\
\vdots &\vdots &\ddots &\vdots &\vert&\vdots\\\\
c_{m1} & c_{m2} &\cdots & c_{mn} &\vert& d_m
\end{pmatrix}

#### 高斯消元法解线性方程组

矩阵的初等变换

* 互换: 行互换 $R_{ij}$，列互换 $C_{ij}$
* 倍乘: 用一个不为0的常数c乘以一个方程。 $cR_{i}$
* 倍加: 将第i个方程倍乘后加到第j个方程。 $R_{j} + cR_{i}$

求解步骤

1. 利用初等变换将矩阵转化为阶梯矩阵
\begin{pmatrix}
c_{11} & c_{12} &\cdots & c_{1n} &\vert& d_1\\\\
0 & c_{22} &\cdots & c_{2n} &\vert& d_2\\\\
\vdots &\vdots &\ddots &\vdots &\vert&\vdots\\\\
0 & 0 &\cdots & c_{rr} &\vert& d_r\\\\
0 & 0 &\cdots & 0 &\vert& 0\\\\
\vdots &\vdots &\ddots &\vdots &\vert&\vdots\\\\
0 & 0 &\cdots & 0 &\vert& 0
\end{pmatrix}

r为矩阵的秩
* r = n, 则线性方程组有唯一解
* r < n, 则线性方程组有无数个解
* $d_{r+1}$ != 0, 则线性方程组没有解

2. 利用初等变换将阶梯头变成1，然后令没有阶梯头的未知数等于$t_{1...}$， $t_{i}为任意数，通过移项则可得出所有未知数的解

举个例子：

对于线性方程组  ：

\begin{cases}
x_1 + 2x_2 + 3x_3 + 4x_4 + 5x_5 = 8 \\\\
2x_1 + 4x_2 + 6x_3 + 8x_4 + 10x_5 = 16 \\\\
3x_1 + 6x_2 + 9x_3 + 12x_4 + 15x_5 = 24 \\\\
x_1 + x_2 + x_3 + x_4 + x_5 = 3 \\\\
2x_1 + 3x_2 + 4x_3 + 5x_4 + 6x_5 = 11
\end{cases}

对应的增广矩阵为：

\begin{pmatrix}
1 & 2 & 3 & 4 & 5 &\vert& 8\\\\
2 & 4 & 6 & 8 & 10 &\vert& 16\\\\
3 & 6 & 9 & 12 & 15 &\vert& 24\\\\
1 & 1 & 1 & 1 & 1 &\vert& 3\\\\
2 & 3 & 4 & 5 & 6 &\vert& 11
\end{pmatrix}

第一步：$R_{2}-2R_{1}$, $R_{3}-3R_{1}$, $R_{4}-R_{1}$, $R_{5}-2R_{1}$


\begin{pmatrix}
1 & 2 & 3 & 4 & 5 &\vert& 8\\\\
0 & 0 & 0 & 0 & 0 &\vert& 0\\\\
0 & 0 & 0 & 0 & 0 &\vert& 0\\\\
0 & -1 & -2 & -3 & -4 &\vert& -5\\\\
0 & -1 & -2 & -3 & -4 &\vert& -5
\end{pmatrix}

第二步：$R_{5}-R_{4}$

\begin{pmatrix}
1 & 2 & 3 & 4 & 5 &\vert& 8\\\\
0 & 0 & 0 & 0 & 0 &\vert& 0\\\\
0 & 0 & 0 & 0 & 0 &\vert& 0\\\\
0 & -1 & -2 & -3 & -4 &\vert& -5\\\\
0 & 0 & 0 & 0 & 0 &\vert& 0
\end{pmatrix}

第三步：${R_{24}}$

\begin{pmatrix}
1 & 2 & 3 & 4 & 5 &\vert& 8\\\\
0 & -1 & -2 & -3 & -4 &\vert& -5\\\\
0 & 0 & 0 & 0 & 0 &\vert& 0\\\\
0 & 0 & 0 & 0 & 0 &\vert& 0\\\\
0 & 0 & 0 & 0 & 0 &\vert& 0
\end{pmatrix}

矩阵的秩为2 < 5, 且$d_{3}$ = 0, 所以矩阵有无数个解。

第四步，$-{R_{2}}$, $R_1-2R_2$

\begin{pmatrix}
1 & 0 & -1 & -2 & -3 &\vert& -2\\\\
0 & 1 & 2 & 3 & 4 &\vert& 5\\\\
0 & 0 & 0 & 0 & 0 &\vert& 0\\\\
0 & 0 & 0 & 0 & 0 &\vert& 0\\\\
0 & 0 & 0 & 0 & 0 &\vert& 0
\end{pmatrix}

第五步，另$x_3$ = $t_1$, $x_4$ = $t_2$, $x_5$ = $t_3$, t为任意数，则可以得到方程的解为
\begin{pmatrix}
x_1=-2+t_1+2t_2+3t_3 \\\\
x_2=5-2t_1-3t_2-4t_3 \\\\
x_3=t_1 \\\\
x_4=t_2 \\\\
x_5=t_3 \\\\ 
\end{pmatrix}