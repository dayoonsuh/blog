---
title: "Bakery Sales Analysis"
date: 2023-01-07T00:41:40-05:00
draft: false
image: "bakery-sales-analysis/sydney-bakery.jpg"
---

I am a big fan of French pastries. I love baguette, croissant, macaron, and many other things. Sipping a cup of black coffee with a bite of pain au chocolat is one of my favorite things to start my morning.

I found an interesting dataset from Kaggle which is set of records of French bakery sales.

Here is the link to the dataset: [French Bakery Daily Sales](https://www.kaggle.com/datasets/matthieugimbert/french-bakery-daily-sales)

The dataset belongs to a French bakery. The dataset provides the daily transaction details of customers from 2021-01-01 to 2022-09-30.
Yearly and weekly saisonalities can be observed.

I will go through the data and see if we can gain insight from the sales data for future sales.

**Let's dive in!**

## Reading csv file using Pandas

Before I bring the data, I will import libraries that I would be using in this analysis.

```
import matplotlib as mpl
import matplotlib.pyplot as plt
import pandas as pd
from datetime import datetime
from collections import Counter
```

Now we are ready to bring the data.

Using pd.read_csv(), I will bring the as pandas dataframe and call it as "sales".

```
sales = pd.read_csv('Bakery sales.csv', parse_dates = ['date'])
```

When reading the data, I parsed the dates for timely analysis later.

Let's look at the first 5 rows to see how the data is like.
![sales.head()](bakery-sales-analysis/saleshead.JPG)

### Hooray! We just made our first step!

Just before we get into data cleaning, let's look at the data.
First, let's see the types and overall summary.  
![salesinfo](bakery-sales-analysis/salesinfo1.JPG)  
There are 234005 rows and 7 columns. All columns do not have any null data.
The "date" column has successfully changed its dtype into datetime object. Great!

However, you might notice that the dtype of "unit_price" is `object` which means the data is stored as string values. To do some mathematic calculations with price, we should transform the data type into numeric - float.

Going back to the `sales.head()`, it seems like that there are a few columns that are not really important, such as "Unnamed: 0 " and "ticket_number". Also, there are commas in "unit_price" where it supposed to be dots. We can also remove the euro sign to transform the column data type from string to float for calculations.

One thing personally intrigued me was what products the bakery has. To find out what kind of pastries they have, I listed the unique values in the "article".  
`sales.article.unique()`  
It is a long list of names, but I found a extraordinary value in the article list.  
![dotproduct](bakery-sales-analysis/dot1.JPG)

**Can anybody see the " . " next to "FRAISE PISTACHE?"**

![dotproduct](bakery-sales-analysis/dot2.JPG)
If we look at the data where article is " . ", we can observe that the prices for all products names "." are all 0. This indicates that articles named "." might be products that were given to customers for free.

## Data Wrangling

We have to prepare our data to be set for analysis.

First, I will remove unnecessary columns and only leave columns that we will use.

```
sales = sales[['date','time','article', 'Quantity', 'unit_price']]
```

Then, I will remove the rows where article name is " . " since it makes noise in our analysis.

```
drop_index = sales[sales['article'] == '.'].index
sales.drop(drop_index, inplace=True)
```

When we check if it worked,
![dotproduct](bakery-sales-analysis/dot3.JPG)
YES it worked! It doesn't show any records of article " . ".

Then I will remove the € symbol and change commas to dots in "unit_price".

```
sales['unit_price'] = sales['unit_price'].str.replace(',','.')
sales['unit_price'] = sales['unit_price'].str.replace(' €','')
sales['unit_price'] = pd.to_numeric(sales['unit_price'])
```

#### Total Price

Next thing we can do is to add a new column "total_price" that shows the total price of each purchase.

```
sales['total_price'] = sales['Quantity'] * sales['unit_price']
```

#### Date & Time - Year, Month, Hour

Last thing we can do for formatting the dataframe is to add columns of year, month, and hour by extracting numbers from existing date and time columns.

```
sales['year'] = sales['date'].map(lambda x: x.year)
sales['month'] = sales['date'].map(lambda x: x.month)
sales['hour'] = sales['time'].map(lambda x: datetime.strptime(x, "%H:%M").hour)
```

#### Now the dataframe looks like...

![sales.head()](bakery-sales-analysis/salesheadanddescribe.JPG)

Since the minimum and maximum value in hour column is 7 and 20, we can guess that the bakery opens at 7 AM and closes at 8 PM. What I can guess for the negative values in quantity seems to be purchase cancellation or refund.

## EDA

#### Top 10 Most Sold Items

What I want to know is the ten most popular items. I will group the data by name of the breads and then sort them by quantity so that we can see how many products are sold for each article.

```
itemed = sales.groupby("article").sum()
top_ten = itemed.sort_values(by = 'Quantity', ascending = False).head(10)
```

![topten](bakery-sales-analysis/topten.JPG)

To show this data with visualization,
![toptengraph](bakery-sales-analysis/toptengraph.JPG)
From 2021-01-02 to 2022-09-30, the most sold item is TRADITIONAL BAGUETTE. The difference between the sales of other popular items, TRADITIONAL BAGUETTE is outstandingly popular.

#### Hourly Sales

It is important to know what time customers visit a lot and how many products are sold at that time since the bakery can prepare for enough breads for customers.

Therefore, I grouped the data by hours using `sales_by_hour = sales.groupby('hour')` to see how many items are sold on average on hourly basis.
![salesbyhour](bakery-sales-analysis/salesbyhour.JPG)
This is sum of the number of sales on hourly basis from Jan 2021 to Sep 2022 - 600 days (excluding closed days).
I divided the sum of quantity grouped by hours by 600 days to show daily hourly sales on average.

Based on the chart, it seems that people buy breads the most in the morning.

Hour 7 means from 7:00 to 7:59, approximately 22 items were sold on average.

We will not consider hour 20 since it seems like 20 is the closing time. A few purchases made in 20 seems like some customers came in just before closing and bought products.

The reason why I am using sum() instead of count() is because of the negative values in Quantity. We are focusing on the number of products sold rather than the number of transactions. count() includes cancelled items while sum() calculates the real total number of products sold.
![salesbyhour](bakery-sales-analysis/hourgraph.JPG)

## Compare Year 2021 and 2022

Now, I will compare the sales in 2021 and 2022.

```
sales_2021 = sales[sales['year'] == 2021]
sales_2022 = sales[sales['year'] == 2022]
```

For both years, I will find 5 most popular items and monthly sales.
![2021](bakery-sales-analysis/2021.JPG)
![2022](bakery-sales-analysis/2022.JPG)
![compare](bakery-sales-analysis/comparemonthly.JPG)

For each month, I will directly compare 2021 and 2022 monthly sales with bar chart. Before I do that, I will make a list of data.

```
total21 = sales_2021.groupby('month').sum().total_price
total22 = sales_2022.groupby('month').sum().total_price
total22[10] = 0
total22[11] = 0
total22[12] = 0
```

![compare](bakery-sales-analysis/comparesalesbymonth.JPG)
I will do the same thing for monthly number of products sold.
![compare](bakery-sales-analysis/comparenumproduct.JPG)

The reason for why 2022 sales are larger than 2021 when the number of products sold is smaller, I guess this is because the bakery increased prices of some of their products.

## Conclusion

Analyzing the data from a French bakery, I learned some of the most popular French breads. I didn't know that traditional baguette was very popular.
I would recommend the store to bake plentiful breads in the morning. Also, I would recommend to hire more workers in summer since the sales were very high during that season.
