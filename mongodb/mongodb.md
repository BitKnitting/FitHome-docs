# Overview
After the Raspberry Pi takes the power readings from the monitor, it stores them within the Raspberry Pi's mongodb.  The mongodb database it stores it in is named `FitHome`.  The `aggregate` collection contains the readings.  This is identified in [ReadAndStore.py](https://github.com/BitKnitting/FitHome_monitor/blob/master/ReadAndStore.py):  
```
store = MongoDB("mongodb://localhost:27017/", "FitHome", "aggregate")
```
This means we need to set up the mongodb on the Rasp Pi prior to running the ReadAndStore service.
# Installing MongoDB
To install mongodb:  
```
sudo apt install mongodb
sudo systemctl enable mongodb
```
# pymongo
- We "downgraded" to this version of pymongo. `pip3 install pymongo==3.4.0`
__NOTE: We needed to use this version of pymongo with the default mongod installed with the Rasp Pi.  If not, we'd get an error:__
```
An error occurred: Server at localhost:27017 reports wire version 0, but this version of PyMongo requires at least 2
(MongoDB 2.6).
```
This code is a way to check if we can access the db via pymongo:  
```
>>> from pymongo import MongoClient
>>> client = MongoClient("mongodb://localhost:27017")
>>> client.server_info()
{'version': '2.4.14', 'gitVersion': 'nogitversion', 'sysInfo': 'Linux bm-wb-03 3.19.0-trunk-armmp #1 SMP Debian 3.19.1-1~exp1+plugwash1 (2015-03-28) armv7l BOOST_LIB_VERSION=1_58', 'loaderFlags': '-fPIC -pthread -rdynamic', 'compilerFlags': '-Wno-unused-local-typedefs -Wnon-virtual-dtor -Woverloaded-virtual -fPIC -fno-strict-aliasing -ggdb -pthread -Wall -Wsign-compare -Wno-unused-function -Wno-unused-variable -Wno-unknown-pragmas -Winvalid-pch -pipe -Werror -fno-builtin-memcmp -O3', 'allocator': 'system', 'versionArray': [2, 4, 14, 0], 'javascriptEngine': 'V8', 'bits': 32, 'debug': False, 'maxBsonObjectSize': 16777216, 'ok': 1.0}
```  
more code examples:  
```
client.database_names()
['FitHome', 'local', 'admin', 'db']
```

There's [more on mongodb in this post](Posts/ExploringEnergyDisaggregation/0-UsingMongoDB.md)
## System Info
After we tweeked the system for remote access, we went to the mongo db web page (e.g.: `http://192.168.86.20:28017/`) and recorded:  
```
db version v2.4.14
sys info: Linux bm-wb-03 3.19.0-trunk-armmp #1 SMP Debian 3.19.1-1~exp1+plugwash1 (2015-03-28) armv7l BOOST_LIB_VERSION=1_58
```
# Backing Up MongoDB
We followed Anatoliy Dimitrov's instructions from the page, [_How To Back Up, Restore, and Migrate a MongoDB Database on Ubuntu 14.04_](https://www.digitalocean.com/community/tutorials/how-to-back-up-restore-and-migrate-a-mongodb-database-on-ubuntu-14-04).    
..._An important argument to mongodump is --db, which specifies the name of the database which you want to back up. If you don’t specify a database name, mongodump backups all of your databases. The second important argument is --out which specifies the directory in which the data will be dumped._
  
```
sudo mkdir /var/backups/mongobackups
sudo mongodump --db newdb --out /var/backups/mongobackups/`date +"%m-%d-%y"`  
```
We changed directories on our Rasp Pi to `~/projects/FitHome_monitor/data_extraction`  
```
today=`date +"%m-%d-%y"`
sudo mongodump --db FitHome --out "mongo_backup-"$today  
  
ls -la ~/projects/FitHome_monitor/data_extraction/mongo_backup-02-08-20/FitHome
total 82152
drwxr-xr-x 2 root root     4096 Feb  8 06:37 .
drwxr-xr-x 3 root root     4096 Feb  8 06:37 ..
-rw-r--r-- 1 root root 84104085 Feb  8 06:37 aggregate.bson
-rw-r--r-- 1 root root       99 Feb  8 06:37 aggregate.metadata.json
-rw-r--r-- 1 root root       72 Feb  8 06:37 system.indexes.bson
```

 
## Installing mongodb on our Mac
We wanted to play around with the readings in mongodb from our mac.  The "best" way to install mongodb is to use brew.  Unfortunately, as noted in [this stackoverflow post](https://stackoverflow.com/questions/57856809/installing-mongodb-with-homebrew): _To our users: if you came here because mongodb stopped working for you, we have removed it from the Homebrew core formulas since it was migrated to a non open-source license.  Fortunately, the team of mongodb is maintaining a custom Homebrew tap. You can uninstall the old mongodb and reinstall the new one from the new tap._  
```
brew services stop mongodb
brew uninstall mongodb

brew tap mongodb/brew
brew install mongodb-community
brew link --overwrite mongodb-community
brew services start mongodb-community  

brew services list
Name              Status  User Plist
mongodb-community started mj   /Users/mj/Library/LaunchAgents/homebrew.mxcl.mongodb-community.plist

``` 
_Note: also started with
mongod --port 27107 --dbpath /data/db
then the mongoremote needed to be redone with:
mongorestore --port 27107 --db=FitHome  /Users/mj/mount/projects/FitHome_monitor/data_extraction/mongo_backup-02-08-20/FitHome/aggregate.bson

...hnn,,,sane version of database...only one is controlled by brew and one is not.  When controlled by brew, database is somewhere else...from what i can tell...i am still having trouble with some of the commands....

then...
use FitHome worked

but 
db.aggregate.find() did not work.
db.getCollection("aggregate").find() DID work...  which is a YIPPEE!!!!

as noted [here](https://docs.mongodb.com/manual/mongo/): _If the mongo shell does not accept the name of a collection, you can use the alternative db.getCollection() syntax. For instance, if a collection name contains a space or hyphen, starts with a number, or conflicts with a built-in function_
mongo
check the install:  

```
$ mongo
MongoDB shell version v4.2.0
connecting to: mongodb://127.0.0.1:27017/?compressors=disabled&gssapiServiceName=mongodb
```
_Note: the version on the Raspberry Pi is much older:_  
  
```
$ mongo
MongoDB shell version: 2.4.14
```
# Moving The Database
Now that we have mongodb installed on our mac, let's migrate the FitHome db in the mongodb on the Rasp Pi to the mongo db on our Mac.  We started by using mongorestore...but it hadn't been installed on our Mac.  We found the directory where mongo was installed:
```
which mongo
lrwxr-xr-x    1 mj    admin        49 Nov 24 04:47 mongo -> ../Cellar/mongodb-community-shell/4.2.0/bin/mongo
```
Then went to the directory mongo is linked to and found mongo was the only tool there:  
```
/usr/local/Cellar/mongodb-community-shell/4.2.0/bin  
ls -la
total 91736
drwxr-xr-x   3 mj  staff        96 Nov 24 04:47 .
drwxr-xr-x  10 mj  admin       320 Nov 24 04:47 ..
-r-xr-xr-x   1 mj  staff  46967016 Aug  8  2019 mongo
```

To do this, we:  
- Use [SSHFS](https://github.com/BitKnitting/FitHome/wiki/RaspPi#mount-drive) to mount the Rasp Pi drive, e.g.:  
  
```
sshfs pi@192.168.86.24: /Users/mj/mount
```
mongorestore --db=FitHome  /Users/mj/mount/projects/FitHome_monitor/data_extraction/mongo_backup-02-08-20/FitHome/aggregate.bson



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


