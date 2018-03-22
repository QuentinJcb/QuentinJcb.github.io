# Bitcoin’s tick-by-tick data analysis

## Introduction
The purpose of this article is to analyse Bitcoin tick-by-tick data [available here](http://api.bitcoincharts.com/v1/csv/). We will focus exclusively on BTC/USD data from Coinbase exchange. This choice is a bit arbitrary but Coinbase seems to be one of the most active exchanges for USD and the CSV file covers a long period of time. As of March 2018, the file contains 39 million BTC/USD transactions, with the corresponding timestamp and volume. There are indeed three columns: the POSIX timestamp, the price (in USD) and the volume (in BTC). The first 10 lines of the csv file look like that: 

   | timestamp |     price   | volume|
   |-----------|:-----------:|-------|
   |1417412036 |300          |0.010000|
   |1417412423 |300          |0.010000|
   |1417415048 |370          |0.010000|
   |1417416612 |370          |0.026556|
   |1417498166 |377          |0.010000|
   |1417517949 |377.75       |0.250000|
   |1417517949 |378          |3.750000|
   |1417518257 |378          |4.900000|
   |1417518340 |378          |5.200000|
   |1417545780 |378          |0.010000|

## What's the plan?
We will try to explore this dataset and analyse the statistical properties of BTC prices. In particular, we will try to model the properties of this time series at different time scales. Nonetheless, we will limit our approach to short time scales given that Bitcoin is a very young asset. Following what can be found in [^fn1] concerning S&P500, GBP/USD and German bunds, we will discuss second order statistics and the distribution of returns over different time scales.
      
## Data cleaning
Before investigating the statistical properties of this dataset, it seems necessary to perform some basic data cleaning tasks. First, Pandas library includes some tools to deal with dates and create statistics based on a given window size.
Let's first read the csv file and convert the timestamps into dates:
```python
import pandas as pd

data = pd.read_csv('coinbaseUSD.csv', header=None, names=['timestamp', 'price', 'volume'])
data['timestamp'] = pd.to_datetime(data['timestamp'], unit='s')
```
Finally, pandas includes a tool to compute open/high/low/close features on specific windows. We will work on windows of 5 minutes.
```python
ts = data['price']
ts.index = data['timestamp']
ts = ts.resample('5Min').ohlc()
```
We obtain the following time series (for the first 10 rows): 
                      
|timestamp            |  open |  high |  low  | close |  
|-------------------- |:-----:|-------|-------|-------|
|2014-12-01 05:30:00  | 300.0 | 300.0 | 300.0 | 300.0 |
|2014-12-01 05:35:00  |  NaN  |  NaN  | NaN   |  NaN  |
|2014-12-01 05:40:00  | 300.0 | 300.0 | 300.0 |  300.0|
|2014-12-01 05:45:00  |  NaN  |  NaN  |  NaN  |  NaN  |
|2014-12-01 05:50:00  |  NaN  |  NaN  |  NaN  |  NaN  |
|2014-12-01 05:55:00  |  NaN  |  NaN  |  NaN  |  NaN  |
|2014-12-01 06:00:00  |  NaN  |  NaN  |  NaN  |  NaN  |
|2014-12-01 06:05:00  |  NaN  |  NaN  |  NaN  |  NaN  |
|2014-12-01 06:10:00  |  NaN  |  NaN  |  NaN  |  NaN  |
|2014-12-01 06:15:00  |  NaN  |  NaN  |  NaN  |  NaN  |
 
Note that some windows of 5 minutes do not contain any transaction. It might be the case in periods of time when BTC was less liquid than today (back in 2014 for example), or in situations of trading halts (specific Coinbase issues for instance).

The next step is to compute the log-returns over each window: 
```python
ts['return'] = np.log(ts['close']) - np.log(ts['open'])
```
Since the first rows of the dataset do not contain many transactions, we give the log-returns of the last 5 lines using ```ts.tail(5)```:

|timestamp           |  open   |   high  |   low   |  close  |  return | 
|--------------------|:-------:|---------|---------|---------|---------|
|2018-03-21 11:30:00 | 9103.50 | 9103.50 | 9103.49 | 9103.50 |0.000000 |
|2018-03-21 11:35:00 | 9103.49 | 9110.99 | 9103.49 | 9110.99 |0.000824 |
|2018-03-21 11:40:00 | 9110.98 | 9117.00 | 9110.98 | 9117.00 |0.000661 |
|2018-03-21 11:45:00 | 9117.00 | 9117.00 | 9092.27 | 9092.28 |-0.002715|
|2018-03-21 11:50:00 | 9092.27 | 9092.28 | 9072.18 | 9072.20 |-0.002210|

### What happened April, 16 2016 ?



## References

[^fn1]: Jean-Philippe Bouchaud, Marc Potters. Theory of Financial Risk and Derivative Pricing: From Statistical Physics to Risk Management. 2004.
