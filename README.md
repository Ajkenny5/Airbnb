## Airbnb NYC Market Analysis: Trends, Pricing & Opportunities

### INTRODUCTION
The rise of the sharing economy has transformed the hospitality industry with Airbnb emerging as a dominant player in short term rental markets worldwide. This report provides a comprehensive analysis of specific focus e.g. price and availability, room types and listings, geographical trend, reviews and popularity patterns.

### TOOLS USED 
Python – Libraries used:

•	Pandas – for data manipulation, cleaning, filtering and analysis.

•	Numpy – for numerical operations especially on arrays and matrices.

•	Matplotlib – static visualization.

•	Plotly – Interactive visualization.

### DATA SOURCE
[Download Here](https://drive.google.com/file/d/132lazdskYuA0iDmKXzetN1hQRuyqztT7/view?usp=drivesdk)

### IMPORTING DATASET 
```mport pandas as pd
import matplotlib.pyplot as plt
import numpy as np
import plotly.express as px
data = pd.read_csv("C:/Users/DELL/Desktop/AB_NYC_2019.csv")
```

### DATA CLEANING & PREPARATION
•	Renaming the Price column to price per night
```
data.rename(columns={'price':'price_per_night'},inplace=True)
```

•	Converting date column to date datatype
```
data['last_review'] = pd.to_datetime(data['last_review'])
```

•	Filling the empty rows of reviews_per_month with 0
```
data['reviews_per_month'].fillna(0.0,inplace=True)
```

•	Dropping the empty rows under name and host_name column
```
data.dropna(subset=['name','host_name'],inplace=True)
```

•	Removing special characters using regex
```
data['name']= data['name'].str.replace(r'[^A-Za-z0-9-&, ]', '',regex=True)
```
•	Removing duplicates
```
data.drop_duplicates(inplace=True)
```

•	Removing rows where availability is zero (0)
```
data = data[(data['availability_365'] > 0)]
```

•	Removing unrealistic price values
```
data = data[(data['price_per_night'] > 0)]

data = data[(data['price_per_night'] <= 5000)]

```

•	Removing unrealistic values for minimum nights
```
data = data[(data['minimum_nights'] <= 366)]
```

•	Standardizing text columns
```
data['name'] = data['name'].str.lower().str.strip()
data['host_name'] = data['host_name'].str.lower().str.strip()
data['neighbourhood_group'] = data['neighbourhood_group'].str.lower().str.strip()
data['neighbourhood'] = data['neighbourhood'].str.lower().str.strip()
data['room_type'] = data['room_type'].str.lower().str.strip()
```

### EXPLORATORY DATA ANALYSIS
**Distribution of Airbnb prices across NYC (New York City)** – This section analyzes the range of Airbnb prices and their frequencies in NYC showing the most common and the least common price categories.
```
Prices = data['price_per_night']


ax =Prices.plot(kind='hist')

for container in ax.containers:
    ax.bar_label(container,fmt='{:,.0f}')

plt.title('Distribution of Prices in New York City')
plt.show()

```
![image](https://github.com/user-attachments/assets/94cc0a5d-a93c-4daa-8b46-9b80c5ad295d)

**Insights** – The histogram charts show the prices between $0-$500 was the most common with a frequency of 30,500 listings. The prices $501-$1000 was the second most common with a frequency of 659 listings. In contrast, the least common price range is $4,501-$5,000 with only 4 listings. The distribution suggests that Airbnb accommodations in New York City are generally affordable with the vast majority of listings falling under $1000.


**Neighborhood-group Average Prices** - This analysis examines the average listing prices across five different neighborhood group in NYC showing the most and least expensive areas during rentals.
```
neighbourhood_avg = data.groupby('neighbourhood_group')['price_per_night'].mean().sort_values(ascending=False)

ax = neighbourhood_avg.plot(kind= 'bar')

for container in ax.containers:
    ax.bar_label(container,fmt='{:,.0f}')
         

plt.title('Neighbourhood group Average Price')
plt.xticks(rotation= 45, ha='right')
plt.show()
```
![image](https://github.com/user-attachments/assets/ca590067-0623-44de-bea6-4c4d956c7bc4)
 
**Insights** – Manhattan has the highest average listing price at $208, making it the most expensive neighborhood group. This is followed by Brooklyn, with an average price of just under $150. In contrast, Bronx is the least expensive, with an average listing price of $89. This suggests a significant variation in Airbnb pricing based on location within New York City.



**Relationship between availability_365 and price per night** – This analysis explores the relationship between the number of days a listing in a year (availability_365) and the corresponding nightly price (price_per_night). The goal is to understand if longer availability affects pricing.

```
data.plot(x='availability_365', y='price_per_night', kind='scatter')

x= data['availability_365']
y= data['price_per_night']
line = np.polyfit(x,y,1)
p = np.poly1d(line)

plt.plot(x, p(x), color= 'red', linestyle='--',linewidth=2, label='Trendline')
plt.title('Relationship between availability_365 and price_per_night')
plt.show()
```
![image](https://github.com/user-attachments/assets/62ac0489-b3c2-4a4a-8885-56f7a4464df7)

 
**Insights** – The scatter plot reveals that listings are priced mostly below $1000 per night with a few extreme outliers reaching over $5000. Most listings cluster around full availability (365 days), but there is no strong visible correlation between availability and price. The red regression line, which appears relatively flat, confirms a very weak or no linear relationship between availability and nightly price.


**Relationship between minimum nights and price per night** – This chart examines the connection between the minimum number of nights required to book a listing (minimum_nights) and the nightly price. It aims to show whether stricter booking policies impact the price charged.
```
data.plot(x='minimum_nights',y='price_per_night',kind='scatter',figsize=(7,6))

x=data['minimum_nights']
y=data['price_per_night']
line = np.polyfit(x,y,1)
p = np.poly1d(line) 

plt.plot(x,p(x),color= 'red', linestyle='--',linewidth=2, label='Trendline')
plt.title('Relationship between minimum_nights and price_per_night')
plt.show()
```

![image](https://github.com/user-attachments/assets/2134df74-dc1e-4dd5-86f6-ce4beb643016)

 
**Insights** – The majority of listings have a minimum stay requirement below 100 nights, with prices still mostly under $1000. A few listings have extreme values in both minimum nights and price per night. The regression line is again mostly flat, suggesting no significant linear relationship between minimum nights and price per night. This indicates that prices are likely influenced more by other factors.


**Room type distribution** - This analyzes the percentage distribution of room types across NYC, giving insight into the most common room types.
```
pie_chart = data.pivot_table(index='room_type',aggfunc='size')

pie_chart.plot(kind='pie',autopct='%.2f%%',shadow=True)


plt.title('Percentage Distribution of Room Type across NYC')
plt.show()
```
![image](https://github.com/user-attachments/assets/c9b3ee16-4f12-42be-9ec5-b8608be55910)

 
**Insights** – The pie chart shows that entire homes/apartments are the most common room type in NYC, accounting for 52.71% of all listings. This is followed closely by private rooms at 44.54%, while shared rooms make up only 2.75%, indicating they are the least available.



**Average room type prices across neighborhood group** – This analysis compares the average prices of three Airbnb room type (entire homes/apartments, private rooms and shared rooms) across five NYC neighborhood groups (Bronx, Brooklyn, Manhattan, Queens, and Staten Island).
```
Clustered_bar = data.pivot_table(index='neighbourhood_group',
                values='price_per_night',columns='room_type',aggfunc='mean')

Clustered_bar.plot(kind='bar',title='Average Room type Prices in neighbourhood Group')


plt.xticks(rotation=45,ha='right')
plt.show()
```

 ![image](https://github.com/user-attachments/assets/3f4fa046-6f06-4c02-a4c3-d9d97e268ac5)


**Insights** – Entire homes/apartments are significantly more expensive than private or shared rooms in all boroughs. Manhattan has the highest average prices across all room types, particularly for entire homes/apartments ($250). Staten Island has higher average prices for entire home/apartments than Queens, but relatively low for other room types. Bronx is the most affordable across all room types, showing a budget friendly market segment.


**Room types number of reviews across months** – This chart displays the monthly trend of the number of reviews for each room types across a 12- month period.
 ```
data['last_review_month'] = data['last_review'].dt.month


time_series =  data.pivot_table( index= 'last_review_month', columns='room_type', values='number_of_reviews', aggfunc='sum')

time_series.plot(kind='line')
plt.title('room types number of reviews across months')
plt.figure(figsize=(12,6))
plt.show()

```

![image](https://github.com/user-attachments/assets/e5d0640b-5871-428a-a5dd-2d7a9b4bc049)


**Insights** – All room shows a sharp peak in reviews around month 6 (June), suggesting a surge in bookings, likely due to the summer travel season. Entire homes received the most reviews overall, followed by private rooms. After June, reviews drop dramatically and remain low throughout the second half of the year. Shared rooms consistently receive the fewest reviews indicating lower usage or availability.


**Host Listing Counts** – This chart ranks the top 10 Airbnb hosts by the number of listings they manage. Each bar representing a host and their respective listing count.
```
host_listing = data.pivot_table(index='host_name',values='calculated_host_listings_count',
             aggfunc='sum').sort_values(by='calculated_host_listings_count',ascending=False).head(10)

ax=host_listing.plot(kind='bar',legend=False)

for container in ax.containers:
    ax.bar_label(container,fmt='{:,.0f}')
plt.title('Top 10 Host listing count')
plt.ylabel('host listing count')
plt.xticks(rotation=45, ha='right')
plt.show()
```

![image](https://github.com/user-attachments/assets/433629eb-476d-499b-9219-5e7133d0fcf7)

 
Insights – Sonder (nyc) dominates the list with over 106,000 listings indicating a commercial scale operation. Blueground and Tara follows as major hosts, with 53,824 and 14,651 listing respectively. The remaining hosts have listing counts under 10,000. This chart suggest that a significant portion of Airbnb listings is controlled by large-scale or corporate host rather than individuals.


**Geographical Distribution of Listing Prices** – This is a scatter map visual created using Plotly, showing Airbnb listings across New York. Each dot represents a listing and the color intensity represents the price per night, using a color gradient scale (dark purple to yellow/green).
```
fig = px.scatter_mapbox(data, lat='latitude', lon='longitude', color= 'price_per_night',
                     size='price_per_night', hover_name='name',mapbox_style='carto-positron',
                     zoom=8,center={'lat':40.7128,'lon':-74.0060}, color_continuous_scale='Viridis',title= 'Listing Price across New York')


fig.show()
```


https://github.com/user-attachments/assets/d02d65bb-8d35-4090-b8a9-047dbacd11fc




**Insights** – Listings are heavily concentrated around Manhattan, Brooklyn and Queens, indicating these boroughs have the highest Airbnb activity. High-priced listings (green/yellow dots) are clustered mostly around Manhattan, particularly in central and downtown areas. The majority of listings are lower-priced (dark purple), spread more widely across the boroughs. Outer boroughs like Staten Island and Bronx have fewer high-priced listings, suggesting a more budget-oriented market. The visual effectively communicates both location density and price range at a glance, helping identifying premium zones and budget friendly areas. 


**Neighborhood with the highest listing** - This chart compares the top 10 neighborhood with the highest listing showing property listing counts across different neighborhoods.
```
Top_10 = data.pivot_table(index='neighbourhood', aggfunc='size').sort_values(ascending=False).head(10)

ax = Top_10.plot(kind='bar')

for container in ax.containers:
    ax.bar_label(container,fmt='{:,.0f}')

plt.xticks(rotation=45, ha='right')
plt.xlabel('neighbourhood')
plt.title('Top 10 Neighbourhood with the Highest Listing')
plt.show()
```
 ![image](https://github.com/user-attachments/assets/23abff3a-d010-4eab-bab7-37a7c7b69f80)


**Insights** – Listings are heavily concentrated around Bedford-Stuyvesant, and Williamsburg with listings above 2,000 representing nearly 32% of the total listings among these top 10 neighborhoods indicating these areas are major rental/property hotspots. Mid-range areas are Bushwick-Upper West Side with 1000-1500 listings.



**Hosts number of reviews** – This analysis examines the top 10 hosts with their respective number of reviews broken down by neighborhood group across New York City.
```
reviews = data.pivot_table(index='host_name',aggfunc='sum',columns='neighbourhood_group',values='number_of_reviews')

top =reviews.sum(axis=1).sort_values(ascending=False).head(10)

top_review = reviews.loc[top.index]

top_review.plot(kind='bar',stacked=True, figsize=(10,6))
plt.xticks(rotation=45, ha='right')
plt.title('Top 10 host name with the highest reviews')
plt.show()

```

![image](https://github.com/user-attachments/assets/302700da-ef5e-4f6d-bd79-17fc1533a174)

 
**Insights** – Micheal significantly outperforms other hosts with approximately 8,745 total reviews, followed by David and John with around 6,835 and 6,515 total reviews respectively suggesting that these hosts have longer hosting experience or superior service quality. Brooklyn and Manhattan consistently dominate and represent the largest portion of reviews across most hosts, particularly prominent for Micheal, David, John, and Jason. Queens shows moderate but consistent presence across all hosts, while Bronx and Staten Island have minimal representation, indicating these areas may have lower Airbnb activity or different dynamics.


### Conclusions

•	Most NYC listings are affordable (< $1,000/night), Manhattan being the most expensive and Bronx the cheapest.

•	Entire homes/apartments dominates (52.7%) while shared rooms are rare (2.75%).

•	No strong link exists between price and availability or minimum stay requirements.

•	June sees peak bookings, with reviews dropping sharply afterward.

### Recommendations

•	Hosts – Adjust prices by location/seasons, prioritize entire homes, and improve service to boost reviews.

•	Travelers – Choose Bronx/Queens for budget stays or Manhattan for premium options.

•	Commercial Hosts – Expand into underserved areas (Staten Island/Bronx).

•	Policymakers – Monitor commercial host dominance to ensure market balance.

•	Tourism Promotion – Promoting tourism in less active boroughs like Staten Island could help distribute Airbnb activity more evenly across NYC.

•	Service Quality – Emulating the review success of top host like Micheal and David by improving service quality could enhance reputation and bookings 



