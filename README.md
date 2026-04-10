# Store Sales 时间序列预测项目

## 项目目标
本项目基于 Kaggle 的 **Store Sales - Time Series Forecasting** 比赛，尝试使用时间序列机器学习方法预测不同门店、不同商品家族的未来销量。

## 当前阶段
当前为第 1 周阶段成果，已完成：

- 数据理解与初始清洗
- baseline 建模
- lag 特征实验
- holiday 特征实验
- safe rolling 特征实验
- 当前主版本决策

## 使用数据
主要使用以下数据文件：

- `train.csv`
- `test.csv`
- `stores.csv`
- `oil.csv`
- `holidays_events.csv`

当前版本未直接使用：

- `transactions.csv`

## 验证方式
由于这是时间序列问题，不能使用随机 KFold。当前统一采用：

- 仅使用最近 365 天数据
- 训练集最后 16 天作为验证集

## 实验结果

### 1. baseline
**特征：**
- stores
- oil
- 基础日期特征

**模型：**
- CatBoostRegressor

**分数：**
- Validation RMSLE = 0.686549

---

### 2. short lag 实验
**特征：**
- baseline 特征
- lag_7
- lag_14

**分数：**
- Validation RMSLE = 0.444306

**说明：**
- 结果很强，但可能偏乐观，因为 short lag 可能使用验证窗口内部真实销量

---

### 3. safe lag 实验（当前最佳）
**特征：**
- baseline 特征
- lag_16
- lag_21
- lag_28

**分数：**
- Validation RMSLE = 0.447398

**说明：**
- 当前最可信、最推荐保留的版本
- 在更真实的验证设定下，lag 依然显著优于 baseline

---

### 4. holiday 实验
**特征：**
- safe lag 特征
- is_holiday
- holiday_count

**分数：**
- Validation RMSLE = 0.460751

**说明：**
- 粗粒度 holiday 特征未优于 safe lag 版本
- 当前不保留为主版本

---

### 5. safe rolling_mean_7 实验
**特征：**
- safe lag 特征
- rolling_mean_7_from_shift16

**分数：**
- Validation RMSLE = 0.450380

**说明：**
- rolling 特征采用相对安全的构造方式：按 `store_nbr + family` 分组，按 `date` 排序，对 `sales` 先 `shift(16)`，再做 `rolling(7).mean()`
- 该构造方式尽量避免直接读取验证窗口内部真实销量
- 结果未优于 safe lag 主版本，因此当前不保留为主版本

## 当前最优版本
当前最佳模型为：

- `safe_lag_16_21_28_catboost_recent365`
- 使用 `lag_16`、`lag_21`、`lag_28`
- Validation RMSLE = `0.447398`

## 本周学到的内容
- 时间序列验证不能随机打乱
- lag 特征对销量预测非常重要
- short lag 可能带来偏乐观验证结果
- holiday 特征不是天然有效，特征设计质量很重要
- rolling 特征也需要先检查是否存在 leakage 风险
- 分数更低不一定更可信，当前阶段先保验证可信，再追求进一步提升

## 当前结论
- 当前主版本仍为 **safe lag（lag_16 / lag_21 / lag_28）**
- `safe rolling_mean_7_from_shift16` 在当前设定下没有提升，因此不保留为主版本
- 当前项目优先保持验证方式稳定，再继续做下一步特征工程

## 下周计划
- 继续围绕当前可信验证方式推进特征工程
- 评估是否尝试新的 safe rolling 变体或其他更有针对性的时间序列特征
- 继续完善实验记录与 GitHub 周存档
