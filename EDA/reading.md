# Overview
We documented how power readings come from the energy monitor and get into the mongo database running on a Raspberry Pi in our [Electricity Monitor](ElectricityMonitor) section.  
![overview_monitor](monitor/images/Electricity_Monitor_Rasp_Pi.png)  

Now that the readings are in the mongo database, we need a simple programmatic way to retrieve the readings from the mongo db and put into a data format where we can do some analysis - whether using statistics, visualization, artificial intelligence....whatever....

Here is an overview image of the how and what of ```readings.py```:  
![overview_readings](EDA/images/mongodb_pandas.png)   
  
```readings.py``` is the layer we use to grab either:
# get_DataFrame_for_date
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

If we use '*' as input instead of an ISODate, _eventually_ (a lot of readings can take awhile to get results!), e.g.:  
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