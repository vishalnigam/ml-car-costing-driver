# ml-car-costing-driver
The provided dataset contains information on 426K cars to ensure the speed of processing. Goal is to understand what factors make a car more or less expensive & based on that, provide recommandation to Car dealers.

# Summary of the findings 
* Many of the features has large numbers of null values and following action has been taken to impute the data.
** Odometer missing values (0.01%) replaced with mean 
    1.2 Condition missing values (37%) replaces with certain logic
        1.2.1 title_type is exists and equal to 'salvege' then set 'salvage'
        1.2.2 title_type is exists and equal to 'parts only' then 'unfit'
        1.2.3 title_type is exists and equal to 'rebuilt' then 'fair'
        1.2.4 title_type is exists and equal to 'missing'
            1.2.4.1 odometer <10,000 then 'like new'
        1.2.5 title_type is exists and equal to 'missing' and odometer > 10,000 -20,000 then 'excellent' 
        1.2.6 title_type is exists and equal to 'missing' and odometer between 20,000-40,000 'fair'
        1.2.7 title_type is exists and equal to 'missing' and odometer >40,000 <100000 then 'good'

**Observation**:
1. Occational Bar visitors redeemed more bar coupon than Frequent bar visitors
2. Young population age <25 redeemed coupon more than age > 25
3. Drivers who never visit bar are likely to accept the coupon
4. Drivers without children passangers are more likely to accept the coupon

**Recommandations** :
1. The system in-place for distributing the coupons to drive for Bar category should consider age of drivers
2. Most of the drivers in the population are driving 'alone' and 'never' visited the bar which could be detrimental
   for the drivers. Possibly, coupons with restriction of use could be good option.

# Link to Jupyter notebook
[notebook link](https://github.com/vishalnigam/ml-coupon-acceptance/blob/main/prompt.ipynb)
