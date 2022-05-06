---
title: Discrete Math Review (1)
date: 2022-02-16 16:08:03
description: 南京大学2021春季学期《离散数学》课程笔记(1)
top_img: https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/miku18.jpg
tags:
    - SELearning
    - Discrete Math
categories:
    - Computer Science
    - Discrete Math
---

## 1 命题逻辑

**Definition(命题(Proposition))**

**命题**是可以判定真假的陈述句(不可既真又假)。

**Definition(命题逻辑的语言)**

命题逻辑包括以下三个部分: 

1. 任意多个**命题符号**：$A_0$, $A_1$, $P$, $Q$, ......;
2. 5个**逻辑连词**：$\lnot$, $\land$, $\lor$, $\to$, $\leftrightarrow$
3. 左括号，右括号

**Definition(公式(Formula))**

1. 每个命题符号都是公式；
2. 如果 $\alpha$ 和 $\beta$ 都是公式，则 ($\lnot \alpha$), ($\alpha \land \beta$), ($\alpha \lor \beta$), ($\alpha \to \beta$) 和 ($\alpha \leftrightarrow \beta$) 都是公式
3. **除此之外别无其他**。

**Lemma (公式的括号匹配性质)**

​	每个公式中左右括号的数目相同。

**Theorem(归纳原理)**

令 $P(\alpha)$ 为一个关于公式的性质。假设
(1) 对所有的命题符号 $A_i$, 性质 $P(A_i)$ 成立; 并且
(2) 对所有的公式 $\alpha$ 和 $\beta$ , 如果 $P(\alpha)$ 和 $P(\beta)$ 成立, 则 $P((\lnot \alpha))$ ，$P((\alpha * \beta))$ 也成立,
那么 $P(\alpha)$ 对所有的公式 $\alpha$ 都成立。

**Definition(公式的长度)**

公式 $\alpha$ 的长度 $\| \alpha \|$ 定义如下:

1. 如果 $\alpha$ 是命题符号，那么$\| \alpha \|$ $=$ 1;
2. 如果 $\alpha$ $=$ ($\lnot \beta$), 则 $\| \alpha\|$ $=$ 1 + $\|\beta \|$
3. 如果 $\alpha = (\beta * \gamma)$, 则 $\| \alpha \| = 1 + \| \beta \| + \| \gamma \|$

**<span style='color:red'>Theorem</span>**

1. **交换律**：...
2. **结合律**：...
3. **分配率**：...
4. **德摩根律**：$\lnot(A \land B) \leftrightarrow (\lnot A \lor \lnot B)$
5. **双重否定律**：$\lnot \lnot A \leftrightarrow A$
6. **排中律**：$A \lor (\lnot A)$
7. **矛盾律**：$\lnot (A \land \lnot A)$
8. **逆否命题**：$(A \to B) \leftrightarrow (\lnot B \to \lnot A)$

**<span style='color:red'>Theorem</span>**

1. $\alpha \to (\beta \to \alpha)$
2. $(\alpha \to \beta) \leftrightarrow (\lnot \alpha \lor \beta)$ 
3. $(\alpha \to (\beta \to \gamma)) \leftrightarrow ((\alpha \land \beta) \to \gamma)$
4. $(\alpha \to (\beta \to \gamma)) \to ((\alpha \to \beta) \to (\alpha \to \gamma))$

**<span style='color:red'>Theorem</span>($\land$ 推理规则)**

1. $\frac{P\quad Q}{P \land Q}$
2. $\frac{P \land Q}{P}$
3. $\frac{P \land Q}{Q}$

**<span style='color:red'>Theorem</span>($\lnot$ $\lnot$ 推理规则)**

1. $\frac{\alpha}{\lnot \lnot \alpha}$
2. $\frac{\lnot \lnot \alpha}{\alpha}$

**<span style='color:red'>Theorem</span>($\to$ 推理规则)**

* $\vdash p \to p$
* $\{\lnot q \to \lnot p\} \vdash p \to \lnot \lnot q$
* $\{p \land q \to r \} \vdash p \to (q \to r)$

**<span style='color:red'>Theorem</span>($\lor$ 推理规则)**

1. $\frac{\alpha}{\alpha \lor \beta}$
2. $\frac{\beta}{\alpha \lor \beta}$
3. $\frac{\alpha \lor \beta \quad \alpha \to \gamma \quad \beta \to \gamma}{\gamma}$

**<span style='color:red'>Theorem</span>($\perp$ 推理规则)**

1. $\frac{\alpha \quad \lnot \alpha}{\perp}$

****



## 2 一阶谓词逻辑

**Definition(一阶谓词逻辑的语言(Language))**

​	一阶谓词逻辑的语言包括了:

1. 逻辑连词
2. 量词符号: $\forall \quad \exists$
3. 变元符号
4. 左右括号



### 一些变换

1. $$
	\forall x.\alpha \land \forall.\beta \leftrightarrow \forall x.(\alpha \land \beta)
	$$

2. $$
	\exists x.\alpha \lor \exists x.\beta \leftrightarrow \exists x.(\alpha \lor \beta)
	$$

3. $$
	\forall x.\alpha \rightarrow \exists x.\alpha
	$$

4. $$
	\exists x.\forall y.\alpha \rightarrow \forall y.\exists x.\alpha
	$$

5. $$
	\forall y.\exists x.\alpha \nrightarrow \exists x.\forall y.\alpha
	$$

6. $$
	\forall x.\alpha \lor \forall x.\beta \rightarrow \forall x.(\alpha \lor \beta)
	$$

7. $$
	\exists x.(\alpha \lor \beta) \rightarrow \exists x.\alpha \lor \exists x.\beta
	$$

8. 以下变换要求 $\beta$ 中不含有 $x$

	1. $$
		\forall x.(\alpha \lor \beta) \leftrightarrow (\forall x.\alpha) \lor \beta
		$$

	2. $$
		\forall x.(\alpha \land \beta) \leftrightarrow (\forall x.\alpha) \land \beta
		$$

	3. $$
		\exists x.(\alpha \lor \beta) \leftrightarrow (\exists x.\alpha) \lor \beta
		$$

	4. $$
		\exists x.(\alpha \land \beta) \leftrightarrow (\exists x.\alpha) \land \beta
		$$



### 推理规则

1. $\forall - elim$

	$\frac{\forall x. \alpha}{\alpha [t/x]}$ 即：**可以用确定的t来代替任意的x，来消去任意符号**

2. $\forall - intro$

	$\quad [t] \\ \ \quad. \\ \quad. \\ \frac{\alpha[t/x]}{\forall x. \alpha}$ 即: **任取t，如果能证明alpha对t成立，则alpha对所有x成立**

3. $\exists - intro$

	$\frac{\alpha[t/x]}{\exists x. \alpha}$ 即：**用原本不在公式中的x来取代t，则可得到存在某个x使alpha成立，即可以获得存在符号**

	$for\ example:$ $P(c) \vdash \exists x.P(x)$

4.  $\exists - elim$

	$\frac{\exists x. \alpha}{\alpha[x_0/x]}$ 即：**可用原本不在公式中的** $x_0$ **来消去存在符号**

	$for\ example:\ \forall x. \alpha \to \exists x. \alpha$

****



## 3 数学归纳法

<span style='color: red'>**Theorem(第一数学归纳法)**</span>

设 $P(n)$ 是关于自然数的一个性质。如果

1. $P(0)$ 成立；
2. 对任意自然数 $n$，如果 $P(n)$ 成立，则 $P(n+1)$ 成立。

那么， $P(n)$ 对所有自然数 $n$ 都成立。



**Theorem(第二数学归纳法)**

设 $Q(n)$ 是关于自然数的一个性质。如果

1. $Q(0)$ 成立；
2. 对任意自然数 $n$， 如果 $Q(0), Q(1), \dots, Q(n)$ 都成立，那么 $Q(n+1)$ 成立。

那么，$Q(n)$ 对所有自然数 $n$ 都成立。



**Theorem(数学归纳法)**

​	第一数学归纳法与第二数学归纳法等价。

<span style='color: red'>**Definition(良序原理)**</span>

​	自然数集的任意非空子集都有一个最小元

**Theorem**

​	良序原理与(第一)数学归纳法等价

**Theorem(费马小定理)**

对于任意自然数 $a$ 与任意素数 $p$， $a^p \equiv a\ (mod\ p)$

**Theorem(做题步骤)**

1. Basis Step(基础步骤):
2. Induction Hypothesis(归纳假设):
3. Induction Step:(归纳步骤)

****



## 4 集合：基本概念与运算

### 集合的基本概念

**Definition(集合)**

<span style='color: red'>**集合**</span>就是任何一个有明确定义的对象的整体

**Theorem(概括原则)**

对于任意性质/谓词 $P(x)$， 都存在一个集合 $X$:  $X = \{x \| P(x) \}$



**Definition(外延性原理)**

两个集合相等 $(A = B)$ 当且仅当它们包含相同的元素

<span style='color: red'>**Theorem(两个集合相等)**</span>

两个集合相等当且仅当它们互为子集(证明两个集合相等的基本方法).

​								$A = b \Longleftrightarrow A \subseteq B \land B \subseteq A$

### 集合的运算(一)

**Definition(集合的并、交、相对补，绝对补)**

1. $A \cup B = \{x\|x \in A \lor x \in B \}$
2. $A \cap B = \{x\|x \in A \land x \in B \}$
3. $A \setminus B = \{x\| x \in A \land x \notin B \}$
4. $\overline{A} = U \setminus A = \{x \in U \| x \notin A\}$

<span style='color: red'>**Remark**:</span> 不存在包罗万象的全集



**Theorem(分配律)**

1. $A \cup (B \cap C) = (A \cup B) \cap (A \cup C)$
2. $A \cap (B \cup C) = (A \cap B) \cup (A \cap C)$

**Theorem(吸收律)**

1. $A \cup (A \cap B) = A$
2. $A \cap (A \cup B) = A$

**Theorem**

​	$A \subseteq B \Longleftrightarrow A \cup B = B \Longleftrightarrow A \cap B = A$

**Theorem(相对补与绝对补的关系)**

设全集为 $U$。		$A \setminus B = A \cap \overline{B}$

<span style='color: red'>**Theorem(德摩根律(绝对补))**</span>

设全集为 $U$。

1. $\overline{A \cup B} = \overline{A} \cap \overline{B}$
2. $\overline{A \cap B} = \overline{A} \cup \overline{B}$

**Theorem**

1. $A \cap (B \setminus C) = (A \cap B) \setminus C = (A \cap B) \setminus (A \cap C)$
2. $A \setminus (B \setminus C) = (A \cap C) \cup (A \setminus B)$

3. $A \subseteq B \Longrightarrow \overline{B} \subseteq \overline{A}$
4. $A \subseteq B \Longrightarrow (B \setminus A) \cup A = B$

**Theorem(对称差)**

​	$A \oplus B = (A \setminus B) \cup (B \setminus A) = (A \cap \overline{B}) \cup (B \cap \overline{A})$

1. $(A \oplus B) \oplus C = A \oplus (B \oplus C)$
2. $A \oplus B = \overline{A} \oplus \overline{B}$
3. $A \oplus B = A \oplus C \Longrightarrow B = C$



### 集合的运算(二)

<span style='color: red'>**Definition(广义并)**</span>

设 $\mathbb{M}$ 是一组集合 $(a\ collection\ of\ sets)$
$$
\bigcup M = \{x |\exists A \in M.x \in A \}
$$
<span style='color: red'>**Definition(广义交)**</span>

设 $\mathbb{M}$ 是一组集合$(a\ collection\ of\ sets)$
$$
\bigcap M = \{x |\forall A \in M.x \in A \}
$$
**Theorem(德摩根律)**

1. $X \setminus \bigcup_{\alpha \in I}A_{\alpha} = \bigcap_{\alpha \in I}(X \setminus A_{\alpha})$
2. $X \setminus \bigcap_{\alpha \in I}A_{\alpha} = \bigcup_{\alpha \in I}(X \setminus A_{\alpha})$



### 集合的运算(三)

<span style='color: red'>**Definition(幂集)**</span>
$$
\mathbb{P}(A) = \{X | X \subseteq A\}
$$
**Theorem**
$$
{\color{Red} S \in \mathbb{P}(X) \Longleftrightarrow S \subseteq X}
$$

****



## 5 集合：关系(Relation)

**Definition(有序对公理(Ordered Pairs))**
$$
(a, b) = (c, d) \Longleftrightarrow a = c \land b = d
$$
**Definition(Ordered Pairs)**
$$
(a, b) {\color{Red} =} \{\{a\}, \{a, b\}\}
$$
**Definition(笛卡尔积)**

The ${\color{Red} Cartesian\ product} \ A \times B$ of ${\color{Blue} A\ and\ B}$ is defined as
$$
A \times B = \{(a, b)|a \in A \land b \in B \}
$$
 <span style='color: red'>**Remark:**</span> 

1. $X \times Y \ne Y \times X$
2. $(X \times Y) \times Z \neq X \times (Y \times Z)$



**Theorem(分配率)**
$$
A \times (B \cap C) = (A \times B) \cap (A \times C) \\
A \times (B \cup C) = (A \times B) \cup (A \times C) \\
A \times (B \setminus C) = (A \times B) \setminus (A \times C)
$$
 **Definition(关系(Relations))**

A ${\color{Red} relation}$ R from $A$ to $B$ is a subset of $A \times B$:
$$
R \subseteq A \times B
$$
**Remark: ** 如果 $A = B$ 那么 $R$ 叫做 $A$ 上的关系

**Definition**
$$
(a, b) \in R \qquad aRb \\
(a, b) \notin R \qquad a \overline{R} b
$$

### 3 Definitions

1. **Definition(定义域)**

$$
dom(R) = \{a | \exists b.(a,b) \in R\}
$$

2. **Definition(值域)**

$$
ran(R) = \{b |\exists a.(a,b) \in R\}
$$

3. **Definition(域)**

$$
fld(R) = dom(R) \cup ran(R)
$$

**Theorem**
$$
dom(R) \subseteq \bigcup \bigcup R \qquad ran(R) \subseteq \bigcup \bigcup R
$$

### 5 Operations

**Definition(逆(Inverse))**
$$
R^{-1} = \{(a,b) | (b,a) \in R\}
$$
**Theorem**
$$
(R^{-1})^{-1} = R
$$
**Theorem(关系的逆)**
$$
R, S \text{均为关系} \\
(R \cup S)^{-1} = R^{-1} \cup S^{-1} \\
\cap \qquad \setminus \qquad 同理
$$
**Definition(左限制)**

如果 $R \subseteq X \times Y$，$S \subseteq X$，那么从 $R$ 到 $S$ 在$X$ 和 $Y$ 上的左限制：
$$
R|_{S} = \{(x,y) \in R|{\color{Red} x\in S}\}
$$
**Definition(右限制)**

如果 $R \subseteq X \times Y$，$S \subseteq Y$，那么从 $R$ 到 $S$ 在$X$ 和 $Y$ 上的右限制：
$$
R|^{S} = \{(x,y) \in R|{\color{Red} y \in S}\}
$$
**Definition(限制)**

如果 $R \subseteq X \times X$，$S \subseteq X$，那么从 $R$ 到 $S$ 在$X$ 和 $Y$ 上的右限制：
$$
R|_{S} = \{(x,y) \in R|{\color{Red} x \in S \land y \in S}\}
$$
**Definition(像(Image))**

$X$ 在关系 $R$ 下的像是集合：
$$
R[x] = \{b \in ran(R)|\exists a \in X.(a,b) \in R\}
$$
**Definition(逆像)**

$Y$ 在关系 $R$ 下的逆像是集合：
$$
R^{-1}[Y] = \{a \in dom(R)|\exists b \in Y.(a, b) \in R\}
$$
**Theorem**
$$
R[X_1 \cup X_2] = R[X_1] \cup R[X_2] \\
R[X_1 \cap X_2] \subseteq R[X_1] \cap R[X_2] \\
R[X_1 \setminus X_2] \supseteq R[X_1] \setminus R[X_2]
$$
**Definition(复合(Composition))**

$R \subseteq X \times Y$ 和 $S \subseteq Y \times Z$ 的复合是关系：
$$
R \circ S = \{(a, c)|\exists b.(a,b) \in S \land (b,c) \in R\}
$$
**Theorem**
$$
(R \circ S)^{-1} = S^{-1} \circ R^{-1} \\
(R \circ S) \circ T = R \circ (S \circ T) \\
(X \cup Y) \circ Z = (X \circ Z) \cup (Y \circ Z) \\
(X \cap Y) \circ Z \subseteq (X \circ Z) \cap (Y \circ Z)
$$

 ### 7 Properties

**Definition**

1. 关系 $R \subseteq X \times X$ 是**自反**的 $if$:

$$
\forall a \in X.(a, a) \in R
$$

2. 关系 $R \subseteq X \times X$ 是**反自反**的 $if$:

$$
\forall a \in X.(a, a) \notin R
$$

3. 关系 $R \subseteq X \times X$ 是**对称**的 $if$:

$$
\forall a, b \in X.aRb \to bRa
$$

4. 关系 $R \subseteq X \times X$ 是**反对称**的 $if$:

$$
\forall a, b \in X.(aRb \land bRa) \to a = b
$$

5. 关系 $R \subseteq X \times X$ 是**传递**的 $if$:

$$
\forall a, b, c \in X.(aRb \land bRc \to aRc)
$$



6. 关系 $R \subseteq X \times X$ 是**连接**的 $if$:

$$
\forall a, b \in X.(aRb \lor bRa)
$$

7. 关系 $R \subseteq X \times X$ 是**三分**的 $if$:

$$
\forall a, b \in X.(aRb, bRa, a = b \text{只有一种存在})
$$

**Theorem**

1. $R$ 是对称的且传递的 $\Longleftrightarrow R = R^{-1} \circ R$
2. $R$ 是对称的 $\Longleftrightarrow R^{-1} = R$
3. $R$ 是传递的 $\Longleftrightarrow R \circ R \subseteq R$



<span style='color: red'>**Definition(等价关系)**</span>

$R \subseteq X \times X$ 是一种 $X$ 上的等价关系 当且仅当 $X$ 上的关系 $R$ 是：

* 自反的：$\forall a \in X.aRa$
* 对称的：$\forall a, b \in X.(aRb \leftrightarrow bRa)$
* 传递的：$\forall a, b, c \in X.(aRb \land bRc \rightarrow aRc)$

<span style='color: red'>**Definition(划分(Partition))**</span>

一组集合 $\Pi = \{A_{\alpha} | \alpha \in I\}$ 是一种 $X$ 的划分当且仅当：

1. (不空)

$$
\forall \alpha \in I.A_{\alpha} \neq \emptyset \\
(\forall \alpha \in I.\exists x \in X.x \in A_{\alpha})
$$

2. (不漏)

$$
\bigcup_{\alpha \in I} A_{\alpha} = X \\
(\forall x \in X. \exists \alpha \in I. x \in A_{\alpha})
$$

3. (不重)

$$
\forall \alpha, \beta \in I.A_{\alpha} \cap A_{\beta} = \emptyset \lor A_{\alpha} = A_{\beta} \\
(\forall \alpha, \beta \in I.A_{\alpha} \cap A_{\beta} \neq \emptyset \Longrightarrow A_{\alpha} = A_{\beta})
$$

<span style='color: red'>**Definition(等价类)**</span>

$R$ 的等价类 $a$ 是一个集合：
$$
[a]_{R} = \{b \in X.(aRb)\}
$$
**Definition(商集)**

关系 $R$ 在 $X$ 上的商集是一个集合：
$$
X / R = \{[a]_{R} | a \in X\}
$$
**Theorem**

1. $X / R = \{[a]_{R} | a \in X\}$ 是 $X$ 的一个划分
2. $\forall a \in X, b \in X.[a]_{R} \cap [b]_{R} = \emptyset \lor [a]_{R} = [b]_{R}$
3. $\forall a, b \in X.([a]_{R} = [b]_{R} \leftrightarrow aRb)$

****



## 6 函数

### Definition of Functions

<span style='color: red'>**Definition(Function)**</span>

$f \subseteq A \times B$ 是一个从 $A$ 到 $B$ 的函数 $if$:
$$
{\color{Red} \forall} a \in A. {\color{Red} \exists!} b \in B.(a, b) \in f.
$$
**For Proof:**
$$
{\color{Red} \exists !b \in B}. \\
\forall b, b' \in B.(a, b) \in f \land (a, b') \in f \Longrightarrow b = b'
$$
**Special Funcition**
$$
I_x : X \rightarrow X \\
X \text{上的恒等函数} \\
\forall x \in X.I_x(x) = x
$$
**Definition**

从 $X$ 到 $Y$ 的所有函数的集合:
$$
Y^X = \{f\ | f : X \rightarrow Y\}
$$
**Theorem**

没有一个集合可以包含所有的函数



### Functions as Sets

**Theorem(函数的外延性原理)**

$f, g$ 都是函数:
$$
f = g \Longleftrightarrow dom(f) = dom(g) \land
(\forall x \in dom(f).f(x) = g(x))
$$
**Theorem**

$f : A \rightarrow B$		$g: C \rightarrow D$

$f \cap g: (A \cap C) \rightarrow (B \cap D)$

$f \cup g : (A \cup C) \rightarrow (B \cup D) \Longleftrightarrow \forall x \in dom(f) \cap dom(g).f(x) = g(x)$ 



### Special Funtion

**Definiton(单射函数 1-1)**
$$
f : A \rightarrow B \qquad f : A {\color{Red} \rightarrowtail} B\\
\forall a_1, a_2 \in A. a_1 \ne a_2 \rightarrow f(a_1) \ne f(a_2)
$$
**For Proof**

To prove that $f$ is 1-1:
$$
\forall a_1, a_2 \in A.f(a_1) = f(a_2) \rightarrow a_1 = a_2
$$
To show that $f$ is not 1-1:
$$
{\color{Red} \exists} a_1, a_2 \in A. a_1 \ne a_2 \land f(a_1) = f(a_2)
$$


**Definition(满射函数 onto)**
$$
f : A \rightarrow B \qquad f: A {\color{Red}\twoheadrightarrow} B \\
ran(f) = B
$$
**For Proof:**

To prove that $f$ is onto:
$$
\forall b \in B.({\color{Red} \exists} a\in A.f(a) = b)
$$
To show that $f$ is not onto:
$$
{\color{Red} \exists} b \in B.({\color{Red} \forall} a \in A.f(a) \ne b)
$$

**Definition(双射函数)**
$$
f : A \rightarrow B \qquad f : A \xleftarrow[onto]{1 - 1} \xrightarrow[]{} B \\
1 - 1\ \&\ onto
$$

**Definition(定义域与值域都一样的函数)**
$$
I_X :X \to X
$$
**Theorem(Cantor Theorem)**

如果 $f: A \to 2^A$，那么 $f$ 不是双射函数。

1. $Not\ Onto: {\color{Red} \exists} B \in 2^A.({\color{Red} \forall}a \in A.f(a) \neq B)$

2. 如何构造这个 $B$：

	> 1. $a \in f(a), a \notin B$
	> 2. $a \notin f(a), a \in B$

3. 反证法 $Proof: \exists a \in A.f(a) = B$



### Functions as Relations

**Definition(限制)**

函数 $f$ 在 $X$ 的限制是函数：
$$
f|_{X} = \{(x, y) \in f\ |\ x \in X\}
$$
**Definition(像和逆像)**

1. $X$ 在 $f$ 下的像是集合
	$$
	f(X) = \{b\ |\ \exists a \in X.(a, b) \in f\}
	$$

2. $Y$ 在 $f$ 下的逆像是集合
	$$
	f^{-1}(Y) = \{a\ |\ \exists b \in Y.(a, b) \in f\}
	$$

**Remark:**

1. $$
	x \in X \cap dom(f) \Longrightarrow f(x) \in f(X)
	$$

	1. ​			$if\ X \subseteq dom(f):x \in X \Longrightarrow f(x) \in f(X)$

2. $$
	y \in f(x) \iff \exists x \in X \cap dom(f).y = f(x)
	$$

	1. ​			$if\ X \subseteq dom(f):y \in f(X) \iff \exists x \in X.y = f(x)$

3. $$
	x \in f^{-1}(Y) \iff f(x) \in Y
	$$

#### 一些定理

1. $f$ 只有在 $\subseteq and\ \cup$ 相等
	1. $A_1 \subseteq A_2 \Longrightarrow f(A_1) \subseteq f(A_2)$
	2. $f(A_1 \cup A_2) = f(A_1) \cup f(A_2)$
	3. $f(A_1 \cap A_2) \  f(A_1) \cap f(A_2)$ ，$f$ 是单射函数时取等号。
	4. $f(A_1 \setminus A_2) \supseteq f(A_1) \setminus f(A_2)$
2. $f^{-1}$ 在 $\subseteq,\cap,\cup,\setminus$ 相等
	1. $B_1 \subseteq B_2 \Longrightarrow f^{-1}(B_1) \subseteq f^{-1}(B_2)$
	2. $f^{-1}(B_1 \cup B_2) = f^{-1}(B_1) \cup f^{-1}(B_2)$
	3. $f^{-1}(B_1 \cap B_2) = f^{-1}(B_1) \cap f^{-1}(B_2)$
	4. $f^{-1}(B_1 \setminus B_2) = f^{-1}(B_1) \setminus f^{-1}(B_2)$

3. $f\ and\ f^{-1} $
	1. $A_0 \subseteq A \Longrightarrow A_0 \subseteq f^{-1}(f(A_0))$
	2. $B_0 \supseteq f(f^{-1}(B_0))$ ，$f$ 是满射函数且 $B_0 \subseteq ran(f)$ 时等号成立

**Definition(函数的复合)**
$$
f: A \to B \qquad g: C \to D \\
ran(f) \subseteq C
$$
那么复合函数 $g \circ f : A \to D$ 被定义为：
$$
(g \circ f)(x) = g(f(x))
$$
**Theorem**
$$
f: A \to B \qquad g:B \to C \qquad h:C \to D \\
h \circ (g \circ f) = (h \circ g) \circ f
$$
**Theorem**
$$
f: A \to B \qquad g : B \to C
$$

1. 如果 $f, g$ 是单射函数，那么 $g \circ f$ 是单射函数
2. 如果 $f, g$ 是满射函数，那么 $g \circ f$ 是满射函数
3. 如果 $f, g$ 是双射函数，那么 $g \circ f$ 是双射函数
4. 如果 $g \circ f$ 是满射函数，那么 $g$ 是满射函数
5. 如果 $g \circ f$ 是单射函数，那么 $f$ 是单射函数

**Definition(函数的逆)**

函数 $f: A\to B$ 是双射函数，那么函数 $f$ 的逆 $f^{-1}：B \to A$ 被定义为
$$
f^{-1}(b) = a \iff f(a) = b
$$
**Theorem**
$$
f : A \to B\ is\ bijective
$$

1. $f \circ f^{-1} = I_B$
2. $f^{-1} \circ f = I_A$
3. $f^{-1}\ is\ bijective$
4. $g: B \to A \land f \circ g = I_B \Longrightarrow g= f^{-1}$
5. $g: B \to A \land g \circ f = I_{A} \Longrightarrow g = f^{-1}$ 

**Theorem(复合的逆)**
$$
f: A\to B \quad g: B \to C\ are\ bijective
$$

1. $g \circ f\ is\ bijective$
2. $(g \circ f)^{-1} = f^{-1} \circ g^{-1}$

**Theorem**
$$
f: A \to B \quad g:B \to A
$$

1. $f \circ g = I_B \land g \circ f = I_A \Longrightarrow g = f^{-1}$

****



## 7 集合：序关系

**Definition(等价关系)**

关系 $R \subseteq X \times X$ 是 $X$ 上的等价关系具有以下三个条件：

1. 自反性
	$$
	\forall x \in X.(x, x) \in R
	$$

2. 对称性
	$$
	\forall x, y \in X.(x, y) \in R\to (y, x) \in R
	$$

3. 传递性
	$$
	\forall x, y, z \in X.(x, y) \in R \land (y, z) \in R \to (x, z) \in R
	$$

4. 

**Definition(偏序关系)**

令 $\preceq \subseteq X \times X$ 是 $X$ 上的二元关系

如果 $\preceq$ 满足以下条件，则称 $\preceq$ 是 $X$ 上的偏序关系

1. $\preceq$ 是自反的
	$$
	\forall x \in X. x \preceq x.
	$$

2. $\preceq$ 是反对称的
	$$
	\forall x, y \in X.x \preceq y \land y \preceq x \rightarrow x = y
	$$

3. $\preceq$ 是传递的
	$$
	\forall x, y, z \in X.x \preceq y \land y \preceq z \rightarrow x \preceq z
	$$

4. 

**Definition**

设 $(X, \preceq)$ 是偏序集。对任意 $a, b \in X$,

<span style='color: blue'> 严格小于</span>:
$$
a \prec b = a \preceq b \land a \neq b
$$
<span style='color: blue'>被覆盖</span>
$$
ab = a \prec b \land(\forall c in X.(c \neq a \land c \neq b) \rightarrow \lnot(a \preceq c \preceq b))
$$
<span style='color: blue'>可比的</span>
$$
a \preceq b \lor b \preceq a
$$
<span style='color: blue'>不可比的</span>
$$
\lnot (a \preceq b \lor b \preceq a)
$$
**Definition(链与反链)**

设 $(X, \preceq)$ 是偏序集。

* 设 $S \subseteq X$ 且 $S$ 中的元素两两可比，则称 $S$ 是链。
* 设 $S \subseteq X$ 且 $S$ 中的元素两两不可比，则称 $S$ 是反链。

**Theorem(Dilworth's Theorem)**

最大反链的大小 = 最小链分解中链的条数

**Definition(严格偏序关系)**

 $\prec \subseteq X \times X$ 是 $X$ 上的二元关系

如果 $\prec$ 满足以下条件，则称 $\prec$ 是 $X$ 上的严格偏序关系

1. $\prec$ 是**反自反**的
	$$
	\forall x \in X. \lnot (x \prec x).
	$$

2. $\prec$ 是**非对称**的
	$$
	\forall x, y \in X.x \prec y \rightarrow \lnot(y \prec x)
	$$

3. $\prec$ 是传递的
	$$
	\forall x, y, z \in X.x \prec y \land y \prec z \rightarrow x \prec z
	$$

4. 

**Theorem**

设 $\prec \subseteq X \times X$ 是 $X$ 上的严格偏序关系。

对于任意 $x, y, z \in X$ :

1. $x \prec y, x = y, y \prec x$ 三者中至多有一个成立
2. $x \preceq y \preceq x \rightarrow x = y$

**Definition(全序关系)**

令 $\preceq \subseteq X \times X$ 是 $X$ 上的偏序，如果 $\preceq$ 满足以下连接性，则称 $\preceq$ 是 $X$ 上的全序关系
$$
\forall a, b \in X.a \preceq b \lor b \preceq a
$$
**Definition(严格全序关系)**

 $\prec \subseteq X \times X$ 是 $X$ 上的二元关系

如果 $\prec$ 满足以下条件，则称 $\prec$ 是 $X$ 上的严格全序关系

1. **反自反**
	$$
	\forall x \in X. \lnot (x \prec x).
	$$

2. 传递性
	$$
	\forall x, y, z \in X.x \prec y \land y \prec z \rightarrow x \prec z
	$$

3. 三岐性
	$$
	\forall x, y \in X.(x \prec y, x = y, y \prec x) 只有一个存在
	$$

**Definition(极大元与极小元)**

令 $\preceq \subseteq X \times X$ 是 $X$ 上的偏序。设 $a \in X$。如果
$$
\forall x \in X. \lnot(a \prec x)
$$
则称 $a$ 是 $X$ 的**极大元**，如果
$$
\forall x \in X. \lnot (x \prec a)
$$
则称 $a$ 是 $X$ 的**极小元**

**Definition(最大元与最小元)**

令 $\preceq \subseteq X \times X$ 是 $X$ 上的偏序。设 $a \in X$。如果
$$
\forall x \in X. x \preceq a
$$
则称 $a$ 是 $X$ 的**最大元**，如果
$$
\forall x \in X. a \preceq x
$$
则称 $a$ 是 $X$ 的**最小元**

**Theorem**

偏序集 $(X, \preceq)$ 如果有最大元或最小元，则它们是唯一的。

**Definition(线性拓展)**

设 $(X, \preceq)$ 是偏序集，$(X, \preceq')$ 是全序集。如果
$$
\forall x, y \in X.x \preceq y \rightarrow x \preceq' y
$$
则称 $\preceq'$ 是 $\preceq$ 的**线性拓展**

**Theorem**

1. 偏序集且 $X$ 为有限集，则 $\preceq$的线性拓展一定存在。
2. 偏序集且 $X$ 为有限集，则极小元一定存在。

**Definition(良序(Well-Ordering))**

设 $(X, \preceq)$ 是全序集。如果 $X$ 的任意**非空**子集都有最小元，则称 $(X, \preceq)$ 为良序集。

****



## 8 集合：无穷

**Definition(等势，$|A| = |B|(A \approx B)$)**

$A$ 和 $B$ 是等势的 $\Longleftrightarrow$ $A$ 和 $B$ 之间存在双射函数

**Definition(Finite)**

$X$ is finite $if$:
$$
\exists n \in \mathbb{N}.|X| = |n| = |\{0, 1, \dots, n - 1\}|
$$
集合 $X$ 是有穷的当且仅当它与某个自然数等势

**Definition(Infinite)**

$X$ is infinite if it is note finite:
$$
\forall n \in \mathbb{N}.|X| \neq n
$$

1. 可数无穷：$|X| = |\mathbb{N}| = \aleph_0$
2. 可数：有穷 $\lor$ 可数无穷
3. 不可数：无穷 $\land$ 非可数无穷

**Theorem**

$\mathbb{N}$ 是无穷集合($\mathbb{Z}, \mathbb{Q}, \mathbb{R}$ 也都是无穷集合)。

**Theorem**

1. $|\mathbb{Z}| = |\mathbb{N}|$
2. $|\mathbb{Q}| = |\mathbb{N}|$
3. $|\mathbb{N} \times \mathbb{N}| = |\mathbb{N}|$
4. $|\mathbb{R}| \neq |\mathbb{N}|$  ，$|\mathbb{R}|$ 是不可数的，且 $|\mathbb{R}|$ 是一个连续统
5. 有穷多个可数集并在一起也是可数的
6. $|(0, 1)| = |\mathbb{R}| = |\mathbb{R} \times \mathbb{R}| = |\mathbb{R}^n|$

**Theorem(康托-伯恩斯坦-施罗德定理)**

若有 $|A| \le |B|$ 且 $|B| \le |A|$ , 则有 $|A| = |B|$ 。

**Theorem(康托定理)**
$$
|A| \neq |P(A)|
$$

$$
|A| {\color{Red} <} |P(A)|
$$

**Definition($|A| \le |B|$)**

$|A| \le |B|$ 当存在从 $A$ 映射到 $B$ 的单射函数

$|A| < |B| \iff |A| \le |B| \land |A| \neq |B|$

**Theorem(可数)**

$X$ 是可数的：
$$
(\exists n \in \mathbb{N} : |X| = n) \lor |X| = |\mathbb{N}|
$$
**Theorem(可数的证明方法)**

$X$ 是可数的 $\iff$
$$
|X| \le |\mathbb{N}|
$$
存在单射函数 $f: X \to N$

**Theorem**

$
