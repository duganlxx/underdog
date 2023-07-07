# 清算明细报表 SETTLEMENT

模块：product 产品、account 账户、unit 单元、investMgr 投资经理、investProduct 投资产品

业务类型：balance 资产信息、position 持仓信息、transit 划拨信息

数据口径：raw 原始数据、pending 内部清算、checking 结算单、confirm 复核后

在数据库的命名上，采用了 `ads_[模块]_[业务类型]_[数据口径]`的规则

在模块上，产品其三种满足勾稽关系`Product:Account = 1:n`和`Account:Unit = 1:n`

不同的业务类型含有不同的字段讯息：

balance{`auCode` 资金账号、`auName` 资金账号名称、`tradeDate` 交易日、`currency` 币种、`totalAsset` 总资产、`totalAssetInitial` 日初总资产、`equity` 持仓市值、`equityInitial` 日初持仓市值、`equityInTransit` 在途市值、`fundInitial` 日初资金、`fundAvailable` 可用资金、`fundDepositWithdraw` 净出入金、`fundDeposit` 资金转入、`fundWithdraw` 资金转出、`totalLiability` 总负债、`cashDebt` 资金负债、`securityDebt` 证劵负债、`totalLiabilityInitial` 日初总负债、`cashDebtInitial` 日初资金负债、`securityDebtInitial` 日初证劵负债、`fundInTransit` 在途资金、`fundFrozen` 冻结资金、`balance` 资金余额、`commission` 手续费、`equityDeposit` 证劵转入、`equityWithdraw` 证劵转出、`type` 账户类型、`netEquityTraded` 净买入证劵市值、`equityBuy` 买入证劵市值、`equitySell` 卖出证劵市值}

position{`auCode` 资产单元、`symbol` 证劵代码、`secCnName` 证劵名称、`side` 买卖方向、`availableVolume` 总持仓、`totalVolume` 可用数、`todayOpen` 今日买入数量、`preVolume` 昨日拥股数、`frozenVolume` 卖冻结、`marketValue` 市值、`mtm` 盯市价格、`isSettled` 是否已结算、`createTime` 创建时间、`updateTime` 更新时间、`tradeDate` 交易日}

transit{`id` 交易流水号、`auCode` 资产单元、`symbol` 证劵代码、`transitSide` 转入转出方向(0: deposit, 1: withdraw)、`side` 交易方向(0: buy, 1: sell)、`price` 成本价、`volume` 交易数量、`fee` 交易费用、`remark` 交易说明、`occurTime` 发生时间(13 位时间戳)、`occurDate` 发生日期(例 20200701)、`settleDate` 交割日期(13 位时间戳, 表示在当日开盘前 资金到帐)}

```
盈亏 = 持仓盈亏 + 平仓盈亏
平仓盈亏 = 卖出市值 - 买入市值
```
