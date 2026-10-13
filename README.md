# Big NSS Express: Predicting Derates using Claude

For this exercise, you'll be working with Claude Claude in VSCode to clean up a dataset and make predictions.

Download the Big NSS Trucking data: https://drive.google.com/drive/folders/1CU3dwxRc84MXs2SQav-7NHw774S4DP3w?usp=sharing

In this project, you will be working with J1939 fault code data and vehicle onboard diagnostic data to try and predict an upcoming full derate.

J1939 is a communications protocol used in heavy-duty vehicles (like trucks, buses, and construction equipment) to allow different electronic control units (ECUs), like the engine, transmission, and brake systems, to talk to each other. Fault codes in this system follow a standard format so that mechanics and diagnostic tools can understand what's wrong, no matter the make or model.

A derate refers to the truck's computer intentionally reducing engine power or speed to protect itself or force the driver to get it serviced. This is a built-in safety measure. A full derate, the main target in this project, means the vehicle is severely limited, requiring a tow for repairs.

You have been provided with a csv file containing data across time for many vehicles.  Each vehicle will have a unique 'EquipmentID'.  Each row
will contain an 'EventTimeStamp' column which will tell you exactly when the measurements were obtained.  The measurements we will use for this
project are 'EngineOilTemperature', 'EngineOilPressure', 'DistanceLtd', 'FuelLtd', 'EngineTimeLtd'.  There will not be values for all measurements
in all rows.

The two 'EngineOil' columns refer to the internal condition of the engine at the time of measurement.   The three 'Ltd' columns are derived from wear-and-tear at the time of measurement.

There is a Derate column that will state if, at the time of measurement, if the vehicle was currently in derate - effectively out of commission
until it can be towed and serviced. When the vehicle is in active derate the related measurements can sometimes be inaccurate and unpredictable.

Our goal in this project is to use AI to solve several problems.
  - First, we want to read in the data and ask AI to assist us in getting a visual representation of the existing data.  
  - Second, we would like to fill in as many nulls as possible.  The steps below will explain how to do this with AI assistance.
  - Third, we would like AI to help us in creating a model that can predict if a vehicle is in derate or not.
  - Next, we would like AI to help us in creating a model that could predict a future derate given the conditions a any specific moment.
  - Finally, we want to evaluate and interpret the reliability of the model's predictions.


1) Read in 'big_nss.csv' and save it as a dataframe named big_nss.  First, create a visualization to look at the 5 measurement columns to see how they compare when there is a derate vs not.  Ask Claude to help you write code that will compare 5 columns of numeric data for trucks experiencing a derate compared to those that aren't.

2) We see there are differences between those in derate and not.  However, there are a lot of null values in the data.  We want to fill those in.
  - Drop all rows that have null values for both "EngineOil" columns and all 3 "Ltd" columns
  - Some trucks are completely missing values for one or more columns. Drop all EquipmentIDs that have only NaNs in at least 2 of the 'Ltd' columns (the entire column not by row)
  - Next for nulls in the 'Ltd' columns where there is at least 1 'Ltd' value, predict the others based on the existing 'Ltd' value.
  - Next for the remaining nulls in the Ltd columns, replace nulls by a value equally incremented from the last nonnull in that columns to the next nonnull for the same EquipmentID.
  - Next for the EngineOil columns we will want to do 1 of two possible methods of replacing nulls.  For rows where both EngineOil columns are
  missing predict the value based on the Ltd columns.  When only 1 of the EngineOil values is missing use the non-null EngineOil value with the
  Ltd columns to predict the value.

3) Next we can use Claude to see if we are able to predict a derate.
  - Start by explaining to Claude that derate is your target variable and you would like to predict if it is true or false based on the values of the 3 Ltd and 2 EngineOil columns. Make sure that Claude knows that you want to be able to validate your model on unseen data, so it needs to do a train/test split. (Ask it to explain what this is if you're unfamiliar.)
  - Try both logistic regression and random forest (Again, ask Claude for an explanation if you are unsure what these mean).
  - Get assistance interpreting the results. Get an odds ratio and an explanation of what it means.
  - How accurate are your predictions?

4) For this analysis to be useful, we don't need to predict if the Derate column is True. Rather, we want to predict if it will be true in the future based on the current known values.
  - Ask Claude to suggest a type of prediction that can predict future triggers.
  - Ask Claude to help find ROC AUC values for making predictions at different amounts of time ahead of a derate. Try this range of times: 12 hours and 1, 3, 7, 14, 21 days. Note: You may have to push Claude to use efficient methods for creating the target variable. Ask it to use built-in pandas methods instead of for loops and iterrows.
  - Have Claude help you evaluate and understand what the ROC AUC values tell you about your predictions. What does the ROC tell you, and not tell you?
  - Explain what the ROC AUC value tells you for the model one day before derate.
