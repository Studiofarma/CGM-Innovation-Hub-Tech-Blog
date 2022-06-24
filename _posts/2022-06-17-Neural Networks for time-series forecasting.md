---
layout: post
title: "Neural Networks for time-series forecasting"
author: luca.piccinelli
categories: [ xxx, yyy ]
image: assets/images/nn_for_time_series_forecasting/0.jpg
---

<style>
    iframe{
        border: 0px;
        width: 130em;
    }
</style>

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

But which type of NN? We are going to test different kind of models on some **artificially generetad time-series**. 
Each time-series will present a different combination of patterns, so that we can compare the different NN results.

After reading, you will know:
 - how to choose a NN for time-series forecasting.
 - How many past samples are needed to discover a pattern.
 - Which is the impact of noise on the prediction quality.

## It is easy to say "Neural Networks"

There exist different kind of NN that can be applied to this use case:
 - **Multi Layer Perceptron** (MLP): the most common and simple.
 - **Recurrent Neural Network** (RNN): in literature, the most suited to time-series forecasting. They combine the information of the current observation, with the information of the previous observations.
 - **Convolutional Neural Network** (CNN): usually applied for Computer Vision, they are raising also for time-series forecasting.

Also there are different kind of time-series, classified by the patterns that they present. It may happen that NN perform differently depending on the time-series features.

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
 - is it the series stationary or not?
 - How many different seasonalities are present in the series (SARIMA).
 - The differentiation order value to make the series stationary (ARIMA).

Also, if you plan to predict only one next value, given a set of past values (many-to-one prediction), then the statical models need to be retrained every time a new value is added to the series.

In contrast, NN **don't need to be retrained** so frequently and **don't require any a-priori knowledge**. In addition, it is quite straightforward to add external information that may correlate to the noise generation (multi-variate input).

## Experiments

You can find the entire notebook [here](https://deepnote.com/workspace/luca-piccinelli-37f2-d1557232-c701-4528-93e2-7d7a65936144/project/Untitled-project-6c406c94-84cc-438d-a6d3-e329a4227d17/%2Fnn_for_time_series_forecasting.ipynb)

Let's start by defining a function to generate a wave, and use it to plot a wave of period 10 with 520 samples and amplitude 1.

<iframe title="Embedded cell output" src="https://embed.deepnote.com/6c406c94-84cc-438d-a6d3-e329a4227d17/59956a37-f3dd-4f96-a563-7469f95ced7f/00003-7671808e-7099-4c3e-98e5-84063753d7cc?height=490" height="490"> </iframe>

We will start from a the most simple MLP with one hidden layer of 5 neurons, an output layer of 1 neuron and an input layer of the same size of the input. Which input? 

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/nn_for_time_series_forecasting/5.png" width="400">
    <br>
    <em> The simple MLP. (Image generated with <a href="http://alexlenail.me/NN-SVG/index.html">NN-SVG</a>)</em>
</center>

We want to forecast the value of the series at time `t`, let's call it `y(t)`. Then we will input to the NN the values `y(t-N)...y(t-1) for N <= t and N > 1`. We will call this input **"lags"**. At first, we want to see how the network performs, feeding it with only the first lag `y(t-1)`.

### Data preparation

NN better perform with dataset values ranging between `[0, 1]`. Then let's apply a scaling function.

<iframe title="Embedded cell output" src="https://embed.deepnote.com/6c406c94-84cc-438d-a6d3-e329a4227d17/59956a37-f3dd-4f96-a563-7469f95ced7f/00007-c560407a-e713-41a7-8789-b531fea9f331?height=456.8999938964844" height="456.8999938964844" width="500"></iframe>

After, let's define a function to prepare our dataset. It will output a pandas DataFrame where each row is an input sample, and the columns are the lags together with the actual output value.

<iframe title="Embedded cell output" src="https://embed.deepnote.com/6c406c94-84cc-438d-a6d3-e329a4227d17/59956a37-f3dd-4f96-a563-7469f95ced7f/00009-21e38a7d-8296-47c0-859e-58a457f9b89a?height=815" height="600" width="500"></iframe>

The last operation is to split the dataset into **train** and **test** sets. We will use the first 60% of the dataset to train, and the left 40% to test.

<iframe title="Embedded cell output" src="https://embed.deepnote.com/6c406c94-84cc-438d-a6d3-e329a4227d17/59956a37-f3dd-4f96-a563-7469f95ced7f/00011-f4ebd2be-a14d-4be1-94b1-705dc5cabe42?height=546.8999938964844" height="546" width="500"></iframe>

### Model Hyper-parameters and evaluation environment

We will use [**Keras**](https://keras.io/) backed by [**Tensorflow**](https://www.tensorflow.org/).

Let's consider some of the hyper-parameters that we adopt for training. We will use **Adam** as the gradient descent optimizer, and **mean squared error** for measuring the training error. The **batch size** will be the default value **32**. The model will train for a maximum of **200 epochs**, **early stopped** if no further improvement is observed for 30 consecutive epochs.

[Elu activation](https://stats.stackexchange.com/questions/384621/neural-networks-what-activation-function-should-i-choose-for-hidden-layers-in-r)

The prediction is going to be compared with the actual test values, measuring the error with the **root mean squared error**.

We will also compare the NN prediction, with two naive predictors. The average value of the test set (not really usable as predictor, as it uses future values) and the test set shifted by 1 time lag (i.e. `y'(t+1) = y(t)`).

<center>
    <iframe title="Embedded cell output" src="https://embed.deepnote.com/6c406c94-84cc-438d-a6d3-e329a4227d17/59956a37-f3dd-4f96-a563-7469f95ced7f/00017-0e894a79-d207-40af-b456-cfcce8eacf96?height=335.96250915527344" height="335.96250915527344" width="500"></iframe>
    <br>
    <em> RMSE 0.70 for the mean. RMSE 0.085 for the shift</em>
</center>

There is always a certain level of randomness when training a model. This makes very difficult understanding the effects of changing the hyper-parameters. 

One counter measure [is to train the same model many times and then average the results](https://machinelearningmastery.com/reproducible-results-neural-networks-keras/). In this experiments we will train the models 5 times. 

The quality of the model is given by it's average *RMSE* and the *Standard Deviation* (std) of the errors. An high level of std means that the model is not stable in its training.

### Model training (finally 😅)

Let's train our simple MLP

<center>
    <iframe title="Embedded cell output" src="https://embed.deepnote.com/6c406c94-84cc-438d-a6d3-e329a4227d17/59956a37-f3dd-4f96-a563-7469f95ced7f/00019-af1dc1df-5d4a-405d-ae71-19c27d7eb84a?height=1903.112548828125" height="1903.112548828125" width="500"></iframe>
    <br>
    <em><b>The last graphic is interactive</b>. Click on the legend to toggle the predictions. Double-click it to keep only the clicked prediction</em>
</center>

The result is not satisfying. The model is unstable as it as a very high std. One of the predictions is just the mean value of the training set. All the others prediction are instead the test series shifted by one lag.

We can think of changing 3 things in order to improve the prediction:
 - the number of hidden neurons
 - the number of training epochs
 - the number of lags

**Let's reason for a while**. 

It is straightforward to exclude the number of epochs, looking a the above graphics of the training losses. All the 5 trainings stopped early, after a consistent number of epochs with an error close to 0 and no improvements.

Now zoom-in our sinusoid, near to one of the higher peeks.

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/nn_for_time_series_forecasting/6.png" width="800">
    <br>
    <em> Zoom-in of the sampled sin function near to 1. (Image by author)</em>
</center>

You can notice that the same values repeats both ascending and descending. The same input `y(t)` can output two different `y(t+1)` values. It doesn't exist a unique relation between input and outputs.

We can conclude that we need at least two input lags to learn the function.

<iframe title="Embedded cell output" src="https://embed.deepnote.com/6c406c94-84cc-438d-a6d3-e329a4227d17/59956a37-f3dd-4f96-a563-7469f95ced7f/00021-ceef45b9-20cc-44e5-bad9-ee607b47e2f4?height=653" height="653" width="500"></iframe>

<iframe title="Embedded cell output" src="https://embed.deepnote.com/6c406c94-84cc-438d-a6d3-e329a4227d17/59956a37-f3dd-4f96-a563-7469f95ced7f/00022-bbb08d9b-6de2-4767-9680-24ba36cc1b65?height=1496.6624755859375" height="1496.6624755859375" width="500"></iframe>

We observed that a simple MLP with one hidden layer, can learn a sinusoidal function, with a minimun input of 2 lags.

### Let's do some noise!

What happens if we introduce noise?

<iframe title="Embedded cell output" src="https://embed.deepnote.com/6c406c94-84cc-438d-a6d3-e329a4227d17/59956a37-f3dd-4f96-a563-7469f95ced7f/00026-c1798f3a-81c8-47e1-8c26-e30159fe827d?height=472" height="472" width="500"></iframe>
<center>
    <iframe title="Embedded cell output" src="https://embed.deepnote.com/6c406c94-84cc-438d-a6d3-e329a4227d17/59956a37-f3dd-4f96-a563-7469f95ced7f/00028-331b046d-f59b-4a31-96de-fdb140229441?height=278.7250061035156" height="278.7250061035156" width="500"></iframe>
    <br>
    <em> RMSE 0.70 for the mean. RMSE 0.1613 for the shift</em>
</center>
<iframe title="Embedded cell output" src="https://embed.deepnote.com/6c406c94-84cc-438d-a6d3-e329a4227d17/59956a37-f3dd-4f96-a563-7469f95ced7f/00029-523019a7-7353-4697-9fbd-8ca9d39cc1d4?height=1814.9375" height="1814.9375" width="500"></iframe>

We should not be surpised to see the prediction look again laggy. Indeed, the RMSE is not much better than the shifted baseline. Given a noise with an std value 0.1, we should expect a RMSE value as close as possible to 0.1.
How many lags do we need to discover the pattern hidden by the noise?