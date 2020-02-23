# Overview
We documented how power readings come from the energy monitor and get into the mongo database running on a Raspberry Pi in our [Electricity Monitor](ElectricityMonitor) section.  
![overview_monitor](monitor/images/Electricity_Monitor_Rasp_Pi.png)  

In this step, we query the mongo database for active power readings and put the readings into a Pandas DataFrame.  The time the reading was taken (actually, the time the reading was put into the mongo database) is the datetimeindex of the DataFrame.  The DataFrame has one column labeled 'Pa' containing the active power readings.
  
```
print(df.head(1))
                            Pa
timestamp                     
2020-01-12 13:56:10  1264.0352  
```  
   
![overview_readings](EDA/images/mongodb_pandas.png)     
  
[```readings.py```](https://github.com/BitKnitting/FitHome_EDA/blob/dafae9d8a2a2da41ff365fd76922e46dcfbd63ee/readings/readings.py) has the code for the ```PowerReadings()``` class.
# PowerReadings() Class
The PowerReadings() class has two methods:
## PowerReadings().get_DataFrame_for_date()
- [code for get_DataFrame_for_date()](https://github.com/BitKnitting/FitHome_EDA/blob/dafae9d8a2a2da41ff365fd76922e46dcfbd63ee/readings/readings.py#L52)
This method returns all the readings in the mongo database for a given date in [ISODate format], e.g.:   
```
from errors.errors import handle_exception
from readings.readings import PowerReadings
try:
    p = PowerReadings()
    df = p.get_DateFrame_for_date('2020-01-12')
    print(describe(df))
except Exception as e:
    handle_exception(e)

print(df.head(1))
                            Pa
timestamp                     
2020-01-12 13:56:10  1264.0352

print(df.describe())
                 Pa
count  35413.000000
mean    1075.727513
std      194.815259
min      962.290560
25%      973.188800
50%      978.560320
75%     1099.600960
max     3204.862720
```  
returns a DataFrame where the index is a datetimeindex derived from the timestamp when the reading was put into the mongo db and the 'Pa' column of active power readings.

If we use '*' as input instead of an ISODate, _eventually_ (a lot of readings can take awhile to get results!) we get all readings.  e.g.:  
```
df = p.get_DataFrame_for_date('*')
print(df.describe())
                 Pa
count  1.293909e+06
mean   4.646382e+02
std    4.920585e+02
min    2.534560e+01
25%    6.085472e+01
50%    3.509210e+02
75%    9.230893e+02
```
# get_connection_to_collection()
- [code for get_connection_to_collection()](https://github.com/BitKnitting/FitHome_EDA/blob/dafae9d8a2a2da41ff365fd76922e46dcfbd63ee/readings/readings.py#L68)  

We use this method if we want to get the list of dates (```get_isodate_list()```).

This method assumes:  
-  The FitHome mongo database is running on the Raspberry Pi on mongodb's default port:  
```
client = MongoClient("mongodb://127.0.0.1:27017")  
```
-  The FitHome database is called 'FitHome'.  
```  
db = client['FitHome']  
```  
- The collection holding the readings within the FitHome db is called 'aggregate'.  
```
return db['aggregate']  
```  
# get_isodate_list()
- [code for get_isodate_list](https://github.com/BitKnitting/FitHome_EDA/blob/dafae9d8a2a2da41ff365fd76922e46dcfbd63ee/readings/readings.py#L96)  
  
This method uses the Object ID mongo db assigned to each entry and figures out (from the first 4 bytes of the Object ID) the datetime.  Once we have all the datetimes, we figure out the dates where we have readings and return the dates within a list of ISODate formatted strings.


