---
title: "Customers-Segmentation-with-RFM"
layout: post
date: 2022-01-01 12:44
star: true
category: blog
description: RFM
---

# Customers-Segmentation-with-RFM
---

![png](/assets/images/post/blog/rfm/rfmpic.png)

## RFM Segmentation
---
* The RFM method was introduced by Bult and Wansbeek in 1995 and has been successfully used by marketers since.
* It analyzes customers' behavior on three parameters:
* Recency: How recent is the last purchase of the customer.
* Frequency: How often the customer makes a purchase.
* Monetary: How much money does the customer spends.
* RFM Analysis is a technique used to segment customer behavior. RFM is a data science analytics application.
* It is a rule-based method, not a machine learning model.
* It helps to determine marketing and sales strategies based on customers' purchasing habits.


## Advantages & Disadvantages
---
* The advantages of RFM is that it is easy to implement and it can be used for different types of business. It helps craft better marketing campaigns and improves CRM and customer's loyalty.

* The disadvantages are that it may not apply in industries where customers are usually one time buyers. It is based on historical data and won't give much insight about prospects.

## RFM Metrics
---

![rfm_metric.png](/assets/images/post/blog/rfm/rfm_metric.png)

## Recency Calculation


We create the recency (R) variable from the date variable we have. What we do is to subtract the last shopping date of each customer from the specified last shopping day.

Here, the perception of size and smallness is different for the Recency score. That is, a value of 1 for Recency (last shopped 1 day ago) is the best value for us, while a value of 80 is worse than 1.

## Frequency Calculation


Frequency (F) consists of the total number of purchases made by each customer.

The point to be noted here is that each unique invoice number can be multiplexed and we need to count it as singular.

## Monetary Calculation


In the Monetary (M) part, we calculate the money that the customer earns for us in this time period.

In our sample dataset, there was no such variable that we could take directly, and we calculated the total amount of each purchase by multiplying the number of products with the unit prices.

## Methodology
---
To get the RFM score of a customer, we need to first calculate the R, F and M scores on a scale from 1 (worst) to 5 (best).

* calculate Recency = number of days since last purchase
* calculate Freqency = number of purchases during the studied period (usually one year)
* calculate Monetary = total amount of purchases made during the studied period
* find quintiles for each of these dimensions
* give a grade to each dimension depending in which quintiles it stands
* combine R, F and M scores to get the RFM score
* map RF scores to segments

For this example, I will use **the Online Retail** dataset.

### We have a Bussines problem.
---
* An e-commerce company segments its customers and determine marketing strategies according to segments wants.
* Customer segments with common behaviors Income increase by doing marketing studies in particular thinks it will.
* For example, retaining customers that are very lucrative for the company different campaigns for new customers. Campaigns are wanted.


### Dataset Story
---
* The dataset named Online Retail II is a UK-based online sales company. Store's sales between 01/12/2009 - 09/12/2011 contains.
* The product catalog of this company includes souvenirs.
* The vast majority of the company's customers are corporate customers.

### RESOURCES AND THANKS
---
* [Oğuz Erdoğan - rfm - analysis](https://oguzerdo.medium.com/rfm-analizi-ile-m%C3%BC%C5%9Fteri-segmentasyonu-e4ddd00d1de8)
* [guillaume-martin-rfm](https://guillaume-martin.github.io/rfm-segmentation-with-python.html)
* [clevertap-rfm](https://clevertap.com/blog/rfm-analysis/)

## Let's move on to our example
![letstry.png](/assets/images/post/blog/rfm/letstry.png)

## Necessary Import
---


```python
!pip install openpyxl
import datetime as dt
import pandas as pd
import matplotlib.pyplot as plt
```

    Collecting openpyxl
      Downloading openpyxl-3.0.9-py2.py3-none-any.whl (242 kB)
         |████████████████████████████████| 242 kB 288 kB/s            
    [?25hCollecting et-xmlfile
      Downloading et_xmlfile-1.1.0-py3-none-any.whl (4.7 kB)
    Installing collected packages: et-xmlfile, openpyxl
    Successfully installed et-xmlfile-1.1.0 openpyxl-3.0.9
    [33mWARNING: Running pip as the 'root' user can result in broken permissions and conflicting behaviour with the system package manager. It is recommended to use a virtual environment instead: https://pip.pypa.io/warnings/venv[0m



```python
df_ = pd.read_excel("../input/online-retail-ii/online_retail_II.xlsx", sheet_name="Year 2010-2011")
df = df_.copy()

```


```python
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Invoice</th>
      <th>StockCode</th>
      <th>Description</th>
      <th>Quantity</th>
      <th>InvoiceDate</th>
      <th>Price</th>
      <th>Customer ID</th>
      <th>Country</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>536365</td>
      <td>85123A</td>
      <td>WHITE HANGING HEART T-LIGHT HOLDER</td>
      <td>6</td>
      <td>2010-12-01 08:26:00</td>
      <td>2.55</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
    </tr>
    <tr>
      <th>1</th>
      <td>536365</td>
      <td>71053</td>
      <td>WHITE METAL LANTERN</td>
      <td>6</td>
      <td>2010-12-01 08:26:00</td>
      <td>3.39</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
    </tr>
    <tr>
      <th>2</th>
      <td>536365</td>
      <td>84406B</td>
      <td>CREAM CUPID HEARTS COAT HANGER</td>
      <td>8</td>
      <td>2010-12-01 08:26:00</td>
      <td>2.75</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
    </tr>
    <tr>
      <th>3</th>
      <td>536365</td>
      <td>84029G</td>
      <td>KNITTED UNION FLAG HOT WATER BOTTLE</td>
      <td>6</td>
      <td>2010-12-01 08:26:00</td>
      <td>3.39</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
    </tr>
    <tr>
      <th>4</th>
      <td>536365</td>
      <td>84029E</td>
      <td>RED WOOLLY HOTTIE WHITE HEART.</td>
      <td>6</td>
      <td>2010-12-01 08:26:00</td>
      <td>3.39</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
    </tr>
  </tbody>
</table>
</div>



# Task 1
---

## Understanding and Preparing Data

Let's examine the descriptive statistics of the dataset


```python
df.shape
```




    (541910, 8)




```python
df.columns
```




    Index(['Invoice', 'StockCode', 'Description', 'Quantity', 'InvoiceDate',
           'Price', 'Customer ID', 'Country'],
          dtype='object')




```python
df.describe().T
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>count</th>
      <th>mean</th>
      <th>std</th>
      <th>min</th>
      <th>25%</th>
      <th>50%</th>
      <th>75%</th>
      <th>max</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Quantity</th>
      <td>541910.0</td>
      <td>9.552234</td>
      <td>218.080957</td>
      <td>-80995.00</td>
      <td>1.00</td>
      <td>3.00</td>
      <td>10.00</td>
      <td>80995.0</td>
    </tr>
    <tr>
      <th>Price</th>
      <td>541910.0</td>
      <td>4.611138</td>
      <td>96.759765</td>
      <td>-11062.06</td>
      <td>1.25</td>
      <td>2.08</td>
      <td>4.13</td>
      <td>38970.0</td>
    </tr>
    <tr>
      <th>Customer ID</th>
      <td>406830.0</td>
      <td>15287.684160</td>
      <td>1713.603074</td>
      <td>12346.00</td>
      <td>13953.00</td>
      <td>15152.00</td>
      <td>16791.00</td>
      <td>18287.0</td>
    </tr>
  </tbody>
</table>
</div>



Are there any missing observations in the dataset? If yes, how many missing observations in which variable?


```python
df.isnull().sum()
```




    Invoice             0
    StockCode           0
    Description      1454
    Quantity            0
    InvoiceDate         0
    Price               0
    Customer ID    135080
    Country             0
    dtype: int64



Let's remove the missing observations from the data set


```python
df.dropna(inplace=True)
```

What is the number of unique products?


```python
df.Description.nunique()
```




    3896



How many of each product are there?


```python
df.Description.value_counts().head(10)
```




    WHITE HANGING HEART T-LIGHT HOLDER    2070
    REGENCY CAKESTAND 3 TIER              1905
    JUMBO BAG RED RETROSPOT               1662
    ASSORTED COLOUR BIRD ORNAMENT         1418
    PARTY BUNTING                         1416
    LUNCH BAG RED RETROSPOT               1358
    SET OF 3 CAKE TINS PANTRY DESIGN      1232
    POSTAGE                               1197
    LUNCH BAG  BLACK SKULL.               1126
    PACK OF 72 RETROSPOT CAKE CASES       1080
    Name: Description, dtype: int64



Let's sort the 5 most ordered products from most to least


```python
df.groupby("Description").agg({"Quantity": "sum"}).sort_values("Quantity", ascending=False).head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Quantity</th>
    </tr>
    <tr>
      <th>Description</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>WORLD WAR 2 GLIDERS ASSTD DESIGNS</th>
      <td>53215</td>
    </tr>
    <tr>
      <th>JUMBO BAG RED RETROSPOT</th>
      <td>45066</td>
    </tr>
    <tr>
      <th>ASSORTED COLOUR BIRD ORNAMENT</th>
      <td>35314</td>
    </tr>
    <tr>
      <th>WHITE HANGING HEART T-LIGHT HOLDER</th>
      <td>34147</td>
    </tr>
    <tr>
      <th>PACK OF 72 RETROSPOT CAKE CASES</th>
      <td>33409</td>
    </tr>
  </tbody>
</table>
</div>



The 'C' in the invoices shows the canceled transactions. Let's remove the canceled transactions from the dataset.


```python
df = df[~df["Invoice"].astype(str).str.contains("C", na=False)]

```


```python
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Invoice</th>
      <th>StockCode</th>
      <th>Description</th>
      <th>Quantity</th>
      <th>InvoiceDate</th>
      <th>Price</th>
      <th>Customer ID</th>
      <th>Country</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>536365</td>
      <td>85123A</td>
      <td>WHITE HANGING HEART T-LIGHT HOLDER</td>
      <td>6</td>
      <td>2010-12-01 08:26:00</td>
      <td>2.55</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
    </tr>
    <tr>
      <th>1</th>
      <td>536365</td>
      <td>71053</td>
      <td>WHITE METAL LANTERN</td>
      <td>6</td>
      <td>2010-12-01 08:26:00</td>
      <td>3.39</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
    </tr>
    <tr>
      <th>2</th>
      <td>536365</td>
      <td>84406B</td>
      <td>CREAM CUPID HEARTS COAT HANGER</td>
      <td>8</td>
      <td>2010-12-01 08:26:00</td>
      <td>2.75</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
    </tr>
    <tr>
      <th>3</th>
      <td>536365</td>
      <td>84029G</td>
      <td>KNITTED UNION FLAG HOT WATER BOTTLE</td>
      <td>6</td>
      <td>2010-12-01 08:26:00</td>
      <td>3.39</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
    </tr>
    <tr>
      <th>4</th>
      <td>536365</td>
      <td>84029E</td>
      <td>RED WOOLLY HOTTIE WHITE HEART.</td>
      <td>6</td>
      <td>2010-12-01 08:26:00</td>
      <td>3.39</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
    </tr>
  </tbody>
</table>
</div>



We want the Quentity and Price variables to be greater than 0


```python
df = df[(df['Quantity'] > 0)]
df = df[(df['Price'] > 0)]
```

Let's create a variable called 'TotalPrice' that represents the total earnings per invoice


```python
df["TotalPrice"] = df["Quantity"] * df["Price"]
```

# Task 2 
---

## Calculating RFM metrics


```python
df["InvoiceDate"].max()                  # Timestamp('2011-12-09 12:50:00')   we look at the date of the last invoice transaction
                                         # Because we will determine the date when we will do our analysis.
today_date = dt.datetime(2011, 12, 11)   # We determine the date of the analysis
rfm = df.groupby('Customer ID').agg({'InvoiceDate': lambda InvoiceDate: (today_date - InvoiceDate.max()).days, # recency
                                     'Invoice': lambda Invoice: Invoice.nunique(),                             # frequency
                                     'TotalPrice': lambda TotalPrice: TotalPrice.sum()})                       # monetary
rfm.head()
rfm.columns = ['recency', 'frequency', 'monetary'] # updating our variable names
rfm = rfm[rfm["monetary"] > 0] # We filter out winners with more than 0
```

# Task 3
---

## Generating RFM scores


```python
rfm["recency_score"] = pd.qcut(rfm["recency"], 5, labels=[5,4,3,2,1])
rfm["frequency_score"] = pd.qcut(rfm["frequency"].rank(method="first"),5,labels=[1,2,3,4,5,])
rfm["monetary_score"] = pd.qcut(rfm["monetary"],5, labels=[1,2,3,4,5])
```


```python
rfm["RFM_SCORE"] = (rfm["recency_score"].astype(str)+rfm["frequency_score"].astype(str)) 
# We did not consider Monetary because we will do a segmentation operation. We calculate this process over recency and frequency.
```

# Task 4
---

## Defining RFM scores as segments
![rfm_matrix.png](/assets/images/post/blog/rfm/rfm_matrix.png)


```python
seg_map = {
    r'[1-2][1-2]': 'hibernating',
    r'[1-2][3-4]': 'at_Risk',
    r'[1-2]5': 'cant_loose',
    r'3[1-2]': 'about_to_sleep',
    r'33': 'need_attention',
    r'[3-4][4-5]': 'loyal_customers',
    r'41': 'promising',
    r'51': 'new_customers',
    r'[4-5][2-3]': 'potential_loyalists',
    r'5[4-5]': 'champions'
}
rfm["SEGMENT"] = rfm["RFM_SCORE"].replace(seg_map,regex=True)
```


```python
rfm.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>recency</th>
      <th>frequency</th>
      <th>monetary</th>
      <th>recency_score</th>
      <th>frequency_score</th>
      <th>monetary_score</th>
      <th>RFM_SCORE</th>
      <th>SEGMENT</th>
    </tr>
    <tr>
      <th>Customer ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>12346.0</th>
      <td>326</td>
      <td>1</td>
      <td>77183.60</td>
      <td>1</td>
      <td>1</td>
      <td>5</td>
      <td>11</td>
      <td>hibernating</td>
    </tr>
    <tr>
      <th>12347.0</th>
      <td>3</td>
      <td>7</td>
      <td>4310.00</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>55</td>
      <td>champions</td>
    </tr>
    <tr>
      <th>12348.0</th>
      <td>76</td>
      <td>4</td>
      <td>1797.24</td>
      <td>2</td>
      <td>4</td>
      <td>4</td>
      <td>24</td>
      <td>at_Risk</td>
    </tr>
    <tr>
      <th>12349.0</th>
      <td>19</td>
      <td>1</td>
      <td>1757.55</td>
      <td>4</td>
      <td>1</td>
      <td>4</td>
      <td>41</td>
      <td>promising</td>
    </tr>
    <tr>
      <th>12350.0</th>
      <td>311</td>
      <td>1</td>
      <td>334.40</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
      <td>11</td>
      <td>hibernating</td>
    </tr>
  </tbody>
</table>
</div>



# Task 5
---

## Action Time
 - Let's choose 3 segments that we find important. These three segments;
 - Both in terms of action decisions,
 - Both in terms of the structure of the segments (mean RFM values)
 let's comment.


```python
rfm[["SEGMENT", "recency", "frequency", "monetary"]].groupby("SEGMENT").agg(["mean", "count","max"])
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }

    .dataframe thead tr:last-of-type th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th colspan="3" halign="left">recency</th>
      <th colspan="3" halign="left">frequency</th>
      <th colspan="3" halign="left">monetary</th>
    </tr>
    <tr>
      <th></th>
      <th>mean</th>
      <th>count</th>
      <th>max</th>
      <th>mean</th>
      <th>count</th>
      <th>max</th>
      <th>mean</th>
      <th>count</th>
      <th>max</th>
    </tr>
    <tr>
      <th>SEGMENT</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>about_to_sleep</th>
      <td>53.312500</td>
      <td>352</td>
      <td>72</td>
      <td>1.161932</td>
      <td>352</td>
      <td>2</td>
      <td>471.994375</td>
      <td>352</td>
      <td>6207.67</td>
    </tr>
    <tr>
      <th>at_Risk</th>
      <td>153.785835</td>
      <td>593</td>
      <td>374</td>
      <td>2.876897</td>
      <td>593</td>
      <td>6</td>
      <td>1084.535297</td>
      <td>593</td>
      <td>44534.30</td>
    </tr>
    <tr>
      <th>cant_loose</th>
      <td>132.968254</td>
      <td>63</td>
      <td>373</td>
      <td>8.380952</td>
      <td>63</td>
      <td>34</td>
      <td>2796.155873</td>
      <td>63</td>
      <td>10254.18</td>
    </tr>
    <tr>
      <th>champions</th>
      <td>6.361769</td>
      <td>633</td>
      <td>13</td>
      <td>12.413902</td>
      <td>633</td>
      <td>209</td>
      <td>6857.963918</td>
      <td>633</td>
      <td>280206.02</td>
    </tr>
    <tr>
      <th>hibernating</th>
      <td>217.605042</td>
      <td>1071</td>
      <td>374</td>
      <td>1.101774</td>
      <td>1071</td>
      <td>2</td>
      <td>488.643307</td>
      <td>1071</td>
      <td>77183.60</td>
    </tr>
    <tr>
      <th>loyal_customers</th>
      <td>33.608059</td>
      <td>819</td>
      <td>72</td>
      <td>6.479853</td>
      <td>819</td>
      <td>63</td>
      <td>2864.247791</td>
      <td>819</td>
      <td>124914.53</td>
    </tr>
    <tr>
      <th>need_attention</th>
      <td>52.427807</td>
      <td>187</td>
      <td>72</td>
      <td>2.326203</td>
      <td>187</td>
      <td>3</td>
      <td>897.627861</td>
      <td>187</td>
      <td>12601.83</td>
    </tr>
    <tr>
      <th>new_customers</th>
      <td>7.428571</td>
      <td>42</td>
      <td>13</td>
      <td>1.000000</td>
      <td>42</td>
      <td>1</td>
      <td>388.212857</td>
      <td>42</td>
      <td>3861.00</td>
    </tr>
    <tr>
      <th>potential_loyalists</th>
      <td>17.398760</td>
      <td>484</td>
      <td>33</td>
      <td>2.010331</td>
      <td>484</td>
      <td>3</td>
      <td>1041.222004</td>
      <td>484</td>
      <td>168472.50</td>
    </tr>
    <tr>
      <th>promising</th>
      <td>23.510638</td>
      <td>94</td>
      <td>33</td>
      <td>1.000000</td>
      <td>94</td>
      <td>1</td>
      <td>294.007979</td>
      <td>94</td>
      <td>1757.55</td>
    </tr>
  </tbody>
</table>
</div>




```python
rfm["SEGMENT"].value_counts().plot(kind='barh', rot=5, fontsize=20)
plt.show()
```


    
![png](/assets/images/post/blog/rfm/output_61_0.png)
    


#### **MY SELECTED SEGMENTS AND ACTION SUGGESTIONS** 
---

 **champions** Champions. They want to listen to champions league music all the time.
 they're like haalaand who even set their alarms to this music on their phones.
*  Our best customers are in this group.
*  It consists of the group whose last shopping dates are the closest and who visit us most frequently.
*  As an average of us; They earned 6857.96392 units of money
*  Frequency of visits 12,41390
*  They last shopped 6.36177 days ago.


```python
rfm[rfm["SEGMENT"] == "champions"].head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>recency</th>
      <th>frequency</th>
      <th>monetary</th>
      <th>recency_score</th>
      <th>frequency_score</th>
      <th>monetary_score</th>
      <th>RFM_SCORE</th>
      <th>SEGMENT</th>
    </tr>
    <tr>
      <th>Customer ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>12347.0</th>
      <td>3</td>
      <td>7</td>
      <td>4310.00</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>55</td>
      <td>champions</td>
    </tr>
    <tr>
      <th>12362.0</th>
      <td>4</td>
      <td>10</td>
      <td>5226.23</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>55</td>
      <td>champions</td>
    </tr>
    <tr>
      <th>12364.0</th>
      <td>8</td>
      <td>4</td>
      <td>1313.10</td>
      <td>5</td>
      <td>4</td>
      <td>4</td>
      <td>54</td>
      <td>champions</td>
    </tr>
    <tr>
      <th>12381.0</th>
      <td>5</td>
      <td>5</td>
      <td>1845.31</td>
      <td>5</td>
      <td>4</td>
      <td>4</td>
      <td>54</td>
      <td>champions</td>
    </tr>
    <tr>
      <th>12417.0</th>
      <td>4</td>
      <td>9</td>
      <td>3649.10</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>55</td>
      <td>champions</td>
    </tr>
  </tbody>
</table>
</div>




```python
rfm[rfm["SEGMENT"] == "champions"].describe().T
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>count</th>
      <th>mean</th>
      <th>std</th>
      <th>min</th>
      <th>25%</th>
      <th>50%</th>
      <th>75%</th>
      <th>max</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>recency</th>
      <td>633.0</td>
      <td>6.361769</td>
      <td>3.683300</td>
      <td>1.00</td>
      <td>3.00</td>
      <td>5.00</td>
      <td>10.00</td>
      <td>13.00</td>
    </tr>
    <tr>
      <th>frequency</th>
      <td>633.0</td>
      <td>12.413902</td>
      <td>16.451672</td>
      <td>3.00</td>
      <td>5.00</td>
      <td>8.00</td>
      <td>14.00</td>
      <td>209.00</td>
    </tr>
    <tr>
      <th>monetary</th>
      <td>633.0</td>
      <td>6857.963918</td>
      <td>20339.763842</td>
      <td>201.12</td>
      <td>1451.28</td>
      <td>2612.96</td>
      <td>4954.84</td>
      <td>280206.02</td>
    </tr>
  </tbody>
</table>
</div>



**Action Suggestions**
*   When we add new products to our catalog >> let's send a message saying "hello handsome/beautiful I have something new for you"
*   will apply a discount from us to their last purchase when they reach the specific purchase. >>
*   If they bring us new customers, we can prepare special campaigns for them >>
---

**loyal_customers** Our loyal friends. They are in the 2nd most traded group
 but one of the most precious to us. In an important situation, they can be our airbag.
*  So they seem to have certain habits we can trust.
*  As an average of us; They earned 2864.24779 units of money
*  Frequency of visits 6.47985
*  They last shopped 33,60806 days ago.


```python
rfm[rfm["SEGMENT"] == "loyal_customers"].head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>recency</th>
      <th>frequency</th>
      <th>monetary</th>
      <th>recency_score</th>
      <th>frequency_score</th>
      <th>monetary_score</th>
      <th>RFM_SCORE</th>
      <th>SEGMENT</th>
    </tr>
    <tr>
      <th>Customer ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>12352.0</th>
      <td>37</td>
      <td>8</td>
      <td>2506.04</td>
      <td>3</td>
      <td>5</td>
      <td>5</td>
      <td>35</td>
      <td>loyal_customers</td>
    </tr>
    <tr>
      <th>12359.0</th>
      <td>58</td>
      <td>4</td>
      <td>6372.58</td>
      <td>3</td>
      <td>4</td>
      <td>5</td>
      <td>34</td>
      <td>loyal_customers</td>
    </tr>
    <tr>
      <th>12370.0</th>
      <td>52</td>
      <td>4</td>
      <td>3545.69</td>
      <td>3</td>
      <td>4</td>
      <td>5</td>
      <td>34</td>
      <td>loyal_customers</td>
    </tr>
    <tr>
      <th>12380.0</th>
      <td>22</td>
      <td>4</td>
      <td>2724.81</td>
      <td>4</td>
      <td>4</td>
      <td>5</td>
      <td>44</td>
      <td>loyal_customers</td>
    </tr>
    <tr>
      <th>12388.0</th>
      <td>16</td>
      <td>6</td>
      <td>2780.66</td>
      <td>4</td>
      <td>4</td>
      <td>5</td>
      <td>44</td>
      <td>loyal_customers</td>
    </tr>
  </tbody>
</table>
</div>




```python
rfm[rfm["SEGMENT"] == "loyal_customers"].describe().T
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>count</th>
      <th>mean</th>
      <th>std</th>
      <th>min</th>
      <th>25%</th>
      <th>50%</th>
      <th>75%</th>
      <th>max</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>recency</th>
      <td>819.0</td>
      <td>33.608059</td>
      <td>15.577050</td>
      <td>15.00</td>
      <td>20.000</td>
      <td>30.00</td>
      <td>44.000</td>
      <td>72.00</td>
    </tr>
    <tr>
      <th>frequency</th>
      <td>819.0</td>
      <td>6.479853</td>
      <td>4.545669</td>
      <td>3.00</td>
      <td>4.000</td>
      <td>5.00</td>
      <td>8.000</td>
      <td>63.00</td>
    </tr>
    <tr>
      <th>monetary</th>
      <td>819.0</td>
      <td>2864.247791</td>
      <td>6007.061883</td>
      <td>36.56</td>
      <td>991.795</td>
      <td>1740.48</td>
      <td>3052.905</td>
      <td>124914.53</td>
    </tr>
  </tbody>
</table>
</div>



**Action Suggestions**
* We can define short-term discounts on the products they buy most often 
* If they bring us new customers, we can prepare special campaigns for them 
---

**at risk** Woowo we haven't seen these friends for a long time, let's get their attention a little bit
*  As an average of us; They earned 1084.53530 units of money
*  Frequency of visits 2.87858
*  They last shopped 153.78583 days ago.


```python
rfm[rfm["SEGMENT"] == "at_Risk"].head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>recency</th>
      <th>frequency</th>
      <th>monetary</th>
      <th>recency_score</th>
      <th>frequency_score</th>
      <th>monetary_score</th>
      <th>RFM_SCORE</th>
      <th>SEGMENT</th>
    </tr>
    <tr>
      <th>Customer ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>12348.0</th>
      <td>76</td>
      <td>4</td>
      <td>1797.24</td>
      <td>2</td>
      <td>4</td>
      <td>4</td>
      <td>24</td>
      <td>at_Risk</td>
    </tr>
    <tr>
      <th>12383.0</th>
      <td>185</td>
      <td>5</td>
      <td>1850.56</td>
      <td>1</td>
      <td>4</td>
      <td>4</td>
      <td>14</td>
      <td>at_Risk</td>
    </tr>
    <tr>
      <th>12393.0</th>
      <td>73</td>
      <td>4</td>
      <td>1582.60</td>
      <td>2</td>
      <td>4</td>
      <td>4</td>
      <td>24</td>
      <td>at_Risk</td>
    </tr>
    <tr>
      <th>12399.0</th>
      <td>120</td>
      <td>4</td>
      <td>1108.65</td>
      <td>2</td>
      <td>4</td>
      <td>4</td>
      <td>24</td>
      <td>at_Risk</td>
    </tr>
    <tr>
      <th>12409.0</th>
      <td>79</td>
      <td>3</td>
      <td>11072.67</td>
      <td>2</td>
      <td>3</td>
      <td>5</td>
      <td>23</td>
      <td>at_Risk</td>
    </tr>
  </tbody>
</table>
</div>




```python
rfm[rfm["SEGMENT"] == "at_Risk"].describe().T
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>count</th>
      <th>mean</th>
      <th>std</th>
      <th>min</th>
      <th>25%</th>
      <th>50%</th>
      <th>75%</th>
      <th>max</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>recency</th>
      <td>593.0</td>
      <td>153.785835</td>
      <td>68.618828</td>
      <td>73.0</td>
      <td>96.00</td>
      <td>139.00</td>
      <td>195.00</td>
      <td>374.0</td>
    </tr>
    <tr>
      <th>frequency</th>
      <td>593.0</td>
      <td>2.876897</td>
      <td>0.951540</td>
      <td>2.0</td>
      <td>2.00</td>
      <td>3.00</td>
      <td>3.00</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>monetary</th>
      <td>593.0</td>
      <td>1084.535297</td>
      <td>2562.073355</td>
      <td>52.0</td>
      <td>412.78</td>
      <td>678.25</td>
      <td>1200.62</td>
      <td>44534.3</td>
    </tr>
  </tbody>
</table>
</div>



**Action Suggestions**
* "Hey "NAME" sir/ma'am, we missed you so much. Let's send you an e-mail if you want to take a look at our special offers. 
* They didn't bring us bad income. Let's remind ourselves of them. We can inform you about existing campaigns. 
