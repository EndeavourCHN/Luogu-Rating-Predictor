# Luogu-Rating-Predictor

**本项目受 Codeforces rating 预测项目 https://github.com/Patchouli-7/Patchouli-7.github.io 启发，借用了其预测公式。**

一个洛谷 -> Codeforces Rating预测小网页。旨在通过统计用户在洛谷的做题情况，预测其 Codeforces Rating 。

## 预测思路

1. **数据来源**
   - 从用户的 `user.json` 文件中读取 `passedProblems`。
   - 每道题包含难度等级 `difficulty`（1~7）。

2. **难度与 Codeforces Rating 对应关系**

   | 洛谷难度等级 | 名称            | 对应 CF Rating |
   |-------------|-----------------|---------------|
   | 1           | 入门            | 800            |
   | 2           | 普及−           | 1000           |
   | 3           | 普及/提高−      | 1400           |
   | 4           | 普及+/提高      | 1800           |
   | 5           | 提高+/省选−     | 2200           |
   | 6           | 省选/NOI−       | 2700           |
   | 7           | NOI/NOI+/CTSC   | 3200           |

   相关数据来源：https://codeforces.com/blog/entry/131266

   **提醒**：由于难度映射较为粗糙，后续预测时倾向于保守，预测结果也仅供参考，请勿过度解读！

1. **逐题更新 Rating**
   - 从初始值 800 开始，按模拟做题顺序（由易到难）依次更新预测 rating。
   - 每道题的贡献与题目难度、当前预测值和递减效应相关。

## 原理
### 2.1 放大因子 φ
```math
\phi = 1 + k \cdot \left(\frac{i}{N}\right)^p
````

* $i$ = 当前做题数
* $N$ = 总做题数
* $k, p$ = 固定参数
* 随着做题进度，题目难度的权重逐渐放大。

### 2.2 题目映射难度

```math
mapped = 800 + (rating - 800) \cdot \phi
```

### 2.3 边际收益递减

```math
base_w = i^{\alpha} - (i-1)^{\alpha}
```

* $α$ 控制递减速度
* $i$ 越大，单题收益越小。

### 2.4 Sigmoid 缩放

```math
scale = \frac{1}{1 + e^{-\Delta / L}}
```

* $Δ$ = $mapped$ - 当前 $rating (R)$
* $L$ 控制缩放平滑度
* 防止单道高难题导致 $rating$ 暴涨。

### 2.5 Elo 期望

```math
expected = \frac{1}{1 + 10^{\Delta / 400}}
```

* $Δ$ 越大 → 期望越低 → 解出高难题收益越大。

### 2.6 更新公式

```math
R \;+=\; K \cdot (1 - expected) \cdot base_w \cdot scale
```

## 关键参数与作用

| 参数      | 设定值  | 作用                | 影响                 |
| --------- | ------ | ------------------- | ------------------- |
| `K_FIXED` | 230    | 每道题的最大贡献系数。| 与收益整体正相关     |
| `L_FIXED` | 700    | 控制高难题收益缩放。  | 与单题涨分负相关。   |
| `ALPHA`   | 0.4625 | 控制递减效应。       | 与边际收益下降正相关。|
| `k`       | 0.65   | 放大因子强度。       | 与难度收益正相关。    |
| `p_exp`   | 3.5    | 放大因子指数。       | 与难度收益正相关。    |

**说明**：由于难度映射较为粗糙，这里的单题收益和难题收益相比参考的模型有所收紧，使获得的预测更保守。

## 使用方法

1. 打开洛谷个人主页，网址形式应当如下：

   ```
   https://www.luogu.com.cn/user/uid
   ```

   其中 `uid` 为用户编号。在最后加上 `?_contentOnly=1` ，回车，即访问如下网址：

   ```
   https://www.luogu.com.cn/user/uid?_contentOnly=1
   ```

2. 右键保存该页面，得到 `uid.json` 文件。

3. 打开 `index.html` 或访问 https://endeavourchn.github.io/Luogu-Rating-Predictor/ (GitHub静态托管访问较慢，请视情况选择)，选择刚刚下载的`uid.json`文件，预测结果即刻呈现。

## 贡献

若有建议与想法，欢迎提交 Pull Request 或是创建 Issues！
