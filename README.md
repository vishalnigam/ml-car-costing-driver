# ml-car-costing-driver
The provided dataset contains information on 426K cars to ensure the speed of processing. Goal is to understand what factors make a car more or less expensive & based on that, provide recommandation to Car dealers.

# Summary of the findings/actions 
1. Missing values
    1.1 Odometer missing values (0.01%) replaced with mean 
    1.2 Condition missing values (37%) replaces with certain logic
        1.2.1 title_type is exists and equal to 'salvege' then set 'salvage'
        1.2.2 title_type is exists and equal to 'parts only' then 'unfit'
        1.2.3 title_type is exists and equal to 'rebuilt' then 'fair'
        1.2.4 title_type is exists and equal to 'missing'
            1.2.4.1 odometer <10,000 then 'like new'
            1.2.4.2 odometer > 10,000 -20,000 then 'excellent' 
            1.2.4.3 odometer between 20,000-40,000 'fair'
            1.2.4.4 odometer >40,000 <100000 then 'good'
            1.2.4.5 Rest missing values (293 rows) has been dropped
    1.3 Title_status missing values (0.02%) dropped
    1.4 Use vin library to replace the missing values for 'Model', 'Cylinders', 'Car Type', 'Drive' ## Long Operations ##
2. Ordinal and nominal features 
    2.1 replaced with ordered values to faciliate regression using numeric values
3. Drop missing values 
    3.1 rows correponding to 'Year', 'Fuel', 'Transmission', 'Model', 'Odometer', 'Manufacturer'
4. Convert datatypes 
    4.1 'Odometer' & 'Year' to int64
5. Categorize cars based on year of manufacturing
    5.1 80s     - Cars manufactured between 1900-1980
    5.2 2000    - Cars manufactured between 1980-2000
    5.2 2010    - Cars manufactured between 2000-2010
    5.2 2020    - Cars manufactured between 2010-2020
    5.2 2025    - Cars manufactured between 2020-onward 
6. Store incremental data
    6.1 Updated data to be stored incrementally in 'incremental_modified_data.csv' to preseve updates to avoid long running process

**Observation**:
1. Clustring by years - based on odmoter vs price with title_status and condition 
    1.1 Before 1980         - title ('clean') condition ('Excellent', ' Fair') Odometer (>10,000) Price (1,000 - 1,00,000)
    1.2 Between 1980-2000   - title ('clean') condition ('Excellent', ' Fair') Odometer (10,000 - 1,00,000) price (1,000 - 1,00,000)
    1.3 Between 2000-2010   - title ('clean' , 'rebuilt') condition ('Excellent', ' Fair') Odometer (>10,000) Price (10,000 - 10,00,000)
    1.4 Between 2010-2020   - title ('clean' , 'rebuilt') condition ('Excellent', ' Fair') Odometer (1,000 - 10,00,000) Price (2 clusters - 100s-1000s & >1000s)
    1.5 Before 2020         - title ('clean' 'lien') condition ('Excellent', ' Fair') Odometer (>10,000)
2. Prices has no relationship with car odometer
3. Most of the inventory in later years i.e. after 2010, are prominently are 'White' or 'black' in color
4. Most of the cars inventory in order of Ford, Cheverolet, toyota & honda
5. Correlation model don't show any clear indication of price variation with any feature - 'condition', 'fuel', 'odometer', 'transmission', 'title_status'

**Modeling**
1. Execute linear regression with 'year' , 'condition', 'fuel', 'odometer', 'title_status', 'transmission' against 'price'
    1.1 Best hyperparameters - 'poly__degree': 3, 'poly__include_bias': False, 'regressor__fit_intercept': False
    1.2 best 2 features which highly impact the car prices based on linear regression
        1.2.1 condition odometer^2 -69627.487811
            [outcome:]condition with odometers are highly impacting negatively the price means new car with higher odomter is less costly then 'like new' or        'new' with less odometer reading
        1.2.2 transmission^2  62729.647111 - positively impact the price which means 'automatic' cars are more pricey then 'manual' transmission  
2.  Execute Regularization techniques - RidgeCV  
    2.1 best 2 feature which highly impacting the car prices for 2010-2020
        2.1.1 condition odometer^2  -64564.451719 [outcome]: Highly Negative new with higher odometer are less price than like 'new' or 'excellent' with less odometer 
        2.1.2 condition odometer    -38237.081637
    2.2 best 3 feature which highly impacting the car prices for 2000-2010
        2.2.1 fuel          -68182.847606 [outcome:] Highly Negative but 'Diesel' engines are better price than 'Gas' 
        2.2.2 transmission  -21820.810879 [outcome]  Negatively impact prices but 'Manual' transmission is better price than 'Automatic'
        2.2.3 title_status  10552.630861  [outcome]  title status positively impact the price i.e. clean title are highly priced than rebuilt or salvage
3.  Execute stricter Regularization techniques - LassoCV
    3.1 best 2 feature which highly impacting the car prices for 2010-2020
        3.1.1  fuel                 -13592.451017 [outcome] Negatively impact higher car price based on fuel types in order - Diesel > Gas > Electric > Hybrid
        3.1.2  condition odometer   13378.182650  [outcome] Positively impact same odometer but better condition car is pricy, same condition with less odomoeter is pricey 
    3.2 best 2 feature which highly impacting the car prices for 2000-2010
        3.2.1 fuel transmission  1.081598e+06  [outcome] positively impact automatic hybrid cars are higher prices then automatic electric cars
        3.2.2 transmission       -2.925253e+05 [outcome] negatively impact manual car prices are higher than automatic


**Recommandations** :
1. No clear guidelines for car price variation with features but some guidelines can be extracted based on the data analysis
2. Guidelines can not be generic but mostly tied with the car manufacturing year 
3. For cars manufactured between 
New car with higher odometer reading will less price

# Link to Jupyter notebook
[notebook link](https://github.com/vishalnigam/ml-coupon-acceptance/blob/main/prompt.ipynb)
