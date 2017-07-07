# LESSON 7 Scheduling Functions

## Trading Calendars
飛ばします、先物が出てきたら解説します。

## Scheduling Functions
これまではアルゴリズムを実行するタイミングとして `handle_data()` を用いてきました。これは毎分実行されますが、毎分トレードしていたら手数料で簡単に破産しそうです。

当然の流れとして、日次や月次で処理したい場合があります。`schedule_function()` 関数は指定した頻度でアルゴリズムをスケジュールできます。

位置|キーワード引数|値
---|---|---
1|func|実行する関数名
2|date_rules|日単位のルール
3|time_rules|時間単位のルール

下記のコードは `rebalance()` 関数を毎日、取引開始から1時間後[^1]に実行しています。

```python
schedule_function(func=rebalance,
                  date_rules=date_rules.every_day(),
                  time_rules=time_rules.market_open(hours=1))
```

またしても、`date_rules` や `time_rules` というインスタンスがなにも定義していないのにいきなり出てきましたね・・・これもQuantopianの独自ルールのようです。

`date_rules` と `time_rules` オブジェクトは下記のメソットが用意されています。

date_rules
* date_rules.every_day()
* date_rules.week_start(days_offset=0)
* date_rules.week_end(days_offset=0)
* date_rules.month_start(days_offset=0)
* date_rules.month_end(days_offset=0)

time_rules
* time_rules.market_open(hours=0, minutes=1)
* time_rules.market_close(hours=0, minutes=1)

下記のコードは `weekly_trades()` 関数を毎週末の取引終了30分前に実行しています。

```python
schedule_function(weekly_trades, date_rules.week_end(), time_rules.market_close(minutes=30))
```

下記のコードは週の始めにSPYをポートフォリオの10%分ロングし、週末の取引終了30分前にポジションを閉じています。
コードは [こちら](https://www.quantopian.com/tutorials/getting-started#lesson7) からクローンできます。

```python
def initialize(context):
    context.spy = sid(8554)

    schedule_function(open_positions, date_rules.week_start(), time_rules.market_open())
    schedule_function(close_positions, date_rules.week_end(), time_rules.market_close(minutes=30))

def open_positions(context, data):
    order_target_percent(context.spy, 0.10)

def close_positions(context, data):
    order_target_percent(context.spy, 0)
```

`schedule_function()` は市場がクローズしている場合はスキップします。また、半日取引の場合は `half_days=False` を設定することでスキップすることもできます。

金融データを扱う場合には休日の扱いが大変面倒なので、この辺はありがたいですね。

[LESSON 6](./LESSON6.md) <--> [LESSON 8](LESSON8.md)

---
[^1]: `time_rules.market_open()` は通常9:30(ET:東部標準時)を返します。
