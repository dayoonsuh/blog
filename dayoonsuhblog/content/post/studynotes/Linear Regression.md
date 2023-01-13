---
title: "Linear Regression"
date: 2022-12-28T23:13:06-05:00
draft: false
---

The simplest model that we can fit to data is a line. When we are trying to find a line that fits a set of data best, we are performing Linear Regression.

A line is determined by its slope and its intercept. In other words, for each point y on a line we can say:

y = m x + by=mx+b

where `m` is the slope, and b is the intercept. `y` is a given point on the y-axis, and it corresponds to a given x on the x-axis.

The goal is to get the “best” m and b for our data to find the best fit model.

For each data point, we calculate loss, a number that measures how bad the model’s prediction was. This loss is calculated as the squared distance from the point to the line. The best m and b is the values that minimize the loss on average across all of the data.

### Gradient Descent

As we try to minimize loss, we take each parameter we are changing, and move it as long as we are decreasing loss. The process by which we do this is called gradient descent. We move in the direction that decreases our loss the most. _Gradient_ refers to the slope of the curve at any point.

For example, let’s say we are trying to find the intercept for a line. We currently have a guess of 10 for the intercept. At the point of 10 on the curve, the slope is downward. Therefore, if we increase the intercept, we should be lowering the loss. So we follow the gradient downwards.

### Wrap Up!

- We can measure how well a line fits by measuring *loss*.
- The goal of linear regression is to minimize loss.
- To find the line of best fit, we try to find the `b` value (intercept) and the `m` value (slope) that minimize loss.
- *Convergence* refers to when the parameters stop changing with each iteration.
- *Learning rate* refers to how much the parameters are changed on each iteration.
- We can use Scikit-learn’s `LinearRegression()` model to perform linear regression on a set of points.

These are important tools to have in your toolkit as you continue your exploration of data science.
