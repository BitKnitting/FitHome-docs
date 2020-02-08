# Overview
After the Raspberry Pi takes the power readings from the monitor, it stores them within the Raspberry Pi's mongodb.  The mongodb database it stores it in is named `FitHome`.  The `aggregate` collection contains the readings.  This is identified in [ReadAndStore.py](https://github.com/BitKnitting/FitHome_monitor/blob/master/ReadAndStore.py):  
```
store = MongoDB("mongodb://localhost:27017/", "FitHome", "aggregate")
```
# Viewing the Readings From the Command Line
- Start an ssh session with the Raspberry Pi.  To get a "quick and dirty" feel for what has been collected by the Raspberry Pi, start an [ssh connection](https://www.raspberrypi.org/documentation/remote-access/ssh/) to the Raspberry Pi.  _Note: We discuss getting ssh to work on your Raspberry Pi on the [RaspPi page](RaspPi)_  
- Type `mongo` at the command line.  This opens the MongoDB shell.
- Splash around with the database., e.g.:
```
> show dbs
> use FitHome
switched to db FitHome
> show collections
> db.aggregate.find()
> db.aggregate.count()
> db.stats()
```