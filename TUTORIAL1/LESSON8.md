# LESSON 8 Your Portfolio and Plotting Variables

## context.portfolio
これまでグローバル変数の受け皿みたいに用いていた `context` オブジェクトですが、配下に `portfolio` オブジェクトが用意されています。このオブジェクトにはポートフォリオに関するさまざまな情報(プロパティ)が格納されています。

プロパティ|内容
---|---
capital_used|取引によって消費された現金
cash|ポートフォリオに残っている現金
pnl|実現損益と含み損益
positions|セキュリティIDをキーとしたすべての未決済のポジション
portfolio_value|現金と未決済ポジションの合計
positions_value|未決済ポジションの合計
returns|ポジションの累積損益(10%のリターンは0.1)
starting_cash|初期資本
start_date|開始日時(UTC)、ライブトレードの場合はアルゴリズムを実行した日時

下記のコードは所有しているポジションをすべて決済しています。

```python
for security in context.portfolio.positions:
    order_target_percent(security, 0)
```

## Plotting Variables
`record()` 関数を用いることでユーザ自身が記録したいデータをグラフ化することができます。記録(プロット)可能なデータの種類は5個までです。

下記のコードでは、AAPLのロングとSPYのショートをそれぞれ50%ずつ毎日の寄りで執行し、毎日の引けで `recode()` 関数を用いて使用した現金と残った現金を記録しています。[^1]

```python
def initialize(context):
    context.aapl = sid(24)
    context.spy = sid(8554)

    schedule_function(rebalance, date_rules.every_day(), time_rules.market_open())
    schedule_function(record_vars, date_rules.every_day(), time_rules.market_close())

def rebalance(context, data):
    order_target_percent(context.aapl, 0.50)
    order_target_percent(context.spy, -0.50)

def record_vars(context, data):
    record(capital_used=context.portfolio.capital_used,
           cash=context.portfolio.cash)
```

![image.png](https://qiita-image-store.s3.amazonaws.com/0/22023/feb25448-3413-d90a-d761-c858f00f6500.png)

[^1]: [元のチュートリアルのコード](https://www.quantopian.com/tutorials/getting-started#lesson8)を変更しています。

[LESSON 7](./LESSON7.md) <--> [LESSON 9](./LESSON9.md)
