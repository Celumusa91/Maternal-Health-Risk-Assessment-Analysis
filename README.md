# Maternal Health Risk Assessment Analysis
## Statistical Analysis on factors influencing the risk of Pregancy

### Project Overview
---

This data analysis project explores how various health status indicators influence Pregnancy risk.

### Data Source
---

Maternal Health Risk Assessment Dataset:The data used for this analysisis is the "Maternal Health Risk Assessment Dataset" file, downloaded from (https://data.mendeley.com/datasets/p5w98dvbbk/1). This file serves as resource for developing predictive models aimed at identifying and managing high-risk pregnancies, providing insights into maternal health factors, and supporting personalized patient care. The objective in this analysis is to demostrate data analysis skills using R.

### Tools
---

R language
 - library(readr) for loading data
 - library(dplyr) for data manipulation
 - library(ggplot2) for data visualization graphics
 - library (skimr) for data exploration

### Data Importing Cleaning and Preparation
---
 - Data loading and examination
```R
library(readr)
data <- read_csv("C:/Users/Fakudze/Downloads/archive (31).zip")
View(data)
```
 - Data exploration
```R
skim(data)
```
 - Data Cleaning (mean and median imputation for missing data in numeric features)
```R
data <- data %>% 
         mutate(`Systolic BP` = if_else(is.na(`Systolic BP`),
                                        median(`Systolic BP`, na.rm = T),
                                        `Systolic BP`))

data <- data %>% 
         mutate(Diastolic = if_else(is.na(Diastolic),
                                    median(Diastolic, na.rm = T),
                                    Diastolic))

data <- data %>% 
         mutate(BS = if_else(is.na(BS),
                             median(BS, na.rm = T),
                             BS))

data <- data %>% 
         mutate(`Heart Rate` = if_else(is.na(`Heart Rate`),
                                       mean(`Heart Rate`, na.rm = T),
                                     `Heart Rate`))
```
 - data cleaning (Recoding character indicators and converting to factor)
```R
data <- data %>% 
         mutate(across(c(`Preexisting Diabetes`, `Gestational Diabetes`,
                         `Mental Health`, `Previous Complications`, `Risk Level`),
                       as.factor)) 
```
 - data cleaning(Replacing Missing values with Unknown in factor variables)
```R
data <- data %>% 
         mutate(across(c(`Preexisting Diabetes`, `Gestational Diabetes`, AgeGroup,
                         `Mental Health`, `Previous Complications`), ~ fct_explicit_na(as.factor(.), na_level
                                                          = "Unknown")))
```
 - Extracting clean data for analysis
```R
clean_data <- data %>% 
         select(-Age) %>% 
         na.omit()
```
### Data Analysis (Visualization and regression modelling)

 - Does Blood Sugar (BS) signicantly predict Risk Level? (Visualization and regression modelling)

```R
ggplot(clean_data)+
  geom_boxplot(mapping = aes(`Risk Level`, BS, fill = `Risk Level`))+
  coord_flip()+
  labs(title = "The relationship between Risk Level and Blood Sugar")

model_BS <- glm(`Risk Level`~ BS, 
                data = clean_data, family = binomial)
summary(model_BS)
```

 - Does Blood Age signicantly predict Risk Level?

```R
ggplot(clean_data)+
  geom_boxplot(mapping = aes(`Risk Level`, Age, fill = `Risk Level`))+
  labs(title = "The relationship between Risk Level and Age")

model_Age <- glm(`Risk Level` ~ Age, data = clean_data,
                 family = binomial)
```

 - What is the relationship between BMI and Risk Level?

```R
ggplot(clean_data)+
  geom_jitter(mapping = aes(`Risk Level`, BMI), width = 0.2, color = "red")+
  labs(title = "The relationship between Risk Level and BMI")

model_BMI <- glm(`Risk Level`~ BMI, data = clean_data,
                 family = binomial)

summary(model_BMI)
```

 - Do Previous Clomplications increase the likelihood of High Risk Level?

```R
ggplot(clean_data)+
  geom_bar(mapping = aes(`Previous Complications`, fill = `Risk Level`),
           position = "dodge")+
  labs(title = "The relationship between Risk Level and Previous Complications")

model_PC <- glm(`Risk Level`~ `Previous Complications`, data = clean_data,
                family = binomial)

summary(model_PC)
```

- How do Heart Rate and Systolic BP together influence Risk Level?

```R
model_HS <- glm(`Risk Level`~ `Heart Rate` + `Systolic BP`, data = clean_data,
                family = binomial)

summary(model_HS)
```

 - Which factors among Age, BMI, BS and Mental Health best predict Risk Level?

```R
model_best_predict <- train(`Risk Level`~ Age + BMI + BS + `Mental Health`,
                            data = clean_data, model = "glm")
varImp(model_best_predict)
```

### Results and Findings
---

1. Does Blood Sugar (BS) signicantly predict Risk Level?
   - Maternal women's Blood Sugar is a statistically significant predictor of Risk Level [pvalue < 0.05]
   - High Risk Maternal women has the highest median Blood Sugar values than the Low Risk women.
   - The coefficient of 0.93987 means that as Blood Sugar increases, the odds of being in the High Risk level increases.
2. Does Age signicantly predict Risk Level?
   - Age is a statistically significant predictor of Risk Level [pvalue < 0.05].
   - High Maternal Risk women have higher median Age value than those from the Low Risk women.
   - For every one year increase in Age, the log-odds of a woman being in the High Risk category increases by 0.0437.


3. What is the relationship between BMI and Risk Level?
   - Maternal women's Body Mass Index is a statistically significant predictor of Risk Level [pvalue < 0.05].
   - High Risk maternal women has the highest median Body Mass Index values than the Low Risk women.
   - The positive coefficient of 0.37841 means that as Body Mass Index increases, the odds of being in the High Risk level also increases.
   - One Unit increase in Body Mass Index maternal woman, the likelihood of her being in the High Risk category is 0.37841.

5. Do Previous Clomplications increase the likelihood of High Risk Level?
   -  Previous Clomplicationsis a statistically significant predictor of Risk Level [pvalue < 0.05].
   - The estimate of 4.50037 means that, compared to the reference group (Previous Complications 0), the log-odds of being in the High Risk Level increase by 4.50037 when previous Complications is 1.
   - The estimate of 14.54499 means that, compared to the reference group (Previous Complications 0), the log-odds of being in the High Risk Level increase by 14.54499 when previous Complications is Unknown.

6. How do Heart Rate and Systolic BP together influence Risk Level?
   - Heart Rate and Systolic BP are statistically significant predictors of maternal Risk Level on women.
   - One Unit increase in Heart Rate of maternal woman, the likelihood of her being in the High Risk category is 0.168549.
   - One Unit increase in Systolic BP of maternal woman, the likelihood of her being in the High Risk category is 0.034087.

7. Which factors among Age, BMI, and Mental Health best predict Risk Level?
   -Maternal woman’s Body Mass Index and Blood Sugar are the best predictors of Maternal Risk Level while Mental Health and the women’ Age are the least to none predictors.

### Recommendations
---
1. Impliment routine blood sugar screening and early intervention programs for all pregnant women as part of standard maternal care.
2. 

