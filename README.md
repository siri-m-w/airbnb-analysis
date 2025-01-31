# Exploratory Data Analysis (EDA) Report: Airbnb Listings
## Explanatory questions

### 1. How many listings are in each city in total and also per type of day?
I created a stacked barchart to compare each cities amount of listings.
```python
table_city_day = pd.crosstab(bnb_df['city'], bnb_df['weekday/weekend'])
table_city_day.plot(kind='bar',stacked=True);
```

### 2. Which city has the biggest proportion of superhosts?
I made a stacked barchart with percentages to compare the proportions of different hosts in each city.
```python
table_city_host = pd.crosstab(bnb_df['city'], bnb_df['host_is_superhost'], normalize='index')
table_city_host.plot(kind='bar',stacked=True);
```
One can see that Athens has the biggest proportion of superhosts.

### 3. Which cities have listings with more than four rooms?
I made a list with all the cities which have listings with more than four rooms.
```python
bnb_df['bedrooms']=bnb_df['bedrooms'].astype(int)
list_cities = []
for i in range(len(bnb_df)):
    city = bnb_df.loc[i, 'city']
    if ((bnb_df.loc[i,'bedrooms']>4) and (bnb_df.loc[i,'city'] not in list_cities)):
            list_cities.append(city)
```
The output is following cities: Amsterdam, Berlin, Barcelona, Lisbon, London, Paris, Rome.

### 4. Which city has the most entire home/apt type listings?
I made a barchart showing the amount of different room types for each city.
```python
table_city_entire = pd.crosstab(bnb_df['city'], bnb_df['room_type'])
table_city_entire.plot(kind='bar');
```
One can see in the graph that Rome has the most "Entire home/apt" listings.

### 5. Are ratings typically high across listings, or is there a wide variation?
I used a scatterplot to visualize the deviation of ratings in each city. 
```python
sns.scatterplot(data=bnb_df,x='guest_satisfaction_overall',y='city');
```
It is shown that the rating is typically high across all countries. 

### 6. How does person_capacity vary across listings? What is the most common capacity of listings?
A countplot can visualize the the amount of all possible capacities (2 to 6).

```python
sns.countplot(data=bnb_df, x='person_capacity');
```
The most common capacity is 2.

### 7. Plot the distribution of realSum for both weekday and weekend offers. Is it normally distributed, skewed, or multimodal? If skewed, consider using transformations (e.g., log transformation) to normalize it.
I coded two histplots (one for weekdays and one for weekends) and transformed their x-axis to get a normalized distribution.
```python
weekday_df=bnb_df[bnb_df['weekday/weekend']=='Weekdays']
weekend_df=bnb_df[bnb_df['weekday/weekend']=='Weekends']
sns.histplot(data=weekday_df,log_scale=True, x='Price',binwidth=0.1)
plt.show()
sns.histplot(data=weekday_df,log_scale=True, x='Price',binwidth=0.1)
plt.show()
```

### 8. Plot the distribution of guest_satisfaction_overall ratings.
I decided for a histplot here.
```python
sns.histplot(data=bnb_df,x='guest_satisfaction_overall',binwidth=1);
```

### 9. Examine the distribution of cleanliness_rating.
I used a countplot analysis to show the distribution of cleanliness_rating.
```python
sns.countplot(data=bnb_df, x='cleanliness_rating')
plt.show()
```
What can be easily seen, is that the ratings are usually very high.

## Questions for testing
Due to time limitations I chose to concentrate on the suggested questions for testing.
### 1. Is there a difference in price between two cities? Choose at least three pairs of cities during weekdays.
**Hypothesis:** There are differences in prices between the cities due to different city sizes.
```python
sns.boxplot(x='city',y='Price',data=weekday_df,showfliers=False)
plt.xticks(rotation=45, ha='right');
```
**Findings:** Yes there are differences between the cities. Amsterdam for example is much more expensive than Budapest. Amsterdam is also more expensive than London, even though London is the bigger city. So there is no correlation.

### 2. Does the price tend to be higher on weekends?
**Hypothesis:** Hosts will charge higher prices on weekends.
```python
sns.boxplot(x='weekday/weekend',y='Price',data=bnb_df,showfliers=False)
plt.xticks(rotation=45, ha='right');
```
**Findings:** It's more or less the same but there is a tendency towards slightly higher prices on weekends.

### 3. Are listings of superhosts more expensive than those of normal hosts?
**Hypothesis:** Superhosts will charge higher prices.
```python
sns.boxplot(x='host_is_superhost',y='Price',data=bnb_df,showfliers=False)
```
**Findings:** No, superhosts charge rather lower prices.

### 4. Are superhosts closer to the city center and metro station than normal hosts?
**Hypothesis:** Superhosts are closer to the city center and metro station.
```python
bnb_df['dist']=bnb_df['dist'].astype(float)
sns.boxplot(x='host_is_superhost',y='dist',data=bnb_df,showfliers=False)
plt.show()
sns.boxplot(x='host_is_superhost',y='metro_dist',data=bnb_df,showfliers=False)
plt.show()
```
**Findings:** Yes, superhosts are usually closer to the city center but there is not really a difference in distance to a metrostation.

### 5. Are superhosts cleaner than normal hosts?
**Hypothesis:** Superhosts will have higher cleanliness ratings.
```python
sns.boxplot(x='host_is_superhost',y='cleanliness_rating',data=bnb_df,showfliers=False)
plt.show()
```
**Findings:** Yes, superhosts are usually rated 10 points in cleanliness whereas ratings normal hosts have a bigger variation.

### 6. Is there a pattern between room types and superhost status?
**Hypothesis:** Superhosts prefer entire homes/apartments.
```python
table_superhost_room = pd.crosstab(bnb_df['host_is_superhost'], bnb_df['room_type'],normalize='index')
table_superhost_room.plot(kind='bar',stacked=True);
```
**Findings:** Yes, one can see that superhosts tend to have more entire homes/appartments on Airbnb.

### 7. Is renting an entire home/apt more expensive than a private room Does that depend on the city?
**Hypothesis:** Entire homes/apartments will be more expensive than private rooms.
```python
sns.boxplot(x='city',y='Price', hue='room_type',data=bnb_df[bnb_df['room_type']!='Shared room'],showfliers=False)
plt.xticks(rotation=45, ha='right');
plt.show()
```
**Findings:** Yes, entire homes/appartments tend to be more expensive than private rooms. One can see, that this applies for every city without exception.

### For advanced: 8. Create a map for each city where the top 10 most expensive Airbnbs are shown and the top 10 cheapest Airbnbs are shown.
Due to timelimits I didn't get to answer this question.

### Bonus question: gentrification
To analyze this problem I calculated the rent per person for each listing for 30 days and made two graphics showing how many (amount and percentage) listings per city are cheaper than 300€.

```python
bnb_df['Price_month']=bnb_df['Price']*7.5 #price is for 2 nights and 2 people
bnb_df['low_Price_month']=bnb_df['Price_month']<300

sns.countplot(data=bnb_df, x='low_Price_month',hue='city')
plt.show()

table_low_price = pd.crosstab(bnb_df['city'], bnb_df['low_Price_month'],normalize='index')
table_low_price.plot(kind='bar',stacked=True)
plt.show()
```
Both graphics show that the amount of apartments under 300€/month in every city is vanishingly small. This implicates that Airbnbs take away affordable living space in the cities.
Due to timelimits I didn't have the capacities to do any further analysis even though this topic is very interesting. 

analysis by *Siri Malin Weber*
