# Bellabeat Capstone Project- Data Analysis with R Programming
### How Can a Wellness TechnologyCompany Play It Smart?
![App Screenshot](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRFoQPNBtZytMqV0NFOItlkpuGHiAe31B_Fig&usqp=CAU)

Author

Vanessa Mestre

Date: February 6, 2023   

Introduction

Bellabeat is a wellness technology company founded in 2013 to empower woman with knowledge about their own health and habits. Bellabeat does this by collecting data on activity, sleep, stress, and their reproductive health. The company has produced several wellness trackers to monitor these trends on a personal basis. 


*This capstone project follows the six steps of data analysis*
# Ask
## Business Task/Scenerio

Bellabeat is looking to become larger player in the global smart device market. They would like to gain insights on how consumers are using their non bellabeat devices. Cofounder Sršen would like to apply this information to one Bellabeat device. These insights will help guide marketing strategy. 

### Key Steakholders:

**Urška Sršen:** Bellabeat's cofounder and Cheif Creative Officer 

**Sando Mur:** Mathematician and Bellabeat's cofounder; key member of the executive team 

# Prepare

#### *In this step I will check for data integrity, limitations, and credibility. This step also includes sorting and filtering the data.*

## Data 
Data Set: [FitBit Fitness Tracker Data](https://www.kaggle.com/arashnic/fitbit)
* This data set is being stored on Kaggle 
* Dataset made available through Mobius on CC0: Public Domain License( no copywright laws)
* Thirty eligible Fitbit users consented to the submission of personal tracker data, including minute-level output for physical activity, heart rate, and sleep monitoring. 
* There are 18 long format cvs files

### Does my data ROCCC? 
#### **Reliable:** 
Age and gender are unknown. How were users selected also unknown. 
#### **Originality:** 
Data was collected by a third party; Amazon Mechanical Turk. 
#### **Comprehensive:** 
This data provides a collection of daily, hourly, and  minute intensities and steps. Along with sleep patterns, calories, and weight/BMI. 
#### **Current:** 
This data was collected almost 7 years ago. COVID-19 was linked with significant decreases in mobility, walking, and physical activity, and increases in sedentary activity. [Study by NIH](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC8979477/)
#### **Cited:** 
Furberg, R., Brinton, J., Keating, M., & Ortiz, A. (2016). Crowd-sourced Fitbit datasets 03.12.2016-05.12.2016 [Data set]. [Zenodo](https://doi.org/10.5281/zenodo.53894)

### Sort and Filter Data 
#### Load Packages in R
```
library("dplyr")
library(tidyverse) 
library(here)
library(skimr)
library(janitor)
library(ggbeeswarm)
library("lubridate")

```
#### Load/Import Data 
```
dailyActivity_merged.cvs
dailyCalories_merged.cvs
dailySteps_merged.cvs 
dailyIntensities_merged.cvs
sleepDay_merged.cvs
weightLogInfo_merged.cvs
```
#### Read in Data 
```
activity <- read_cvs(here("data", "dailyActivity_merged.cvs"))
calories <- read_cvs(here("data", "dailyCalories_merged.cvs"))
steps <- read_cvs(here("data", "dailySteps_merged.cvs"))
intensities <- read_cvs(here("data", "dailyIntensities_merged.cvs"))
sleep <- read_cvs(here("data", "sleepDay_merged.cvs"))
weight <- read_cvs(here('data", "weightLogInfo_merged.cvs"))
``` 

# Process 
#### *In this step I will be using R programming to clean the data and look for inconsistencies:*

```
 glimpse(dailyActivity_merged)

```
![Bellabeat -Glimpse shot](https://user-images.githubusercontent.com/124302237/217386617-78254173-cfc8-4e34-a152-8cb70e8ce69c.png)

```
glimpse(dailyCalories_merged)
```
![DailyCalories- Glimpse shot](https://user-images.githubusercontent.com/124302237/219078737-dceb4113-d0be-4410-8b05-daa2c472f001.png)

```
glimpse(sleepDay_merged)
```
![sleepDay- Glimpse shot](https://user-images.githubusercontent.com/124302237/219081222-78613d0d-3e98-4334-836e-529f03a06c1a.png)

```
glimpse(dailyIntensities_merged)
```
![2023-02-15 (6)](https://user-images.githubusercontent.com/124302237/219113740-66084cc9-dc98-43f8-83f3-244646e8713e.png)


```
glimpse(dailySteps_merged)
```
![glimpseDailySteps](https://user-images.githubusercontent.com/124302237/219100931-93f2b121-831d-4772-ab2b-1db3004701f9.png)

#### *Taking a closer look I can see the 'Activity date' columns are in chr form. I will change to date format for better analysis....* 

```
# dailyActivity_merged
dailyActivity_merged$ActivityDate = as.POSIXct(dailyActivity_merged$ActivityDate, format = "%m/%d/%Y", tz=Sys.timezone())
dailyActivity_merged$date <- format(dailyActivity_merged$ActivityDate, format = "%m/%d/%y")
```
```
#dailyCalories_merged
dailyCalories_merged$ActivityDay = as.POSIXct(dailyCalories_merged$ActivityDay, format = "%m/%d/%Y", tz=Sys.timezone())
dailyCalories_merged$date <- format(dailyCalories_merged$ActivityDay, format = "%m/%d/%y") 
```
```
#dailySteps_merged
dailySteps_merged$ActivityDay = as.POSIXct(dailySteps_merged$ActivityDay, format = "%m/%d/%Y", tz=Sys.timezone())
dailySteps_merged$date <- format(dailySteps_merged$ActivityDay, format = "%m/%d/%y") 
```
```
#dailyIntensities_merged
dailyIntensities_merged$ActivityDay = as.POSIXct(dailyIntensities_merged$ActivityDay, format = "%m/%d/%Y", tz=Sys.timezone())
dailyIntensities_merged$date <- format(dailyIntensities_merged$ActivityDay, format = "%m/%d/%y") 
```
```
#hourlyIntensities_merged... keeping am/pm 
hourlyIntensities_merged$ActivityHour = as.POSIXct(hourlyIntensities_merged$ActivityHour, format = "%m/%d/%Y %I:%M:%S %p", tz=Sys.timezone())
hourlyIntensities_merged$date <- format(hourlyIntensities_merged$ActivityHour, format = "%m/%d/%y")
hourlyIntensities_merged$Time <- format(hourlyIntensities_merged$ActivityHour, format = "%H:%M:%S")
```
### *Looking for duplicates...*

```
dailyActivity_merged %>% distinct(Id) = 33
dailyCalories_merged %>% distinct(Id) = 33
dailySteps_merged %>% distinct(Id) = 33
dailyIntensities_merged %>% distinct(Id) = 33
sleepDay_merged %>% distinct(Id) = 24
weightLogInfo_merged %>% distinct(Id) = 8
hourlyIntensities_merged %>% distinct(Id)= 33
```
```
sum(duplicated(dailyActivity_merged)) = 0
sum(duplicated(dailyCalories_merged)) = 0
sum(duplicated(dailyIntensities_merged)) = 0
sum(duplicated(sleepDay_merged)) = 3
sum(duplicated(dailySteps_merged)) = 0
sum(duplicated(hourlyIntensities_merged)) = 0
```

*SleepDay_merged had 3 duplicates I removed. Leaving a total of 21 users.*
```
sleepDay_merged1 <- sleepDay_merged %>% distinct()
```
There are inconsistencies with the data. Four datasets contain 33 distinct Id's when there should only be 30 Fitbit users. SleepDay_merged contains 21 distinct Id's and weightLogInfo_merged contains 8 Id's. I will not be working with the weight dataset since more data would be needed to make full accurate analysis. 


# Analyze

#### *In this step I will be taking a closer look at the data;*

```
summary(dailyActivity_merged)
```
![2023-02-18 (1)](https://user-images.githubusercontent.com/124302237/219903618-9850a7ce-1b18-4b4e-b503-40e9100bb600.png)


##### *Review of "daily activity" Summary:*

*  The average steps taken per day is 7638.
* On average users spent :

    21.16 minutes "Very Active" per day
    
    192.8 minutes "Lighty Active" per day

    991.2 minutes "Sedentary" per day 

* The average "Calories" burned per day was 2304 kcal. 

According to the CDC, each week adults need 150 minutes of moderate-intensity physical activity and 2 days of muscle strengthening activity. 
The average 'Very Active" minutes users spent per week 148.12. And "Lighty Active" 1,349.60 minutes.  [CDC Guidelines](https://health.gov/our-work/nutrition-physical-activity/physical-activity-guidelines/current-guidelines)


According to the Dietary Guidelines for Americans 2020–2025, the average adult woman expends roughly 1,600 to 2,400 calories per day, while the average adult man expends 2,000 to 3,000. On average these users are burning 2304 kcal per day. [Everyday Health](https://www.everydayhealth.com/weight/how-to-achieve-one-pound-of-weight-loss.aspx#:~:text=According%20to%20the%20Dietary%20Guidelines,man%20expends%202%2C000%20to%203%2C000.)

*Looking for correlation between steps and calories* 

```
cor(dailyActivity_merged$TotalSteps, dailyActivity_merged$Calories)
```
![2023-02-22](https://user-images.githubusercontent.com/124302237/220740824-115d04d7-db6b-496e-822c-8e97bab7fab8.png)

The correlation between sleep and step is 0.591; that makes it a positive correlation between the two. The more daily steps, the more daily calories you burn. 

![image](https://user-images.githubusercontent.com/124302237/220756433-656a8083-1a01-4ecb-9d25-5a04107fe644.png)


```
summary(sleepDay_merged)
```
![2023-02-19](https://user-images.githubusercontent.com/124302237/219948758-2eb49ce1-abd8-4dc0-8ba2-4209b47a3fb7.png)

##### *Review of "sleep" Summary*:
* Only 21 users recorded their  sleeping patterns. 
* The average user spent 7:38:24 hours/minutes in bed 
* Out of those 7:38:24 they spent 6:59:21 hours/minutes asleep
* It took on average 39 minutes for users to fall asleep. 

According to the National Sleep Foundation: healthy adults need between 7 and 9 hours of sleep per night. Assuming these users are healthy adults, based on their average time alseep, they have good sleeping habits. [National Sleep Foundation](https://www.sleepfoundation.org/how-sleep-works/how-much-sleep-do-we-really-need)

I would also find the relationship between sleep and steps. I will merge sleep and daily activities to have a better analysis. 
```
sleep_activityData <- merge(sleepDay_merged, dailyActivity_merged, by= c('Id', 'date'))
```


```
summary(dailySteps_merged)
```
![2023-02-21](https://user-images.githubusercontent.com/124302237/220351964-ceda781f-0dd8-4380-8a3b-3ac4d6f00836.png)

##### *Review of "steps" Summary:*

According to the CDC the new guidelines recommend 8000 steps or more. Taking 8,000 steps per day was associated with a 51% lower risk for all-cause mortality (or death from all causes). [CDC](https://www.cdc.gov/media/releases/2020/p0324-daily-step-count.html#:~:text=They%20found%20that%2C%20compared%20with,compared%20with%20taking%204%2C000%20steps.)

The average daily steps per user is 7638, just a bit below the recommended CDC guidelines. 


Most steps occured on Saturdays and Sundays. 

![image](https://user-images.githubusercontent.com/124302237/220362537-f298f4ad-02ee-4c6d-863b-a1f20b2ea2d9.png)

```
summary(hourlyIntensities_merged)
```
![2023-02-22 (3)](https://user-images.githubusercontent.com/124302237/220748039-fc10689a-1ad0-4fb6-9c2b-6963ff6fc21b.png)

##### *Review of "hourly Intensities" Summary:*
* The average hourly intensity occured around midday, at approximately 11:46:42am. 
* Looking at my chart below we can visualize most intense activities took place between 9am and 8pm. This makes sense because the majority of people work from 9-5pm. 



![2023-02-22 (2)](https://user-images.githubusercontent.com/124302237/220744431-48027960-b681-4e10-88be-4850c7d8fc19.png)

## Act

### Conclusion:
*Based on my analysis of the data:* 
* There is a positive correlation beteween daily steps and daily calories burned. 
* The average of daily steps were below the recommended amount of 8,000 or more. 
* Most users maintained a healthy amount of activity based on CDC guidelines. 
* Users also slept the average recommended hours of 7-9 hours. 
* These users were mostly active on Saturdays and on a dayly basis from 9am-8pm.  
* Although users met guidelines, the average of sleep, daily activity, and steps were all borderline. 

### Recommendations:

Although, some more data was needed to make better analysis; based on what we have I can recommend Bellabeat to include several features on their *Time* device. 

* Allow users to set daily recommended goals on their device. These goals can include daily steps, daily calories burned, and sleeping hours. 
* Send notifications to users not meeting their daily goals to remind them. 
* Reward users when daily goals are completed. These can include badges or different levels challenge. 
* Send notifications and reminders with factual data to educate users on reasons why those guidelines are recommended. 








