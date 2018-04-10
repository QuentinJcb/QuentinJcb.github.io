# Taxonomy of the Dow Jones Industrial Average using Minimum Spanning Tree

In this short post, we analyse the correlations between the 30 constituents of the DJIA. The daily prices of all US stocks and ETF are available
on [Kaggle](https://www.kaggle.com/borismarjanovic/price-volume-data-for-all-us-stocks-etfs). This dataset is made of one file for each security containing
the date, the open, high, low and close prices, the volume and the open interest. The approach followed is the following:
1. First, we load the datasets of all DJIA stocks and compute the daily returns
2. We then select the days for which all the data are available
3. On this period, we compute the pairwise correlations and pairwise distances derived from these quantities
4. Finally, we compute the minimum spanning tree associated with these distances

