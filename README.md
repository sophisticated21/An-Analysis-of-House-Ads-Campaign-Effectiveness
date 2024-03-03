
# An Analysis of House Ads Campaign Effectiveness

## Business Problem
Our company is concerned about the performance of its marketing campaigns during January 2018, specifically whether there was a decrease in conversion rates throughout the month. The key questions are: Was there a decline in conversion rates? If so, what factors contributed to this decrease, and what were the underlying causes?

__What is Conversion Rate?__  
The conversion rate is a key performance indicator (KPI) in digital marketing that measures the effectiveness of a marketing campaign. It is defined as the percentage of users who take a desired action out of the total number of visitors. The desired action can vary depending on the campaign's objectives, such as making a purchase, signing up for a newsletter, or downloading a software trial.
```
Conversion Rate = (Number of Conversions / Total Number of Visitors)*100
```  
```
Let's consider a hypothetical scenario: Imagine that during January 2018, our company launched a new online advertisement campaign aiming to increase the number of sign-ups for our newsletter.

Here are the details:

Total visitors to the landing page: 50,000
Total number of sign-ups: 2,500

In this context, a "conversion" can be any action that you want the user to perform, such as making a purchase, while "visitors" refers to the people who have been exposed to your campaign.
```


## Project Overview

In this project, we performed a comprehensive analysis of our marketing campaign data to identify trends and issues affecting our conversion rates during January 2018. Here's a summary of the steps we took and the findings from our investigation:
- [1. Data Retrieval](#data-retrieval) We extracted the relevant data using SQL queries from a PostgreSQL database, ensuring we had all necessary information for a thorough analysis.
- [2. Initial Exploration and Data Cleaning](#initial-exploration-and-data-cleaning): Using SQL, we conducted an initial exploration of the data to understand the overall trends and patterns, setting the stage for a deeper analysis. Then, we focused on cleaning the date columns to ensure consistency and accuracy in our dataset, which was crucial for time-based analysis.
- [3. Data Handling and Visualization](#data-handling-and-visualization): By using Python, We initiated by establishing interaction with the SQL Database, importing necessary libraries for secure access, and verifying the connection using SQLAlchemy. Subsequently, we managed the data by importing essential manipulation libraries, loading data into a Pandas DataFrame, and analyzing its structure including duplicates and missing values. Finally, we visualized insights through Matplotlib, analyzing categorical columns, illustrating distributions with pie charts, and observing daily user trends by grouping data and plotting.
- [4. Conversion Rate Analysis](#conversion-rate-analysis): We calculated the conversion rates for various groups within our data. By segmenting the data, we were able to identify specific trends and patterns that were not immediately apparent from the overall data.
- [5. Identifying Key Issues](#identifying-key-issues): Our analysis revealed a significant drop in the conversion rate for the 'House_Ads' segment during the month. This prompted a more detailed investigation to understand the underlying causes. We hypothesized that the mismatch between the preferred language of the audience and the language used in the advertisements could be causing the drop in conversion rates. By comparing the data before and after the identified drop, we confirmed that there was a significant impact due to the language discrepancy.
- [Reporting Findings] (ongoing): We compiled our findings, insights, and recommendations into a comprehensive report. This included a detailed explanation of the methodology, analysis, results, and suggested actions to address the identified issues.
- [Visualization and Reporting with PowerBI] (ongoing): To make our findings more accessible and understandable to stakeholders, we visualized the key data points and analysis results using PowerBI. T

## Data Retrieval
In simple terms, the data retrieval process involves two steps:
- Creating a Table: We define a structure in the PostgreSQL database to hold our marketing data, specifying what type of information each column contains (like text, dates, or true/false values).
- Importing Data: We take a CSV file from github, which contains marketing data from DataCamp, and insert its contents into the database table we just created. This makes the data ready for analysis and querying within the database.
```
CREATE TABLE marketing (
    user_id VARCHAR(255),
    date_served DATE,
    marketing_channel VARCHAR(255),
    variant VARCHAR(255),
    converted BOOLEAN,
    language_displayed VARCHAR(255),
    language_preferred VARCHAR(255),
    age_group VARCHAR(255),
    date_subscribed DATE,
    date_canceled DATE,
    subscribing_channel VARCHAR(255),
    is_retained BOOLEAN
);
```
```
COPY marketing FROM 'https://raw.githubusercontent.com/sophisticated21/An-Analysis-of-House-Ads-Campaign-Effectiveness/main/Data/marketing.csv' DELIMITER ',' CSV HEADER;
```

## Initial Exploration and Data Cleaning 
In this part, we're doing initial exploration and cleaning of the marketing data:
- Viewing Data: We start by looking at the first three entries of the marketing table to understand what kind of data we're dealing with.
- Assessing Size: We then check the total number of rows and columns in our marketing table to see how large the dataset is.
- Removing Duplicates: Next, we create a new table called marketing_unique by removing any duplicate entries from the original marketing table, ensuring our data is unique for each record.
- Checking for Missing Values: We check for missing user IDs in the cleaned (unique) data.
- Analyzing Unique Values: Finally, we explore the unique values in each categorical column (like marketing_channel, variant, etc.) of the marketing_unique table to understand the diversity of our data. This helps us see how many different types of entries we have for each category.

```
SELECT * FROM marketing LIMIT 3;
```

```
Output
"a100000029"	"2018-01-01"	"House Ads"	"personalization"	true	"English"	"English"	"0-18 years"	"2018-01-01"
"a100000030"	"2018-01-01"	"House Ads"	"personalization"	true	"English"	"English"	"19-24 years"	"2018-01-01"
"a100000031"	"2018-01-01"	"House Ads"	"personalization"	true	"English"	"English"	"24-30 years"	"2018-01-01"
```

```
SELECT 'Total Rows' AS Type, COUNT(*) AS Count FROM marketing
UNION ALL
SELECT 'Total Columns' AS Type, COUNT(*) AS Count FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME = 'marketing';
```
```
Output:
"Total Rows"	10037
"Total Columns"	12
```

```
CREATE TABLE marketing_unique AS SELECT DISTINCT * FROM marketing;
```

```
SELECT 'Total Rows' AS Type, COUNT(*) AS Count FROM marketing_unique
UNION ALL
SELECT 'Total Columns' AS Type, COUNT(*) AS Count FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME = 'marketing_unique';
```

```
Output:
"Total Rows"	10000
"Total Columns"	12
```

```
SELECT
  COUNT(*) - COUNT(user_id) AS missing_user_id
FROM marketing_unique;
```

```
Output:
0
```

```
-- Unique values for Marketing_channel
SELECT 'marketing_channel' AS feature, CAST(marketing_channel AS TEXT) AS unique_values, COUNT(*) AS count
FROM marketing_unique
GROUP BY marketing_channel

UNION ALL

-- Unique values for Variant
SELECT 'variant' AS feature, CAST(variant AS TEXT) AS unique_values, COUNT(*) AS count
FROM marketing_unique
GROUP BY variant

UNION ALL

-- Unique values for Converted 
SELECT 'converted' AS feature, CAST(converted AS TEXT) AS unique_values, COUNT(*) AS count
FROM marketing_unique
GROUP BY converted

UNION ALL

-- Unique values for Language_displayed
SELECT 'language_displayed' AS feature, CAST(language_displayed AS TEXT) AS unique_values, COUNT(*) AS count
FROM marketing_unique
GROUP BY language_displayed

UNION ALL

-- Unique values for Language_preferred
SELECT 'language_preferred' AS feature, CAST(language_preferred AS TEXT) AS unique_values, COUNT(*) AS count
FROM marketing_unique
GROUP BY language_preferred

UNION ALL

-- Unique values for Age_group
SELECT 'age_group' AS feature, CAST(age_group AS TEXT) AS unique_values, COUNT(*) AS count
FROM marketing_unique
GROUP BY age_group

UNION ALL

-- Unique values for Subscribing_channel
SELECT 'subscribing_channel' AS feature, CAST(subscribing_channel AS TEXT) AS unique_values, COUNT(*) AS count
FROM marketing_unique
GROUP BY subscribing_channel

UNION ALL
```

```
-- Unique values for Is_retained
SELECT 'is_retained' AS feature, CAST(is_retained AS TEXT) AS unique_values, COUNT(*) AS count
FROM marketing_unique
GROUP BY is_retained;
```

```
Output:
"variant"	"personalization"	4931
"variant"	"control"	5069
"is_retained"	"false"	575
"is_retained"		8155
"is_retained"	"true"	1270
"converted"		15
"subscribing_channel"		8155
"subscribing_channel"	"Push"	167
"subscribing_channel"	"Instagram"	594
"subscribing_channel"	"Email"	286
"subscribing_channel"	"Facebook"	444
"subscribing_channel"	"House Ads"	354
"age_group"	"30-36 years"	1351
"age_group"	"55+ years"	1181
"age_group"	"0-18 years"	1534
"age_group"	"19-24 years"	1676
"age_group"	"36-45 years"	1350
"age_group"	"24-30 years"	1564
"age_group"	"45-55 years"	1344
"converted"	"false"	8911
"converted"	"true"	1074
"language_preferred"	"German"	166
"language_displayed"	"German"	81
"language_displayed"	"Spanish"	135
"language_displayed"	"Arabic"	27
"language_displayed"	"English"	9757
"marketing_channel"		15
"marketing_channel"	"Instagram"	1868
```  

## Data Handling and Visualization
We began by interacting with the SQL Database. Firstly, we imported necessary libraries to facilitate this interaction. Using the 'getpass' module, we securely acquired the username and password from the user. Utilizing SQLAlchemy, we established a connection to the PostgreSQL database and subsequently verified this connection.  

Following this, we moved on to handling the data. Initially, we imported essential data manipulation libraries such as Pandas and numerical libraries like NumPy. Subsequently, we loaded the 'marketing_unique' table from the database into a Pandas DataFrame. Creating a duplicate of this DataFrame allowed for safe data manipulation. We then examined the structure by displaying the first few entries and provided an overview including column and row counts, duplicates, and missing values.  

Finally, we visualized the data to gain insights. This involved setting up various visual configurations including colors and font sizes for plots. Utilizing Matplotlib, we created visual representations of our dataset. We analyzed categorical columns for unique values and presented them visually. Additionally, we created pie charts to illustrate the distribution of values within each categorical feature. Lastly, we grouped the data by 'date_served', counted unique 'user_id's per day, and plotted this data to observe the trend of daily users.

## Conversion Rate Analysis:
We initiated by defining functions crucial for Conversion Rate analysis. Firstly, we crafted the 'conversion_rate' function, designed to compute conversion rates based on specified groupings within a DataFrame, such as 'date_served' and 'age_group'. This function calculates the ratio of unique users who converted to the total unique users within each group. Additionally, we ensured any missing values in the conversion rate data were filled with zeros to prevent calculation errors or misinterpretations

Subsequently, we developed a plotting function named 'plotting_conv'. This versatile function iterates over each column of a DataFrame, generating line plots for each category. It encompasses setting up the figure size, labels, title, and limits, along with formatting the x-axis to display dates. Furthermore, we optimized readability by configuring the background and grid settings.  

We then applied the Conversion Rate analysis across various data groupings, including 'date_served', 'age_group', and 'marketing_channel'. This involved employing the 'conversion_rate' function to analyze how conversion rates varied across different segments. The results were converted into DataFrames for ease of manipulation and visualization.  

Utilizing the 'plotting_conv' function, we visualized the conversion rates over time, examining fluctuations across different age groups and marketing channels.  

For a more detailed temporal analysis, we refined our dataset by converting the 'date_served' column to datetime format. Additionally, we introduced a 'DoW_served' column representing the day of the week served. By plotting conversion rates based on both date and weekday, alongside marketing channel, we aimed to uncover patterns and trends within our data.  

Ultimately, these comprehensive analyses unveiled a concerning decline in retention rates within the House Ads channel starting from January 11th. This observation prompted a deeper investigation into the specifics of House Ads campaigns and user interactions during this period, aiming to identify the underlying reasons behind this decline.


## Identifying Key Issues
During this phase of the Python analysis, our focus centered on exploring the influence of language disparities on the conversion rate within the House Ads marketing campaign. Here's a breakdown of our approach:

- __Isolating House Ads Data:__ We created a subset of our original DataFrame, 'house_ads', containing rows exclusively associated with the 'House Ads' marketing channel.
- __Analyzing Conversion by Language:__ By calculating conversion rates based on 'date_served' and 'language_displayed' within the House Ads campaign, we gained insights into how different languages impacted conversion. Visualization of these rates using our custom 'plotting_conv' function enhanced our understanding.
- __Checking Language Accuracy:__ Introduction of a new column, 'is_correct_lang', in our 'house_ads' DataFrame allowed us to determine the frequency of correct language display. Grouping data by 'date_served' and 'is_correct_lang' facilitated visualization of these findings.
- __Evaluating the Impact of Language Errors:__ Proportions of ads displayed in the correct language over time were calculated and visualized, aiding identification of significant deviations in language accuracy.
- __Pre-Error Conversion Rate Calculation:__ Prior to pinpointing the error date (January 11, 2018), we computed conversion rates for various languages compared to English. This provided a baseline for understanding normal conversion rates per language.
- __Conversion Analysis by Language Preference:__ Grouping House Ads data by 'date_served' and 'language_preferred', we determined unique user counts and conversion sums to ascertain conversion rates based on language preference.
- __Expected vs. Actual Conversions:__ Expected conversion numbers for each language, based on pre-error rates, were contrasted with actual post-error figures. This comparison shed light on the impact of language mismatch.
- __Estimating Lost Subscribers:__ By quantifying the disparity between expected and actual conversions, we estimated the total number of lost subscribers attributable to the language error.

Through this meticulous analysis, we deduced that __discrepancies between users' preferred languages and the languages of displayed ads significantly contributed to the retention rate decline from January 11th onwards.__ This revelation spurred further investigation into the underlying causes of this discrepancy and underscored the pivotal role of language accuracy in effective marketing campaigns.
