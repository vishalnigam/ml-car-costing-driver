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
     - _odometer <10,000 then 'like new'_
     - _odometer > 10,000 -20,000 then 'excellent'_ 
     - _odometer between 20,000-40,000 'fair'_
     - _odometer >40,000 <100000 then 'good'_
     - _Rest missing values (293 rows) has been dropped_
  - Title_status missing values (0.02%) dropped
  - Use vin library to replace the missing values for _'Model', 'Cylinders', 'Car Type', 'Drive'_ __Long Operations__
- Ordinal and nominal features 
  - replaced with ordered values to faciliate regression using numeric values
- Drop missing values 
  - rows correponding to _'Year', 'Fuel', 'Transmission', 'Model', 'Odometer', 'Manufacturer'_
- Convert datatypes 
  - 'Odometer' & 'Year' to int64
- Categorize cars based on year of manufacturing
  - _80s     - Cars manufactured between 1900-1980_
  - _2000    - Cars manufactured between 1980-2000_
  - _2010    - Cars manufactured between 2000-2010_
  - _2020    - Cars manufactured between 2010-2020_
  - _2025    - Cars manufactured between 2020-onward_ 
- Store incremental data
  - Updated data to be stored incrementally in 'incremental_modified_data.csv' to preseve updates to avoid long running process

**Observation**:
- Clustring by years - based on odmoter vs price with title_status and condition 
  - _Before 1980         - title ('clean') condition ('Excellent', ' Fair') Odometer (>10,000) Price (1,000 - 1,00,000)_
  - _Between 1980-2000   - title ('clean') condition ('Excellent', ' Fair') Odometer (10,000 - 1,00,000) price (1,000 - 1,00,000)_
  - _Between 2000-2010   - title ('clean' , 'rebuilt') condition ('Excellent', ' Fair') Odometer (>10,000) Price (10,000 - 10,00,000)_
  - _Between 2010-2020   - title ('clean' , 'rebuilt') condition ('Excellent', ' Fair') Odometer (1,000 - 10,00,000) Price (2 clusters - 100s-1000s & >1000s)_
  - _Before 2020         - title ('clean' 'lien') condition ('Excellent', ' Fair') Odometer (>10,000)_
- Prices has no relationship with car odometer
- Most of the inventory in later years i.e. after 2010, are prominently 'White' or 'black' in color
- Most of the cars inventory in order of Ford, Cheverolet, Toyota & Honda
- Correlation model don't show any clear indication of price variation with any feature - 'condition', 'fuel', 'odometer', 'transmission', 'title_status'

**Modeling**
- Execute linear regression with _'year' , 'condition', 'fuel', 'odometer', 'title_status', 'transmission' against 'price'_
   - __Best hyperparameters__ - 'poly__degree': 3, 'poly__include_bias': False, 'regressor__fit_intercept': False
   - __Best 2 features__ which highly impact the car prices based on linear regression
      - condition odometer**2 (69627.487811)
        - [*outcome*:]condition with odometers are highly impacting negatively the price means new car with higher odomter is less costly then 'like new' or        'new' with less odometer reading
      - transmission**2 (62729.647111) 
        - [*outcome*] positively impact the price which means 'automatic' cars are more pricey then 'manual' transmission  

- Execute Regularization techniques - RidgeCV  
   - __Best 2 feature__ which highly impacting the car prices for _2010-2020_
     - condition odometer**2 (64564.451719) 
       - [*outcome*] `Highly Negative` 'new' with higher 'odometer' are less price than like 'new' or 'excellent' with less 'odometer' 
     - condition odometer (-38237.081637)
       - [*outcome*] `Less negatively` 
   - __Best 3 feature__ which highly impacting the car prices for _2000-2010_
     - fuel (-68182.847606) 
       - [*outcome*:] `Highly Negative` 'Diesel' engines are better price than 'Gas' 
     - transmission (-21820.810879) 
       - [*outcome*]  `Negatively impact` prices but 'Manual' transmission is better price than 'Automatic'
     - title_status (10552.630861)  
       - [*outcome*]  `Positively impact` 'title_status' positively impact the price i.e. 'clean' title are highly priced than 'rebuilt' or 'salvage'

- Execute stricter Regularization techniques - LassoCV
   - __Best 2 feature__ which highly impacting the car prices for _2010-2020_
     - fuel (-13592.451017) 
       - [*outcome*] `Negatively impact` higher car price based on 'fuel' types in order - Diesel > Gas > Electric > Hybrid 
     - condition odometer (13378.182650)  
       - [*outcome*] `Positively impact` same 'odometer' but better 'condition' car is pricy, same 'condition' with less 'odomoeter' is pricey
   - __Best 2 feature__ which highly impacting the car prices for _2000-2010_
     - fuel transmission (1.081598e+06)  
       - [*outcome*] `Positively impact` 'automatic' 'hybrid' cars are higher prices then 'automatic' 'electric' cars
     - transmission (-2.925253e+05)
       - [*outcome*] `Negatively impact` 'manual' car prices are higher than 'automatic'


**Recommandations** :
1. No clear guidelines for car price variation with features but some guidelines can be extracted based on the data analysis
2. Guidelines can not be generic but mostly tied with the car manufacturing year 
3. Cars manufacutered between 2010 to 2020, Higher car price based on 'fuel' types in order - Diesel > Gas > Electric > Hybrid
4. Cars manufacutered between 2010 to 2020, Same 'odometer' but better 'condition' car is pricy, same 'condition' with less 'odomoeter' is pricey
5. Cars manufacutered between 2000 to 2010, 'automatic hybrid' cars are higher prices then 'automatic electric' cars similarly, 'manual hybrid' is higher price than 'automatic hybrid'
6. Cars manufacutered between 2000 to 2010, 'manual' car prices are higher than 'automatic'

# Link to Jupyter notebook
[notebook link](https://github.com/vishalnigam/ml-car-costing-driver/blob/main/car-costing-drivers.ipynb)
