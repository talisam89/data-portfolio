# Data Portfolio by Talisa Manker
## 📬 Connect with Me
- 📧 [Email](mailto:talisamanker@gmail.com)
- 🔗 [LinkedIn](https://www.linkedin.com/in/talisamanker/)

## Projects
### ✈️ [Flight Prices](https://github.com/talisam89/data-portfolio/tree/main/flight-prices)
#### Business Problem
What is the optimal number of days before departure to book a flight for the lowest fare?
#### Project Summary
A large Flight Prices data set from Kaggle was used to answer the question. The data was explored and cleaned in a Jupyter Notebook create in PyCharm. The Python Dask library was used to increase data processing speeds. Much of the EDA and cleaning focused on reducing the data set to a size that could be uploaded within the file size restrictions for Looker Studio.  Creative problem solving and strategic thinking helped to shrink the dataset while keeping the data variety needed to answer the business question. The cleaned data was presented in a dashboard created in Looker Studio that split the data by airline and allows the user to filter by departure and destination airport.
#### Tech Stack 
Pycharm, Jupyter Notebook, Python (libraries: Dask, Pandas), Looker Studio 
#### Skills
Exploratory analysis, data cleaning, analyzing big data, data visualization, dashboard design, creative problem solving, strategic thinking
#### Project Outputs
- [EDA & Cleaning Notebook](https://github.com/talisam89/data-portfolio/blob/main/flight-prices/Flight_data_EDA.ipynb)
- [Looker Studio Dashboard](https://lookerstudio.google.com/reporting/fabdcd7e-3789-4d10-abc8-9696a26eed5e)
#### Project Description
##### Step 1 - Managing dataset size for exploratory analysis

Exploring the data was challenging due to its size. I immediately realized Pandas wasn't going to cut it, the data was taking too long to load. I searched for solutions and came across the Dask library which lets you work with data without loading it all into memory at once. 

Rather than immediately processing code, the Dask library creates a task graph that consists of primary tasks and subtasks with edges indicating dependencies. It can split the tasks across CPU cores and run them parallel when you tell it to start processing.

Tasks still took several minutes each to run and there were some issues with previewing the data due to not having all of it loaded into memory, but overall Dask worked well for cleaning and Exploratory Analysis
##### Step 2 - Exploratory Analysis

The initial Exploratory Analysis aimed to answer these questions:
- What columns can be dropped to reduce the dataset size?
- How can the data be filtered to reduce the dataset size?
- Which columns would be most useful as filters to enhance insight into the optimal number of days before departure to book a flight?

Initial exploratory analysis included looking at the data types and number of missing values in each column. It also included segmenting the data by different features (like airline, nonstop, airport etc.) to find ways to shrink the output so it could be uploaded to Looker Studio. 

Exploratory analysis turned into a bit of an iterative process where new questions came up after moving on to cleaning and even after uploading the data to Looker Studio. Initially I shrunk the data set to nonstop flights only, since that was the type of flight I was most interested in. I then tried to separate the data by airline to further reduce the data set. 

After separating out airlines and uploading data to Looker Studio I realized:
- There were some flights that were only $.01
- Not all flights had been checked every day.

I went back to exploratory analysis and discovered, with the help of ChatGPT, that the total fare for the airlines and dates used in this data set should be $25 or above. ChatGPT also mentioned that budget carriers like Spirit and Frontier could potentially have had sales making the total fare between $19 and $39 dollars.

A review of the airlines with total fare less than $25 showed it was only budget airlines (Spirit and Frontier) with the very low total fares. So I concluded I did not need to filter out any data due to unreasonable fare prices.

I had been using the base fare rather than the total fare as the flight price based on the assumption that taxes and fees differ by state and base fare would be a more even comparison. It turned out that taxes and fees are regulated pretty well and it’s the base fare the varies wildly. If I had explored the variance of these two price columns I might have figured that out sooner.

As I was working out the fare price issue I noticed that not all the flights had 60 records of price data. The price data had been recorded over 60 days and I wanted to compare flights that had been tracked for the entirety of this period so I could look at prices over time. I filtered out any flights that had less than 60 records, which ended up greatly reducing my data set and solving my optput size issue.

A downside to filtering out all those records is that it also filtered out all flights that weren't coach which meant I wouldn't be able to use cabin code as a filter on my dashboard. But this was a concession I was willing to make since I was more interested in coach flights anyways. 

##### Step 3 - Data Cleaning

Like exploratory analysis, data cleaning was a bit of an iterative process that I came back to after uploading the data to Looker Studio. Getting the data uploaded to Looker Studio was also tedious, I exported a lot of CSVs before finally arriving at a cleaned data set that would work for my purposes.  

One of the most challenging cleaning tasks was calculating and converting the number of days before departure a price was being checked. I needed to convert the date columns in the data to date time objects, create a new column that calculated the difference between the dates, and convert this difference to a number that could be used in a graph. 

The cleaning tasks I focused on included:
- Remove more unnecessary columns
- Convert searchDate and FlightDate to date objects that can be subtracted from each other
- Create days before departure column and numeric days before departure column
- Calculate daysBeforeDeparture using searchDate and FlightDate
- Convert segmentsDurationInSeconds from string to float64
- Convert segmentsDurationInSeconds to hours
- Limit data to flight IDs with 60 or more records

Columns To Remove: Why Remove Them:
- fareBasisCode: don't know what this means
- elapsedDays: don't think this impacts price much, usually 0
- segmentsDepartureTimeEpochSeconds: likely impacts price but choosing to not include
- segmentsDepartureTimeRaw: likely impacts price but choosing to not include
- segmentsArrivalTimeEpochSeconds: likely impacts price but choosing to not include
- segmentsArrivalTimeEpochSeconds: likely impacts price but choosing to not include
- segmentsArrivalTimeRaw: likely impacts price but choosing to not include
- segmentsArrivalAirportCode: focusing on departure and destination not layovers
- segmentsDepartureAirportCode: focusing on departure and destination not layovers
- segmentsAirlineCode: using the airline name
- segmentsEquipmentDescription: don't care about the type of plane
- travelDuration: in a weird format, using segmentsDurationInSeconds instead
- isBasicEconomy: doesn't match numbers of class, info in segment class more helpful
- isNonStop: already filtered on this and know data is only for nonstop flights
- segmentsDistance: going to use totalTravelDistance instead
- DistanceComp: column I added that I don't need

##### Step 4 - Building the Dashboard

The first graphs I created were the time series graphs showing the average flight price over time broken out by airline. The graphs looked cluttered with all the airlines on it so I separated the data by major and budget airlines. To help people visually distinguish the individual airline I color coded the lines based on the airlines official brand colors. 

I could format the y axes as currency despite the data being imported into Looker Studio as currency, but the currency format did show up in the tool tip. To reduce clutter in the graphs I did not include grid lines or axis labels. The interactive tool tips provide the context that grid lines would have provided. A combination of the graph title and trusting users are familiar with a time series format made the axis titles redundant. 

<img width="1198" height="371" alt="image" src="https://github.com/user-attachments/assets/d1a76a0a-8960-49cc-b8a5-97fd5cef7e14" />


