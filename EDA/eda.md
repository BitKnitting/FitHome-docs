# Overview
Our goal is to provide Jupyter notebooks so that you can quickly start explore the powe readings and customize the output.  We see these notebooks as a "getting started" effort that will give you a head start in customizing your power readings analysis.
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




# FitHome_EDA Notebook
The FitHome EDA Jupyter Notebook:
- connects with the mongodb db running on the raspberry pi.