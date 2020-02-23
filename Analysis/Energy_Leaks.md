# Overview
Our appliances are plugged in all the time.  Energy Leaks is amount of electricity that is leaked when appliances are plugged in but are not being used.  

A [study by the NRDC](https://www.nrdc.org/resources/home-idle-load-devices-wasting-huge-amounts-electricity-when-not-active-use) found the homes in the study on average wasted nearly 23% of their electricity consumption on devices that were not being used but were slurping up electricity.

Imagine the amount of electricity saved if all of us took the step to stop our electricity leaks!  
# Getting The Energy Leaks Value
## Using Python



# What's next
Now that we know how many watts we lose, what does that mean for the amount of kWh wasted in a year, and how much does that cost?

watt_leakage = e.get_watt_leakage()
kWh_waste = e.get_kWh(watt_leakage)
kWh_year_waste = e.get_kWh_year_waste(watt_leakage)


- take measurements when the house is doing normal stuff (e.g.: don't recharge an electric car at night if this doesn't happen all the time....)