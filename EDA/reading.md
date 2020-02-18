# Overview
We documented how power readings come from the energy monitor and get into the mongo database running on a Raspberry Pi in our [Electricity Monitor](ElectricityMonitor) section.  
![overview](monitor/images/Electricity_Monitor_Rasp_Pi.png)  

Now that the readings are in the mongo database, we need a simple programmatic way to retrieve the readings from the mongo db and put into a data format where we can do some analysis - whether using statistics, visualization, artificial intelligence....whatever....
