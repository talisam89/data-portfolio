# Flight Prices 
### Project Overview
#### Business Problem
What is the optimal number of days before departure to book a flight for the lowest fare?
#### Summary
A large Flight Prices data set from Kaggle was used to answer the question. The data was explored and cleaned in a Jupyter Notebook create in PyCharm. The Python Dask library was used to increase data processing speeds. Much of the EDA and cleaning focused on reducing the data set to a size that could be uploaded within the file size restrictions for Looker Studio.  Creative problem solving and strategic thinking helped to shrink the dataset while keeping the data variety needed to answer the business question. The cleaned data was presented in a Looker Studio dashboard that split the data by airline and allowed the user to filter by departure and destination airport. 

#### Tech Stack 
Pycharm, Jupyter Notebook, Python (libraries: Dask, Pandas), Looker Studio 
#### Skills
Exploratory analysis, data cleaning, analyzing big data, data visualization, dashboard design, creative problem solving, strategic thinking, data wrangling, data quality assessment, iterative analysis, tool optimization 

#### Data Set
The data set was created by Dillon Wong on Kaggle. The dataset contains **82.1M rows** and **27 fields**, totaling **31.1 GB** in size.
[Click here to view the Flight Prices dataset on Kaggle](https://www.kaggle.com/datasets/dilwong/flightprices?utm_source=chatgpt.com)

### Project Outputs
- 📊 [Click here to view the interactive Looker Studio dashboard](https://lookerstudio.google.com/reporting/fabdcd7e-3789-4d10-abc8-9696a26eed5e)
- 📓 [Click here to view the EDA & cleaning notebook](https://github.com/talisam89/data-portfolio/blob/main/flight-prices/Flight_data_EDA.ipynb) 
### Project Description
##### Step 1 - Managing dataset size for exploratory analysis

Exploring the data was challenging due to its size. It was immediately clear that Pandas wasn't going to cut it, the data was taking too long to load. I searched for solutions to working with large data sets and came across the Dask library which lets you work with data without loading it all into memory at once. 

Rather than immediately processing code, the Dask library creates a task graph that consists of primary tasks and subtasks with edges indicating dependencies. It can split the tasks across CPU cores and run them parallel when you tell it to start processing.

Tasks still took several minutes each to run and there were some issues with previewing the data due to not having all of it loaded into memory, but overall Dask worked well for cleaning and exploratory analysis

##### Step 2 - Exploratory Analysis

The initial Exploratory Analysis aimed to answer these questions:
	- What columns can be dropped to reduce the dataset size?
	- How can the data be filtered to reduce the dataset size?
	- Which columns would be most useful as filters to enhance insight into the optimal number of days before departure to book a flight?

Initial exploratory analysis included analysis of data types and number of missing values. It also included segmenting the data by different features (like airline, nonstop, airport etc.) to find ways to shrink the output so it could be uploaded to Looker Studio. 

Exploratory analysis turned into a bit of an iterative process. New questions came up after moving on to cleaning and after uploading the data to Looker Studio. Initially I shrunk the data set to nonstop flights only, since that was the type of flight I was most interested in. I then tried to separate the data by airline to further reduce the data set. 

After separating out airlines and uploading data to Looker Studio I realized 2 things.
	1) There were some flights that were only $.01
	2) Not all flights had been checked every day.

I went back to exploratory analysis and discovered, with the help of ChatGPT, that the total fare for the airlines and dates used in this data set should be $25 or above. ChatGPT also mentioned that budget carriers like Spirit and Frontier could potentially have had sales making the total fare between $19 and $39 dollars.

A review of the airlines with total fare less than $25 showed it was only budget airlines (Spirit and Frontier) with the very low total fares. So I concluded I did not need to filter out any data due to unreasonable fare prices.

I had been using the base fare rather than the total fare as the flight price based on the assumption that taxes and fees differ by state and base fare would be a more even comparison. It turned out that taxes and fees are regulated pretty well and it’s the base fare the varies wildly. If I had explored the variance of these two price columns I might have figured that out sooner.

As I was working out the fare price issue I noticed that not all the flights had 60 records of price data. The price data had been recorded over 60 days and I wanted to compare flights that had been tracked for the entirety of this period so I could look at prices over time. I filtered out any flights that had less than 60 records, which ended up greatly reducing my data set and solving my output size issue.

A downside to filtering out all those records was that it also filtered out all flights that weren't coach which meant I wouldn't be able to use cabin code as a filter on my dashboard. But this was a concession I was willing to make to achieve a smaller data set.

##### Step 3 - Data Cleaning

Like exploratory analysis, data cleaning was a bit of an iterative process that I came back to after uploading the data to Looker Studio. One of the most challenging cleaning tasks was calculating and converting the number of days before departure a price was being checked. I needed to convert the date columns in the data to date time objects, create a new column that calculated the difference between the dates, and convert this difference to a number that could be used in a graph. 

The cleaning tasks I focused on included:
	- Remove more unnecessary columns
	- Convert searchDate and FlightDate to date objects that can be subtracted from each other
	- Create days before departure column and numeric days before departure column
	- Calculate daysBeforeDeparture using searchDate and FlightDate
	- Convert segmentsDurationInSeconds from string to float64
	- Convert segmentsDurationInSeconds to hours
	- Limit data to flight IDs with 60 or more records

Columns To Remove: Why Remove Them:
	- fareBasisCode: determined this is an internal code unrelated to price
	- elapsedDays: usually 0, doesn't add much information to analysis
	- segmentsDepartureTimeEpochSeconds: chose to focus on other features impact on price
	- segmentsDepartureTimeRaw: chose to focus on other features impact on price
	- segmentsArrivalTimeEpochSeconds: chose to focus on other features impact on price
	- segmentsArrivalTimeEpochSeconds: chose to focus on other features impact on price
	- segmentsArrivalTimeRaw: chose to focus on other features impact on price
	- segmentsArrivalAirportCode: this is not relevant for nonstop flights
	- segmentsDepartureAirportCode: this is not relevant for nonstop flights
	- segmentsAirlineCode: using the airline name field instead
	- segmentsEquipmentDescription: chose to focus on other features impact on price
	- travelDuration: in a weird format, using segmentsDurationInSeconds instead
	- isBasicEconomy: doesn't match numbers of class, info in segment class more helpful
	- isNonStop: already filtered on this and know data is only for nonstop flights
	- segmentsDistance: going to use totalTravelDistance instead
	- DistanceComp: column I added that I don't need

##### Step 4 - Building the Dashboard

The first graphs I created were the time series graphs showing the average flight price over time broken out by airline. The graphs looked cluttered with all the airlines on it so I separated the data by major and budget airlines. To help people visually distinguish the individual airline I color coded the lines based on the airlines official brand colors. To reduce clutter in the graphs I did not include grid lines or axis labels. The interactive tool tips provide the context that grid lines would have provided. A combination of the graph title and trusting users are familiar with a time series format made the axis titles redundant. 

<img width="1198" height="371" alt="image" src="https://github.com/user-attachments/assets/d1a76a0a-8960-49cc-b8a5-97fd5cef7e14" />

Next I worked on the average flight price by days before departure. To make the graph more readable I binned the data into bins of size 7 (7 days equivalent to 1 week). To keep the graph clutter free I used data labels in place of an x axis and did not include grid lines or axis labels. I found the tool tip for this graph redundant, and used a clear box over the graph to prevent the tool tip from popping up. 

I wanted the eye to be drawn to this graph, as it best answers my business question. I chose a blue color that would grab user attention without being offensive. The blue is tied into the rest of the dashboard through the dashboard title. 
<p align="center">
<img width="697" height="315" alt="image" src="https://github.com/user-attachments/assets/46605763-7301-4b33-a975-413b3bb92c6c" />
</p>

I kept the filters simple since most of the information users would be curious about is captured in the graphs. I used the labels "From" and "To" because they are used on most travel sites. I trust that users will figure out the filters list airport codes. I decided I would not include a date filter because the date information was captured in the graphs. 

<p align="center">
<img width="373" height="98" alt="image" src="https://github.com/user-attachments/assets/499b9f29-ee00-4538-9356-a95eaf5d0bc9" />
<br>
<img width="448" height="538" alt="image" src="https://github.com/user-attachments/assets/2fb933d2-8543-4ab8-8185-ea580c0de831" />
</p>

I chose number of flights as a metric because it gives context to the average price, especially when using the airport filters. I added the average ticket price metric because I realized I did not have a total average price anywhere - it was always broken out by some other attribute. This metric is most informative when using the filters.

<p align="center">
<img width="401" height="95" alt="image" src="https://github.com/user-attachments/assets/7f74dc42-b874-424b-88ce-40e754b59ffc" />
</p>

I intentionally chose a grey color scheme because it is soft and creates just enough of a contrast that color of the bar graph pops. I centered the metric tiles because I wanted the bar graph to be the center piece of the dashboard. I thought having the metric tiles in the upper left corner would draw the eye to them rather than the bar graph. However I wanted to keep the metric tiles at the top, consistent with common dashboard design practices, so I moved them to the middle. 

<p align="center">
<img width="700" height="480" alt="image" src="https://github.com/user-attachments/assets/aa663afe-4cd6-484c-a228-e174cdfce733" />
</p>

##### Conclusion
The optimal number of days before departure to book a flight is 21 to 28 days (3-4 weeks) before departure. However the price varies by only a few dollars until 7-14 days before departure, and may be a negligible difference for most customers. 

There is variation depending on the departure location and destination as well as the airline. When flying from JFK to LAX the optimal number of days to book a flight is 14 to 21 days (2-3 weeks) before departure.  Airlines like Frontier, American and JetBlue seem to keep their prices relatively steady, whereas airlines like Delta and Spirit have large jumps in prices as the purchase date gets closer to the departure date. 

It should be noted that these conclusions only apply to nonstop flights in coach and were drawn using data from 2022. 

