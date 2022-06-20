---
layout: post
title: "Neural Networks for time-series forecasting"
author: luca.piccinelli
categories: [ xxx, yyy ]
image: assets/images/nn_for_time_series_forecasting/0.jpg
---

So you want to forecast your sales? 

Or maybe you would like to know the future price of bitcoin?

In both cases, you are trying to solve a problem known as "time-series forecasting". A time-series is a set of values that varies depending on time.

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/nn_for_time_series_forecasting/1.png" width="400">
    <br>
    <em> Example of a time-series. (Image by author)</em>
</center>

No one can predict the future, but one can search in the past looking for patterns, and hope that those are going to repeat.

Guess what is very good in finding patterns? Neural networks!

## Patterns in time-series

Time-series presents mainly two types of patterns:
 - **Trend**: the values continue to increase or decrease.
 - **Seasonality**: the values periodically repeat.

 <center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/nn_for_time_series_forecasting/2.png" width="800">
    <br>
    <em> Examples of a seasonal and a trendy time-series. (Image by author)</em>
</center>

A time-series is a non-linear combination of one or more trends, one ore more seasonalities and some noise.

```
y = f1(x) * trend1(x) + ... + fN(x) * trendN(x) +
    g1(x) * season1(x) ... + gN(x) * seasonN(x) +
    noise(x)
```

Trends and seasonalities are auto-correlated, i.e. future values totally depends on past values. The noise component may be instead totally random, or it could present correlation with some feature external to the time-series.

In this article, we are going to train neural networks without considering anything external to time-series. This means that our model can be as good as the quantity of random noise.

In other words, even the best model will present an average error value very close to the average noise value.


In case `g1..gN` are some constants values and it presents no trend at all, then the time-series is a *stationary* one.

Stationary time-series can be approached with statistical methods, like the *ARIMA* model.