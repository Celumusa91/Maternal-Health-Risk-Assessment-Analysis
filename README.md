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
         mutate(BMI = if_else(is.na(BMI),
                              median(BMI, na.rm = T),
                              BMI))

data <- data %>% 
         mutate(AgeGroup = case_when(
                  Age <= 14 ~ "10-14",
                  Age <= 19 ~ "15-19",
                  Age <= 24 ~ "20-24",
                  Age <= 29 ~ "25-29",
                  Age <= 34 ~ "30-34",
                  Age <= 39 ~ "35-39",
                  Age <= 44 ~ "40-44",
                  Age <= 49 ~ "45-49",
                  Age <= 54 ~ "50-54",
                  Age <= 59 ~ "55-59",
                  Age <= 65 ~ "60+"
         )) %>% 
         mutate(AgeGroup = factor(AgeGroup, levels = c("10-14", "15-19",
                                                       "20-24", "25-29",
                                                       "30-34", "35-39",
                                                       "40-44", "45-49",
                                                       "50-54", "55-59",
                                                       "60+")))
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
