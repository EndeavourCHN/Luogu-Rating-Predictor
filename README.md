# Luogu-Rating-Predictor

一个洛谷等级分预测小网页。旨在通过统计用户的做题情况，预测其洛谷等级分。

**提醒**：预测结果仅供参考，请勿过度解度。

**目前本项目所使用的预测公式相当粗糙，后续会补充训练集优化拟合结果。**

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

3. 打开 `index.html` ，选择刚刚下载的`uid.json`文件，预测结果即刻呈现。

## 贡献

若有建议与想法，欢迎提交 Pull Request 或是创建 Issues！
