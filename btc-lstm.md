# Bitcoin’s Price Movement Prediction With LSTM Neural Networks: Part 1

## Introduction
This article is the first part of a long project overlapping **finance** and **machine learning**. Several papers explore the possibility to forecast the evolution of stock's market price movement using different machine learning algorithms, from SVM to neural networks [^fn1] [^fn2] [^fn3]. All these papers have in common the use of technical indicators to capture signal in the noise. Actually, such a quest goes against Eugene Fama's **Efficient Markets Hypothesis** [^fn4]. If there were any algorithm able to predict the evolution of stock's market price better than random, the price would immediately integrate this information. In fact, such a possibility only goes against the strong Efficient Market Hypothesis. There are empirical evidence of pockets of inefficiency in the market, mainly because of irrational behaviours from some investors, that a machine learning algorithm could exploit. Assuming the predictive power of technical analysis is yet another hypothesis. A lot of papers have been published on that topic, with contradictory results. After all, the purpose of this project is more about Python programming and data handling than making money!

The data we are going to use contain information on 18 million BTC/EUR transactions made on Kraken. They were collected [here](http://api.bitcoincharts.com/v1/csv/). The csv file contains 3 columns: the POSIX timestamp, the price (in EUR) and the volume (in BTC). The first 10 lines of the csv file look like that: 
  
   | timestamp |     price   | volume|
   |-----------|:-----------:|-------|
   |1389173189 |624.01000    |0.20000|
   |1389173198 |624.01000    |0.09767|
   |1389173198 |623.53836    |0.01358|
   |1389173265 |623.53836    |0.14896|
   |1389173339 |623.54066    |0.20000|
   |1389173528 |623.54066    |0.17630|
   |1389173534 |623.54065    |0.40633|
   |1389173545 |623.53846    |0.20000|
   |1389173545 |623.53836    |1.80000|
   |1389173668 |623.53836    |0.12370|


## What's the plan?
We will try to replicate the approach followed in [^fn1]:
1. The csv file is devided into periods of 15 minutes
2. On each period, candles are calculated (open, close, high, low)
3. Based on the candles and volumes, about 175 technical indicators are generated using the library [ta-lib](http://www.ta-lib.org/)
4. The objective is to predict the evolution of Bitcoin's price on the next period of 15 minutes (up or down, it's a binary classification problem)
5. A single model is trained on the last 10 months of prior data, and applied during one day, after what it is regenerated. If for a stock it's quite natural to retrain a model after each trading day, it seems to be different in the case of Bitcoin for which exchanges don't close. Nontheless, dealing with non-stationnary data gives us a rationale to learn over and over again the rules of the game.

![Pipeline](crypto.png)

## Data exploration

Let's try to load the csv file and plot the price as a function of the time stamp:
```python
import pandas as pd
import matplotlib.pyplot as plt

df_btc = pd.read_csv('krakenEUR.csv', names=['timestamp', 'price', 'volume'])
plt.plot(df_btc['timestamp'], df_btc['price'])

```
![Price](plot.png)

Actually, the data cover the period between August 2014 and July 2017.

Now, let's regroup the data in periods of 15 minutes and compute the open, high, low and close prices per period:
```python
df_btc['timestamp'] = pd.to_datetime(df_btc['timestamp'], unit='s')

prices = df_btc['price']
prices.index = df_btc['timestamp']

volumes = df_btc['volume']
volumes.index = df_btc['timestamp']

df_ohlc = prices.resample('15Min').ohlc()
df_ohlc['volume'] = volumes.resample('15Min').sum()

```
 **Wait! Can we look closer a the data?**
 Until now we haven't said anything about the time between two succesive transactions in the csv file. With one single line of code, we can compute all the time steps between two transactions: 
 ```python
delta_t = df_btc['timestamp'] - df['timestamp'].shift()
delta_t.dropna(inplace=True)
```
In fact, focusing only on the data from Kraken is very limitating, since data from other exchanges are available. One possibility would be to merge all of these data. At least, we could compare the frequencies of the data available and the time span. Nonetheless, it will rapidly require more than 4 GB of ram...Let's try to use Dask then.

## Muche more data available thanks to Dask!
Dask is a library that provides multi-core and distributed parallel execution on larger-than-memory datasets. In particular, Dask provides an easy solution to distribute dataframe-like structures on multiple computers. In our case, we will distribute the date on two different configurations: 
1. A MacBook Air (2013), with an i5 @ 1.3 GHz, 4 GB of RAM, running on MacOS X
2. A computer with an i7 6700k, 8 GB of RAM, runnig on Ubuntu

All we need to do is creating a scheduler, a worker and a client. On the MacBook Air, we initialize a scheduler with the command ```dask-scheduler```. On the second computer, we create a worker by typing ```dask-worker 192.168.0.21:8786```. Actually, ```192.168.0.21``` is the local address of the MacBook Air. Then, on the MacBook Air, in a Python console, we can type the following commands:
 ```python
from dask.distributed import Client

# be sure to shut down other kernels running distributed clients
c = Client('192.168.0.21:8786')
```
This client will allow us to communicate between the different workers of the cluster.

To test our configuration, we can type the following commands in the Python console:
 ```python
from dask import delayed
import time

def inc(x):
    time.sleep(5)
    return x + 1

def dec(x):
    time.sleep(3)
    return x - 1

def add(x, y):
    time.sleep(7)
    return x + y
    
x = delayed(inc)(1)
y = delayed(dec)(2)
total = delayed(add)(x, y)
total.compute()
```
At the same time, Dask provides a UI based on Bokeh, available at this address: ```http://127.0.0.1:8787/```.

## References

[^fn1]: David M. Q. Nelson, Adriano C. M. Pereira, and Renato A. de Oliveira. Stock market’s price movement prediction with LSTM neural networks. IEEE, 2017.
[^fn2]: Kyoung jae Kim. Financial time series forecasting using support vector machines. Neu- rocomputing, 55, 2003.
[^fn3]: arXiv:1605.00003 
[^fn4]: Eugene F. Fama. Efficient capital markets : A review of theory and empirical work. The Journal of Finance, 25(2) :383–417, 1970.
