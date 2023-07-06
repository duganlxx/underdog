# 清算明细报表 SETTLEMENT





模块：product 产品、account 账户、unit 单元、investMgr 投资经理、investProduct 投资产品

业务类型：balance 资产信息、position 持仓信息、transit 划拨信息

数据口径：raw 原始数据、pending 内部清算、checking 结算单、confirm 复核后

在数据库的命名上，采用了 `ads_[模块]_[业务类型]_[数据口径]`的规则



在模块上，产品其三种满足勾稽关系`Product:Account = 1:n`和`Account:Unit = 1:n`

在业务类型上，balance包含字段有 {auCode 资金账号、auName 资金账号名称、tradeDate 交易日、currency 币种、totalAsset 总资产、totalAssetInitial 日初总资产、equity 持仓市值、equityInitial 日初持仓市值、equityInTransit 在途市值、fundInitial 日初资金、fundAvailable 可用资金、fundDepositWithdraw 净出入金、fundDeposit 资金转入、fundWithdraw 资金转出、totalLiability 总负债、cashDebt 资金负债、securityDebt 证劵负债、totalLiabilityInitial 日初总负债、cashDebtInitial 日初资金负债、securityDebtInitial 日初证劵负债、fundInTransit 在途资金、fundFrozen 冻结资金、balance 资金余额、commission 手续费、equityDeposit 证劵转入、equityWithdraw 证劵转出、type 账户类型、netEquityTraded 净买入证劵市值、equityBuy 买入证劵市值、equitySell 卖出证劵市值}



**网络通讯**

全部数据均通过数据集市的接口去ClickHouse中获取得到。基础封装的`GetSql()`函数返回的数据格式是矩阵数据，通过工具方法`transform2Object<T>()`方法，可以将返回的数据转换类型`T`的数组数据。通过这样的方式基本上可以很舒适地使用数据。

在结算中，存在了各种不同数据口径、不同业务类型、不同模块的数据，按照排列组合计算一共有3x3x4=36张表，所以需要进行进一步封装。按照

> references
>
> - `transform2Object<T>()`：具体的签名为`transform2Objects<T>(data: Record<string, any[]>, opt?: TransformOption): T[]`，所在位置为`/src/services/eam-api/datamarket/transfer_data.ts`。
> - 



**整体结构**

```txt
src/pages/operation/settlement/detail
|- components	# 通用组件
	|- assetOutlineView.tsx	# 资产概要组件
	|- detailInfoET.tsx		# 证劵划转数据展示组件
	|- detailInfoFT.tsx		# 资金划转数据展示组件
	|- detailInfoPosition.tsx # 
	|- detailInfoTrade.tsx
	|- positionFlows.tsx
	|- summaryIndexChart.tsx
	|- summaryIndexView.tsx
|- settleDetailByManager	# 按投资经理
	|- components
		|- assetFlows.tsx
		|- assetOutline.tsx
		|- detailInfo.tsx
		|- managerSearch.tsx
		|- productFlows.tsx
	|- index.tsx	# 按投资经理的入口文件
|- settleDetailByProduct	# 按产品
	|- components
		|- assetCompare.tsx
		|- assetFlows.tsx		# 资产概要
		|- balanceS2Diff.tsx
		|- detailInfo.tsx
		|- diffHeatmapView.tsx
		|- positionCompare.tsx
		|- positionCompare2.tsx
		|- positionS2Diff.tsx
		|- productAsset.tsx		# 产品资产组件
		|- productFlows.tsx		# 产品流水信息组件
		|- productSearch.tsx
		|- unitDetail.tsx
	|- index.less	# 样式文件
	|- index.tsx	# 按产品的入口文件，其中分为ProductAsset 和 ProductFlows两个组件
|- common.ts	# 通用函数
|- index.less	# 样式文件
|- index.tsx	# 结算明细报表的入口
|- model.ts		# 存放全局变量

```



**model.ts**

将一些需要在多处使用的变量放入其中，类似于放入到上下文中，其他页面要使用的时候可以非常方便的取得数据，而不需要利用`props` 一层一层的传递。其他页面只需要`useModel()`即可取得。

> references
>
> - [Umi Max 数据流](https://umijs.org/docs/max/data-flow)



**xxx.tsx**

.tsx 文件是TypeScript JSX 文件的扩展名。 JSX 是一种JavaScript 的语法扩展，可以在JavaScript 代码中写入HTML。TypeScript 是一种用于开发JavaScript 应用程序的类型检查语言，它可以让JavaScript 具有更强的类型检查能力。

其中编写有如下规范

```tsx

type ProductFlowsProps = {
    // ...
}

const ProductFlows: React.FC<ProductFlowsProps> = (props) => {
    // props中取数
    // useModal()
    // useState()
    // useEffect()
    
    // 一些配置变量，非纯函数式函数
}
```



**useEmotionCss()**

`useEmotionCss()`函数让我们可以在TS中编写CSS样式，跟先前将样式写到.less文件相比，该函数让我们可以更加灵活的去更改样式，最典型的应用场景就是切换主题色。

> references
>
> - [Emotion - Introduction](https://emotion.sh/docs/introduction)



**开发原则**

- 尽可能封装函数格式。
- 尽量抽离组件
- 一定要判断清楚事情的优先级。


