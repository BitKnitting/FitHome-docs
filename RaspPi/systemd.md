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
* disable the service with ```sudo systemctl disable {service script}```.  Most of the services will restart when the Rasp Pi is rebooted. We must disable the service to prevent restarting.
* check to make sure the service has been enabled with ```systemctl is-enabled {service script}```
* start the service with ```sudo systemctl start {service script}```.
* stop the service with ```sudo systemctl stop {service script}```.
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

 
