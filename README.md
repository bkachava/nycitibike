# Citi Bike Analytics - Tableau Assignment

## Background

Citi Bike, NYC's official bike share program, was designed to give residents 
and visitors a fun, affordable and convenient alternative to walking, taxis, 
buses and subways.

There are 800 Citi Bike stations and 13,000 bikes across Manhattan, Brooklyn, 
Queens and Jersey City. 

You can join as an Annual Member (Subscriber) or buy a Day Pass or 3-Day Pass 
(Customer), and you can take as many rides as you want while your membership 
or pass is active.


## Assignment

To implement a dashboard or reporting process for answering City officials 
questions on the program taking in consideration the bike data collected, organized, 
and made public on the [Citi Bike System Data](https://www.citibikenyc.com/system-data),
using Tableau in some way.


## Data Analysis Process

Although the activities are listed in order, some of them were revisited 
more than once to refine the analysis.


### 1. Defining the problem to solve

In this activity, the questions requiring answers were identified and
grouped by similarity. Some of the initial questions are:

* How many trips have been recorded during the chosen period
* By what percentage has total ridership grown
* How has the proportion of short-term customers and annual subscribers changed
* What is the gender breakdown of active participants (Male v. Female)
* How effective has gender outreach been in increasing female ridership over the timespan
* What are the peak hours in which bikes are used during summer months? 
* What are the peak hours in which bikes are used during winter months?
* What are the top 10 stations in the city for starting a journey
* What are the top 10 stations in the city for ending a journey
* What are the bottom 10 stations in the city for starting a journey
* What are the bottom 10 stations in the city for ending a journey
* How does the average trip duration change by age
* What is the average distance in miles that a bike is ridden
* Which bikes (by ID) are most likely due for repair or inspection in the timespan
* How variable is the utilization by bike ID


### 2. Gathering business knowledge

The objective for this activity was learning about the Program and the rules 
that shaped the data to be analyzed.

Also, the knowledge acquired by doing this activity was useful in the decision
making process done in the next steps.


### 3. Collecting Citi Bike trip data

The Citi Bike System Data provides csv files that include:

Trip Duration (seconds) | Start Time and Date | Stop Time and Date |
Start Station Name | End Station Name | Station ID | Station Lat/Long |
Bike ID | User Type (Customer = 24-hour pass or 3-day pass user; Subscriber = Annual Member) |
Gender (Zero=unknown; 1=male; 2=female) | Year of Birth

During this activity, the period needed to answer the questions 
was set to 2017 and 2018, and it was necessary to download 24 csv files.

Additionally, the stations catalog was downloaded from this
[URL](https://gbfs.citibikenyc.com/gbfs/en/station_status.json).

The csv files were not saved in this repository because they are very large, 
however, the aggregates created are available. See activity 5 for more
detail.


### 4. Cleaning and transforming data

The content of the monthly files was read using Pandas and converted to data frames.

For all the files the treatment was the same, however, there were differences
in the data that required specific actions for each year.

For 2017, after reading each file, missing values for Birth Year and 
User Type were identified. The Birth Year was filled with the median 
of the grouping by Gender and Birth Year. The User Type was filled as a 
Customer.

For 2018, after reading each file, missing values for Start Stations and 
End Stations were identified. The Start Station Id was set to 8000 and
the End Station Id was set to 9000, for later identification.

Also, the name of the columns had changes from year to year, and even from
month to month in the same year, so adaptations to the previous written code 
had to be made.

With respect to outliers or surprising data points there were certain concerns: 
Trip Duration, Birth Year, and Mileage estimates.

`Trip Duration`

The Citi Bike System Data establish in its web page that the data
has been processed to exclude some types of trips, however there 
were data points like the following:

Year 2018 | Month January | Trip duration in seconds 4'421,929 - Approximately 51 days |
From station 270 - Adelphi St & Myrtle Ave	 | To station 3652 - NYCBS Depot - 3AV	

Year 2018 | Month May | Trip duration in seconds 2'587,767 - Approximately 30 days |
From station 3374 - Central Park North & Adam Clayton Powell Blvd | 
To station 3426 - JCBS Depot

Year 2018 | Month July | Trip duration in seconds 301,182 - Approximately 3.5 days |
From station 423 - W 54 St & 9 Ave | To station 3645 - Bike Mechanics at Riis Room B

These rows could be considered as trips, because they started at a
publicly available stations, however the trip duration goes
against a good customer decision, due to the extra fees after 
the first 30 or 45 minutes.

To make visualizations by trip duration, a calculated column was
created for storing the conversion from seconds to minutes.


`Birth Year`

The other concern was Birth Year. It is not clear when this
information is asked to the customer, but for the analysis two things
were considered: this Program is not recommended if you plan to ride 
with children under 16 years old and there is no validation applied to
this column.

To make visualizations by age a calculated column was created, simply by
subtracting the Birth Year to the year of analysis (2017 or 2018).


`Mileage estimates` 
 
According to the Citi Bike System Data:

- Trip count and mileage estimates include trips with a duration 
of greater than one minute.

- Mileage estimates are calculated using an assumed speed of 
7.456 miles per hour, up to two hours. 
Trips over two hours max-out at 14.9 miles. 

- Once you opt into Ride Insights, the Citi Bike app will use 
your phone's location to record the route you take 
between your starting and ending Citi Bike station to 
give exact mileage.

To make visualizations by mileage, a calculated column was
created taking into consideration the trip duration and the 
number of miles per hour proposed by Citi Bike System Data.

However, considering that most of the trips are made by
Subscribers and the issues described above related with trips 
that lasted more than 3 hours, an alternative approach for 
measuring the distance in miles was proposed.

A function in Python was used to calculate the distance between 
latitude-longitude pairs. See [haversine.py](https://gist.github.com/rochacbruno/2883505).
In this approach, the miles are calculated taking into consideration
only the geographic location of the starting and the ending stations.


At this point, the decision to include all data points in the results,
for conserving the totals reported by the Program in their
press releases, was made.


### 5. Exploring and analyzing data

Data exploration was made to build intuition and find patterns,
that later were reported as conclusions. The results can be seen 
in the [Jupyter Notebooks](/Notebooks).

Before trying to load more than 33 million records in Tableau, 
__aggregates__ using Pandas were created. These aggregates where saved as 
csv files for later use as data sources in Tableau. The aggregates
for 2017 can be seen [here](/Notebooks/2017), the aggregates
for 2018 can be seen [here](/Notebooks/2018), and the comparison 
can be seen [here](/Notebooks/comparison).


### 6. Creating visualizations

In order to provide an answer to the initial questions, the aggregates
created were loaded in Tableau and some visualizations were made to test
if the information contained on them was enough to satisfy the users'
information needs. 

Some adaptations were made after creating the first visualizations, mostly
to treat missing data or outliers. Also, since this is a project for the city, 
time was spent to find colors that were aligned to the visual footprint of 
the Program.

For the maps required in the assignment, a decision to present one map for
each year was made (static + dynamic). In each map, all bike stations with a 
visual indication of the most popular locations to start and end a journey 
with zip code data are showed. Also, through filters the user can see how each 
station's popularity changes over time.

The visualizations for 2017 can be seen in the Tableau file located 
[here](/Tableau/citibike2017.twbx).

The visualizations for 2018 can be seen in the Tableau file located 
[here](/Tableau/citibike2018.twbx).

The comparing visualizations between the two years can be seen in the 
Tableau file located [here](/Tableau/citibikecomp.twbx).


### 8. Drawing conclusions

For each visualization a brief conclusion was made, adding other pieces of
information on socioeconomic or geographic data. Weather was researched, 
but was not included in the conclusions because even when 2018 was on average
colder than 2017, there were more trips in 2018.

With respect to the unexpected phenomena, a few visualizations considering 
the day of the week were included and an alternative approach to measure
the distance in miles was proposed.


### 9. Communicating the results

For communicating the results, the first task was to identify who will be the users. 

For this project, the users will be city officials, public administrators, and heads 
of New York City departments. Therefore, the results are presented in a way that is 
focused, concise, easy-to-understand, and visually compelling. 

The selected tool to communicate the results was building a dashboard using __HTML, 
Bootstrap with the Lux theme and JavaScript__, mostly to call the visualizations 
made in Tableau Desktop and hosted in Tableau Public, without compromising the 
interactivity that Tableau provides.

The visualizations are colorful enough to be included in press releases, 
and the analysis is thoughtful enough for dictating programmatic changes.
 

## Dashboard

See the [dashboard](https://bkachava.github.io/nycitibike/Dashboard/index.html).

![dash](/Dashboard/images/dashboard.png)

