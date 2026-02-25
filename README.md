# 第 1 章 目标分布与可采样分解

本章只做三件事：

1. 精确定义在什么集合上采样、采什么分布；
2. 给出一个与半开语义严格一致的扫描事件序；
3. 证明一个无重分解：把全体相交对 $J$ 分解成若干互不重叠的“事件块” $J_e$，从而把“在 $J$ 上均匀采样”化为“按块权重采块 + 块内均匀”。

---

## 1.1 输入模型与半开语义下的相交

给定两类二维轴对齐半开矩形集合：
$$
R_c=\{r_{c1},\dots,r_{cn}\},\qquad 
R_{\bar c}=\{r_{\bar c1},\dots,r_{\bar cm}\}.
$$
每个矩形 $r$ 表示为
$$
r=[L_x(r),R_x(r))\times[L_y(r),R_y(r)),\qquad L_x(r)<R_x(r),\ \ L_y(r)<R_y(r).
$$
所有对象都携带唯一标识 $\mathrm{id}(r)$（即使几何参数完全相同也视为不同元素），用于确定性打破事件同坐标的平局。

半开语义下，两矩形 $r,s$ 的交集非空当且仅当同时满足 $x$ 与 $y$ 两个方向的严格重叠：
$$
r\cap s\neq\varnothing
\iff
\big(L_x(r)<R_x(s)\big)\wedge\big(L_x(s)<R_x(r)\big)\wedge
\big(L_y(r)<R_y(s)\big)\wedge\big(L_y(s)<R_y(r)\big).
$$
严格不等号保证“贴边”（例如 $R_x(r)=L_x(s)$ 或 $R_y(r)=L_y(s)$）不计为相交。

---

## 1.2 目标集合与采样目标分布

仅考虑跨集合、有序方向固定的相交对集合：
$$
J=\{(r_c,r_{\bar c})\mid r_c\in R_c,\ r_{\bar c}\in R_{\bar c},\ r_c\cap r_{\bar c}\neq\varnothing\}.
$$
给定 $t\ge 1$，输出序列
$$
Z_1,\dots,Z_t\in J
$$
满足 **i.i.d. 均匀（有放回）**：
$$
\Pr\{Z_j=P\}=\frac{1}{|J|}\quad(\forall P\in J),\qquad Z_1,\dots,Z_t\ \text{相互独立}.
$$
若 $|J|=0$，输出空序列。

---

## 1.3 扫描轴事件序与相交对的无重块分解

本节沿 $x$ 轴做一次 plane sweep，并将每个相交对唯一归属到某个 START 事件块。

### 1.3.1 扫描维度、事件与全序

固定扫描维度为 $x$。对每个矩形 $r\in R_c\cup R_{\bar c}$ 定义两类事件：

- $\mathrm{START}(r)$：坐标 $x=L_x(r)$；
- $\mathrm{END}(r)$：坐标 $x=R_x(r)$。

令 $E$ 为所有事件集合，令 $E^+\subset E$ 为所有 START 事件集合。

为了与半开语义严格一致，并消除同坐标歧义，对 $E$ 定义一个总序 $\prec$：

1. 若 $x(e)<x(e')$，则 $e\prec e'$；
2. 若 $x(e)=x(e')$，则所有 END 事件严格先于所有 START 事件：
   $\mathrm{END}(\cdot)\prec \mathrm{START}(\cdot)$；
3. 若同为 START 且坐标相同，则按 $\mathrm{id}(r)$ 的固定全序打破平局；
   若同为 END 且坐标相同，也用固定规则打破平局（仅为确定性）。

该序保证：当 $R_x(r)=L_x(s)$ 时有 $\mathrm{END}(r)\prec \mathrm{START}(s)$，从而在处理 $\mathrm{START}(s)$ 之前 $r$ 已从活跃集合移除，贴边不会被误判为相交。

### 1.3.2 活跃集合

按 $\prec$ 顺序处理事件时，维护两类活跃集合：
$$
A_c\subseteq R_c,\qquad A_{\bar c}\subseteq R_{\bar c}.
$$
处理规则为：

- 遇到 $\mathrm{END}(r)$：将 $r$ 从其所属类的活跃集合中删除；
- 遇到 $\mathrm{START}(r)$：先定义该事件的“事件块”（见下节），然后将 $r$ 插入其所属类的活跃集合。

因此，在处理某个 START 事件 $e=\mathrm{START}(q)$ 的时刻，活跃集合精确包含了所有满足
$$
L_x(s)<L_x(q)\quad\text{或}\quad\big(L_x(s)=L_x(q)\ \wedge\ \mathrm{START}(s)\prec \mathrm{START}(q)\big),
$$
且同时
$$
L_x(q)<R_x(s)
$$
的矩形 $s$（并按类别分在 $A_c$ 或 $A_{\bar c}$ 中）。后一条件来自“尚未 END”，与半开语义严格一致。

### 1.3.3 $y$ 轴投影与事件块定义

对任意矩形 $r$，记其 $y$ 轴投影区间
$$
r^{(y)}=[L_y(r),R_y(r)).
$$
两区间在半开语义下相交当且仅当
$$
r^{(y)}\cap s^{(y)}\neq\varnothing
\iff
L_y(r)<R_y(s)\ \wedge\ L_y(s)<R_y(r).
$$

**引理 1.0（扫描维自动满足 $x$ 方向严格重叠）**  
当处理 START 事件 $e=\mathrm{START}(q)$（坐标 $x=L_x(q)$）时，任意对侧活跃矩形 $s$ 都满足
$$
L_x(s)<R_x(q)\quad\text{且}\quad L_x(q)<R_x(s).
$$

**证明**  
由于 $s$ 在活跃集中，其 START 已发生且 END 尚未发生，故 $L_x(s)\le x$ 且 $x<R_x(s)$。又矩形非空意味着 $x=L_x(q)<R_x(q)$。于是 $L_x(s)\le x<R_x(q)$ 推出 $L_x(s)<R_x(q)$，并且 $L_x(q)=x<R_x(s)$ 亦成立。证毕。

因此，在 START 事件处判定相交只需检查 $y$ 轴投影是否相交。

对任意 START 事件 $e=\mathrm{START}(q)\in E^+$，定义其 partner 集合 $K_e$ 与事件块 $J_e$：

- 若 $q\in R_c$（事件矩形来自左类）：
  $$
  K_e=\{s\in A_{\bar c}\mid q^{(y)}\cap s^{(y)}\neq\varnothing\},
  \qquad
  J_e=\{(q,s)\mid s\in K_e\}.
  $$

- 若 $q\in R_{\bar c}$（事件矩形来自右类）：
  $$
  K_e=\{s\in A_{c}\mid s^{(y)}\cap q^{(y)}\neq\varnothing\},
  \qquad
  J_e=\{(s,q)\mid s\in K_e\}.
  $$

注意：$J$ 的方向固定为 $(r_c,r_{\bar c})$。因此当事件矩形 $q\in R_{\bar c}$ 时，块内元素写作 $(s,q)$ 以保持方向一致。

定义块权重（块大小）：
$$
w_e:=|J_e|=|K_e|.
$$

### 1.3.4 核心定理：全体相交对的无重分解

**定理 1.1（按“较晚 START”归属的块分解）**  
在上述事件序 $\prec$ 与事件块定义下，有
$$
J=\biguplus_{e\in E^+} J_e,
\qquad
|J|=\sum_{e\in E^+} w_e.
$$
其中 $\biguplus$ 表示不交并（互不重叠的并）。

**证明** 分两步：覆盖性与互斥性。

**(1) 覆盖性：任意 $(r_c,r_{\bar c})\in J$ 必落入某个 $J_e$。**  
令
$$
e_c=\mathrm{START}(r_c),\qquad e_{\bar c}=\mathrm{START}(r_{\bar c}),
$$
并令
$$
e^\star=\max_{\prec}\{e_c,e_{\bar c}\}
$$
为二者中在总序 $\prec$ 下更晚的 START 事件。设 $e^\star=\mathrm{START}(q)$，则 $q$ 是 $\{r_c,r_{\bar c}\}$ 中“较晚开始”的那个（若同坐标则由 tie-break 决定）。

不妨设 $q=r_c$（另一种情形完全对称）。由于 $(r_c,r_{\bar c})\in J$，两矩形在 $x$ 方向严格重叠，特别地
$$
L_x(r_c)<R_x(r_{\bar c}).
$$
事件 $e^\star$ 发生在 $x=L_x(r_c)$，并且所有同坐标 END 事件已先处理，因此 $r_{\bar c}$ 不会在此时被误认为仍活跃。又因为 $e_{\bar c}\prec e^\star$，$r_{\bar c}$ 的 START 已先发生并被插入活跃集合，从而 $r_{\bar c}\in A_{\bar c}$ 在 $e^\star$ 时刻成立。再结合 $y$ 方向相交 $r_c^{(y)}\cap r_{\bar c}^{(y)}\neq\varnothing$，可得 $r_{\bar c}\in K_{e^\star}$，于是
$$
(r_c,r_{\bar c})\in J_{e^\star}.
$$
因此每个 $(r_c,r_{\bar c})\in J$ 都属于某个事件块。

**(2) 互斥性：任意 $(r_c,r_{\bar c})\in J$ 至多属于一个 $J_e$。**  
若 $(r_c,r_{\bar c})\in J_e$，则 $e$ 必为 $\{e_c,e_{\bar c}\}$ 中较晚的 START：在处理较早 START 时，较晚 START 的矩形尚未进入活跃集合，不可能被枚举为 partner；而在处理较晚 START 时，较早者若与之相交则必仍活跃并被纳入 partner 集合。由于 $\prec$ 是总序，较晚 START 唯一，因此归属的事件块唯一。

由 (1)(2) 得 $J=\biguplus_{e\in E^+}J_e$。权重求和式 $|J|=\sum_e |J_e|=\sum_e w_e$ 由不交并立即得到。证毕。

---

## 1.4 从全局均匀采样到“按块加权 + 块内均匀”的统一原理

由定理 1.1，均匀采样 $J$ 等价为对事件块的两阶段抽样。

令
$$
W:=|J|=\sum_{e\in E^+} w_e.
$$
若 $W=0$，则 $J=\varnothing$ 且应输出空序列。

否则定义如下抽样过程（单次输出）：

1. 以概率
   $$
   \Pr\{E=e\}=\frac{w_e}{W}
   $$
   从 $E^+$ 中抽取一个 START 事件 $e$；
2. 在该事件块 $J_e$ 内做一次均匀抽样，得到 $Z\in J_e$。

等价地，块内均匀可以表述为：先在 $K_e$ 上均匀抽取一个 partner $s$，再输出相应有序对（若 $q\in R_c$ 输出 $(q,s)$，否则输出 $(s,q)$）。

**命题 1.2（单次输出的均匀性）**  
上述过程输出的 $Z$ 在 $J$ 上均匀：
$$
\Pr\{Z=P\}=\frac{1}{|J|},\quad\forall P\in J.
$$

**证明** 任取 $P\in J$。由定理 1.1，存在唯一事件 $e(P)$ 使得 $P\in J_{e(P)}$。因此
$$
\Pr\{Z=P\}
=\Pr\{E=e(P)\}\cdot \Pr\{Z=P\mid E=e(P)\}
=\frac{w_{e(P)}}{W}\cdot \frac{1}{w_{e(P)}}
=\frac{1}{W}
=\frac{1}{|J|}.
$$
证毕。

**命题 1.3（多次输出的 i.i.d.）**  
若对每次输出都使用相互独立的随机性重复执行该两阶段过程，并且块内抽样为有放回，则 $Z_1,\dots,Z_t$ 相互独立且同分布为 $J$ 上的均匀分布。

---

### 1.4.1 事件块原语

定理 1.1 与命题 1.2–1.3 将整个问题规约为：对每个 START 事件 $e$，能够有效获得/生成事件块 $J_e$ 的信息。等价地，只需对 partner 集 $K_e$ 支持三类原语：

- **计数**：返回 $w_e=|K_e|$；
- **枚举**：输出 $K_e$（从而得到 $J_e$）；
- **均匀采样**：从 $K_e$ 上 i.i.d. 均匀（有放回）抽取 $k$ 个 partner。

由于扫描线维护的活跃集合已经保证 $x$ 方向严格重叠（引理 1.0），$K_e$ 的判定完全发生在 $y$ 轴区间上：
$$
q^{(y)}\cap s^{(y)}\neq\varnothing.
$$
因此后续章节的核心任务，就是在动态活跃集上实现对 $y$ 轴区间相交的 `COUNT/REPORT/SAMPLE`，并将这些原语组合成对 $J$ 的 i.i.d. 均匀采样。

# 第 2 章 事件块查询的数据结构：模式化分解与段树递归

在沿 $x$ 轴的扫描过程中，每当遇到一个 $\mathrm{START}(q)$ 事件，需要在“对侧类”的动态活跃集合中生成该事件的 partner 集，并支持如下三类事件块原语：

- `COUNT(e)`：返回 $|K_e|$；
- `REPORT(e)`：枚举 $K_e$ 的全部元素（每个一次）；
- `SAMPLE(e,k)`：在 $K_e$ 上返回 $k$ 个 **独立同分布**（i.i.d.）、**均匀**、**有放回**的样本。

第 1 章已经说明：在处理 $\mathrm{START}(q)$ 的时刻，任意对侧活跃矩形 $s$ 与 $q$ 在 $x$ 方向的严格重叠自动成立，因此事件块判定只需在 $y$ 方向完成。令
$$
I(s)=[L_y(s),R_y(s)),\qquad J(q)=[L_y(q),R_y(q)),
$$
则 partner 条件等价于一维半开区间相交 $I(s)\cap J(q)\neq\varnothing$。

本章给出一种完全显式的实现路线：将“一维区间相交”划分为两种互斥形态（两种模式），并分别用两套段树结构在动态活跃集上实现 `COUNT/REPORT/SAMPLE`，最后将两种模式按权重混合得到事件块原语。

---

## 2.1 一维相交的两种模式与事件块的无重分解

本节只讨论一维半开区间。设
$$
I=[L(I),R(I)),\qquad J=[L(J),R(J)),\qquad L(\cdot)<R(\cdot),
$$
半开语义下相交当且仅当
$$
I\cap J\neq\varnothing\iff L(I)<R(J)\ \wedge\ L(J)<R(I).
$$

### 2.1.1 两种互斥形态：stabbing 与 range

令查询点为 $x:=L(J)$，定义两类条件：

- **A（stabbing 条件）**
  $$
  \mathbf{A}(I\mid J):\quad L(I)\le x<R(I).
  $$
  即区间 $I$ 包含查询点 $x$。

- **B（range 条件）**
  $$
  \mathbf{B}(I\mid J):\quad L(J)<L(I)<R(J).
  $$
  即区间 $I$ 的左端点严格落在 $J$ 的内部。

**引理 2.1（一维相交的互斥完备划分）**  
对任意半开区间 $I,J$，有
$$
I\cap J\neq\varnothing \iff \mathbf{A}(I\mid J)\ \vee\ \mathbf{B}(I\mid J),
$$
且 $\mathbf{A}$ 与 $\mathbf{B}$ 互斥。

**证明**  

- 充分性：  
  若 $\mathbf{A}(I\mid J)$ 成立，则 $x\in I$，同时 $x=L(J)\in J$，故 $x\in I\cap J$。  
  若 $\mathbf{B}(I\mid J)$ 成立，则 $L(I)\in (L(J),R(J))\subseteq J$，且 $L(I)\in I$，因此 $L(I)\in I\cap J$。

- 必要性：  
  若 $I\cap J\neq\varnothing$，则 $L(J)<R(I)$ 且 $L(I)<R(J)$。分两种情形：  
  1) 若 $L(I)\le L(J)=x$，由 $L(J)<R(I)$ 得 $x<R(I)$，故 $L(I)\le x<R(I)$，即 $\mathbf{A}(I\mid J)$；  
  2) 若 $L(I)>L(J)$，由 $L(I)<R(J)$ 得 $L(J)<L(I)<R(J)$，即 $\mathbf{B}(I\mid J)$。

- 互斥性：  
  $\mathbf{A}$ 蕴含 $L(I)\le L(J)$，而 $\mathbf{B}$ 蕴含 $L(I)>L(J)$，不可能同时成立。证毕。

---

### 2.1.2 二维事件块的两模式分解

在二维扫描中，对每个 $\mathrm{START}(q)$ 事件，令对侧活跃集合为 $S$。其 partner 集为
$$
K(q)=\{\,s\in S\mid I(s)\cap J(q)\neq\varnothing\,\}.
$$

定义两种模式对应的子集合：

- **A 模式 partner（stabbing）**
  $$
  K_A(q)=\{\,s\in S\mid L_y(s)\le L_y(q)<R_y(s)\,\}.
  $$

- **B 模式 partner（range）**
  $$
  K_B(q)=\{\,s\in S\mid L_y(q)<L_y(s)<R_y(q)\,\}.
  $$

**定理 2.2（事件块的两模式无重分解）**  
对任意查询矩形 $q$，有
$$
K(q)=K_A(q)\uplus K_B(q),
$$
其中 $\uplus$ 表示不交并（两集合互不重叠）。

**证明**  
将引理 2.1 应用于区间 $I(s)$ 与 $J(q)$，可知 $I(s)\cap J(q)\neq\varnothing$ 当且仅当 $\mathbf{A}(I(s)\mid J(q))$ 或 $\mathbf{B}(I(s)\mid J(q))$ 成立；两条件互斥，因此 $K(q)$ 恰为 $K_A(q)$ 与 $K_B(q)$ 的不交并。证毕。

因此，事件块原语可以通过分别实现 $K_A(q)$ 与 $K_B(q)$ 的 `COUNT/REPORT/SAMPLE` 再混合得到。

---

## 2.2 模式 A：stabbing 型段树（点刺穿的区间集合）

本节维护动态区间集合（来自对侧活跃矩形的 $y$ 投影），支持对任意查询点 $x$：

- `COUNT_A(x)`：返回活跃区间中包含 $x$ 的个数；
- `REPORT_A(x)`：枚举所有包含 $x$ 的区间对应对象；
- `SAMPLE_A(x,k)`：在这些对象上返回 $k$ 个 i.i.d. 均匀样本（有放回）。

### 2.2.1 坐标压缩与段树结点段

设该侧所有可能出现的区间端点来自静态全集（输入固定）。取端点集合
$$
Y=\{L_y(r),R_y(r)\mid r\ \text{属于该侧全部矩形}\},
$$
去重排序得到
$$
y_0<y_1<\cdots<y_M.
$$
建立段树 $T$，每个结点 $v$ 对应一个半开段
$$
\mathrm{seg}(v)=[y_\ell,y_r),
$$
叶结点对应基本段 $[y_i,y_{i+1})$。段树高度为 $O(\log M)$。

对任意半开区间 $I=[a,b)$（其中 $a,b\in Y$ 且 $a<b$），其 **规范覆盖**（canonical cover）$\mathcal{C}(I)$ 是一组结点集合，满足：

1. $\bigcup_{v\in\mathcal{C}(I)}\mathrm{seg}(v)=[a,b)$；
2. 对所有 $v\in\mathcal{C}(I)$，$\mathrm{seg}(v)\subseteq [a,b)$；
3. 结点段两两不交；
4. 覆盖“尽量粗”：对任意 $v\in\mathcal{C}(I)$，其父结点段不完全包含于 $[a,b)$。

标准性质给出 $|\mathcal{C}(I)|=O(\log M)$，并可在 $O(\log M)$ 时间求得。

---

### 2.2.2 桶与动态更新

对每个结点 $v$ 维护桶 $B(v)$，存放当前活跃区间中满足 $v\in\mathcal{C}(I)$ 的对象：
$$
B(v):=\{\,s\ \text{活跃}\mid v\in\mathcal{C}(I(s))\,\}.
$$

桶采用“致密数组 + 位置句柄”实现：

- 桶内用数组保存元素；
- 每个活跃对象在其被插入的每个桶中保存一个句柄（该桶内的数组下标）；
- 删除时用 swap-with-last 保持致密，从而在每个桶内 $O(1)$ 删除。

于是：

- `INSERT(s)`：对所有 $v\in\mathcal{C}(I(s))$，将 $s$ 插入 $B(v)$；
- `DELETE(s)`：用句柄从上述桶中逐一删去。

单次插入/删除触及 $O(\log M)$ 个桶。

---

### 2.2.3 查询路径与无重分解

给定查询点 $x$，若 $x\in[y_0,y_M)$，则存在唯一叶段 $[y_i,y_{i+1})$ 满足 $x\in[y_i,y_{i+1})$。令 $\mathsf{Path}(x)$ 为根到该叶子的路径结点集合。

**引理 2.3（路径唯一归属）**  
若活跃区间 $I$ 满足 $x\in I$，则存在唯一结点 $v^\star\in\mathcal{C}(I)$ 使得 $v^\star\in\mathsf{Path}(x)$。

**证明**  
$\mathcal{C}(I)$ 的结点段两两不交且并为 $I$，因此 $x\in I$ 时必落在其中唯一一段 $\mathrm{seg}(v^\star)$ 上，从而 $v^\star$ 唯一。又 $\mathrm{seg}(v^\star)$ 包含 $x$，故 $v^\star$ 位于查询路径 $\mathsf{Path}(x)$ 上。证毕。

由此得到答案集合的无重并：

**推论 2.4（stabbing 答案的无重并）**  
令 $\mathcal{V}(x):=\mathsf{Path}(x)$，则
$$
\{\,s\ \text{活跃}\mid x\in I(s)\,\}
=
\biguplus_{v\in\mathcal{V}(x)} B(v).
$$

---

### 2.2.4 三原语与均匀采样

记
$$
C_A(x):=\sum_{v\in\mathcal{V}(x)} |B(v)|.
$$

- `COUNT_A(x)`：返回 $C_A(x)$；
- `REPORT_A(x)`：依次枚举所有 $v\in\mathcal{V}(x)$ 的桶 $B(v)$ 并输出（由推论 2.4 不重）；
- `SAMPLE_A(x,k)`：若 $C_A(x)=0$ 返回空序列；否则对每个样本位置独立执行：
  1) 以权重 $|B(v)|$ 在 $v\in\mathcal{V}(x)$ 上抽取结点；
  2) 在桶内均匀抽取一个元素。

为了以 $O(1)$ 时间完成“按权重抽桶”，可对 $\{|B(v)|\}_{v\in\mathcal{V}(x)}$ 建立 alias 表；由于 $|\mathcal{V}(x)|=O(\log M)$，预处理代价为 $O(\log M)$。

**定理 2.5（模式 A 采样的均匀性与独立性）**  
`SAMPLE_A(x,k)` 返回的样本在集合 $\{s\mid x\in I(s)\}$ 上均匀、独立同分布（有放回）。

**证明**  
任取答案元素 $s$。由引理 2.3，$s$ 唯一归属到某个路径结点 $v^\star\in\mathcal{V}(x)$，且 $s\in B(v^\star)$。一次采样命中 $s$ 的概率为
$$
\Pr[s]
=\Pr[v^\star]\cdot \Pr[s\mid v^\star]
=\frac{|B(v^\star)|}{C_A(x)}\cdot \frac{1}{|B(v^\star)|}
=\frac{1}{C_A(x)},
$$
与 $s$ 无关，因此均匀。各样本位置使用独立随机性并有放回，故 i.i.d.。证毕。

---

### 2.2.5 复杂度

段树高度为 $O(\log M)$：

- `INSERT/DELETE`：$O(\log M)$；
- `COUNT_A`：$O(\log M)$；
- `REPORT_A`：$O(\log M+K)$（$K$ 为输出规模）；
- `SAMPLE_A(x,k)`：构建 alias 表 $O(\log M)$，随后 $k$ 次抽样 $O(k)$，总计 $O(\log M+k)$。

---

## 2.3 模式 B：range 型段树（按左端点的开区间选择）

模式 B 需要返回所有活跃对象 $s$，满足
$$
L_y(q)<L_y(s)<R_y(q),
$$
并支持均匀采样。它只依赖于对象的左端点，因此先在静态全集上建立全局秩域，再在秩域上用段树维护活跃性。

### 2.3.1 全局秩域：处理重复左端点与严格不等号

对该侧每个对象 $s$ 定义唯一键
$$
\alpha(s):=(L_y(s),\mathrm{id}(s)),
$$
按字典序排序得到全局秩
$$
\mathrm{rank}(s)\in\{1,2,\dots,N\},
$$
其中 $N$ 为该侧对象总数（静态全集规模）。

给定查询开区间 $(\ell,r)$（本章取 $\ell=L_y(q),\ r=R_y(q)$），定义秩边界：

- 左边界 $L$：满足 $\alpha(s)>(\ell,+\infty)$ 的最小秩（等价于 $L_y(s)>\ell$ 的最小秩）；
- 右边界 $R$：满足 $\alpha(s)\ge (r,0)$ 的最小秩（等价于 $L_y(s)\ge r$ 的最小秩）。

于是
$$
\{\,s\mid \ell<L_y(s)<r\,\}=\{\,s\mid \mathrm{rank}(s)\in[L,R)\,\}.
$$
若 $L\ge R$，答案为空。

---

### 2.3.2 段树、路径累积桶与规范分解

在秩域 $\{1,\dots,N\}$ 上建立段树。每个结点 $v$ 对应秩段
$$
\mathrm{seg}(v)=[p_\ell,p_r),
$$
并维护桶 $B(v)$。动态更新采用“叶到根路径累积”：

- `INSERT(s)`：从叶结点 $\mathrm{rank}(s)$ 沿父指针到根，对路径上所有结点 $v$ 将 $s$ 插入桶 $B(v)$；
- `DELETE(s)`：用句柄从同一路径上的桶中删除。

每次更新触及 $O(\log N)$ 个桶。桶实现与 2.2.2 相同（致密数组 + 句柄），支持桶内均匀抽样与 $O(1)$ 删除。

对查询秩区间 $[L,R)$，令 $\mathcal{U}(L,R)$ 为该区间在段树上的规范分解结点集合（结点段两两不交，且并为 $[L,R)$），满足 $|\mathcal{U}(L,R)|=O(\log N)$。

**引理 2.6（range 答案的无重并）**  
对任意秩区间 $[L,R)$，
$$
\{\,s\ \text{活跃}\mid \mathrm{rank}(s)\in[L,R)\,\}
=
\biguplus_{v\in\mathcal{U}(L,R)} B(v).
$$

**证明**  
取任意活跃对象 $s$ 且 $\mathrm{rank}(s)\in[L,R)$。由于 $\mathcal{U}(L,R)$ 的结点段不交且覆盖 $[L,R)$，存在唯一结点 $v^\star\in\mathcal{U}(L,R)$ 满足 $\mathrm{rank}(s)\in\mathrm{seg}(v^\star)$。  
另一方面，$v^\star$ 是秩叶 $\mathrm{rank}(s)$ 的祖先结点；插入时沿叶到根路径把 $s$ 加入了所有祖先桶，因此 $s\in B(v^\star)$。唯一性由 $v^\star$ 的唯一性保证。证毕。

---

### 2.3.3 三原语与均匀采样

给定开区间 $(\ell,r)$，计算秩区间 $[L,R)$ 与分解集合 $\mathcal{U}(L,R)$，记
$$
C_B(\ell,r):=\sum_{v\in\mathcal{U}(L,R)} |B(v)|.
$$

- `COUNT_B(ℓ,r)`：返回 $C_B(\ell,r)$；
- `REPORT_B(ℓ,r)`：枚举所有 $v\in\mathcal{U}(L,R)$ 的桶 $B(v)$ 并输出（由引理 2.6 不重）；
- `SAMPLE_B(ℓ,r,k)`：若 $C_B(\ell,r)=0$ 返回空序列；否则对每个样本位置独立执行：
  1) 以权重 $|B(v)|$ 在 $v\in\mathcal{U}(L,R)$ 上抽取结点；
  2) 在选中桶内均匀抽取一个对象。

同样可对 $\{|B(v)|\}$ 构建 alias 表以 $O(1)$ 按权重选桶（预处理 $O(\log N)$）。

**定理 2.7（模式 B 采样的均匀性与独立性）**  
`SAMPLE_B(\ell,r,k)` 返回的样本在集合 $\{s\mid \ell<L_y(s)<r\}$ 上均匀、独立同分布（有放回）。

**证明**  
由引理 2.6，答案集合可写为不交并 $\biguplus_{v\in\mathcal{U}(L,R)}B(v)$。任取答案元素 $s$，其唯一属于某个桶 $B(v^\star)$。单次抽样命中 $s$ 的概率为
$$
\Pr[s]
=\frac{|B(v^\star)|}{C_B(\ell,r)}\cdot \frac{1}{|B(v^\star)|}
=\frac{1}{C_B(\ell,r)},
$$
与 $s$ 无关，因此均匀。各样本位置独立且有放回，故 i.i.d.。证毕。

---

### 2.3.4 复杂度

- `INSERT/DELETE`：$O(\log N)$；
- `COUNT_B`：$O(\log N)$；
- `REPORT_B`：$O(\log N+K)$；
- `SAMPLE_B(ℓ,r,k)`：$O(\log N+k)$。

---

## 2.4 事件块原语：两模式混合

本节将 2.2 与 2.3 的结果合并为对一维相交集合 $K(q)$ 的 `COUNT/REPORT/SAMPLE`。由定理 2.2，
$$
K(q)=K_A(q)\uplus K_B(q).
$$

令
$$
w_A(q):=|K_A(q)|,\qquad w_B(q):=|K_B(q)|,\qquad w(q):=|K(q)|=w_A(q)+w_B(q).
$$

### 2.4.1 COUNT 与 REPORT

- `COUNT(q)`：
  $$
  \mathrm{COUNT}(q)=\mathrm{COUNT}_A(L_y(q))+\mathrm{COUNT}_B(L_y(q),R_y(q)).
  $$

- `REPORT(q)`：输出 $\mathrm{REPORT}_A(L_y(q))$ 与 $\mathrm{REPORT}_B(L_y(q),R_y(q))$ 的拼接即可；两部分无重。

---

### 2.4.2 SAMPLE：按权重混合 + 模式内均匀

`SAMPLE(q,k)` 的实现遵循两阶段抽样：

1) 按权重在两种模式间选择：
$$
\Pr[\text{选 A}]=\frac{w_A(q)}{w(q)},\qquad \Pr[\text{选 B}]=\frac{w_B(q)}{w(q)}.
$$
2) 条件于所选模式，在该模式对应集合上均匀抽取一个 partner。

为批量生成 $k$ 个样本且保持 i.i.d.，采用“位置指派—分组采样—回填”的流程：

- 先对两权重 $(w_A,w_B)$ 构建 alias 表；
- 对每个样本位置 $t=1..k$ 独立抽取模式 $M_t\in\{A,B\}$；
- 统计 $k_A=|\{t\mid M_t=A\}|$、$k_B=k-k_A$；
- 调用 `SAMPLE_A(L_y(q),k_A)` 与 `SAMPLE_B(L_y(q),R_y(q),k_B)`；
- 按模式把返回结果回填到对应位置。

**命题 2.8（两模式混合采样的均匀性与独立性）**  
`SAMPLE(q,k)` 返回的 $k$ 个样本在 $K(q)$ 上 i.i.d. 且均匀（有放回）。

**证明**  
取任意 $s\in K(q)$。由定理 2.2，$s$ 唯一属于 $K_A(q)$ 或 $K_B(q)$ 之一。

- 若 $s\in K_A(q)$，一次采样命中 $s$ 的概率为
  $$
  \Pr[s]
  =\Pr[\text{选 A}]\cdot \Pr[s\mid \text{选 A}]
  =\frac{w_A(q)}{w(q)}\cdot \frac{1}{w_A(q)}
  =\frac{1}{w(q)}.
  $$
- 若 $s\in K_B(q)$，同理得到 $\Pr[s]=1/w(q)$。

因此单次输出在 $K(q)$ 上均匀。各样本位置的模式选择与模式内采样均使用独立随机性且为有放回抽样，因此得到 i.i.d. 序列。证毕。

---

### 2.4.3 事件块原语的总体复杂度

设对侧段树 A 的端点规模为 $M$，段树 B 的秩域规模为 $N$，则单次事件块查询：

- `COUNT(q)`：$O(\log M+\log N)$；
- `REPORT(q)`：$O(\log M+\log N+|K(q)|)$；
- `SAMPLE(q,k)`：$O(\log M+\log N+k)$。

---

## 2.5 与扫描线过程的对接

扫描过程中分别为两类矩形维护对侧活跃集索引。对每一侧（例如 $R_c$）维护两套结构：

- A 索引：stabbing 段树，索引该侧活跃矩形的区间 $I(s)=[L_y(s),R_y(s))$；
- B 索引：range 段树，索引该侧活跃矩形的左端点 $L_y(s)$（通过秩域维护）。

处理事件遵循第 1 章的顺序：END 先删、START 先查再插。

- 若事件为 $\mathrm{END}(r)$：将 $r$ 从其所属侧的 A/B 索引中 `DELETE`；
- 若事件为 $\mathrm{START}(q)$：
  1) 在对侧索引上对 $J(q)=[L_y(q),R_y(q))$ 执行 `COUNT/REPORT/SAMPLE` 得到 $K(q)$ 的信息；
  2) 将 $q$ 插入其所属侧索引：`INSERT(q)`。

当需要输出 Join 对 $(r_c,r_{\bar c})$ 时，对每个 partner $s\in K(q)$ 按事件侧别映射为有序对：

- 若 $q\in R_c$，输出 $(q,s)$；
- 若 $q\in R_{\bar c}$，输出 $(s,q)$。

至此，事件块原语在二维情形下完全落地为两套段树结构与一次两模式混合；它们将作为第 3 章三种采样框架的直接输入。

# 第 3 章 从事件块原语到 i.i.d. 均匀 Join 采样的三种采样框架

本章研究二维平面上的 Join 采样：给定两类轴对齐半开矩形集合 $R_c$ 与 $R_{\bar c}$，令
$$
J=\{(r_c,r_{\bar c})\mid r_c\in R_c,\ r_{\bar c}\in R_{\bar c},\ r_c\cap r_{\bar c}\neq\varnothing\}
$$
为跨集合、有序方向固定的相交对集合。目标是在 $J$ 上生成 $t$ 个样本 $Z_1,\dots,Z_t$，满足 **独立同分布（i.i.d.）且均匀（有放回）**。

本章把几何部分抽象为“事件块原语”接口：对扫描线上的每个 $\mathrm{START}$ 事件，都能在对侧活跃集中执行 `COUNT/REPORT/SAMPLE`。只要接口语义成立，以下三套框架均能在 $J$ 上得到严格的 i.i.d. 均匀输出；至于原语由何种索引结构实现，仅影响性能取舍，不影响分布正确性。

三套框架的对比如下（$M$ 为 $\mathrm{START}$ 事件总数，$M=|R_c|+|R_{\bar c}|$）：

| 框架                 | 扫描次数 |       额外空间 | 主要优点                                    | 主要代价                             |
| -------------------- | -------: | -------------: | ------------------------------------------- | ------------------------------------ |
| 框架 I：显式枚举     |        1 |  $\Theta(|J|)$ | 逻辑最直接                                  | $|J|$ 可能极大                       |
| 框架 II：两遍回填    |        2 |       $O(M+t)$ | 不存 $J$，分布精确                          | 需要可重放第二遍扫描                 |
| 框架 III：预算自适应 |  $\le 2$ | $\le B+O(M+t)$ | 在预算 $B$ 下尽量减少第二遍工作，可一遍结束 | 需要缓存策略与代价模型（只影响性能） |

---

## 3.1 采样目标、扫描事件与事件块

### 3.1.1 半开矩形、相交与 Join 目标分布

每个矩形 $r$ 用半开形式表示为
$$
r=[L_x(r),R_x(r))\times[L_y(r),R_y(r)),
\qquad L_x(r)<R_x(r),\ L_y(r)<R_y(r).
$$
半开语义下，两矩形 $r,s$ 相交当且仅当两轴都满足严格不等式：
$$
r\cap s\neq\varnothing
\iff
\big(L_x(r)<R_x(s)\wedge L_x(s)<R_x(r)\big)
\ \wedge\
\big(L_y(r)<R_y(s)\wedge L_y(s)<R_y(r)\big).
$$
严格不等号确保“贴边”（例如 $R_x(r)=L_x(s)$ 或 $R_y(r)=L_y(s)$）不计为相交。

采样目标：若 $|J|>0$，输出 $Z_1,\dots,Z_t\in J$，满足
$$
\Pr\{Z_j=P\}=\frac{1}{|J|}\quad(\forall P\in J,\ \forall j),
\qquad Z_1,\dots,Z_t\ \text{相互独立}.
$$
若 $|J|=0$，输出空序列。

---

### 3.1.2 扫描维、事件总序与活跃集

固定沿 $x$ 轴进行扫描。对每个矩形 $r\in R_c\cup R_{\bar c}$ 定义两个事件：

- $\mathrm{START}(r)$，坐标 $x=L_x(r)$；
- $\mathrm{END}(r)$，坐标 $x=R_x(r)$。

对所有事件定义一个可复现的总序 $\prec$：

1. 坐标小者先；
2. 坐标相同则 **END 严格先于 START**；
3. 若同为 START（或同为 END）且坐标相同，则按唯一 $\mathrm{id}(r)$（或任意固定全序）打破平局。

扫描过程中维护两侧活跃集 $A_c,A_{\bar c}$：

- 处理 $\mathrm{END}(r)$：将 $r$ 从其所属活跃集中删除；
- 处理 $\mathrm{START}(r)$：先执行事件块查询，再将 $r$ 插入其所属活跃集。

该顺序与半开语义一致：当 $R_x(r)=L_x(s)$ 时，$\mathrm{END}(r)\prec \mathrm{START}(s)$，因此 $r$ 不会成为 $s$ 的活跃候选，从而贴边不产生相交对。

---

### 3.1.3 partner 集与事件块

对每个矩形 $r$ 定义其 $y$ 区间：
$$
I(r)=[L_y(r),R_y(r)).
$$

令 $E^+$ 为所有 START 事件集合，并按 $\prec$ 顺序编号：
$$
E^+=\{e_1,e_2,\dots,e_M\},\qquad e_i=\mathrm{START}(q_i).
$$

**引理 3.0（扫描维自动满足相交的一半）**  
当处理 START 事件 $e=\mathrm{START}(q)$（坐标 $x=L_x(q)$）时，任意对侧活跃矩形 $s$ 都满足
$$
L_x(s)<R_x(q)\quad\text{且}\quad L_x(q)<R_x(s).
$$

**证明**  
由于 $s$ 处于对侧活跃集中，其 START 已发生且 END 尚未发生，故 $L_x(s)\le x$ 且 $x<R_x(s)$。又 $R_x(q)>L_x(q)=x$（矩形非空），于是 $L_x(s)\le x<R_x(q)$，从而 $L_x(s)<R_x(q)$；同时 $L_x(q)=x<R_x(s)$ 亦成立。证毕。

因此，在 START 事件处判断相交只需检查 $y$ 轴区间是否相交，即
$$
I(q)\cap I(s)\neq\varnothing.
$$

**定义（partner 集合）**  
当处理 START 事件 $e=\mathrm{START}(q)$ 时：

- 若 $q\in R_c$，定义
  $$
  K_e=\{\,s\in A_{\bar c}\mid I(q)\cap I(s)\neq\varnothing\,\};
  $$
- 若 $q\in R_{\bar c}$，定义
  $$
  K_e=\{\,s\in A_{c}\mid I(q)\cap I(s)\neq\varnothing\,\}.
  $$

为统一输出方向恒为 $(r_c,r_{\bar c})$，定义映射 $\Phi_e$：

- 若 $q\in R_c$，则对 $s\in K_e$，$\Phi_e(s)=(q,s)$；
- 若 $q\in R_{\bar c}$，则对 $s\in K_e$，$\Phi_e(s)=(s,q)$。

**定义（事件块）**  
对每个 START 事件 $e\in E^+$ 定义事件块与权重
$$
J_e=\{\Phi_e(s)\mid s\in K_e\}\subseteq J,\qquad w_e:=|J_e|=|K_e|.
$$

下面给出 $J$ 的无重分解。

**定理 3.1（事件块无重分解）**  
有
$$
J=\biguplus_{e\in E^+} J_e,\qquad |J|=\sum_{e\in E^+} w_e,
$$
其中 $\biguplus$ 表示不交并。

**证明**  

*覆盖性*：任取 $(r_c,r_{\bar c})\in J$。考虑二者的 START 事件
$$
e_c=\mathrm{START}(r_c),\qquad e_{\bar c}=\mathrm{START}(r_{\bar c}),
$$
令
$$
e^\star=\max_{\prec}\{e_c,e_{\bar c}\}.
$$
设 $e^\star=\mathrm{START}(q)$，另一侧矩形为 $s$（即 $\{q,s\}=\{r_c,r_{\bar c}\}$）。由于 $e^\star$ 是较晚 START，处理 $e^\star$ 时 $s$ 的 START 已发生。又 $(r_c,r_{\bar c})$ 在 $x$ 轴上相交，必有 $L_x(q)<R_x(s)$；结合 END-before-START 的事件序，$s$ 在时刻 $L_x(q)$ 尚未 END，因而 $s$ 必在对侧活跃集中。再由 $y$ 轴相交可知 $I(q)\cap I(s)\neq\varnothing$，于是 $s\in K_{e^\star}$，从而
$$
(r_c,r_{\bar c})=\Phi_{e^\star}(s)\in J_{e^\star}.
$$

*互斥性*：任意相交对 $(r_c,r_{\bar c})$ 只可能在两者 START 中较晚的那一次被“看见”。在较早 START 时，较晚者尚未插入活跃集，不可能成为 partner；因此该对不可能同时落入两个不同事件块。证毕。

---

### 3.1.4 事件块原语与随机性约定

本章三套框架仅依赖如下原语语义。对每个 START 事件 $e$（即对每个 $K_e$）假定可调用：

- `COUNT(e)`：返回 $w_e=|K_e|$；
- `REPORT(e)`：枚举 $K_e$ 中每个元素一次（顺序任意）；
- `SAMPLE(e,k)`：返回 $k$ 个在 $K_e$ 上 **i.i.d. 均匀（有放回）** 的样本。

为严格保证全局 i.i.d.，需要不同阶段与不同调用使用相互独立的随机性。一个可复现的实现方式是：给每个事件 $e_i$ 分配整数编号 $i$，并为每个阶段（如“位置指派”“预取采样”“残差采样”等）分配固定的 `phase_id`，用哈希派生子种子：
$$
\texttt{seed}(i,\texttt{phase\_id},\texttt{ctr})=\mathrm{Hash}(\texttt{master\_seed},i,\texttt{phase\_id},\texttt{ctr}),
$$
其中 `ctr` 为该阶段的调用计数器。这样可在不共享随机流的前提下保证可重现实验。

---

## 3.2 事件块混合采样：全局均匀与 i.i.d.

令
$$
W:=|J|=\sum_{e\in E^+} w_e.
$$
若 $W=0$，则 $J=\varnothing$。

否则考虑以下单次输出过程：

1. 按分布 $\Pr\{E=e\}=w_e/W$ 抽取一个 START 事件 $E$；
2. 在 $K_E$ 上均匀抽取一个 partner $S$（有放回），输出 $Z=\Phi_E(S)$。

**引理 3.2（单次输出均匀）**  
上述过程输出的 $Z$ 在 $J$ 上均匀：
$$
\Pr\{Z=P\}=\frac{1}{|J|},\qquad \forall P\in J.
$$

**证明**  
任取 $P\in J$。由定理 3.1，存在唯一事件 $e(P)$ 使 $P\in J_{e(P)}$。因此
$$
\Pr\{Z=P\}
=\Pr\{E=e(P)\}\cdot\Pr\{Z=P\mid E=e(P)\}
=\frac{w_{e(P)}}{W}\cdot\frac{1}{w_{e(P)}}
=\frac{1}{W}
=\frac{1}{|J|}.
$$
证毕。

**引理 3.3（多次输出 i.i.d.）**  
若独立重复上述两步 $t$ 次（每次事件选择与块内抽样使用独立随机性，且为有放回），得到 $Z_1,\dots,Z_t$，则它们相互独立且同分布为 $J$ 上的均匀分布。

---

## 3.3 框架 I：显式枚举 Join 后均匀抽样

框架 I 在一次扫描中显式构造 $J$ 的全部元素，再在数组上独立均匀抽样。

### 3.3.1 算法

~~~text
Algorithm 3.1  Materialize-and-Sample
Input : R_c, R_{\bar c}, sample size t
Output: Z_1..Z_t

1: Pairs ← empty dynamic array
2: Scan all events in order ≺, maintaining active sets
3: For each START event e:
4:     For each s in REPORT(e):            // enumerates K_e
5:         Pairs.append( Φ_e(s) )          // append one element of J_e
6: If |Pairs| = 0: return empty sequence
7: For j = 1..t:
8:     Draw I_j uniformly from {1..|Pairs|} (with replacement)
9:     Z_j ← Pairs[I_j]
10:return Z_1..Z_t
~~~

### 3.3.2 正确性

**定理 3.4（框架 I 的正确性）**  
算法 3.1 输出 $Z_1,\dots,Z_t$ 为 $J$ 上 i.i.d. 均匀（有放回）样本。

**证明**  
扫描阶段对每个事件 $e$ 追加 $J_e$ 的全部元素。由定理 3.1，`Pairs` 恰为 $J$ 的无重枚举。随后对 `Pairs` 做独立均匀索引抽样即在 $J$ 上独立均匀抽样。证毕。

### 3.3.3 代价

时间与空间均由 $|J|$ 主导：时间 $\Theta(|J|)+O(t)$，空间 $\Theta(|J|)$（加上扫描维护开销）。

---

## 3.4 框架 II：两遍扫描的精确计数—位置指派—回填生成

框架 II 不显式构造 $J$，而是用第一遍扫描精确获得每个事件块权重 $w_i$，再按 $w_i/W$ 为每个输出位置指派事件索引；第二遍仅对被指派到的事件块生成所需样本并回填。

### 3.4.1 算法

将 START 事件按 $\prec$ 顺序记为 $e_1,\dots,e_M$，令 $w_i:=w_{e_i}$。

~~~text
Algorithm 3.2  Two-Pass Count-and-Backfill
Input : R_c, R_{\bar c}, sample size t
Output: Z_1..Z_t

// Pass 1: exact block weights
1: Scan events in order ≺
2: For each START event e_i:
3:     w_i ← COUNT(e_i)
4: W ← sum_{i=1}^M w_i
5: If W = 0: return empty sequence

// Planning: assign each output position to an event index
6: Build a discrete sampler over {1..M} with Pr{I=i} = w_i / W
7: For i = 1..M: L_i ← empty list
8: For j = 1..t:
9:     Draw I_j independently from Pr{I=i} = w_i / W
10:    Append position j to L_{I_j}

// Pass 2: backfill samples by blocks
11: Initialize output array Z[1..t]
12: Scan events in order ≺ again (same tie-break rules)
13: For each START event e_i:
14:     k_i ← |L_i|
15:     If k_i = 0: continue
16:     Draw partners s_1..s_{k_i} ← SAMPLE(e_i, k_i)   // i.i.d. on K_{e_i}
17:     For ℓ = 1..k_i:
18:         j ← L_i[ℓ]
19:         Z[j] ← Φ_{e_i}( s_ℓ )
20: return Z[1..t]
~~~

**可重放约束**  
第二遍扫描必须使用与第一遍完全相同的事件总序 $\prec$（包括 END-before-START 与 tie-break 规则），以保证两遍中 $K_{e_i}$ 的定义一致。

---

### 3.4.2 正确性

**定理 3.5（框架 II 的 i.i.d. 均匀性）**  
若 `COUNT` 返回精确 $w_i$，`SAMPLE(e_i,k_i)` 返回 $K_{e_i}$ 上 i.i.d. 均匀（有放回）样本，且各阶段随机性独立，则算法 3.2 输出 $Z_1,\dots,Z_t$ 在 $J$ 上 i.i.d. 均匀（有放回）。

**证明**  
规划阶段对每个位置 $j$ 独立抽取事件索引 $I_j$，满足 $\Pr\{I_j=i\}=w_i/W$。条件于 $I_j=i$，回填阶段令 $Z_j=\Phi_{e_i}(S)$，其中 $S$ 在 $K_{e_i}$ 上均匀（有放回）。因此由引理 3.2，$Z_j$ 的边缘分布在 $J$ 上均匀。

独立性来自两点：$\{I_j\}$ 相互独立；对每个事件 $i$，`SAMPLE(e_i,k_i)` 输出 $k_i$ 个独立样本，并且不同事件的采样调用使用独立随机性。位置回填是确定性写入，不引入相关性。因此 $Z_1,\dots,Z_t$ 相互独立且同分布，证毕。

---

### 3.4.3 原语调用代价（以次数计）

- Pass 1：对每个 START 事件一次 `COUNT`，共 $M$ 次；
- 规划阶段：$t$ 次离散抽样；
- Pass 2：对所有 $k_i>0$ 的事件各调用一次 `SAMPLE(e_i,k_i)`；总样本数 $\sum_i k_i=t$。

额外空间主要为数组 $w[1..M]$ 与位置列表总大小 $t$。

---

## 3.5 框架 III：预算约束下的自适应生成（预取 + 缓存 + 流式回填）

框架 III 在不改变目标分布的前提下，引入预算 $B$（以“可缓存的 partner 记录条数”计量），通过第一遍扫描的缓存与预取尽量减少第二遍仍需执行的原语工作量；当缓存覆盖足够时可在一遍扫描内结束。

### 3.5.1 预算与两类缓存

对每个事件 $e_i$，允许保存两种对象（可同时存在）：

1. **全量缓存** $\mathcal C_i$：保存完整 partner 列表 $K_{e_i}$（由一次 `REPORT(e_i)` 得到），占用 $|\mathcal C_i|=w_i$ 单位预算。此后可通过均匀随机访问在 $K_{e_i}$ 上进行任意次数的均匀抽样（有放回）。
2. **预取样本缓存** $\mathcal S_i$：保存 $s_i$ 个由 `SAMPLE(e_i,s_i)` 得到的 i.i.d. 样本，占用 $|\mathcal S_i|=s_i$ 单位预算。

总预算约束为
$$
\sum_i |\mathcal C_i|+\sum_i |\mathcal S_i|\le B.
$$

分布正确性只依赖于两点：位置指派按 $w_i/W$；每个被写入的位置对应的 partner 是从 $K_{e_i}$ 上独立均匀（有放回）生成。缓存策略仅改变这些样本“何时生成、从哪来”，不改变它们的分布属性。

---

### 3.5.2 预算分配的离线基准：边际收益与最优槽位选择

规划阶段把 $t$ 个输出位置按分布
$$
p_i=\frac{w_i}{W}
$$
指派给事件块。令
$$
k_i:=|\mathcal L_i|
$$
为事件 $i$ 被指派的位置数，则向量 $(k_1,\dots,k_M)$ 服从多项分布 $\mathrm{Multinomial}(t;p_1,\dots,p_M)$，从而每个边缘 $k_i\sim\mathrm{Binomial}(t,p_i)$。

若第一遍为事件 $i$ 预取 $s_i$ 个样本，则第二遍需补齐的残差为
$$
r_i=(k_i-s_i)_+.
$$
自然目标是使 $\mathbb E[\sum_i r_i]$ 尽可能小。

**引理 3.6（边际收益）**  
对固定事件 $i$，将预取数从 $s$ 增加到 $s+1$ 带来的期望残差减少量为
$$
\Delta_i(s+1)
=\mathbb E[(k_i-s)_+]-\mathbb E[(k_i-(s+1))_+]
=\Pr(k_i\ge s+1).
$$

**证明**  
利用恒等式 $(k-s)_+=\sum_{r=s+1}^{t}\mathbf 1[k\ge r]$，取期望得
$$
\mathbb E[(k_i-s)_+]=\sum_{r=s+1}^{t}\Pr(k_i\ge r),
$$
相邻相减即得结论。证毕。

由于 $\Pr(k_i\ge r)$ 随 $r$ 单调递减，每个事件的预取具有“边际收益递减”。

**定理 3.7（离线最优：选择最大价值的 $B$ 个槽位）**  
若已知所有 $p_i$，且预算全部用于预取样本（每单位预算对应 1 个预取样本），则使 $\mathbb E[\sum_i (k_i-s_i)_+]$ 最小的整数解可表述为：从槽位集合
$$
\{(i,r)\mid i\in\{1,\dots,M\},\ r\in\{1,\dots,t\}\}
$$
中选择恰好 $B$ 个槽位，使总价值最大，其中槽位 $(i,r)$ 的价值为
$$
v_{i,r}=\Pr(k_i\ge r).
$$
并且最优解对每个事件 $i$ 取一个前缀：若选择了 $(i,r)$，则必选择所有 $(i,1),\dots,(i,r-1)$。

**证明要点**  
由引理 3.6，每增加 1 个预取样本等价于选择一个槽位，其价值等于期望残差的减少量。预算为 $B$ 时，总减少量是所选槽位价值之和，因此最优策略为选择价值最大的 $B$ 个槽位。由于 $v_{i,r}$ 对 $r$ 递减，最优解对每个事件必为前缀。证毕。

---

### 3.5.3 在线预取与可撤销的槽位堆

第一遍扫描时尚未知 $W$，从而 $p_i=w_i/W$ 无法直接使用。这里给出一个在线策略：用估计值构造每个槽位的“价值评分”，并用全局堆维护在预算内优先保留的槽位。该策略用于性能优化；评分是否精确不影响输出分布。

**在线估计与评分**  
扫描到第 $i$ 个 START 事件时，已知 $w_i$ 与前缀和 $W_{\text{sofar}}=\sum_{\ell\le i} w_\ell$。可用外推得到 $\widehat W_i$，例如
$$
\widehat W_i = W_{\text{sofar}} + (M-i)\cdot \frac{W_{\text{sofar}}}{i}.
$$
并令
$$
\widehat p_i=\frac{w_i}{\widehat W_i},\qquad \widehat\mu_i=t\widehat p_i.
$$
为槽位 $(i,r)$ 定义一个单调递减的评分序列 $\widehat v_{i,1}\ge \widehat v_{i,2}\ge\cdots$ 来近似 $v_{i,r}=\Pr(k_i\ge r)$。常用做法是 Poisson 近似：
$$
k_i\approx \mathrm{Poisson}(\widehat\mu_i),
\qquad
\widehat v_{i,r}\approx \Pr(\mathrm{Poisson}(\widehat\mu_i)\ge r).
$$

**可撤销堆维护**  
令 $B_{\text{samp}}=B-\sum_i|\mathcal C_i|$ 为分配给预取样本缓存的剩余预算。维护一个最小堆 $H$，其中每个元素表示一个被保留的预取槽位，记录二元组 $(\widehat v,i)$。并维护计数 $s_i$ 表示事件 $i$ 当前保留的预取槽位数（因此最终应缓存 $s_i$ 个样本）。

堆维护不变式：

- $\sum_i s_i=|H|\le B_{\text{samp}}$；
- 对每个 $i$，被保留的槽位对应前缀 $\{(i,1),\dots,(i,s_i)\}$。

当处理事件 $i$ 时，按 $r=1,2,\dots$ 依次尝试加入槽位 $(i,r)$：

- 若 $|H|<B_{\text{samp}}$，直接加入；
- 若 $|H|=B_{\text{samp}}$ 且 $\widehat v_{i,r}>\min(H)$，则加入并弹出堆顶（全局最小评分槽位）；
- 否则停止（由于 $\widehat v_{i,r}$ 随 $r$ 递减，后续槽位更不可能进入堆）。

被弹出的槽位来自某个事件 $j$，则令 $s_j\leftarrow s_j-1$。若事件 $j$ 已有样本缓存 $\mathcal S_j$，则从末尾丢弃一个样本以保持 $|\mathcal S_j|=s_j$。这一丢弃规则与样本取值无关，保留下来的子序列仍为 i.i.d.，因此不改变分布属性。

事件 $i$ 的槽位决策结束后，调用一次
$$
\mathcal S_i\leftarrow \texttt{SAMPLE}(e_i,s_i)
$$
并保存返回的 $s_i$ 个样本。若将来 $s_i$ 因堆弹出而减少，则同样从末尾丢弃即可。

**小块全量缓存**  
当 $w_i$ 小于阈值 $w_{\text{small}}$ 且预算允许时，可设置 $\mathcal C_i=K_{e_i}$（一次 `REPORT` 获取并保存）。当新增全量缓存使 $B_{\text{samp}}$ 下降时，需要反复弹出堆顶直至 $|H|\le B_{\text{samp}}$，并同步减少对应事件的 $s_j$ 与丢弃样本，保持预算不变式。

---

### 3.5.4 仅一次 REPORT 的流式有放回采样

当某事件块在规划后需要补齐 $r$ 个样本时，除直接调用 `SAMPLE(e,r)` 外，还可用一次 `REPORT(e)` 实现 i.i.d. 有放回采样。

~~~text
Algorithm 3.3  STREAM-REPORT-SAMPLE(e, r, w)
Input : START event e, required samples r, block size w=|K_e|
Output: r i.i.d. samples from K_e (with replacement)

1: Draw u_1..u_r i.i.d. uniformly from {1..w}
2: Sort pairs (u_j, j) by u_j ascending
3: p ← 0
4: For each x in REPORT(e):                 // x is the next element of K_e
5:     p ← p + 1                            // x is the p-th element in this enumeration
6:     While next pair (u_j, j) has u_j = p:
7:         out[j] ← x
8:         advance to next (u_j, j)
9: return out[1..r]
~~~

**引理 3.8（流式回填正确性）**  
算法 3.3 输出在 $K_e$ 上 i.i.d. 均匀（有放回）。

**证明**  
`REPORT(e)` 给出 $K_e$ 的某个排列 $(x_1,\dots,x_w)$。算法输出 $out[j]=x_{u_j}$，其中 $u_j$ 独立且在 $\{1,\dots,w\}$ 上均匀。因此每个 $out[j]$ 在 $K_e$ 上均匀，且不同 $j$ 独立；重复的 $u_j$ 产生重复输出，对应有放回抽样。证毕。

---

### 3.5.5 完整算法：预算自适应 Join 采样

算法由“第一遍计数与缓存”“位置指派”“缓存回填”“必要时第二遍补齐”组成。

~~~text
Algorithm 3.4  Adaptive Budgeted Join Sampling (at most two passes)
Input : R_c, R_{\bar c}, sample size t, budget B
Output: Z_1..Z_t

// Pass 1: exact weights + caching/prefetch under budget
1: Initialize arrays w[1..M], C_i ← empty, S_i ← empty, s_i ← 0
2: mem_full ← 0, heap H ← empty
3: Scan events in order ≺
4: For each START event e_i:
5:     w_i ← COUNT(e_i)
6:     // optional: full cache for small blocks
7:     If (w_i ≤ w_small) and (mem_full + w_i ≤ B):
8:         C_i ← REPORT(e_i)                // store full partner list
9:         mem_full ← mem_full + w_i
10:        // shrink prefetch heap capacity if needed
11:        B_samp ← B - mem_full
12:        While |H| > B_samp:
13:            pop (v, j) from H; s_j ← s_j - 1; discard last sample from S_j
14:        continue
15:    // prefetch slots by global heap under remaining capacity
16:    B_samp ← B - mem_full
17:    While true:
18:        r ← s_i + 1
19:        compute score v_hat ← score(i, r)   // monotone decreasing in r
20:        If |H| < B_samp:
21:            push (v_hat, i) into H; s_i ← s_i + 1
22:        Else if v_hat > min(H):
23:            push (v_hat, i) into H; s_i ← s_i + 1
24:            pop (v_min, j) from H; s_j ← s_j - 1; discard last sample from S_j
25:        Else:
26:            break
27:    If s_i > 0:
28:        S_i ← SAMPLE(e_i, s_i)            // store i.i.d. samples (with replacement)

// Planning: assign t output positions to events by exact weights
29: W ← sum_{i=1}^M w_i
30: If W = 0: return empty sequence
31: Build discrete sampler over {1..M} with Pr{I=i} = w_i / W
32: For i = 1..M: L_i ← empty list
33: For j = 1..t:
34:     Draw I_j independently and append j to L_{I_j}

// Fill from caches/prefetch, record residual positions
35: Initialize output array Z[1..t], residual list R_i ← empty for all i
36: For i = 1..M:
37:     If |L_i| = 0: continue
38:     If C_i nonempty:
39:         For each j in L_i:
40:             draw s uniformly from C_i (with replacement)
41:             Z[j] ← Φ_{e_i}(s)
42:     Else:
43:         let a ← min(|L_i|, |S_i|)
44:         For ℓ = 1..a:
45:             j ← L_i[ℓ]
46:             Z[j] ← Φ_{e_i}( S_i[ℓ] )
47:         For ℓ = a+1..|L_i|:
48:             append L_i[ℓ] into R_i

// If no residual, finish in one pass
49: If R_i empty for all i: return Z[1..t]

// Pass 2: generate residual samples only where needed
50: Scan events in order ≺ again
51: For each START event e_i:
52:     If R_i empty or C_i nonempty: continue
53:     r_i ← |R_i|
54:     Choose one:
55:         partners ← SAMPLE(e_i, r_i)
56:         partners ← STREAM-REPORT-SAMPLE(e_i, r_i, w_i)
57:     For ℓ = 1..r_i:
58:         j ← R_i[ℓ]
59:         Z[j] ← Φ_{e_i}( partners[ℓ] )
60: return Z[1..t]
~~~

**选择 `SAMPLE` 还是流式 `REPORT`**  
可用简单代价比较决定第 55–56 行：设 `SAMPLE` 单样本代价近似为 $c_s$，`REPORT` 枚举单元素代价近似为 $c_r$，则当
$$
r_i\cdot c_s \gtrsim w_i\cdot c_r
$$
更倾向用一次 `REPORT` 的流式生成，否则更倾向直接 `SAMPLE(e_i,r_i)`。该选择只影响代价，不改变分布。

---

### 3.5.6 正确性与预算满足

**定理 3.9（框架 III 的 i.i.d. 均匀性与预算约束）**  
在以下前提下：

1) `COUNT/REPORT/SAMPLE` 满足其语义：`COUNT` 精确、`REPORT` 枚举一次、`SAMPLE` 在 $K_e$ 上返回 i.i.d. 均匀（有放回）样本；  
2) 位置指派与各次采样调用使用相互独立的随机性；  
3) 两遍扫描使用相同事件序 $\prec$；  

则算法 3.4 的输出 $Z_1,\dots,Z_t$ 在 $J$ 上 i.i.d. 均匀（有放回），且缓存占用始终不超过预算 $B$。

**证明**  

（预算）算法只在满足 `mem_full + w_i ≤ B` 时写入全量缓存；预取样本槽位通过堆维护始终满足 $|H|\le B_{\text{samp}}=B-mem_full$，并且每次堆弹出都会同步减少对应事件的样本缓存大小，因此
$$
\sum_i |\mathcal C_i| + \sum_i |\mathcal S_i|
= mem_full + |H| \le mem_full + (B-mem_full)=B.
$$

（分布）规划阶段对每个位置 $j$ 独立抽取事件索引 $I_j$，满足 $\Pr\{I_j=i\}=w_i/W$。因此只需证明：对任意事件 $i$，填入到 $L_i$ 与 $R_i$ 的 partner 记录都是在 $K_{e_i}$ 上独立均匀（有放回）生成，且与 $\{I_j\}$ 独立。

- 若使用全量缓存 $\mathcal C_i=K_{e_i}$：每次通过均匀随机访问从 $\mathcal C_i$ 取出一个元素（有放回），得到 i.i.d. 均匀样本。
- 若使用预取样本缓存 $\mathcal S_i$：$\mathcal S_i$ 由一次 `SAMPLE(e_i,s_i)` 得到，内部为 i.i.d. 均匀。丢弃尾部样本不改变剩余样本的 i.i.d. 结构。将这些样本按固定顺序写入若干位置不会引入相关性。
- 若存在残差：第二遍对事件 $i$ 生成的 `partners` 来自 `SAMPLE(e_i,r_i)` 或算法 3.3（由引理 3.8），两者都在 $K_{e_i}$ 上输出 $r_i$ 个 i.i.d. 均匀（有放回）样本。该阶段随机性与预取与位置指派独立。

综上，对每个位置 $j$，条件于 $I_j=i$，$Z_j$ 等价于从 $K_{e_i}$ 上独立均匀抽取一个 partner 并经 $\Phi_{e_i}$ 映射得到的元素。由引理 3.2，$Z_j$ 在 $J$ 上均匀；由随机性独立性约定，$Z_1,\dots,Z_t$ 相互独立，从而为 i.i.d. 均匀（有放回）。证毕。

---

### 3.5.7 代价刻画与实践建议（不影响分布）

- 框架 III 的第一遍必做 $M$ 次 `COUNT`；同时可能做部分 `REPORT`（小块全量缓存）与部分 `SAMPLE`（预取样本）。
- 若缓存覆盖了所有被指派的需求（即所有 $R_i$ 为空），则无需第二遍扫描，实现“一遍结束”。
- 第二遍只对 $R_i\neq\varnothing$ 的事件执行补齐操作；对每个事件最多进行一次补齐（选择 `SAMPLE` 或一次 `REPORT` 流式生成）。
- 当 $B$ 很小或代价模型难以估计时，取 $w_{\text{small}}=0$ 且不做预取，框架 III 退化为框架 II；当 $B$ 足够大时，可通过全量缓存或预取使第二遍显著缩短。

---

# 第 4 章 对照方法一：二维正交范围树实现事件块原语

本章给出一种“直接型”的事件块原语实现路线：在扫描线过程中不做模式化分解，而是将“第 2 维区间相交”整体提升为二维点集上的正交范围查询，并用二维正交范围树（range tree）在扫描过程中动态维护对侧活跃集，从而在每个 $\mathrm{START}(q)$ 事件处实现：

- `COUNT(e)`：返回事件块 $e$ 的 partner 集大小 $w_e=|K_e|$；
- `REPORT(e)`：枚举 $K_e$ 中每个元素一次（顺序任意）；
- `SAMPLE(e,k)`：在 $K_e$ 上返回 $k$ 个 i.i.d.、均匀、有放回样本。

与扫描框架一致：扫描轴固定为第 1 维（记作 $x$ 轴），事件序采用 **END-before-START** 以严格匹配半开语义；因此在处理某个 START 事件时，对侧活跃集已经自动满足第 1 维相交的两条严格不等式，事件块判定只需要检查第 2 维（记作 $y$ 轴）投影是否相交。

---

## 4.1 第 2 维相交的二维提升

### 4.1.1 投影区间与 partner 集

本章在二维空间中工作。每个对象是一个轴对齐半开矩形
$ r=[L_1(r),R_1(r))\times[L_2(r),R_2(r)) $，
其中 $L_i(r)<R_i(r)$。

固定扫描维为第 1 维。对任意矩形 $r$，其在第 2 维上的投影为半开区间
$ r^\downarrow=[L_2(r),R_2(r)) $。

在处理某个 START 事件 $e=\mathrm{START}(q)$ 时，对侧活跃集记为 $S$（其来源由 $q$ 属于哪一侧决定）。partner 集定义为
$ K_e=\{\,s\in S\mid q^\downarrow\cap s^\downarrow\neq\varnothing\,\}. $
由于扫描维的 END-before-START 事件序已保证第 1 维相交语义正确，$K_e$ 的判定仅由第 2 维区间相交决定。

---

### 4.1.2 投影区间 $\rightarrow$ 二维点

对对侧活跃集中的每个投影区间 $s^\downarrow=[L_2(s),R_2(s))$，构造二维点
$ p(s)=\big(L_2(s),\,R_2(s)\big)\in\mathbb{R}^{2}. $
点 $p(s)$ 携带对象的唯一标识 $\mathrm{id}(s)$，用于在查询后还原 partner 身份。

---

### 4.1.3 事件矩形 $\rightarrow$ 二维正交范围

对事件矩形 $q$（投影为 $q^\downarrow=[L_2(q),R_2(q))$），定义二维正交范围
$ Q(q)=(-\infty,\ R_2(q))\times(L_2(q),\ +\infty). $
这里两条边界均为**严格不等号**，与半开语义一致（贴边不算相交）。

---

### 4.1.4 等价性：投影相交 $\Longleftrightarrow$ 点落在范围内

**引理 4.1（二维提升等价性）**  
对任意投影区间 $q^\downarrow=[L_2(q),R_2(q))$ 与 $s^\downarrow=[L_2(s),R_2(s))$，有
$ q^\downarrow\cap s^\downarrow\neq\varnothing \iff p(s)\in Q(q). $

**证明**  
半开语义下，两个区间相交当且仅当
$ L_2(q)<R_2(s)\ \wedge\ L_2(s)<R_2(q). $
其中 $L_2(s)<R_2(q)$ 等价于点 $p(s)$ 的第 1 坐标落入 $(-\infty,R_2(q))$；而 $L_2(q)<R_2(s)$ 等价于点 $p(s)$ 的第 2 坐标落入 $(L_2(q),+\infty)$。两者合并即 $p(s)\in Q(q)$；反向同理。证毕。

由此，在 START 事件 $e$ 时刻，若对侧活跃点集为
$ P_S=\{p(s)\mid s\in S\}\subset\mathbb{R}^{2} $，
则
$ K_e=\{s\in S\mid p(s)\in Q(q(e))\},\qquad w_e=|K_e|=|P_S\cap Q(q(e))|. $

---

### 4.1.5 严格边界与重复坐标的实现约定

由于 $Q(q)$ 使用严格不等号，实现中必须确保“贴边点”不会被误计入。为此，本章统一采用如下总序键消除重复坐标的歧义。

对任意点 $p$，其唯一 id 为 $\mathrm{id}(p)\in\{1,2,\dots\}$。在任何一维排序/比较中，不直接用实数坐标 $x$，而是用键
$ \kappa(x,p)=(x,\mathrm{id}(p)) $
并按字典序比较。这样即便坐标相同，不同点也可严格区分。

当需要表达“严格阈值”时，引入两个概念哨兵：
- $\mathrm{id}_{\min}=0$（小于任何真实 id）；
- $\mathrm{id}_{\max}=+\infty$（大于任何真实 id）。

于是对任意一维严格约束，都可转为键比较：
- 约束 $x<b$ 等价于 $\kappa(x,p)<(b,\mathrm{id}_{\min})$；
- 约束 $x>a$ 等价于 $\kappa(x,p)>(a,\mathrm{id}_{\max})$；
- 开区间 $a<x<b$ 等价于 $(a,\mathrm{id}_{\max})<\kappa(x,p)<(b,\mathrm{id}_{\min})$。

后续所有范围查询均按上述严格语义执行。

---

## 4.2 二维正交范围树：结构定义与三原语实现

本节构造一个二维数据结构，在扫描过程中动态维护对侧活跃点集 $P_S\subset\mathbb{R}^2$，并在任意正交范围 $Q$ 上支持：

- `INSERT(p)` / `DELETE(p)`：活跃集变化；
- `COUNT(Q)`：返回 $|P_S\cap Q|$；
- `REPORT(Q)`：枚举 $P_S\cap Q$ 中的点（进而得到 partner id）；
- `SAMPLE(Q,k)`：从 $P_S\cap Q$ 上输出 $k$ 个 i.i.d. 均匀（有放回）样本。

整体思路分两层：
1. 用二维 range tree 把第一维（这里是 $L_2(\cdot)$）上的范围过滤变为若干互不重叠的子树块；
2. 在每个子树块上，用一维顺序统计结构（Treap）在第二维（这里是 $R_2(\cdot)$）上显式完成区间计数、枚举与均匀采样。

---

### 4.2.1 一维引擎：带顺序统计的 Treap（支持 COUNT/REPORT/SAMPLE）

定义一个一维动态集合结构，维护当前活跃点在某一维上的键集合（本章为 $(y,\mathrm{id})$），支持：

- `insert(key)`：插入一个键（对应一个点）；
- `erase(key)`：删除一个键；
- `count(I)`：返回开区间/半无限开区间 $I$ 内键的数量；
- `report(I)`：枚举区间 $I$ 内所有键；
- `sample(I)`：在区间 $I$ 内的键上均匀采样一个键。

为完全显式，本章采用随机化 Treap（笛卡尔树）作为顺序统计树。Treap 节点包含：
- `key`：键 $(y,\mathrm{id})$；
- `prio`：独立随机优先级；
- 左右子树指针；
- `sz`：子树大小（用于顺序统计）。

Treap 的关键原语是 `split` 与 `merge`：

- `split(T, key0)`：把 Treap $T$ 分裂成 $(A,B)$，满足  
  $\mathrm{keys}(A)<key0$ 且 $\mathrm{keys}(B)\ge key0$；
- `merge(A,B)`：在满足 $\max(\mathrm{keys}(A))<\min(\mathrm{keys}(B))$ 时合并为一棵 Treap。

对任意开区间/半无限区间 $I$，可用两次分裂把 $T$ 裁切为三段并获取中段。例如对开区间 $(a,b)$（允许 $a=-\infty$ 或 $b=+\infty$），令
$ key_L=(a,\mathrm{id}_{\max}),\qquad key_R=(b,\mathrm{id}_{\min}). $
一般有限情形下：
1. $(T_{\le a},T_{>a})=\mathrm{split}(T,key_L)$；
2. $(T_{(a,b)},T_{\ge b})=\mathrm{split}(T_{>a},key_R)$。

则：
- `count((a,b))=sz(T_{(a,b)})`；
- `report((a,b))`：对 $T_{(a,b)}$ 中序遍历输出全部键；
- `sample((a,b))`：若 $K=sz(T_{(a,b)})>0$，取 $r\sim\mathrm{Unif}\{1,\dots,K\}$，返回 `kth(T_{(a,b)},r)`，在该集合上均匀。

最后用
$ T=\mathrm{merge}\big(T_{\le a},\mathrm{merge}(T_{(a,b)},T_{\ge b})\big) $
恢复原 Treap。

> 以上过程只依赖局部分裂/合并与子树大小维护，不需要任何外部“区间采样黑盒”。

---

### 4.2.2 二维范围树：主树 + 关联 Treap

记对侧点全集规模上界为 $N$（对应某一侧矩形数）。二维 range tree 采用“静态主树 + 动态关联结构”的形式：

- **主树 $T_L$（第一维）**：  
  按第一坐标 $x_1=L_2(\cdot)$ 的键 $\kappa(L_2(p),p)=(L_2(p),\mathrm{id}(p))$，对全集点建立一棵平衡二叉树。每个节点 $v$ 对应一个子树点集 $P(v)$（全集固定；点是否活跃动态变化）。

- **关联结构 $\mathrm{assoc}(v)$（第二维）**：  
  对每个主树节点 $v$，维护一棵 Treap，存储所有“当前活跃且属于 $P(v)$”的点在第二坐标上的键  
  $\kappa(R_2(p),p)=(R_2(p),\mathrm{id}(p))$。  
  该 Treap 支持对形如 $(a,+\infty)$ 的严格区间执行 `count/report/sample`。

这种结构本质上是二维 range tree 的动态版本：第一维用主树做范围分解，第二维在每个分解块上用 Treap 做精确操作。

---

### 4.2.3 动态更新：INSERT / DELETE

点集在扫描过程中随矩形进入/离开活跃集发生变化。对点 $p=(L_2(p),R_2(p))$：

- `INSERT(p)`：在主树 $T_L$ 中按键 $\kappa(L_2(p),p)$ 走根到叶路径，沿路径上的每个节点 $v$ 在 $\mathrm{assoc}(v)$ 中插入键 $\kappa(R_2(p),p)$；
- `DELETE(p)`：同理沿同一路径，在每个 $\mathrm{assoc}(v)$ 中删除对应键。

主树高度为 $O(\log N)$，每个 Treap 插入/删除期望 $O(\log N)$，故单次更新期望时间为 $O(\log^2 N)$。

---

### 4.2.4 查询分解：二维正交范围 $\rightarrow$ 不交终端块

本章查询范围始终为
$ Q=(-\infty,b)\times(a,+\infty) $
（严格语义），其中在事件块查询里 $b=R_2(q)$、$a=L_2(q)$。

核心是将第一维前缀范围 $(-\infty,b)$ 分解为若干不交主树节点子树，使这些子树点集两两不交且并为所有满足 $L_2(\cdot)<b$ 的点。记该分解结点集合为
$ \mathcal{C}(b)=\{v_1,\dots,v_t\},\qquad t=O(\log N). $

这些结点满足：
- 子树点集 $P(v_i)$ 两两不交；
- $\{p\mid L_2(p)<b\}=\biguplus_{i=1}^t P(v_i)$。

对每个 $v_i$，其关联 Treap $\mathrm{assoc}(v_i)$ 存放的是 $P(v_i)$ 中的活跃点在第二维的键，因此在 $Q$ 上的命中点集可写成不交并：
$ P_S\cap Q=\biguplus_{i=1}^t \{p\in P(v_i)\cap P_S \mid R_2(p)>a\}. $

由此得到**终端块**列表
$ \mathcal{B}(Q)=\{b_1,\dots,b_t\},\qquad b_i=(\mathrm{assoc}(v_i),\ (a,+\infty)). $
每个块 $b_i$ 的点集就是在对应 Treap 上对区间 $(a,+\infty)$ 的查询结果。

---

### 4.2.5 分解正确性：覆盖与不交

**定理 4.2（终端块分解的覆盖与不交）**  
令 $Q=(-\infty,b)\times(a,+\infty)$，并令 $\mathcal{B}(Q)$ 由 4.2.4 构造。则：

1.（覆盖性）任意 $p\in P_S\cap Q$ 必属于某个块 $b\in\mathcal{B}(Q)$；  
2.（不交性）任意两个不同块 $b\neq b'$，其对应点集不相交。

因此
$ P_S\cap Q=\biguplus_{b\in\mathcal{B}(Q)} P(b). $

**证明**  
对任意 $p\in P_S\cap Q$，有 $L_2(p)<b$，故 $p$ 必属于第一维规范分解中的唯一子树点集 $P(v_i)$；又 $R_2(p)>a$，因此 $p$ 会被计入块 $b_i=(\mathrm{assoc}(v_i),(a,+\infty))$ 的 Treap 区间结果中，覆盖性成立。

不交性来自 $\mathcal{C}(b)$ 的子树点集两两不交：不同 $v_i$ 的子树不共享点，因此不同块的候选点集不可能重叠。证毕。

---

### 4.2.6 在终端块之上实现 COUNT / REPORT / SAMPLE

记 $\mathcal{B}(Q)=\{b_1,\dots,b_B\}$（此处 $B=t=O(\log N)$），每个块
$ b_i=(\mathcal{T}_i,(a,+\infty)) $
其中 $\mathcal{T}_i$ 为某个节点的关联 Treap 句柄。

定义块权重（块内活跃命中点数）：
$ w_i=\mathcal{T}_i.\mathrm{count}((a,+\infty)),\qquad W=\sum_{i=1}^B w_i=|P_S\cap Q|. $

#### `COUNT(Q)`
返回
$ \mathrm{COUNT}(Q)=\sum_{i=1}^B w_i. $

#### `REPORT(Q)`
对 $i=1..B$，输出 $\mathcal{T}_i.\mathrm{report}((a,+\infty))$ 枚举到的每个点 id。由定理 4.2，不同块之间不会重复。

#### `SAMPLE(Q,k)`
若 $W=0$ 返回空列表。否则需要在 $P_S\cap Q$ 上生成 $k$ 个 i.i.d. 均匀（有放回）样本。本章采用“按块权重混合 + 块内均匀”的两阶段抽样：

1. **按权重选择块**  
   构造前缀和
   $ S_0=0,\quad S_i=\sum_{j=1}^i w_j\ (1\le i\le B),\quad S_B=W. $
   取随机整数 $R\sim\mathrm{Unif}\{1,\dots,W\}$，二分找最小 $i$ 使 $S_i\ge R$，选中块 $b_i$。  
   由于 $B=O(\log N)$，该步为 $O(\log B)=O(\log\log N)$。

2. **块内均匀采样**  
   在选中块 $b_i$ 内调用
   $ p\leftarrow \mathcal{T}_i.\mathrm{sample}((a,+\infty)). $
   由 4.2.1，`sample((a,+\infty))` 在该区间内的活跃点上均匀。

重复上述两步 $k$ 次即得 $k$ 个样本。若希望把选块代价降到常数，可在块权重上构建 alias 表，使选块期望 $O(1)$。

---

### 4.2.7 采样正确性：均匀与独立（有放回）

**定理 4.3（`SAMPLE(Q,k)` 的均匀性与独立性）**  
设 $W=|P_S\cap Q|>0$。上述 `SAMPLE(Q,k)` 输出的每个样本在 $P_S\cap Q$ 上均匀；且 $k$ 次输出相互独立（有放回）。

**证明**  
由定理 4.2，$P_S\cap Q$ 是块点集的不交并：
$ P_S\cap Q=\biguplus_{i=1}^B P(b_i),\qquad |P(b_i)|=w_i,\quad \sum_i w_i=W. $
任取 $p\in P_S\cap Q$，它属于唯一块 $b_{i^\star}$。一次采样命中 $p$ 的概率为
$ \Pr[p]=\Pr[\text{选中 }b_{i^\star}]\cdot\Pr[p\mid b_{i^\star}]
=\frac{w_{i^\star}}{W}\cdot\frac{1}{w_{i^\star}}=\frac{1}{W}, $
与 $p$ 无关，故均匀。

独立性来自：每次采样都重新独立地执行“选块 + 块内采样”，且为有放回抽样（不改变活跃集合）。证毕。

---

### 4.2.8 复杂度与空间

设对侧全集规模上界为 $N$。二维 range tree 的典型代价如下（Treap 带来的对数因子为期望界）：

- **动态更新**（`INSERT/DELETE`）：主树路径长度 $O(\log N)$，每个节点 Treap 更新 $O(\log N)$，故  
  $ T_{\mathrm{upd}}=O(\log^2 N). $

- **计数**（`COUNT(Q)`）：终端块数 $B=O(\log N)$；每块 Treap 计数 $O(\log N)$，故  
  $ T_{\mathrm{count}}=O(\log^2 N). $

- **枚举**（`REPORT(Q)`）：构造块与计数的边界开销 $O(\log^2 N)$；输出规模 $K=|P_S\cap Q|$，故  
  $ T_{\mathrm{report}}=O(\log^2 N+K). $

- **采样**（`SAMPLE(Q,k)`）：构造块并计算权重 $O(\log^2 N)$；每个样本包含选块与块内采样：  
  - 前缀和二分选块：$O(\log B)=O(\log\log N)$；  
  - 块内 Treap 采样：$O(\log N)$；  
  因此  
  $ T_{\mathrm{sample}}=O(\log^2 N + k(\log N+\log\log N)). $  
  若用 alias 表选块，则可写为  
  $ T_{\mathrm{sample}}=O(\log^2 N + k\log N). $

- **空间**：每个点被复制到其主树根到叶路径上的 $O(\log N)$ 个关联 Treap 中，故总空间  
  $ S=O(N\log N). $

---

## 4.3 与扫描事件块原语的对接

本节说明如何在扫描过程中使用二维 range tree 实现事件块 $K_e$ 的三原语，并直接供采样框架调用。

### 4.3.1 两侧索引结构

分别为两类集合维护对侧活跃集的索引：

- $T_c$：维护当前活跃的 $R_c$ 矩形对应的点 $p(r_c)=(L_2(r_c),R_2(r_c))$；
- $T_{\bar c}$：维护当前活跃的 $R_{\bar c}$ 矩形对应的点 $p(r_{\bar c})=(L_2(r_{\bar c}),R_2(r_{\bar c}))$。

两棵树结构相同，只是维护的点全集不同。

---

### 4.3.2 扫描事件处理

按全局事件总序 $\prec$ 处理事件（坐标升序；同坐标 END 先于 START；再以唯一 id 打破平局）：

- 若事件为 $\mathrm{END}(r)$：  
  从其所属集合对应的树中执行 `DELETE(p(r))`。

- 若事件为 $\mathrm{START}(q)$：  
  先对对侧树执行范围查询：
  - 若 $q\in R_c$，对 $T_{\bar c}$ 查询 $Q(q)=(-\infty,R_2(q))\times(L_2(q),+\infty)$；
  - 若 $q\in R_{\bar c}$，对 $T_c$ 查询同一形式的 $Q(q)$。

  因而事件块原语可直接实现为：
  - $\texttt{COUNT}(e)=\texttt{COUNT}(Q(q(e)))$；
  - $\texttt{REPORT}(e)=\texttt{REPORT}(Q(q(e)))$；
  - $\texttt{SAMPLE}(e,k)=\texttt{SAMPLE}(Q(q(e)),k)$。

  对 `REPORT/SAMPLE` 返回的每个 partner id，可按既定的方向映射（输出恒为 $(r_c,r_{\bar c})$）还原为有序对并输出/回填。

  最后将 $q$ 插入其所属集合对应的树中：`INSERT(p(q))`。

---

### 4.3.3 与采样框架的组合与代价

采样框架只依赖事件块原语的语义正确性以及不同阶段随机性相互独立的约定。本章已给出 `SAMPLE` 的均匀性与独立性证明，并且 `COUNT/REPORT` 在严格边界语义下返回精确计数与无重复枚举，因此将本章原语实现替换进任一采样框架后，整体输出仍为 Join 集合上的 i.i.d. 均匀（有放回）。

令 $N$ 为对侧全集规模上界、$M$ 为 START 事件数，则单次事件的主要代价为：

- 更新（插入/删除）：$O(\log^2 N)$；
- 计数：$O(\log^2 N)$；
- 枚举：$O(\log^2 N+w_e)$；
- 采样（$k$ 个）：$O(\log^2 N + k\log N)$（或显式前缀和选块版本为 $O(\log^2 N + k(\log N+\log\log N))$）。

这些代价可直接代入采样框架对原语调用次数的统计，从而得到总复杂度表达式。

# 第 5 章 对照方法二：kd-tree 上的 $4$ 维范围计数与范围采样 Join

本章给出一套独立的 Join 采样对照体系：将二维半开矩形的相交关系提升为四维点集上的正交范围问题，在右侧集合上建立静态 kd-tree，并通过“按度加权选择左端 + 条件范围内均匀采样选择右端”实现对 Join 集合的 **i.i.d. 均匀（有放回）**采样。该路线结构直接、证明链条清晰，适合作为基线方法；其代价与查询维度固定为 $4$ 密切相关。

---

## 5.1 问题定义与 $4$ 维提升

### 5.1.1 输入、半开语义与 Join

给定两类二维轴对齐半开矩形集合
$$
R_c=\{r_1,\dots,r_n\},\qquad R_{\bar c}=\{s_1,\dots,s_m\}.
$$
每个矩形写作
$$
r=[L_1(r),R_1(r))\times [L_2(r),R_2(r)),\qquad L_i(r)<R_i(r)\ (i=1,2).
$$
并假定每个对象携带唯一标识 $\mathrm{id}(\cdot)$（即便几何参数完全相同，也视为不同元素）。

半开语义下，两矩形相交当且仅当在两个维度上都满足严格不等式
$$
r\cap s\neq\varnothing
\iff
\big(L_1(r)<R_1(s)\big)\wedge \big(L_1(s)<R_1(r)\big)\wedge
\big(L_2(r)<R_2(s)\big)\wedge \big(L_2(s)<R_2(r)\big).
$$
严格不等号确保贴边（例如 $R_1(r)=L_1(s)$）不计为相交。

本章研究方向固定的 Join 集合
$$
J=\{(r,s)\mid r\in R_c,\ s\in R_{\bar c},\ r\cap s\neq\varnothing\},
$$
目标是在 $J$ 上输出 $t$ 个样本 $Z_1,\dots,Z_t$，满足
$$
\Pr\{Z_j=P\}=\frac{1}{|J|}\ (\forall P\in J),\qquad Z_1,\dots,Z_t\ \text{相互独立},
$$
即 **i.i.d. 且均匀（有放回）**。若 $|J|=0$，输出空序列。

---

### 5.1.2 右侧矩形 $\rightarrow$ $4$ 维点

对每个右侧矩形 $s\in R_{\bar c}$，构造点
$$
p(s)=\big(L_1(s),L_2(s),R_1(s),R_2(s)\big)\in\mathbb{R}^{4},
$$
得到点集
$$
P=\{p(s)\mid s\in R_{\bar c}\}.
$$
点 $p(s)$ 携带 $\mathrm{id}(s)$，用于在查询后恢复对应右侧矩形。

记提升维度
$$
D:=4.
$$

---

### 5.1.3 左侧矩形 $\rightarrow$ $4$ 维正交范围

对每个左侧矩形 $r\in R_c$，定义四维正交范围
$$
Q(r)=(-\infty,\ R_1(r))\times(-\infty,\ R_2(r))\times(L_1(r),+\infty)\times(L_2(r),+\infty).
$$
所有边界均为开边界（严格不等号），与半开矩形相交判定完全一致。

---

### 5.1.4 等价性与 Join 度数

**引理 5.1（$4$ 维提升等价性）**  
对任意 $r\in R_c$ 与 $s\in R_{\bar c}$，有
$$
p(s)\in Q(r)\iff r\cap s\neq\varnothing.
$$

**证明**  
$p(s)\in Q(r)$ 等价于四个严格约束
$$
L_1(s)<R_1(r),\quad L_2(s)<R_2(r),\quad R_1(s)>L_1(r),\quad R_2(s)>L_2(r).
$$
后两条分别等价于 $L_1(r)<R_1(s)$ 与 $L_2(r)<R_2(s)$，合并即得到半开语义下相交的充要条件。证毕。

据此定义左侧矩形的 Join 度数（连接度）
$$
w(r)=|\{s\in R_{\bar c}\mid r\cap s\neq\varnothing\}|=|P\cap Q(r)|.
$$
并有
$$
|J|=\sum_{r\in R_c} w(r).
$$

---

## 5.2 静态 kd-tree：结构与构建

本节在静态点集 $P\subset\mathbb{R}^{4}$ 上建立平衡 kd-tree，用于支持对任意正交范围 $Q$ 的精确计数 `COUNT(Q)` 与范围内均匀采样 `SAMPLE(Q,k)`。

### 5.2.1 节点信息

每个节点 $v$ 存储：

- `left(v), right(v)`：左右子节点指针；
- `split_dim(v)\in\{0,1,2,3\}` 与 `split_val(v)`：分割维度与阈值；
- `size(v)`：子树点数；
- 子树外包框（闭盒）
  $$
  \mathrm{MBR}(v)=\prod_{j=0}^{3}[\min_j(v),\max_j(v)],
  $$
  其中 $\min_j(v),\max_j(v)$ 分别是子树点在第 $j$ 维的最小/最大坐标。

此外，为支持“当 $\mathrm{MBR}(v)$ 被查询范围完全包含时，以 $O(1)$ 块内均匀抽样一个子树点”，采用如下静态构建约定：

- 构建过程对点数组做 in-place partition；
- 每个节点子树对应数组的一个连续切片 $[l_v,r_v)$；
- 因而 `size(v)=r_v-l_v`，并可在该切片上直接均匀抽取下标实现块内均匀采样。

该约定仅依赖 kd-tree 为静态索引（构建后不再重排点数组），不要求额外存储子树点列表。

---

### 5.2.2 构建方式与代价

设 $m=|P|$。递归构建平衡 kd-tree：

1. 在深度 $h$ 的节点选择 `split_dim = h mod 4`；
2. 在该维以中位数划分，使左右子树规模尽量均衡；
3. 递归直到子树点数 $\le B$（叶子阈值，例如 32 或 64）；
4. 自底向上计算每个节点的 `MBR` 与 `size`，并记录数组切片边界。

构建代价取决于“取中位数并 partition”的实现：

- 若每层使用线性期望的选择算法（例如 `nth_element` 风格）完成中位数划分，则构建期望时间为
  $$
  \mathbb{E}[O(m\log m)].
  $$
- 若每层对当前子数组排序后取中位数，则构建时间为
  $$
  O(m\log^2 m).
  $$

空间方面，kd-tree 节点数为 $O(m)$，每个节点存长度为 $4$ 的 $\min/\max$ 向量与常数指针字段，因此
$$
S_{\mathrm{tree}}=O(4m)=O(m).
$$

---

## 5.3 开边界范围与闭盒 MBR 的关系判定

本章查询范围均来自 $Q(r)$，其每一维区间都是开边界的半无限区间：$(-\infty,b)$ 或 $(a,+\infty)$。为统一描述，本节也允许一般开区间 $(a,b)$。

令查询范围
$$
Q=\prod_{j=0}^{3} I_j,
$$
其中 $I_j$ 为以下三类之一：

- $I_j=(-\infty,b)$（严格 $x<b$）；
- $I_j=(a,+\infty)$（严格 $x>a$）；
- $I_j=(a,b)$（严格 $a<x<b$）。

对节点 $v$ 的闭盒外包框
$$
\mathrm{MBR}(v)=\prod_{j=0}^{3}[\min_j(v),\max_j(v)].
$$

定义三类关系：

- `disjoint(v,Q)`：$\mathrm{MBR}(v)\cap Q=\varnothing$；
- `contained(v,Q)`：$\mathrm{MBR}(v)\subset Q$（注意 $Q$ 是开边界）；
- 其余为 `partial(v,Q)`。

逐维判定规则（严格语义）：

1. 若 $I_j=(-\infty,b)$：
   - 不相交：$\min_j(v)\ge b$；
   - 完全包含：$\max_j(v)<b$。
2. 若 $I_j=(a,+\infty)$：
   - 不相交：$\max_j(v)\le a$；
   - 完全包含：$\min_j(v)>a$。
3. 若 $I_j=(a,b)$：
   - 不相交：$\min_j(v)\ge b$ 或 $\max_j(v)\le a$；
   - 完全包含：$\min_j(v)>a$ 且 $\max_j(v)<b$。

这些不等式确保：当 $\mathrm{MBR}(v)$ 边界恰好等于查询开边界时，不会被误判为完全包含，从而不引入“贴边点”的错误计入。

---

## 5.4 `COUNT(Q)`：精确范围计数

### 5.4.1 递归计数算法

定义递归函数 `COUNT(v,Q)`：

- 若 `disjoint(v,Q)`：返回 $0$；
- 若 `contained(v,Q)`：返回 `size(v)`；
- 若 $v$ 为叶子：逐点检查叶内点 $p$ 是否满足 $p\in Q$（逐维严格比较），返回命中点数；
- 否则返回
  $$
  \texttt{COUNT}(\texttt{left}(v),Q)+\texttt{COUNT}(\texttt{right}(v),Q).
  $$

全树计数为 `COUNT(root,Q)`。

---

### 5.4.2 正确性

**定理 5.2（`COUNT` 的精确性）**  
对任意开边界正交范围 $Q$，`COUNT(root,Q)` 的返回值等于 $|P\cap Q|$。

**证明**  
对任意节点 $v$：

- 若不相交，则 $P(v)\cap Q=\varnothing$，返回 0 正确；
- 若完全包含，则 $P(v)\subset Q$，返回 `size(v)=|P(v)|` 正确；
- 否则递归分解到子节点，或在叶子逐点严格判定并计数。

三分情况覆盖且互斥，递归累加恰好统计所有命中点数，因此总计数为 $|P\cap Q|$。证毕。

---

### 5.4.3 访问代价的常见刻画

令 $m=|P|$，维度为 $4$。在平衡 kd-tree、数据与查询不对抗的常见平均模型下，范围计数访问的节点数经常用
$$
T_{\mathrm{count}}(m)=\tilde O\!\left(m^{1-\frac{1}{4}}\right)=\tilde O\!\left(m^{\frac{3}{4}}\right)
$$
来刻画，其中 $\tilde O(\cdot)$ 隐含多对数因子与叶阈值常数。  
在最坏情况下（例如对抗分布/查询），访问代价仍可能退化到 $O(m)$。

---

## 5.5 `SAMPLE(Q,k)`：范围内 i.i.d. 均匀采样

本节给出在 $P\cap Q$ 上生成 $k$ 个 **i.i.d. 且均匀（有放回）**点样本的过程，并给出完整正确性论证。

### 5.5.1 不交块分解：contained 块与边界叶块

对 kd-tree 做一次遍历，按 5.3 的三类关系处理节点：

- 若某节点 $v$ 满足 `contained(v,Q)`：将 $v$ 作为一个 **contained 块**加入集合 $\mathcal{I}(Q)$，其块权重为
  $$
  w_v:=\texttt{size}(v).
  $$
  该块代表子树点集 $P(v)$ 整体属于 $P\cap Q$。

- 若节点与 $Q$ 不相交：剪枝跳过。

- 若节点与 $Q$ 部分相交：
  - 若非叶子，继续下探；
  - 若为叶子 $\ell$，逐点检查叶内点是否属于 $Q$，将命中点的数组位置收集为索引表 $\mathrm{hit}(\ell)$，并记
    $$
    w_\ell := |\mathrm{hit}(\ell)|.
    $$
    当 $w_\ell>0$ 时，将该叶子作为 **边界叶块**加入集合 $\mathcal{B}(Q)$。

定义总命中数
$$
C(Q)=\sum_{v\in\mathcal{I}(Q)} w_v+\sum_{\ell\in\mathcal{B}(Q)} w_\ell.
$$
若 $C(Q)=0$，返回空序列。

---

### 5.5.2 分解正确性

**引理 5.3（块分解覆盖且无重）**  
由上述遍历得到的块集合满足不交并分解
$$
P\cap Q
=
\biguplus_{v\in\mathcal{I}(Q)} P(v)
\ \uplus\
\biguplus_{\ell\in\mathcal{B}(Q)} \big(P(\ell)\cap Q\big),
$$
其中 $P(v)$ 为节点 $v$ 子树点集，$P(\ell)$ 为叶子 $\ell$ 内点集。

**证明**  

- 不交性：遍历遇到 `contained` 节点即停止下探，因此 $\mathcal{I}(Q)$ 中不会出现祖先与后代同时入块，不同 contained 块对应的子树点集两两不交。叶子之间点集天然不交。边界叶块来自“未被任何 contained 祖先截断”的路径末端，因此边界叶不可能位于任一 contained 块的子树内，从而两类块之间也不重叠。

- 覆盖性：任取 $p\in P\cap Q$。沿根到叶的路径下行：若途中出现第一个满足 `contained` 的节点 $v$，则 $p\in P(v)$ 并被该块覆盖；否则路径最终到达某个部分相交叶子 $\ell$，且逐点筛选会把 $p$ 纳入 $P(\ell)\cap Q$。因此所有命中点均被覆盖。

综上得到不交并分解。证毕。

由引理 5.3 立刻得到 $C(Q)=|P\cap Q|$。

---

### 5.5.3 两阶段采样：按块权重 + 块内均匀

对块集合 $\mathcal{I}(Q)\cup\mathcal{B}(Q)$ 建立按权重的离散分布，使一次选块满足
$$
\Pr\{\text{选中块 }b\}=\frac{w_b}{C(Q)}.
$$

块内均匀抽样：

- 若选中 contained 块 $v\in\mathcal{I}(Q)$：在其数组切片 $[l_v,r_v)$ 上均匀抽取
  $$
  u\sim \mathrm{Unif}\{l_v,\dots,r_v-1\},
  $$
  返回点数组第 $u$ 个点的 $\mathrm{id}$。

- 若选中边界叶块 $\ell\in\mathcal{B}(Q)$：在命中索引表 $\mathrm{hit}(\ell)$ 上均匀抽取一个位置并返回相应点的 $\mathrm{id}$。

重复 $k$ 次，且每次使用独立随机性，即得到 $k$ 个样本。

---

### 5.5.4 正确性：均匀与 i.i.d.

**定理 5.4（`SAMPLE(Q,k)` 的正确性）**  
当 $C(Q)>0$ 时，上述 `SAMPLE(Q,k)` 生成的 $k$ 个点样本在 $P\cap Q$ 上 **均匀且相互独立（有放回）**。

**证明**  
由引理 5.3，$P\cap Q$ 为块集合的不交并。任取 $p\in P\cap Q$，它属于唯一块 $b(p)$，且该块大小为 $w_{b(p)}$。一次采样命中 $p$ 的概率为
$$
\Pr[p]
=
\Pr[b(p)]\cdot \Pr[p\mid b(p)]
=
\frac{w_{b(p)}}{C(Q)}\cdot \frac{1}{w_{b(p)}}
=
\frac{1}{C(Q)},
$$
与 $p$ 无关，故单次输出在 $P\cap Q$ 上均匀。每次采样独立重做“选块 + 块内抽样”，且为有放回抽样，因此 $k$ 次输出相互独立且同分布。证毕。

---

### 5.5.5 代价：遍历 + 选块 + 块内抽样

记本次查询产生的块数为
$$
B_Q:=|\mathcal{I}(Q)|+|\mathcal{B}(Q)|.
$$
一次 `SAMPLE(Q,k)` 的代价由两部分构成：

1. 遍历 kd-tree，形成块集合并计算权重（与 `COUNT(Q)` 同阶，并在边界叶做逐点筛选）；
2. 生成 $k$ 个样本：每个样本包含一次“按权重选块”与一次块内均匀抽样。

按权重选块可用两种常见实现：

- 前缀和 + 二分：预处理 $O(B_Q)$，每次选块 $O(\log B_Q)$；
- alias：预处理 $O(B_Q)$，每次选块期望 $O(1)$。

因此可写成两种表达：

- 前缀和版本：
  $$
  T_{\mathrm{sample}}=\tilde O\!\left(T_{\mathrm{visit}} + B_Q + k\log B_Q\right),
  $$
- alias 版本：
  $$
  T_{\mathrm{sample}}=\tilde O\!\left(T_{\mathrm{visit}} + B_Q + k\right),
  $$
  其中 $T_{\mathrm{visit}}$ 为遍历访问节点的代价。固定维度 $4$ 的常见平均模型下，$T_{\mathrm{visit}}$ 常用 $\tilde O(m^{3/4})$ 刻画；最坏情况下仍可能退化到 $O(m)$。

---

## 5.6 基于 kd-tree 的 Join 采样器

本节将 5.1 的提升与 5.4–5.5 的 `COUNT/SAMPLE` 组合，得到 $J$ 上的 i.i.d. 均匀（有放回）采样器。

### 5.6.1 预处理：构建 kd-tree 与计算度数

令
$$
n:=|R_c|,\qquad m:=|R_{\bar c}|.
$$
步骤如下：

1. 构造点集 $P=\{p(s)\mid s\in R_{\bar c}\}\subset\mathbb{R}^{4}$；
2. 在 $P$ 上构建 kd-tree 索引 $T$；
3. 对每个 $r\in R_c$，构造范围 $Q(r)$ 并计算
   $$
   w(r)\leftarrow \texttt{COUNT}(T,Q(r))=|P\cap Q(r)|.
   $$
4. 计算
   $$
   W:=\sum_{r\in R_c} w(r)=|J|.
   $$
   若 $W=0$，输出空序列并终止；
5. 在集合 $\{r\in R_c\mid w(r)>0\}$ 上建立离散采样结构，使一次抽取满足
   $$
   \Pr\{r\}=\frac{w(r)}{W}.
   $$

---

### 5.6.2 采样：生成 $t$ 个 Join 样本

对 $j=1,\dots,t$：

1. 按 $\Pr\{r\}=w(r)/W$ 抽取左端矩形 $r\in R_c$；
2. 调用 `SAMPLE(T,Q(r),1)` 得到一个在 $P\cap Q(r)$ 上均匀的点 id，并据此恢复对应右侧矩形 $s\in R_{\bar c}$；
3. 输出 $Z_j=(r,s)$。

为降低重复查询开销，可使用批量化生成：先独立抽取 $t$ 次左端矩形得到序列 $r^{(1)},\dots,r^{(t)}$，统计每个左端 $r$ 被抽到的次数 $k_r$，然后对每个 $k_r>0$ 的 $r$ 只调用一次 `SAMPLE(T,Q(r),k_r)`，再按位置写回输出。该批量化操作不改变分布。

---

### 5.6.3 伪代码

~~~text
Algorithm 5.1  KD-Tree Join Sampler (2D rectangles, 4D lifting)
Input : R_c, R_{\bar c}, sample size t
Output: Z_1..Z_t (i.i.d. uniform on J, with replacement)

1:  P ← { p(s) : s ∈ R_{\bar c} }, where p(s) = (L_1(s), L_2(s), R_1(s), R_2(s))
2:  Build a balanced kd-tree T on P   // static, with subtree array slices
3:  For each r ∈ R_c:
4:      Q(r) ← (-∞, R_1(r)) × (-∞, R_2(r)) × (L_1(r), +∞) × (L_2(r), +∞)
5:      w(r) ← COUNT(T, Q(r))
6:  W ← Σ_{r∈R_c} w(r)
7:  If W = 0: return empty sequence
8:  Build a discrete sampler over {r ∈ R_c : w(r) > 0} with Pr[r] = w(r)/W

9:  For j = 1..t:
10:     Draw r ~ Pr[r] = w(r)/W
11:     Draw s_id ← SAMPLE(T, Q(r), 1)   // uniform in P ∩ Q(r)
12:     Let s be the rectangle in R_{\bar c} with id = s_id
13:     Z_j ← (r, s)
14: return Z_1..Z_t
~~~

---

## 5.7 正确性：$J$ 上的均匀性与 i.i.d.

### 5.7.1 单次均匀性

**定理 5.5（Join 采样的均匀性）**  
当 $W=|J|>0$ 时，算法 5.1 的单次输出 $Z$ 在 $J$ 上均匀：
$$
\Pr\{Z=(r,s)\}=\frac{1}{|J|},\qquad \forall (r,s)\in J.
$$

**证明**  
任取 $(r,s)\in J$。由引理 5.1，$p(s)\in Q(r)$，故 $w(r)=|P\cap Q(r)|>0$。算法输出 $(r,s)$ 的概率为
$$
\Pr\{(r,s)\}
=
\Pr\{r\}\cdot \Pr\{s\mid r\}
=
\frac{w(r)}{W}\cdot \frac{1}{w(r)}
=
\frac{1}{W}
=
\frac{1}{|J|},
$$
其中 $\Pr\{s\mid r\}=1/w(r)$ 由 `SAMPLE(T,Q(r),1)` 在 $P\cap Q(r)$ 上均匀得到。证毕。

---

### 5.7.2 多次输出的独立同分布

**定理 5.6（Join 采样的 i.i.d.）**  
若每次左端抽取与每次 `SAMPLE` 调用都使用独立随机性，且 `SAMPLE` 为有放回采样，则算法 5.1 输出的 $Z_1,\dots,Z_t$ 相互独立且同分布为 $J$ 上的均匀分布。

**证明**  
由定理 5.5，每个 $Z_j$ 的边缘分布为 $J$ 上均匀。不同轮次的随机选择相互独立，且每轮采样均不改变后续轮次的候选集合（有放回）。因此 $Z_1,\dots,Z_t$ 相互独立且同分布。证毕。

---

## 5.8 复杂度与维度效应（$4$ 维）

### 5.8.1 预处理

- kd-tree 构建：期望 $O(m\log m)$（使用线性期望选择算法取中位数），或 $O(m\log^2 m)$（每层排序取中位数）。
- 度数计算：对每个 $r\in R_c$ 调用一次 `COUNT(Q(r))`。在固定维度 $4$ 的常见平均模型下，单次计数常用
  $$
  \tilde O\!\left(m^{1-\frac{1}{4}}\right)=\tilde O\!\left(m^{\frac{3}{4}}\right)
  $$
  刻画，因此预处理总计常写作
  $$
  \tilde O\!\left(n\cdot m^{\frac{3}{4}}\right).
  $$
  在最坏情况下该部分可能退化到 $O(nm)$。
- 左端加权采样结构：$O(n)$ 级别（alias 或前缀和）。

---

### 5.8.2 采样阶段

朴素逐样本方式：每个样本调用一次 `SAMPLE(Q(r),1)`，其主要代价是一次范围遍历与块构造。在固定维度 $4$ 的常见平均模型下常写作
$$
\tilde O\!\left(m^{\frac{3}{4}}\right),
$$
因此总采样代价近似
$$
\tilde O\!\left(t\cdot m^{\frac{3}{4}}\right),
$$
最坏情况可到 $O(tm)$。

批量化方式：若将 $t$ 次左端抽样后分组，对每个出现过的左端 $r$ 只调用一次 `SAMPLE(Q(r),k_r)`，则总遍历次数等于不同左端的个数。设其为 $n_{\mathrm{dist}}$，则代价形态可写为
$$
\tilde O\!\left(n_{\mathrm{dist}}\cdot m^{\frac{3}{4}} + t\right),
$$
其中 $t$ 来自回填写入与块内采样的线性部分。

---

### 5.8.3 空间

kd-tree 节点存 MBR（长度 $4$ 的 $\min/\max$）与常数结构字段，空间为
$$
S=O(m).
$$
此外需要存储左侧度数数组与加权采样结构 $O(n)$。总空间量级
$$
S_{\mathrm{total}}=O(m)+O(n).
$$

---

### 5.8.4 维度效应

本方法把二维相交提升为四维正交范围查询。固定维度为 $4$ 时，kd-tree 的范围计数/范围采样在常见平均模型下通常呈现 $\tilde O(m^{3/4})$ 量级的访问开销；与二维索引相比，这一开销明显更重，但仍保持为次线性形态。该特征使得本方法非常适合作为结构化分解方法之外的一条“直接提升 + 通用空间索引”对照基线。