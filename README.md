# EDA_Waykitech
Analyzed social media Data collected by Waykitech Viewers for the year 2023 - 2024
## Overview
We will analyze LinkedIn social media Data collected from 2023 to 2024. To conduct this exploratory Data analysis (EDA), we will:
- Extract relevant Data from the LinkedIn account for the years 2023 and 2024.
- Identify patterns in views and interactions by examining whether certain days of the week saw higher activity.
- Calculate the percentage of total annual views that occurred each month.
- Visualize the results using a bar chart.
## Import packages and libraries
Before getting started, we will need to import all the required libraries and extensions. Throughout the course, we will be using pandas for operations, and matplotlib and seaborn for plotting.
```r
# Import statements
import pandas as pd
import numpy as np
import seaborn as sns
import datetime
from matplotlib import pyplot as plt
```
## Organize the Data
Note: This Dataset is a sample extract for practice purposes and can be used to run your analysis.
```r
# Read in the 2023-2024 Data.
df = pd.read_csv('Date_Viewers.csv') 
df.head()
```
| Date       | Total Unique Visitors (Desktop) | Total Unique Visitors (Mobile) | Total Unique Visitors (Total) |
|------------|---------------------------------|--------------------------------|-------------------------------|
| 10/15/2023 | 1                               | 0                              | 1                             |
| 10/16/2023 | 0                               | 0                              | 0                             |
| 10/17/2023 | 0                               | 1                              | 1                             |
| 10/18/2023 | 2                               | 1                              | 3                             |
| 10/19/2023 | 2                               | 0                              | 2                             |

Start by converting the Date column to datetime.
```r
# Convert the `Date` column to datetime.
df['Date'] = pd.to_datetime(df['Date'])
```
Now, let's check the shape of the Dataframe.
```r
df.shape
```
As result we got (365, 4), Considering from 10/15/2023 to 10/13/2024

Check for duplicates. If the shape of the Data is different after running this code, you will know there were duplicate rows.
```r
df.drop_duplicates().shape
```
We keep the same ammount (365, 4), Perceiving no duplicates.

## Visitors with most views in a single day
To identify the viewers with the most views in a single day, we will sort the `Total unique visitors (total)` column in descending value, or by most to least views.
```r
# Sort by number of views in descending order.
df.sort_values(by='Total unique visitors (total)', ascending=False).head(10)
```
| Index | Date       | Total Unique Visitors (Desktop) | Total Unique Visitors (Mobile) | Total Unique Visitors (Total) |
|-------|------------|---------------------------------|--------------------------------|-------------------------------|
| 93    | 2024-01-16 | 27                              | 89                             | 113                           |
| 94    | 2024-01-17 | 17                              | 49                             | 65                            |
| 335   | 2024-09-14 | 16                              | 11                             | 27                            |
| 326   | 2024-09-05 | 3                               | 11                             | 14                            |
| 101   | 2024-01-24 | 9                               | 6                              | 14                            |
| 338   | 2024-09-17 | 3                               | 10                             | 13                            |
| 109   | 2024-02-01 | 4                               | 9                              | 13                            |
| 95    | 2024-01-18 | 4                               | 9                              | 13                            |
| 325   | 2024-09-04 | 6                               | 6                              | 12                            |
| 354   | 2024-10-03 | 3                               | 9                              | 12                            |

## Views by day of week
To calculate this, we will take advantage of the fact that the Data in our Date column is of the `datetime` class. Because these entries are datetime objects, we can extract Date-related information from them and create new columns.

First, we will create a column called `week` using `dt.isocalendar()` on the `Date` column. This function is designed to be used on a pandas series, and it will return a new Dataframe with year, week, and day columns. The information is formatted numerically; for example, January 3, 1950, would be represented as:
| Year | Week | Day |
|------|------|-----|
| 1950 | 1    | 3   |

Because we only want to extract the week number, we will add `.week` to the end.

```r
# Create two new columns.
df['week'] = df.Date.dt.isocalendar().week
df['weekday'] = df.Date.dt.day_name()
df.head()
```
| Date       | Total Unique Visitors (Desktop) | Total Unique Visitors (Mobile) | Total Unique Visitors (Total) | Week | Weekday   |
|------------|---------------------------------|--------------------------------|-------------------------------|------|-----------|
| 2023-10-15 | 1                               | 0                              | 1                             | 41   | Sunday    |
| 2023-10-16 | 0                               | 0                              | 0                             | 42   | Monday    |
| 2023-10-17 | 0                               | 1                              | 1                             | 42   | Tuesday   |
| 2023-10-18 | 2                               | 1                              | 3                             | 42   | Wednesday |
| 2023-10-19 | 2                               | 0                              | 2                             | 42   | Thursday  |

Now, we can calculate the mean number of views for each weekday of the year. We will use the `groupby()` function to do this.
```r
# Calculate the mean count of views for each weekday.
df[['weekday','Total unique visitors (total)']].groupby(['weekday']).mean()
```
| Weekday   | Total Unique Visitors (Average) |
|-----------|---------------------------------|
| Friday    | 3.480769                       |
| Monday    | 3.326923                       |
| Saturday  | 2.615385                       |
| Sunday    | 1.773585                       |
| Thursday  | 4.134615                       |
| Tuesday   | 6.000000                       |
| Wednesday | 5.442308                       |

It seems that Saturday and Sunday have fewer views on average than the other five weekdays. We will plot the distributions of the view counts for each day of the week. We want each distribution to be represented as a boxplot.

We will begin by defining the order of the days, starting with Monday and ending with Sunday. This is how the days will be ordered in the plot we create.
```r
# Define order of days for the plot.
weekday_order = ['Monday','Tuesday', 'Wednesday', 'Thursday','Friday','Saturday','Sunday']
```
Now, we will code the plot. Remember that `showfliers` is the parameter that controls whether or not outliers are displayed in the plot. If we input `True`, outliers are included; if we input `False`, outliers are left off of the box plot. Keep in mind, we aren’t deleting any outliers from the dataset when we create this chart—we are only excluding them from the visualization.
```r
# Create boxplots of view counts for each day of week.
g = sns.boxplot(data=df, 
            x='weekday',
            y='Total unique visitors (total)', 
            order=weekday_order, 
            showfliers=False 
            );
g.set_title('Views distribution per weekday (2023-2024)');
```
![Alt text](https://github.com/FrankCoRa/EDA_Waykitech/blob/main/Boxplot_Views.png)

Notice that the median remains different on all of the days of the week. As for Saturday and Sunday, however, the distributions are both lower than they are during the rest of the week. We also know that the mean numbers of views that occurred on Saturday and Sunday were lower than on the other weekdays. Why might this be? Perhaps people prefers to spend more time with the family. In the U.S., Saturday and Sunday are days that many people don't work, so there may be fewer people  on their mobiles or computers. This is only speculation, but it's one possible path for further exploration.

## Monthly Views 2023–2024
Finally, we will examine monthly visitor's views data from 2023–2024. We will calculate the total views for each year that occurred in a given month. We will then plot this data on a bar graph.

To help us name the bars of the bar plot, we will create three new columns that isolate the year, month number, and month name.
```r
# Add 3 new columns.
df['year'] = union_df.date.dt.year
df['month'] = union_df.date.dt.month
df['month_txt'] = union_df.date.dt.month_name()
df.head()
```
To simplify the results of our combined dataframe, we will `drop` these added columns. Note that the following code doesn't permanently modify df.

| Date       | Total Unique Visitors (Desktop) | Total Unique Visitors (Mobile) | Total Unique Visitors (Total) | year | month | month_txt |
|------------|---------------------------------|--------------------------------|-------------------------------|------|-------|--------------|
| 2023-10-15 | 1                               | 0                              | 1                             | 2023 | 10    | October      |
| 2023-10-16 | 0                               | 0                              | 0                             | 2023 | 10    | October      |
| 2023-10-17 | 0                               | 1                              | 1                             | 2023 | 10    | October      |
| 2023-10-18 | 2                               | 1                              | 3                             | 2023 | 10    | October      |
| 2023-10-19 | 2                               | 0                              | 2                             | 2023 | 10    | October      |

Let's check the overall view count for each year.
```r
# Calculate total number of views per year
df[['Total unique visitors (total)','year']].groupby(['year']).sum()
```
| Year | Total Unique Visitors (Total) |
|------|-------------------------------|
| 2023 | 42                            |
| 2024 | 1352                          |

Now we can plot the views by month in a bar graph.
```r
plt.figure(figsize=(12, 6))

# Ensure month_order matches the format of the 'month_txt' column in df
month_order = ['January', 'February', 'March', 'April', 'May', 'June', 
               'July', 'August', 'September', 'October', 'November', 'December']

# Create the bar plot and remove error bars, assigning it to 'ax'
ax = sns.barplot(
    data=df2,
    x='month_txt',
    y='Total unique visitors (total)',
    hue='year',
    order=month_order,
    errorbar=None  # This removes the error bars
)

# Add labels and title
plt.xlabel("Month")
plt.ylabel("Number of Unique Visitors")
plt.title("Number of Unique Visitors Each Month (2023-2024)")

# Adding value labels on top of each bar
for container in ax.containers:
    ax.bar_label(container, fmt='%.0f', label_type='edge')  # Add value labels

# Display the plot
plt.show()
```
![Alt text](https://github.com/FrankCoRa/EDA_Waykitech/blob/main/Barchart_Views.png)

Over the past two years, we observed that the Company’s website marketing activities and posts began in October 2023. One standout month is January 2024, which accounted for more than 50% of the total views compared to the other months in 2024.
