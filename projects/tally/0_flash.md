# 头脑风暴

## 预想中的功能

1. 类似Python 的 Bean 的记账模式，文件后缀名：.ot
2. 选择本地文件夹，作为主账本
3. 左侧导航栏
   1. 主界面: 设定周期内的交易统计、账户变更情况[周期前余额、周期内余额]、饼图（消费占比）、交易明细（今日交易）
   2. 搜索栏：搜索指定账户
   3. 账本：
      1. 金钱账户
      2. 收入账户
      3. 贷款账户
      4. 消费账户
      5. 平账账户
      6. 存款账户
   4. 明细：选择视图 年、月、周、日
   5. 设置：
   6. 帮助：显示快捷键图

## 设计方案

1. .ot 文件存储结构

```cmd {.line-numbers}

tally
|--accounts
   |--asset.ot
   |--income.ot
   |--loan.ot
   |--expense.ot
   |--saving.ot
   |--balance.ot
|--transactions
   |--yyyy
      |--yyyy_mm.ot
|--cache
   |--
```
