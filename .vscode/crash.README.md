# **Google Data Analytics Capstone**

 ## **Introduction**

For completion of the Google Data Analytics Certification, students must complete a Capstone Case Study with 2 options. Use a predetermined dataset from the course or research and download our own. This project was a case study of documented accidents in the US ranging from 2018 to 2021. I began following the 6 steps data analysis which are **Ask**, **Prepare**, **Process**, **Analyze**, **Share** and **Act** to outline my analysis project.

 ## **Ask:**

The first step in the Data Analysis Process is Ask. There were several questions I wanted to ask...

    - How many accidents were there in the US over the 4-year period? Which states had the most, least and were there major differences during those years?
    - Then, I wanted to look at every month individually and see which months had the greatest and least deaths in the 4 years?
    - How many deaths occurred each month during the 4 years? How did the percentages align compared to the total number of accidents?
    - If weather and driving conditions play a major factor in the number of accidents? And if so, what were the most dangerous conditions?
    - If different speed limits were a major factor of accidents and which zones had the highest?
    - Last, what type of vehicles were most involved in accidents?

## **Prepare:**

I began my search for datasets that were consistent and would be suitable for the questions asked in my case study. There was no shortage of data on accidents, and I eventually came across 4 datasets from National Highway Traffic Safety Administration <https://www.nhtsa.gov/file-downloads?p=nhtsa/downloads/FARS/>. They were downloaded and saved to my secure drive and password protected.

## **Process:**

 After downloading the datasets, cleaning was the next step. Each file had several documents with different information which I started compiling into my own worksheet in Excel and had separate sheets for each year. After compiling all the necessary data, I went through each set removing redundant information, correcting spelling, spacing, correcting time and date formats, keeping the same layout and column names for easier analysis, and made sure the data had not been compiled and arranged in a bias manner. The overall integrity of the datasets was overall great compared to others I had encountered. Once the cleaning was complete, they were saved so they could be analyzed.

## **Analyze and Share:**

Over the past several years, and during this course, I have learned how to use several data analysis tools such as Microsoft Word, Excel, SQL, R, Tableau and others. I chose to complete this project in R because I could complete my analysis and create the visuals in the same program. I created a Posit Cloud account and began uploading my datasets, answering the questions for this project, and visualizing the results.

The first step after uploading the datasets was to import them to R, and to install the packages I needed for this process, and then view my datasets-
```r
## Install Packages ##

install.packages('ggplot2')
install.packages('ggmap')
install.packages('ggraph')
install.packages('tidyr')
install.packages('dplyr')
install.packages('tidyverse')
install.packages('data.table')
install.packages('readxl')
install.packages('RColorBrewer')
install.packages('usmap')
install.packages('gridExtra')
install.packages('plotly')
install.packages('wesanderson')


library(ggplot2)
library(ggmap)
library(ggraph)
library(tidyr)
library(dplyr)
library(tidyverse)
library(data.table)
library(readxl)
library(RColorBrewer)
library(usmap)
library(gtrendsR)
library(gridExtra)
library(plotly)
library(wesanderson)

## Upload and Import Data sets ##

view(crash2018)
view(crash2019)
view(crash2020)
view(crash2021)
```
Then I needed to arrange the months in each datasets to chronological order because R will, by default, arrange the results in alphabetical order.
```r
#### Total Number of Accidents Per Year ####

## Ordering Months in Chronological Order ##

crash2018$MONTH <- factor(crash2018$MONTH, levels = c("January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"))
crash2019$MONTH <- factor(crash2019$MONTH, levels = c("January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"))
crash2020$MONTH <- factor(crash2020$MONTH, levels = c("January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"))
crash2021$MONTH <- factor(crash2021$MONTH, levels = c("January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"))
```
I wanted to use a map of the US to display the results for this analysis so I needed to compile and do an initial plot of the information, apply a blank map of the US in the console, assign all the data to the correct state and use a map plot after all the data was rendered.
```r
## Calculate Total Accidents per State ##

crash2018 %>%
  count(STATE, sort = TRUE) %>% 
  arrange(STATE)
crash2019 %>%
  count(STATE, sort = TRUE) %>% 
  arrange(STATE)
crash2020 %>%
  count(STATE, sort = TRUE) %>% 
  arrange(STATE)
crash2021 %>%
  count(STATE, sort = TRUE) %>% 
  arrange(STATE)

plot_data <- crash2018 %>%
  count(STATE, sort = TRUE)

## Plot map data blank ##

plot_usmap(regions = "states") + 
  labs(title = "US",
       subtitle = "Blank Map")

## Create Data Frame with Values ##

df <- data.frame(
state = c('Alabama', 'Alaska', 'Arizona', 'Arkansas', 'California',
          'Colorado', 'Connecticut', 'Delaware', 'Florida', 'Georgia', 
          'Hawaii', 'Idaho', 'Illinois', 'Indiana', 'Iowa', 'Kansas', 
          'Kentucky', 'Louisiana', 'Maine', 'Maryland', 'Massachusetts', 
          'Michigan', 'Minnesota', 'Mississippi', 'Missouri', 'Montana', 
          'Nebraska', 'Nevada', 'New Hampshire', 'New Jersey', 'New Mexico',
          'New York', 'North Carolina', 'North Dakota', 'Ohio', 'Oklahoma',
          'Oregon', 'Pennsylvania', 'Rhode Island', 'South Carolina', 
          'South Dakota', 'Tennessee', 'Texas', 'Utah', 'Vermont', 'Virginia', 
          'Washington', 'West Virginia', 'Wisconsin', 'Wyoming'),
values = c(876, 69, 918, 476, 3485, 588, 275, 104, 2917, 1408, 110, 215, 951, 776, 291, 367, 664, 719, 127, 485, 338, 907, 349, 596, 848, 167, 201, 299, 134, 524, 351, 910, 1321, 95, 996, 603, 446, 1103, 56, 969, 110, 973, 3311, 237, 60, 778, 490, 265, 531, 100))
  
## All data rendered, Plotting for visualization ##

plot_usmap(data = df, regions = 'states', values = 'values', color = "black") + 
  scale_fill_continuous(name = "Accidents") + 
  theme(legend.position = "right")
  labs(title = "Accidents 2018")
```
![Accident map of US](/crash.vis/map%202018.png)
According to this survey, in the 4 years, there were a combined total of 140,679 accidents in the US, with California, Texas and Florida having the highest percentages and Rhode Island, Alaska and Vermont being the lowest.

The next question was the death per month ratio in each year, so I had to aggregate the totals for each month individually, create a new datata set and plot the results on individual plots.
```r
#### Determine how many Deaths Per Year ####

## Renaming Columns ##
  
colnames(df_t)<-c(MONTH = Month,"2018","2019","2020","2021")
colnames(df_md1)<-c("Month","2018")
colnames(df_md2)<-c("Month","2019")
colnames(df_md3)<-c("Month","2020")
colnames(df_md4)<-c("Month","2021")
         

## Find Total Deaths per Year

aggregate(x = crash2018$DEATHS,
          by = list(y = crash2018$MONTH),
          FUN = sum)
aggregate(x = crash2019$DEATHS,
          by = list(y = crash2019$MONTH),
          FUN = sum)
aggregate(x = crash2020$DEATHS,
          by = list(y = crash2020$MONTH),
          FUN = sum)
aggregate(x = crash2021$DEATHS,
          by = list(y = crash2021$MONTH),
          FUN = sum)

#### Create New Data Frame with Death/Month Totals and Plot ###

Month = c('January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October','November', 'December')
factor(Month, levels = month.name)
d2018 = c(1752, 1501, 1707, 1780, 2025, 2067, 2128, 2024, 2001, 1964, 1757, 1719)
d2019 = c(1561, 1394, 1655, 1733, 2017, 2039, 2112, 2086, 2052, 1934, 1786, 1774)
d2020 = c(1512, 1534, 1501, 1481, 1949, 2358, 2278, 2348, 2226, 2194, 1995, 1926)
d2021 = c(1847, 1533, 1931, 2195, 2397, 2383, 2388, 2492, 2354, 2444, 2102, 1977)
df_ts <- data.frame(Month,d2018,d2019,d2020,d2021)
  
## Factor Months in Chronological Order ##

df_ts$Month <- factor(df_ts$Month, month.name)

## Plot Deaths Per Year ##

plot1 <- ggplot(df_ts, mapping = aes(x = Month, y = d2018,))+
  geom_col(fill = 'blue')+
  labs(title = "2018 US Deaths",x='Month',y='Deaths')

plot2 <- ggplot(df_ts, mapping = aes(x = Month, y = d2019,))+
  geom_col(fill = 'purple')+
  labs(title = "2019 US Deaths",x='Month',y='Deaths')

plot3 <- ggplot(df_ts, mapping = aes(x = Month, y = d2020,))+
  geom_col(fill = 'red')+
  labs(title = "2020 US Deaths",x='Month',y='Deaths')

plot4 <- ggplot(df_ts, mapping = aes(x = Month, y = d2021,))+
  geom_col(fill = 'grey')+
  labs(title = "2021 US Deaths",x='Month',y='Deaths')

## Arrange Plots Together ##
 
grid.arrange(plot1,plot2,plot3,plot4)
``` 
![Multiplot of Deaths per year](/crash.vis/4%20death%20total.png)

These findings showed the months with the highest death rate for accidents were between June, July and August, while February and March were the lowest. 

The next question to answer was, compare the total number of accidents to the total number of deaths that occured and calculate the percentages of those results.
```r
#### Find Percentages of Deaths per Year ####

## Count Death Totals Per Year ##

crash2018 %>%
  count(DEATHS, sort = TRUE)

crash2019 %>%
  count(DEATHS, sort = TRUE)

crash2020 %>%
  count(DEATHS, sort = TRUE)

crash2021 %>%
  count(DEATHS, sort = TRUE)

## Create Data Frame With Death Totals##

Year <- c(2018,2019,2020,2021)
Deaths <- c(20958,20714,21397,24423)
Crashes <- c(33889,33487,33835,39468)
df_p1 <- data.frame(Year,Deaths,Crashes)

## Calculate Percentage Of Deaths ##

df_p1 %>%
  group_by(Year,) %>%
  summarise(Perc = Deaths / Crashes) %>%
  spread(Year, Perc)

## Create Data Frame For Percentages ##

Year = c(2018,2019,2020,2021)
Percent = c(0.618,0.619,0.632,0.619)
plot12 <- data.frame(Year,Percent)

## Potting Death Percentages ##

ggplot()+
  geom_col(data = plot12,mapping = aes(x=Year,y=Percent,fill = Percent))
```   
![Death percentages per year](/crash.vis/Death%20Percentages.png)
The percentages of deaths were fairly consistant over the 4 years and accounted for less than 1% of total accidents.

 Then I wanted to know if weather and driving conditions played a major factor in the number of accidents. And if so, what were the most dangerous conditions. So I needed to calculate the total accidents by different road conditions, arrange them in a dataset and compare them.
```r
#### Find What Road Conditions Play Role In Accidents ####

crash2018 %>%
  count(CONDITION, sort = TRUE)

crash2019 %>%
  count(CONDITION, sort = TRUE)

crash2020 %>%
  count(CONDITION, sort = TRUE)

crash2021 %>%
  count(CONDITIONS, sort = TRUE)

## Create Data Frame With Condition Totals ##

Condition=c('Dry','Wet','Other','Ice/Snow','Mud/Dirt/Gravel','Sand')
Accidents=c(98079,17195,3565,2396,102,16)
Con2018 <- data.frame(Condition,Accidents)

ggplot(Con2018,aes(x=Condition,y=Accidents))+
    geom_point(
      color = 'black',
      fill = 'red',
      shape = 21,
      size = 4)
```
![Road Condition Ratio](/crash.vis/Condition%20Percentages.png)
From the graph shown, you can see that a majority of the accidents happened in dry conditions at almost 100,000 total. The next closest was in wet conditions at just over 17,000.

Next, I wanted to know if higher speed limits were a major factor of accidents and which zones had the highest, and which were the lowest. I needed to calculate each year totals for each speed limit, which were compiled in groups at 75, 65, 55, 45, 35, 25 and 15 mph.
```r
#### 4 Year Combined Total for Accidents in Different Speed Limits ####

crash2018 %>%
  count(SPD_LIM, sort = TRUE) %>%
  arrange(desc(SPD_LIM))
  
crash2019 %>%
  count(SPD_LIM, sort = TRUE) %>%
  arrange(desc(SPD_LIM))
  
crash2020 %>%
  count(SPD_LIM, sort = TRUE) %>%
  arrange(desc(SPD_LIM))
  
crash2021 %>%
  count(SPD_LIM, sort = TRUE) %>%
  arrange(desc(SPD_LIM))

## Create Data Frame For Speed Limit Ratios ##
```r
Speed_Limit=c('15 MPH','25 MPH','35 MPH','45 MPH','55 MPH','65 MPH','75 MPH')
Accidents=c(509,7726,23371,32818,40611,17015,11608)
df_s <- data.frame(Speed_Limit,Accidents)

## Plot Pie Chart For Speed Limit Ratios ## 

plot_ly(df_s, labels = ~Speed_Limit, values = ~Accidents, type = 'pie') %>%
    layout(title = "Speed Limit to Accident Ratio",
           legend=list(title=list(text='Speed Limit', font = 2)), 
           xaxis = list(showgrid = FALSE, zeroline = FALSE, showticklabels = FALSE),
           yaxis = list(showgrid = FALSE, zeroline = FALSE, showticklabels = FALSE),
           geom_label(aes(label = scales::percent(percentage), x = 1.3),
           position = position_stack(vjust = 0.5)))
```
![Speed Limit Ratios](/crash.vis/SLR.png)

With these findings, I found that 55 mph and 45 mph had the highest percentages of accidents in these zones and 25 mph and 15 mph had the lowest. The 2 zones with the highest rates of speed were in the lower ratios. There were even more accidents in 35 mph zones than in 65 mph and 75 mph zones.

 Last, I wanted to know which type of vehicles were involved in the most accidents. They were broken down into 9 different classes. They were station wagons, sedans, SUV, pickup, motorcycle/scooter, minivan, bus, box truck/tractor trailer and if they did not fall into any of these classes, they were documented as other. I grouped them by body type for each year, calculated each type, put them in individual datasets, plotted each and combined them into one graph.
```r
#### Vehicle Body Types Involved In Accidents Each Year ####

## Create Body Type Data Frames For Each Year ##
df_bt1 <- crash2018 %>%
  count(BODY_TYPE, sort = TRUE) %>%
  arrange(desc(BODY_TYPE))

df_bt2 <- crash2019 %>%
  count(BODY_TYPE, sort = TRUE) %>%
  arrange(desc(BODY_TYPE))

df_bt3 <- crash2020 %>%
  count(BODY_TYPE, sort = TRUE) %>%
  arrange(desc(BODY_TYPE))

df_bt4 <- crash2021 %>%
  count(BODY_TYPE, sort = TRUE) %>%
  arrange(desc(BODY_TYPE))

## Plot Each Data Frame ##

plot_bt1 <- ggplot(df_bt1, aes(fill = BODY_TYPE,x=BODY_TYPE,y=n,))+
  geom_col()+
  scale_fill_brewer(palette = "Blues")+
  scale_y_continuous(limits = c(0, 12500),
                     oob = scales::squish)+
  labs(x = 'Body Type', y = 'Accidents')+
  theme(axis.text.x = element_text(angle = 90, vjust = 0.5, hjust=1))

plot_bt2 <- ggplot(df_bt2, aes(fill = BODY_TYPE,x=BODY_TYPE,y=n,))+
  geom_col()+
  scale_fill_brewer(palette = "BuGn")+
  scale_y_continuous(limits = c(0, 12500),
                     oob = scales::squish)+
  labs(x = 'Body Type', y = 'Accidents')+
  theme(axis.text.x = element_text(angle = 90, vjust = 0.5, hjust=1))

plot_bt3 <- ggplot(df_bt3, aes(fill = BODY_TYPE,x=BODY_TYPE,y=n,))+
  geom_col()+
  scale_fill_brewer(palette = "Oranges")+
  scale_y_continuous(limits = c(0, 12500),
                     oob = scales::squish)+
  labs(x = 'Body Type', y = 'Accidents')+
  theme(axis.text.x = element_text(angle = 90, vjust = 0.5, hjust=1))

plot_bt4 <- ggplot(df_bt4, aes(fill = BODY_TYPE,x=BODY_TYPE,y=n,))+
  geom_col()+
  scale_fill_brewer(palette = "BuPu")+
  scale_y_continuous(limits = c(0, 12500),
                     oob = scales::squish)+
  labs(x = 'Body Type', y = 'Accidents')+
  theme(axis.text.x = element_text(angle = 90, vjust = 0.5, hjust=1))
  
## Combine Each Plot Into 1 ##

grid.arrange(plot_bt1,plot_bt2,plot_bt3,plot_bt4)
``` 
![Body Type Involved each year](/crash.vis/Body%20Type.png)

From this data I learned that sedans and SUVs were consistantly involved in more accidents than any other type of vehicle during this time.

## **Act:**

The final step of the Data Analysis Process is Act. This the step where you apply insight and make decisions based on the data you have analyzed. I learned that from 2018 through 2021 which states had the highest and lowest amount of accidents, what months had the highest and lowest number of accidents, percentage of death rates that occurred during this time, if weather conditions played a role in the amount of accidents, if speed had any factor in amount of accidents and what type of vehicles were involved the most and the least. 