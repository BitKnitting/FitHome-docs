# Overview
After the Raspberry Pi takes the power readings from the monitor, it stores them within the Raspberry Pi's mongodb.  The mongodb database it stores it in is named `FitHome`.  The `aggregate` collection contains the readings.  This is identified in [ReadAndStore.py](https://github.com/BitKnitting/FitHome_monitor/blob/master/ReadAndStore.py):  
```
store = MongoDB("mongodb://localhost:27017/", "FitHome", "aggregate")
```
# Installing MongoDB
To install mongodb:  
```
sudo apt install mongodb
sudo systemctl enable mongodb
```
- We "downgraded" to this version of pymongo. `pip3 install pymongo==3.4.0`
__NOTE: We needed to use this version of pymongo.  If not, we'd get an error:__
```
An error occurred: Server at localhost:27017 reports wire version 0, but this version of PyMongo requires at least 2
(MongoDB 2.6).
```
There's [more on mongodb in this post](Posts/ExploringEnergyDisaggregation/0-UsingMongoDB.md)

# Uninstall MongoDB
...just in case...  

[From this post](https://askubuntu.com/questions/147135/how-can-i-uninstall-mongodb-and-reinstall-the-latest-version):  
  
```
sudo apt-get purge mongodb mongodb-clients mongodb-server mongodb-dev
sudo apt-get purge mongodb-10gen
sudo apt-get autoremove
```
This should also remove your config from `/etc/mongodb.conf`. If you want to completely clean up, you might also want to remove the data directory `/var/lib/mongodb`, so long as you backed it up or don't want it any more.

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
# Important Files
`/etc/mongodb.conf` is the configuration file.  There are times we need to tweek this file, for example - in order to remotely connect.  
  
One of the things `mongodb.conf` contains is the path to the database:
```
# Where to store the data.
dbpath=/var/lib/mongodb  
```


# Remote Access to MongoDB
_Thanks to the Data Frog for the [MongoDB: Remote Access (raspberry pi) post](https://thedatafrog.com/en/mongodb-remote-raspberry-pi/)._  

There are a few "twiddles and tweaks" that must be done in order to access the mongodb on the Raspberry Pi from a Jupyter Notebook running on a Mac/Windows/Linux box.  These include:
- install lsof: `$ sudo apt install lsof`
- Execute the command:  
```
$ sudo lsof -i -P -n | grep LISTEN
mongod      323 mongodb    9u  IPv4  14852      0t0  TCP 127.0.0.1:27017 (LISTEN)
mongod      323 mongodb   10u  IPv4  13013      0t0  TCP 127.0.0.1:28017 (LISTEN)
sshd        530    root    3u  IPv4  14838      0t0  TCP *:22 (LISTEN)
sshd        530    root    4u  IPv6  14840      0t0  TCP *:22 (LISTEN)
```
TCP*:22 lets us know ssh is open to all IP addresses. _Note: Hopefully port 22 is blocked on your router such that it is not available to any computer on the Internet._

The mongodb ports (27017 and 28017) are only open to the localhost.
- Open the mongodb ports
    - Using the ufw tool: Here we took a gulp of air and followed the Data Frog's directions....
```
sudo apt install ufw
sudo ufw allow ssh
sudo ufw allow 27017
sudo ufw allow 28017
sudo ufw enable
sudo ufw status
```
    - Configure mongodb for remote access.
```
sudo nano /etc/mongodb.conf   
```
comment out the line:
```
# bind_ip = 127.0.0.1
```
Save the change, then restart mongodb:  
```
sudo systemctl restart mongodb  
```  
    - Test:
```
http://192.168.86.20:28017/
```
Where the IP address is the Raspberry Pi's address.  This worked for us. 

## Not Initially Working
One challenge we were able to get over was due to REST not being enabled for mongodb.  Luckily Ajay Singh's article, [_MongoDB on Ubuntu = REST not enabled.](https://ajay555.wordpress.com/2011/06/08/mongodb-on-ubuntu-rest-is-not-enabled-use-rest-to-turn-on-error/) walked us through fixing this.

## Still Not Working
We are not able to 