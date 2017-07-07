# LESSON 6 The history() Function

`data.history()` メソッド[^1]はヒストリカルデータを取得します。
渡す引数は下記のとおりです。

位置|キーワード|値
---|---|---
1||銘柄のインスタンス
2|fields|'price', 'open', 'high', 'low', 'close'
3|bar_count|データの個数
4|frequency|頻度

戻り値はpandasのDataFrame型になります。
下記の例ではAAPLの10日分の価格を取得し、平均を算出しています。

```python
# Get the 10-day trailing price history of AAPL in the form of a Series.
hist = data.history(sid(24), 'price', 10, '1d')

# Mean price over the last 10 days.
mean_price = hist.mean()
```

第四引数に '1d' を指定することで1日毎の頻度でデータを取得しています。'1m' を指定すると毎分のデータを取得します。

'1d' を指定した場合、当日の価格も含まれます。移動平均を目安にするようなトレードでは前日までの平均を使うことがよくあると思います。前日までの10日間の平均を算出する場合は下記のように記載します。

```python
data.history(sid(8554), 'volume', 11, '1d')[:-1].mean()
```
LESSON 5で紹介した `data.current()` と同様に、複数銘柄を指定した場合にはpandasのDataFrame型が返ります。

```python
# Get the last 5 minutes of volume data for each security in our list.
hist = data.history([sid(24), sid(8554), sid(5061)], 'volume', 5, '1m')

# Calculate the mean volume for each security in our DataFrame.
mean_volumes = hist.mean(axis=0)
```

複数銘柄でかつ複数のフィールドを指定した場合にはpandasのPanel型[^2]が返ります。

```python
# Low and high minute bar history for each of our securities.
hist = data.history([sid(24), sid(8554), sid(5061)], ['low', 'high'], 5, '1m')

# Calculate the mean low and high over the last 5 minutes
means = hist.mean()
mean_lows = means['low']
mean_highs = means['high']
```

下記のコードではAAPL、MSFT、SPYの10分間の出来高の平均を出力しています。

```python
def initialize(context):
    # AAPL, MSFT, SPY
    context.security_list = [sid(24), sid(8554), sid(5061)]

def handle_data(context, data):
    hist = data.history(context.security_list, 'volume', 10, '1m').mean()
    print hist.mean()
```

[LESSON 5](./LESSON5.md) <--> [LESSON 7](./LESSON7.md)

---
[^1]: 前回と同様にメソッドと表記します。
[^2]: Panelはxrayに統合されるという話をきいたことがあります。詳細をご存知の方がいらっしゃいましたら教えてください。
