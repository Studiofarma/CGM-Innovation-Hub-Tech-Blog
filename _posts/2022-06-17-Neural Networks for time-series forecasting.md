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

Guess what is very good in finding patterns? Neural networks (NN from now on).

## It is easy to say "Neural Networks"

But which type of neural network?

There exist different kind of NN that can be applied to this use case:
 - **Multi Layer Perceptron** (MLP): the most common and simple.
 - **Recurrent Neural Network** (RNN): in literature, the most suited to time-series forecasting. They combine the information of the current observation, with the information of the previous observations.
 - **Convolutional Neural Network** (CNN): usually applied for Computer Vision, they are raising also for time-series forecasting.

 Also there are different kind of time-series, classified by the patterns that they present. It may happen that NN perform differently depending on the time-series features.

 In this article we are going to test all those kind of NN on **artificially generetad time-series**. Each time-series will present a different combination of patterns, so that we can compare the different NN results.

 It is not in the purpose of this article going deep about each kind of network. Anyway I will leave useful links for those who want to.

## Patterns and composition of time-series

Time-series presents mainly two types of patterns:
 - **Seasonality**: the values periodically repeat.
 - **Trend**: the values continue to increase or decrease.

 <center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/nn_for_time_series_forecasting/2.png" width="800">
    <br>
    <em> Examples of a seasonal and a trendy time-series. (Image by author)</em>
</center>

A time-series forms from a non-linear combination of one or more **trends**, one ore more **seasonalities** and some **noise**.

```
y = f1(x) * trend1(x) + ... + fN(x) * trendN(x) +
    g1(x) * season1(x) ... + gN(x) * seasonN(x) +
    noise(x)
```

Trends and seasonalities are **auto-correlated**: future values depends on past values. The noise component may be instead totally random, or it could present correlation with some feature external to the time-series.

We will conduct this analysis in order to test which NN is the best in finding seasonalities, trend and their non-linearities.

During the experiment, the **noise will be generated randomly**. This means that there is nothing to discover that could predict the noise. It goes that our best models will present an average error very close the amount of noise.

This is ok, because we want to test how good is a model in discovering the patterns hidden by the noise.

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/nn_for_time_series_forecasting/3.png" width="400">
    <br>
    <em> Examples of noise. (Image by author)</em>
</center>
<br/>
<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/nn_for_time_series_forecasting/4.png" width="400">
    <br>
    <em> Examples of seasonality + trend + noise. (Image by author)</em>
</center>

## Neural networks benefits over statistical techniques.

Time-series forecasting is traditionally approached with statistical techniques, like ARMA, ARIMA, SARIMA or Facebook Prophet models.
These requires that you have some a-priori knowledge about the series, like:
 - is the series stationary or not.
 - How many different seasonality are present in the series (SARIMA).
 - The differentiation order value to make the series stationary (ARIMA).

Also, if you plan to predict only one next value, given a set of past values (many-to-one prediction), then the statical models need to be retrained every time a new value is added to the series.

In contrast, NN **don't need to be retrained** so frequently and **don't require any a-priori knowledge**. In addition, it is quite straightforward to add external information that may correlate to the noise generation (multi-variate input).

## Experiments

Let's start by defining a functions for generating a wave, and use it to plot a wave of period 10 with 520 samples and amplitude 1.



To better focus, we could pretend that those are weekly sales of a product, collected over 10 years. 
