# LESSON 11 Putting It All Together
これまでに習ったことを総動員して、単純な [mean reversion 戦略](http://www.investopedia.com/terms/m/meanreversion.asp) [^1] を実装してみます。

## Planning the Strategy
まずは戦略のプランを立ててみます。10日間の単純移動平均(以降SMA)が30日間のSMAより高い場合は株価が下落し、逆の場合は株価が上昇するという仮設を立てます。これは平均回帰の性質を利用しています。

## Selecting Assets to Trade
この種の戦略は通常、[pipeline](https://www.quantopian.com/help#pipeline-title) を用いて銘柄を設定しますが、これまでに習った `sid()` を使用します。
下記のコードでは5つの銘柄を設定しています。[^2]

```python
# MSFT, UNH, CTAS, JNS, COG
context.security_list = [sid(5061), sid(7792), sid(1941), sid(24556), sid(1746)]
```

## <a name="Schedule"></a>Setting a Rebalance Schedule
下記のコードでは週の始めの寄りで `rebalance()` 関数(後述)を実行しています。

```python
schedule_function(rebalance,
                  date_rules.week_start(days_offset=0),
                  time_rules.market_open())
```

## Computing Weights
ポートフォリオのウェイトを算出します。前述した戦略に従い、条件に合った銘柄をロングまたはショートします。

* 10日SMA > 30日SMA: ショート
* 10日SMA < 30日SMA: ロング

下記のコードでは10日SMAと30日SMAの差が大きければウェイトを大きくするようし、全体を1として正規化しています。

```python
def compute_weights(context, data):

  # Get the 30-day price history for each security in our list.
  hist = data.history(context.security_list, 'price', 30, '1d')

  # Create 10-day and 30-day trailing windows.
  prices_10 = hist[-10:]
  prices_30 = hist

  # 10-day and 30-day simple moving average (SMA)
  sma_10 = prices_10.mean()
  sma_30 = prices_30.mean()

  # Weights are based on the relative difference between the short and long SMAs
  raw_weights = (sma_30 - sma_10) / sma_30

  # Normalize our weights
  normalized_weights = raw_weights / raw_weights.abs().sum()

  # Return our normalized weights. These will be used when placing orders later.
  return normalized_weights
```

## Order Execution
[Setting a Rebalance Schedule](#Schedule) でスケジュールした `rebalance()` 関数の中身です。前述の `compute_weights` 関数で算出したウェイトに従って設定した銘柄をロングまたはショートしています。

```python
def rebalance(context, data):

  # Calculate our target weights.
  weights = compute_weights(context, data)

  # Place orders for each of our securities.
  for security in context.security_list:
    if data.can_trade(security):
      order_target_percent(security, weights[security])
```

## Recording and Plotting
ロングとショートの個数を記録して可視化します。
下記のコードでは `record_vars()` 関数に記録用のコードを実装し、`schedule_function()` 関数で実行します。毎日の引けにスケジュールされています。

```python
schedule_function(record_vars,
                  date_rules.every_day(),
                  time_rules.market_close())
```

`record_vars()` 関数の中身は下記のコードになります。`context.portfolio.positions` を参照することでポートフォリオの銘柄数とポジションの量が確認できます。ここでは単純にロングした数とショートした数をカウントしています。

```python
def record_vars(context, data):

  # Check how many long and short positions we have.
  longs = shorts = 0
  for position in context.portfolio.positions.itervalues():
    if position.amount > 0:
      longs += 1
    elif position.amount < 0:
      shorts += 1

  # Record our variables.
  record(leverage=context.account.leverage, long_count=longs, short_count=shorts)
```

## Putting It Together
今回の戦略は週次でポートフォリオをリバランスし、日次で記録をしています。従って毎分実行される `handle_data()` 関数は必要ありません。
前述までのコードをまとめると下記のコードになります。
コードは [こちら](https://www.quantopian.com/tutorials/getting-started#lesson11) からcloneできます。

```python
def initialize(context):
    """
    initialize() is called once at the start of the program. Any one-time
    startup logic goes here.
    """

    # An assortment of securities from different sectors:
    # MSFT, UNH, CTAS, JNS, COG
    context.security_list = [sid(5061), sid(7792), sid(1941), sid(24556), sid(1746)]

    # Rebalance every Monday (or the first trading day if it's a holiday)
    # at market open.
    schedule_function(rebalance,
                      date_rules.week_start(days_offset=0),
                      time_rules.market_open())

    # Record variables at the end of each day.
    schedule_function(record_vars,
                      date_rules.every_day(),
                      time_rules.market_close())

def compute_weights(context, data):
    """
    Compute weights for each security that we want to order.
    """

    # Get the 30-day price history for each security in our list.
    hist = data.history(context.security_list, 'price', 30, '1d')

    # Create 10-day and 30-day trailing windows.
    prices_10 = hist[-10:]
    prices_30 = hist

    # 10-day and 30-day simple moving average (SMA)
    sma_10 = prices_10.mean()
    sma_30 = prices_30.mean()

    # Weights are based on the relative difference between the short and long SMAs
    raw_weights = (sma_30 - sma_10) / sma_30

    # Normalize our weights
    normalized_weights = raw_weights / raw_weights.abs().sum()

    # Determine and log our long and short positions.
    short_secs = normalized_weights.index[normalized_weights < 0]
    long_secs = normalized_weights.index[normalized_weights > 0]

    log.info("This week's longs: " + ", ".join([long_.symbol for long_ in long_secs]))
    log.info("This week's shorts: " + ", ".join([short_.symbol for short_ in short_secs]))

    # Return our normalized weights. These will be used when placing orders later.
    return normalized_weights

def rebalance(context, data):
    """
    This function is called according to our schedule_function settings and calls
    order_target_percent() on every security in weights.
    """

    # Calculate our target weights.
    weights = compute_weights(context, data)

    # Place orders for each of our securities.
    for security in context.security_list:
        if data.can_trade(security):
            order_target_percent(security, weights[security])

def record_vars(context, data):
    """
    This function is called at the end of each day and plots our leverage as well
    as the number of long and short positions we are holding.
    """

    # Check how many long and short positions we have.
    longs = shorts = 0
    for position in context.portfolio.positions.itervalues():
        if position.amount > 0:
            longs += 1
        elif position.amount < 0:
            shorts += 1

    # Record our variables.
    record(leverage=context.account.leverage, long_count=longs, short_count=shorts)
```

# おわりに
以上でチュートリアル1が完了となります。
[チュートリアル](https://www.quantopian.com/tutorials) は全部で4つあり、[チュートリアル2](https://www.quantopian.com/tutorials/pipeline) はPipelineの解説になります。
ご要望があればチュートリアル2もやるかもしれませんが、とりあえずは終了とします。おつかれさまでした。

[LESSON 10](./LESSON10) <-

---
[^1]: リターン・リバーサル手法とも呼ばれます。平均回帰の性質を利用して平均値から乖離した銘柄を逆張りして利益を得ようとする手法です。
[^2]: 選択した銘柄に特別な意味はありません。
