# ml-car-costing-driver
The provided dataset contains information on 426K cars to ensure the speed of processing. Goal is to understand what factors make a car more or less expensive & based on that, provide recommandation to Car dealers.

# Summary of the findings/actions 
- Missing values
  - Odometer missing values (0.01%) replaced with mean 
  - Condition missing values (37%) replaces with certain logic
   - title_type is exists and equal to 'salvege' then set 'salvage'
   - title_type is exists and equal to 'parts only' then 'unfit'
   - title_type is exists and equal to 'rebuilt' then 'fair'
   - title_type is exists and equal to 'missing'
    - odometer <10,000 then 'like new'
    - odometer > 10,000 -20,000 then 'excellent' 
    - odometer between 20,000-40,000 'fair'
    - odometer >40,000 <100000 then 'good'
    - Rest missing values (293 rows) has been dropped
  - Title_status missing values (0.02%) dropped
  - Use vin library to replace the missing values for 'Model', 'Cylinders', 'Car Type', 'Drive' ## Long Operations ##
- Ordinal and nominal features 
  - replaced with ordered values to faciliate regression using numeric values
- Drop missing values 
  - rows correponding to 'Year', 'Fuel', 'Transmission', 'Model', 'Odometer', 'Manufacturer'
- Convert datatypes 
  - 'Odometer' & 'Year' to int64
- Categorize cars based on year of manufacturing
  - 80s     - Cars manufactured between 1900-1980
  - 2000    - Cars manufactured between 1980-2000
  - 2010    - Cars manufactured between 2000-2010
  - 2020    - Cars manufactured between 2010-2020
  - 2025    - Cars manufactured between 2020-onward 
- Store incremental data
  - Updated data to be stored incrementally in 'incremental_modified_data.csv' to preseve updates to avoid long running process

**Observation**:
- Clustring by years - based on odmoter vs price with title_status and condition 
  - Before 1980         - title ('clean') condition ('Excellent', ' Fair') Odometer (>10,000) Price (1,000 - 1,00,000)
  - Between 1980-2000   - title ('clean') condition ('Excellent', ' Fair') Odometer (10,000 - 1,00,000) price (1,000 - 1,00,000)
  - Between 2000-2010   - title ('clean' , 'rebuilt') condition ('Excellent', ' Fair') Odometer (>10,000) Price (10,000 - 10,00,000)
  - Between 2010-2020   - title ('clean' , 'rebuilt') condition ('Excellent', ' Fair') Odometer (1,000 - 10,00,000) Price (2 clusters - 100s-1000s & >1000s)
  - Before 2020         - title ('clean' 'lien') condition ('Excellent', ' Fair') Odometer (>10,000)
- Prices has no relationship with car odometer
- Most of the inventory in later years i.e. after 2010, are prominently are 'White' or 'black' in color
- Most of the cars inventory in order of Ford, Cheverolet, toyota & honda
- Correlation model don't show any clear indication of price variation with any feature - 'condition', 'fuel', 'odometer', 'transmission', 'title_status'

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
[notebook link](https://github.com/vishalnigam/ml-car-costing-driver/blob/main/car-costing-drivers.ipynb)

condition_ord = ['unfit', 'salvage', 'fair', 'good','excellent' , 'like new' ,'new' ]
title_status_ord = [ 'missing', 'parts only', 'lien', 'salvage','rebuilt', 'clean'  ]
fuel_ord = ['other', 'diesel', 'gas', 'electric','hybrid']
transmission_ord = ['other', 'manual', 'automatic' ]
