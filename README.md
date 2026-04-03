# Store Sales - Time Series Forecasting 项目

## 项目目标
本项目基于 Kaggle 的 Store Sales - Time Series Forecasting 比赛，尝试使用时间序列机器学习方法预测不同门店、不同商品家族的未来销量。

## 当前阶段
当前为第 1 周阶段成果，已完成：
1. 数据理解与初始清洗
2. baseline 建模
3. lag 特征实验
4. holiday 特征实验

## 使用数据
主要使用以下数据文件：
- train.csv
- test.csv
- stores.csv
- oil.csv
- holidays_events.csv

当前版本未直接使用：
- transactions.csv

## 验证方式
由于这是时间序列问题，不能使用随机 KFold。
当前统一采用：
- 仅使用最近 365 天数据
- 训练集最后 16 天作为验证集

## 实验结果

### 1. baseline
特征：
- stores
- oil
- 基础日期特征

模型：
- CatBoostRegressor

分数：
- Validation RMSLE = 0.686549

---

### 2. short lag 实验
特征：
- baseline 特征
- lag_7
- lag_14

分数：
- Validation RMSLE = 0.444306

说明：
- 结果很强，但可能偏乐观，因为短 lag 会使用验证窗口内部真实销量

---

### 3. safe lag 实验（当前最佳）
特征：
- baseline 特征
- lag_16
- lag_21
- lag_28

分数：
- Validation RMSLE = 0.447398

说明：
- 当前最可信、最推荐保留的版本
- 在更真实的验证设定下，lag 依然显著优于 baseline

---

### 4. holiday 实验
特征：
- safe lag 特征
- is_holiday
- holiday_count

分数：
- Validation RMSLE = 0.460751

说明：
- 粗粒度 holiday 特征未优于 safe lag 版本
- 当前不保留为主版本

## 当前最优版本
当前最佳模型为：
- safe lag 版本
- 使用 lag_16、lag_21、lag_28
- Validation RMSLE = 0.447398

## 本周学到的内容
1. 时间序列验证不能随机打乱
2. lag 特征对销量预测非常重要
3. 短 lag 可能带来偏乐观验证结果
4. holiday 特征不是天然有效，特征设计质量很重要

## 下周计划
1. 尝试 rolling 特征
2. 优化 holiday 特征，结合地区和门店信息
3. 进一步完善时间序列验证方式
