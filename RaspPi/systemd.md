# Systemd Service
We perform several tasks using [Systemd](https://en.wikipedia.org/wiki/Systemd).

## New to SystemD
Not being fluent systemd users, we found the following info useful:
* [Intro to systemd video](https://youtu.be/AtEqbYTLHfs?t=147).  
  * [Place in the video where using commands starts](https://youtu.be/AtEqbYTLHfs?t=230).
* [systemd in Raspberry Pi documentation](https://www.raspberrypi.org/documentation/linux/usage/systemd.md)
* [Article on how to autorun service using systemd](https://www.raspberrypi-spy.co.uk/2015/10/how-to-autorun-a-python-script-on-boot-using-systemd/).
## Make sure to...
* set permissions so systemd can execute the script: ```sudo chmod +x {python script}```
* copy service file to where systemd expects it to be.  ```sudo cp {service script} /lib/systemd/system/.```
* enable the service with ```sudo systemctl enable {service script}```.
* check to make sure the service has been enabled with ```systemctl is-enabled {service script}```
* start the service with ```sudo systemctl start {service script}```.
* check to make sure the service has been started with ```systemctl is-active {service script}```
See the ```systemd status``` command info below to debug why your service did not start.
## Debugging
If `$ systemctl status {service script}` returns something like....
```
PlugE.service - Collect and send power readings.
   Loaded: loaded (/lib/systemd/system/PlugE.service; enabled; vendor preset: enabled)
   Active: failed (Result: exit-code) since Sun 2019-10-13 21:03:49 BST; 22s ago
 Main PID: 2813 (code=exited, status=1/FAILURE)
 ```  
 Check the debug records with `journalctl _PID=2813` = where the PID comes from the Main PID.  
 ## Changes to Code and Systemd
 Changes made to the systemd service (in this case the Python code), requires the command `systemctl daemon-reload` to make systemd aware of the changes.  This is followed by `systemctl restart {service script}`.
 ## List All Services
 ```systemctl list-units | grep .service``` - lists all the current services running.
 # MongoDB
 The Rasp Pi OS comes with a super easy and flexible datastore, mongodb. [ReadAndStore.py](https://github.com/BitKnitting/FitHome_monitor/blob/master/ReadAndStore.py) stores readings into the mongodb database that comes with the Rasperry Pi. Installation is discussed within our [Rasp Pi Setup documentation](RaspPi.md). Refer to our [mongod db documentation](mongodb) for more information.  
 
## [Export Readings to Pandas](#readings_to_pandas)
We run a SystemD service - [extract_readings.service](https://github.com/BitKnitting/FitHome_monitor/blob/master/data_extraction/extract_readings.service) that relies on a [SystemD timer file](https://wiki.archlinux.org/index.php/Systemd/Timers) -  [extract_readings.timer](https://github.com/BitKnitting/FitHome_monitor/blob/master/data_extraction/extract_readings.timer) - to run
 [extract_readings.py](https://github.com/BitKnitting/FitHome_monitor/blob/master/data_extraction/extract_readings.py).  This python script relies on the [MonitorData](https://github.com/BitKnitting/FitHome_monitor/blob/master/data_extraction/monitor_data.py) to get the records out of the mongodb (database = FitHome, collection = aggregate) and create a pickled zip file.  We chose this format because it is easy to read the data with the Pandas package.
 ### Environment Variables
Notice the MonitorData class uses environment variables. We used the technique discussed [in this post](https://unix.stackexchange.com/questions/287743/making-environment-variables-available-for-downstream-processes-started-within-a).
