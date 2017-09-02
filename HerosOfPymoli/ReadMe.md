
# Heros Of Pymoli Data Analysis:
- Male players purchased more than four times more than female and non-specified gender players combined.
- 19 -22 year olds purchased the most
- Players in the 39 - 42 year old range spent approximately 1.5 times the amount that players in the 19 - 22 year range spent.
- Item price did not negatively impact purchasing decision. In fact, the highest priced item (Orenmir, $4.95), was one of the most profitable items.


```python
import pandas as pd
import numpy as np
```


```python
file = "resources/purchase_data.json"
df = pd.read_json(file)
df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Age</th>
      <th>Gender</th>
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Price</th>
      <th>SN</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>38</td>
      <td>Male</td>
      <td>165</td>
      <td>Bone Crushing Silver Skewer</td>
      <td>3.37</td>
      <td>Aelalis34</td>
    </tr>
    <tr>
      <th>1</th>
      <td>21</td>
      <td>Male</td>
      <td>119</td>
      <td>Stormbringer, Dark Blade of Ending Misery</td>
      <td>2.32</td>
      <td>Eolo46</td>
    </tr>
    <tr>
      <th>2</th>
      <td>34</td>
      <td>Male</td>
      <td>174</td>
      <td>Primitive Blade</td>
      <td>2.46</td>
      <td>Assastnya25</td>
    </tr>
    <tr>
      <th>3</th>
      <td>21</td>
      <td>Male</td>
      <td>92</td>
      <td>Final Critic</td>
      <td>1.36</td>
      <td>Pheusrical25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>23</td>
      <td>Male</td>
      <td>63</td>
      <td>Stormfury Mace</td>
      <td>1.27</td>
      <td>Aela59</td>
    </tr>
  </tbody>
</table>
</div>



##### Total Number of Players


```python
#total number of players
# this should be unique count of SN (Screen Name)
players = pd.DataFrame({"Unique Player Count":[df["SN"].nunique()]})
players
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Unique Player Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>573</td>
    </tr>
  </tbody>
</table>
</div>



##### Purchasing Analysis (Total)


```python
# Number of Unique Items
itemCount = df["Item ID"].nunique()

# Average Purchase Price
avgPrice = df["Price"].mean()
avgPrice = "${:.2f}".format(avgPrice)

# Total Number of Purchases
purchaseCount = df["Item ID"].count()

# Total Revenue
totalRevenue = df["Price"].sum()
totalRevenue = "${:,.2f}".format(totalRevenue)

# let's put all these in a new table
analysis = pd.DataFrame(
    {"Number of Unique Items":[itemCount], 
     "Average Purchase Price":[avgPrice],
     "Total Number of Purchases":[purchaseCount],
     "Total Revenue":[totalRevenue]}
)
analysis
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Purchase Price</th>
      <th>Number of Unique Items</th>
      <th>Total Number of Purchases</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>$2.93</td>
      <td>183</td>
      <td>780</td>
      <td>$2,286.33</td>
    </tr>
  </tbody>
</table>
</div>



##### Gender Demographics


```python
gender = df.groupby("Gender")["SN"].nunique().to_frame("Unique Player Count")
gender["Percentage"] = (gender["Unique Player Count"]/gender["Unique Player Count"].sum()) * 100
gender["Percentage"] = gender["Percentage"].apply("{:.2f}%".format)
gender
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Unique Player Count</th>
      <th>Percentage</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>100</td>
      <td>17.45%</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>465</td>
      <td>81.15%</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>8</td>
      <td>1.40%</td>
    </tr>
  </tbody>
</table>
</div>



##### Purchasing Analysis (Gender)


```python
# Purchase Count
genderPurchaseCount = df.groupby("Gender")["Item ID"].count().to_frame("Purchase Count")
# Average Purchase Price
genderAveragePurchasePrice = df.groupby("Gender")["Price"].mean().to_frame("Average Purchase Price")
# Total Purchase Value
genderTotalPurchaseAmount = df.groupby("Gender")["Price"].sum().to_frame("Total Purchase Amount")
# Normalized Totals
genderUniquePlayers = df.groupby("Gender")["SN"].nunique().to_frame("Player Count")
# join the tpa (total purchase amount) and the up (unique players) for additional manipulation
genderNormalizedAverage = genderTotalPurchaseAmount.merge(genderUniquePlayers, left_index=True, right_index=True)
genderNormalizedAverage["Normalized Average"] = genderNormalizedAverage["Total Purchase Amount"]/genderNormalizedAverage["Player Count"] 

# merge all the data
genderMerged = genderPurchaseCount.merge(genderAveragePurchasePrice, left_index=True, right_index=True).merge(genderNormalizedAverage, left_index=True, right_index=True) 

# format the dollar amounts
genderMerged["Average Purchase Price"] = genderMerged["Average Purchase Price"].apply("${:.2f}".format)
genderMerged["Total Purchase Amount"] = genderMerged["Total Purchase Amount"].apply("${:.2f}".format)
genderMerged["Normalized Average"] = genderMerged["Normalized Average"].apply("${:.2f}".format)
genderMerged
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Amount</th>
      <th>Player Count</th>
      <th>Normalized Average</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>136</td>
      <td>$2.82</td>
      <td>$382.91</td>
      <td>100</td>
      <td>$3.83</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>633</td>
      <td>$2.95</td>
      <td>$1867.68</td>
      <td>465</td>
      <td>$4.02</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>11</td>
      <td>$3.25</td>
      <td>$35.74</td>
      <td>8</td>
      <td>$4.47</td>
    </tr>
  </tbody>
</table>
</div>



##### Purchasing Analysis (Age)


```python
# create a range to use as bins of age groups of 4
# add 4 to max to ensure we get highest age bracket/bin
ageBins = np.arange(df.Age.min(),df["Age"].max()+4,4)
ageBinLabels = []
binLength = len(str(ageBins.max()))
mask = binLength * "0"

for index, a in enumerate(ageBins):
    if index != len(ageBins)-1:
        ageBinLabels.append((mask + str(a))[-binLength:] + " - " + str((int(a+3))))
df["AgeBin"] = pd.cut(df["Age"],ageBins, right=False, include_lowest=True)
df["AgeBinLabel"] = pd.cut(df["Age"],ageBins, right=False, include_lowest=True, labels=ageBinLabels)

# Purchase Count
agePurchaseCount = df.groupby("AgeBinLabel")["Item ID"].count().to_frame("Purchase Count")

# Average Purchase Price
ageAveragePurchasePrice = df.groupby("AgeBinLabel")["Price"].mean().to_frame("Average Purchase Price")

# Total Purchase Value
ageTotalPurchaseAmount = df.groupby("AgeBinLabel")["Price"].sum().to_frame("Total Purchase Amount")

# Normalized Totals
#get the unique number of purchasers for each age bin
ageUniquePlayers = df.groupby("AgeBinLabel")["SN"].nunique().to_frame("Player Count")

ageUniquePlayers["Percentage of Players"] = (ageUniquePlayers["Player Count"]/ageUniquePlayers["Player Count"].sum()) * 100


#join the total purchase amount and unique players to get the normalized average price
ageNormalizedAverage = ageTotalPurchaseAmount.merge(ageUniquePlayers, left_index=True, right_index=True)
#add a column and populate normalized average
ageNormalizedAverage["Normalized Average"] = ageNormalizedAverage["Total Purchase Amount"]/ageNormalizedAverage["Player Count"]

#merge all the data
ageMerged = agePurchaseCount.merge(ageAveragePurchasePrice, left_index=True, right_index=True).merge(ageNormalizedAverage, left_index=True, right_index=True)
#format the columns
ageMerged["Average Purchase Price"] = ageMerged["Average Purchase Price"].apply("${:.2f}".format)
ageMerged["Total Purchase Amount"] = ageMerged["Total Purchase Amount"].apply("${:.2f}".format)
ageMerged["Normalized Average"] = ageMerged["Normalized Average"].apply("${:.2f}".format) 
ageMerged["Percentage of Players"] = ageMerged["Percentage of Players"].apply("{:.2f}%".format)
ageMerged
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Amount</th>
      <th>Player Count</th>
      <th>Percentage of Players</th>
      <th>Normalized Average</th>
    </tr>
    <tr>
      <th>AgeBinLabel</th>
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
      <th>07 - 10</th>
      <td>32</td>
      <td>$3.02</td>
      <td>$96.62</td>
      <td>22</td>
      <td>3.84%</td>
      <td>$4.39</td>
    </tr>
    <tr>
      <th>11 - 14</th>
      <td>31</td>
      <td>$2.70</td>
      <td>$83.79</td>
      <td>20</td>
      <td>3.49%</td>
      <td>$4.19</td>
    </tr>
    <tr>
      <th>15 - 18</th>
      <td>111</td>
      <td>$2.88</td>
      <td>$319.32</td>
      <td>84</td>
      <td>14.66%</td>
      <td>$3.80</td>
    </tr>
    <tr>
      <th>19 - 22</th>
      <td>231</td>
      <td>$2.93</td>
      <td>$676.20</td>
      <td>178</td>
      <td>31.06%</td>
      <td>$3.80</td>
    </tr>
    <tr>
      <th>23 - 26</th>
      <td>207</td>
      <td>$2.94</td>
      <td>$608.02</td>
      <td>153</td>
      <td>26.70%</td>
      <td>$3.97</td>
    </tr>
    <tr>
      <th>27 - 30</th>
      <td>63</td>
      <td>$2.98</td>
      <td>$187.99</td>
      <td>44</td>
      <td>7.68%</td>
      <td>$4.27</td>
    </tr>
    <tr>
      <th>31 - 34</th>
      <td>46</td>
      <td>$3.07</td>
      <td>$141.24</td>
      <td>34</td>
      <td>5.93%</td>
      <td>$4.15</td>
    </tr>
    <tr>
      <th>35 - 38</th>
      <td>37</td>
      <td>$2.81</td>
      <td>$104.06</td>
      <td>25</td>
      <td>4.36%</td>
      <td>$4.16</td>
    </tr>
    <tr>
      <th>39 - 42</th>
      <td>20</td>
      <td>$3.13</td>
      <td>$62.56</td>
      <td>11</td>
      <td>1.92%</td>
      <td>$5.69</td>
    </tr>
    <tr>
      <th>43 - 46</th>
      <td>2</td>
      <td>$3.26</td>
      <td>$6.53</td>
      <td>2</td>
      <td>0.35%</td>
      <td>$3.26</td>
    </tr>
  </tbody>
</table>
</div>



##### Top Spenders


```python
# Get the screen names of the 5 players who purchased the most
topSpenders = df.groupby("SN")["Price"].sum().nlargest(5).to_frame("Total Purchase Amount")
#merge withe full data set to see all purchases
topSpendersMerge = topSpenders.merge(df, left_index=True, right_on="SN")

# SN
# determined in initial query of top spenders
# Purchase Count
topSpendersPurchaseCount = topSpendersMerge.groupby("SN")["Item ID"].count().to_frame("Purchase Count")
# Average Purchase Price
topSpendersAveragePurchasePrice = topSpendersMerge.groupby("SN")["Price"].mean().to_frame("Average Purchase Price")
# Total Purchase Value
#calculated above to determine top spender

#merge data together
topSpendersAnalysis = topSpenders.merge(topSpendersPurchaseCount, left_index=True, right_index=True).merge(topSpendersAveragePurchasePrice, left_index=True, right_index=True)
#reorder the columns
cols = ["Purchase Count","Average Purchase Price","Total Purchase Amount"]
topSpendersAnalysis = topSpendersAnalysis[cols]
topSpendersAnalysis["Average Purchase Price"] = topSpendersAnalysis["Average Purchase Price"].map("${:.2f}".format)
topSpendersAnalysis["Total Purchase Amount"] = topSpendersAnalysis["Total Purchase Amount"].map("${:.2f}".format)
topSpendersAnalysis
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Amount</th>
    </tr>
    <tr>
      <th>SN</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Undirrala66</th>
      <td>5</td>
      <td>$3.41</td>
      <td>$17.06</td>
    </tr>
    <tr>
      <th>Saedue76</th>
      <td>4</td>
      <td>$3.39</td>
      <td>$13.56</td>
    </tr>
    <tr>
      <th>Mindimnya67</th>
      <td>4</td>
      <td>$3.18</td>
      <td>$12.74</td>
    </tr>
    <tr>
      <th>Haellysu29</th>
      <td>3</td>
      <td>$4.24</td>
      <td>$12.73</td>
    </tr>
    <tr>
      <th>Eoda93</th>
      <td>3</td>
      <td>$3.86</td>
      <td>$11.58</td>
    </tr>
  </tbody>
</table>
</div>



##### Most Popular Items


```python
#Note: Different items have the same name - There was a 4-way tie for 2nd most popular item, so pulled 6 most popular items
mostPopular = df.groupby("Item ID")["Item ID"].count().nlargest(6).to_frame("Purchase Count")

# join to the raw data
mostPopularMerged = mostPopular.merge(df, left_index=True, right_on="Item ID")
#set the index to assist in removing duplicates
mostPopularIndexed = mostPopularMerged.set_index("Item ID")

# Identify the 5 most popular items by purchase count, then list (in a table):
# Item ID
# Item Name
# Purchase Count
# Item Price
# we have these values
mostPopularIndexed.loc[:,["Item Name","Purchase Count","Price"]].drop_duplicates()

# Total Purchase Value - use the sum of the item from the df with all purchases
mostPopularTotalPurchaseAmount = mostPopularIndexed.groupby("Item ID")["Price"].sum().to_frame("Total Purchase Value")

#merge the most popular with the total purchase value data
mostPopularAnalysis = mostPopularIndexed.merge(mostPopularTotalPurchaseAmount, left_index=True, right_index=True)

# select only the columns we want to display and eliminate duplicates
mostPopularAnalysis = mostPopularAnalysis.loc[:,["Item Name","Purchase Count","Price","Total Purchase Value"]].drop_duplicates().sort_values(by=["Purchase Count"], ascending=False)
mostPopularAnalysis.columns=["Item Name","Purchase Count","Item Price","Total Purchase Value"]
mostPopularAnalysis["Item Price"] = mostPopularAnalysis["Item Price"].apply("${:.2f}".format)
mostPopularAnalysis["Total Purchase Value"] = mostPopularAnalysis["Total Purchase Value"].apply("${:.2f}".format)
mostPopularAnalysis
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Item Name</th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>39</th>
      <td>Betrayal, Whisper of Grieving Widows</td>
      <td>11</td>
      <td>$2.35</td>
      <td>$25.85</td>
    </tr>
    <tr>
      <th>84</th>
      <td>Arcane Gem</td>
      <td>11</td>
      <td>$2.23</td>
      <td>$24.53</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Serenity</td>
      <td>9</td>
      <td>$1.49</td>
      <td>$13.41</td>
    </tr>
    <tr>
      <th>31</th>
      <td>Trickster</td>
      <td>9</td>
      <td>$2.07</td>
      <td>$18.63</td>
    </tr>
    <tr>
      <th>34</th>
      <td>Retribution Axe</td>
      <td>9</td>
      <td>$4.14</td>
      <td>$37.26</td>
    </tr>
    <tr>
      <th>175</th>
      <td>Woeful Adamantite Claymore</td>
      <td>9</td>
      <td>$1.24</td>
      <td>$11.16</td>
    </tr>
  </tbody>
</table>
</div>



##### Most Profitable Items


```python
# Identify the 5 most profitable items by total purchase value, then list (in a table):

mostProfitable = df.groupby("Item ID")["Price"].sum().nlargest(5).to_frame("Total Purchase Value")
mostProfitableMerged = mostProfitable.merge(df, left_index=True, right_on="Item ID")
# set the index to help with deduping
mostProfitableIndexed = mostProfitableMerged.set_index("Item ID")
# Item ID
# Item Name
# Purchase Count
# Item Price
# Total Purchase Value
mostProfitableIndexed["Purchase Count"] = mostProfitableIndexed.groupby("Item ID")["Item Name"].count()

mostProfitableIndexed = mostProfitableIndexed.loc[:,["Item Name","Purchase Count","Price","Total Purchase Value"]].drop_duplicates()
mostProfitableIndexed.columns = ["Item Name","Purchase Count","Item Price","Total Purchase Value"]
mostProfitableIndexed["Item Price"] = mostProfitableIndexed["Item Price"].apply("${:.2f}".format)
mostProfitableIndexed["Total Purchase Value"] = mostProfitableIndexed["Total Purchase Value"].apply("${:.2f}".format)
mostProfitableIndexed.sort_values(["Total Purchase Value"], ascending=False)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Item Name</th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>34</th>
      <td>Retribution Axe</td>
      <td>9</td>
      <td>$4.14</td>
      <td>$37.26</td>
    </tr>
    <tr>
      <th>115</th>
      <td>Spectral Diamond Doomblade</td>
      <td>7</td>
      <td>$4.25</td>
      <td>$29.75</td>
    </tr>
    <tr>
      <th>32</th>
      <td>Orenmir</td>
      <td>6</td>
      <td>$4.95</td>
      <td>$29.70</td>
    </tr>
    <tr>
      <th>103</th>
      <td>Singed Scalpel</td>
      <td>6</td>
      <td>$4.87</td>
      <td>$29.22</td>
    </tr>
    <tr>
      <th>107</th>
      <td>Splitter, Foe Of Subtlety</td>
      <td>8</td>
      <td>$3.61</td>
      <td>$28.88</td>
    </tr>
  </tbody>
</table>
</div>


