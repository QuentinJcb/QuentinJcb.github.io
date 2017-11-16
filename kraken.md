# A Gentle introduction to Kraken API with python

[Kraken](https://www.kraken.com "Kraken") is one of the biggest exchanges for crypto currencies, providing an [API](https://www.kraken.com/help/api#example-api-code "Kraken API") supporting queries to access public information such as 
last prices/volumes, limit order book and private information like positions, balance, etc. It is also possible to submit orders via the API.

[Krakenex](https://github.com/veox/python3-krakenex "Krakenex") is a Python wrapper to send queries to Kraken. The basic command `pip install krakenex`
will install the library.

After signing up on Kraken, a public and private keys need to be generated to use the API. 

To configure the API, simply type this code specifying your key and secret generated at the previous step.

```python
import krakenex

k = krakenex.API(key=MY_PUBLIC_KEY, secret=MY_PRIVATE_KEY)

```

We can check the connection with Kraken by sending a public query requestig current time: 
```python
start = k.query_public('Time')
```
This should return a dictionnary with `'error'`, `'result'` and  `'unixtime'` as keys. If everything worked properly, the value associated with `'error'`
should be an empty list.

More complex queries can be sent, for instance:
```python
query = k.query_public('Trades', {'pair':'XXBTZEUR'})
last_trades = query['result']['XXBTZEUR']
```
This will return the last transactions of the pair Bitcoin/Euro. The structure of the queries always follows the same pattern as above:
* there are two methods: `'query_public'` and `'query_private'`
* the first argument is always the name of the query: `'OHLC'`, `'Spread'`, `'Trades'` for instance. The complete list of queries is
available [here](https://www.kraken.com/help/api#example-api-code "Kraken API")
* the second argument is always a dictionnary of parameters, see [this link](https://www.kraken.com/help/api#example-api-code "Kraken API") for more details.

To get the current order book: 
```python
query = k.query_public('Depth', {'pair':'XXBTZEUR'})
depth = query['result']['XXBTZEUR']
```

To access balance and open positions:
```python
balance = k.query_private('Balance')['result']
open_pos = k.query_private('OpenPositions')['result']
```

More critically, to send a limit order to buy 1 Bitcoin at €6,500, just type
```python
params = {'pair': 'XXBTZEUR', 'type': 'buy', 'order_type': 'limit', 'price': 6500, 'volume': 1}
limit_order = k.query_private('AddOrder', params)
```
I think you are now able to use Krakenex to send any query described [here](https://www.kraken.com/help/api#example-api-code "Kraken API").
Have fun!
