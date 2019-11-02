### GitHub Location
[energy monitor firmware GitHub repository](https://github.com/BitKnitting/energy_monitor_firmware)
# The Electricity Monitor
The whole home electricity monitor we use to gather power and current readings consists of:
- [CircuitSetup's Split Single Phase Real Time Whole House Energy Meter (v 1.4)](https://circuitsetup.us/index.php/product/split-single-phase-real-time-whole-house-energy-meter-v1-4/).  
- an ESP32.

![monitor wiring](images/EnergyMonitorFirmware/monitorWiring.png) 

# Thanks to Those That Went Before
There is _so much_ prior work that made it easier to evolve the atm90e32 micropython library.  Efforts include:  
* Tisham Dhar's [atm90e26 Arduino library](https://github.com/whatnick/ATM90E26_Arduino).    
* The [atm90e26 Circuit Python library I wrote](https://github.com/BitKnitting/HappyDay_ATM90e26_CircuitPython).
* Circuit Setup's [atm90e32 Arduino library](https://github.com/CircuitSetup/Split-Single-Phase-Energy-Meter/tree/master/Software/libraries/ATM90E32).
# What the Firmware Does
The ESP32 is connected to [CircuitSetup's Split Single Phase Real Time Whole House Energy Meter (v 1.4)](https://circuitsetup.us/index.php/product/split-single-phase-real-time-whole-house-energy-meter-v1-4/).  The micropython firmware: 
- [main.py](https://github.com/BitKnitting/energy_monitor_firmware/blob/master/workspace/main.py) reads the monitor and then sends the reading to a Firebase database project.
- atm90e32_u.py and atm90e32_registers.py [at this GitHub](https://github.com/BitKnitting/energy_monitor_firmware/tree/master/workspace/atm90_e32) are thehe libraries called to read and write to the monitor's registers.

# Overview
The energy monitor firmware is built on micropython to:
* Join the homeowner's wifi.  
* Send energy readings to the Firebase RT db.  
## Hardware
- Energy Monitor - The firmware was written for [CircuitSetup's Split Single Phase Real Time Whole House Energy Meter (v 1.4)](https://circuitsetup.us/index.php/product/split-single-phase-real-time-whole-house-energy-meter-v1-4/).  This breakout board is based on the [ATM90e32 chip](https://www.microchip.com/wwwproducts/en/atm90e32as).
- Microprocessor - At least for testing, we are using [the ESP32 DevKit C](https://amzn.to/2JInYgj).
### Wiring
The ATM90e32 on the Energy Meter speaks to the ESP32 using the [HSPI pins](https://docs.micropython.org/en/latest/esp8266/quickref.html#hardware-spi-bus):
```
mosi_pin = Pin(13)
miso_pin = Pin(12)
sck_pin = Pin(14)
cs_pin = 15
```
 
In addition to SPI wiring, a red and green led - each with a resistor - are wired:
- red LED on pin 27
- green LED on pin 32
The resistors are between 220 and 1K ohm.
# Powering Up
--------------------->  
Hmmm....a gotcha we haven't debugged yet....In order for the ESP32 to get past boot, the ESP32 __must__ be plugged in __before__ the monitor is plugged in.  
<---------------------
## Software 
- OS: [micropython v1.11](https://github.com/BitKnitting/energy_monitor_firmware/tree/master/micropython_build)
- IDE: [uPyCraft](http://docs.dfrobot.com/upycraft/).  I started with uPyCraft.  Then modifying a file, copying to the ESP32 started having too many issues (we can't wait for true USB!).  So we started using [rshell](https://pypi.org/project/rshell/).  
#### rshell
rshell has great documentation.  We start a Terminal within the workspace folder of the project.  From within rshell we can go between repl and copying files from our Mac to the ESP32.
- get into rshell:
```
$ rshell
Welcome to rshell. Use Control-D (or the exit command) to exit rshell.

No MicroPython boards connected - use the connect command to add one
```
- attach to the ESP32:
```
connect serial /dev/tty.SLAB_USBtoUART 115200
Connecting to /dev/tty.SLAB_USBtoUART (buffer-size 512)...
Trying to connect to REPL  connected
Testing if sys.stdin.buffer exists ... Y
Retrieving root directories ... /boot.py/
Setting time ... Sep 30, 2019 11:00:04
Evaluating board_name ... pyboard
Retrieving time epoch ... Jan 01, 2000
```

#### uPyCraftism
_Note: We've given up on uPyCraft. We now stick with rshell_  
  
__IMPORTANT__: uPyCraft has a folder in it called _workspace_.  This folder is mapped (mounted) to a directory on the Mac's/PC's hard drive.  This is why the firmware starts below the _workspace_ folder.  This folder needs to be mapped to uPyCraft's folder.  These are the steps we took:
- Choose Tools/InitConfig  You'll be asked if you want to init. Choose yes.  
- Click on the workspace folder.  This brings up a Finder dialog box.  Choose the energy_monitor_firmware directory.
# In Practice
There are a few "not ready for primetime" gotchas we found out about while testing that we just had to work around:
- After setting up an Access Point (see below under Monitor Install), ocassionally the micropython libraries seemed to leak memory.  Then the ESP32 froze.  Restarting the ESP32 at this point "fixed" this.
- The ESP32 needs to be plugged in BEFORE the 9V power transformer starts up the energy monitor.  We put a few second delay in main.py to accomodate plugging in the 9V monitor after restarting the ESP32.
# Preparing the ESP32
 At least for testing, we are using [the ESP32 DevKit C](https://amzn.to/2JInYgj).  
 # Install micropython
 The [micropython binary we used is v1.11](https://github.com/BitKnitting/energy_monitor_firmware/tree/master/micropython_build)  

 Steps: 
 - Plug ESP32 into USB port.  
 - Open a Terminal Window.  Easiest to open where the micropython binary is located.
 - (Just to make sure...) Check to see the port's there, e.g.: `ls /dev/tty*
 - Erase the chip: `esptool.py --chip esp32 -p /dev/tty.SLAB_USBtoUART erase_flash`  
 e.g.:  
 ```
 $ esptool.py -p /dev/cu.SLAB_USBtoUART erase_flash
esptool.py v2.7
Serial port /dev/cu.SLAB_USBtoUART
Connecting...
Detecting chip type... ESP32
Chip is ESP32D0WDQ5 (revision 1)
Features: WiFi, BT, Dual Core, 240MHz, VRef calibration in efuse, Coding Scheme None
Crystal is 40MHz
MAC: bc:dd:c2:d4:58:78
Uploading stub...
Running stub...
Stub running...
Erasing flash (this may take a while)...
Chip erase completed successfully in 9.6s
Hard resetting via RTS pin...
```  
- Flash micropython: `esptool.py  -p /dev/cu.SLAB_USBtoUART write_flash -z 0x1000 esp32-20190529-v1.11.bin`  
e.g.:  
```
$ esptool.py  -p /dev/tty.SLAB_USBtoUART write_flash -z 0x1000 esp32-20190529-v1.11.bin
esptool.py v2.7
Serial port /dev/tty.SLAB_USBtoUART
Connecting........_
Detecting chip type... ESP32
Chip is ESP32D0WDQ5 (revision 1)
Features: WiFi, BT, Dual Core, 240MHz, VRef calibration in efuse, Coding Scheme None
Crystal is 40MHz
MAC: bc:dd:c2:d4:58:78
Uploading stub...
Running stub...
Stub running...
Configuring flash size...
Auto-detected Flash size: 4MB
Compressed 1146864 bytes to 717504...
Wrote 1146864 bytes (717504 compressed) at 0x00001000 in 63.4 seconds (effective 144.8 kbit/s)...
Hash of data verified.

Leaving...
Hard resetting via RTS pin...
```  

## Copy micropython Libraries

- cd into the workspace dir of this project.  
- start an rshell session: `$rshell`  
- Connect to the ESP32:   
```
workspace>connect serial /dev/tty.SLAB_USBtoUART 115200
Connecting to /dev/tty.SLAB_USBtoUART (buffer-size 512)...
Trying to connect to REPL  connected
Testing if sys.stdin.buffer exists ... Y
Retrieving root directories ... /boot.py/
Setting time ... Oct 10, 2019 09:34:23
Evaluating board_name ... pyboard
Retrieving time epoch ... Jan 01, 2000  
```
- Check that we're connected: `boards`  returns  
```
pyboard @ /dev/tty.SLAB_USBtoUART connected Epoch: 2000 Dirs: /boot.py /pyboard/boot.py  
```  
- Make lib directory: `mkdir /pyboard/lib`  
- Copy libraries from Mac/PC, e.g.: `cp atm90_e32/atm90e32_registers.py /pyboard/lib`  
The libraries we use to connect to wifi and read/send energy readings include:
  - [atm90e32_registers.py](https://github.com/BitKnitting/energy_monitor_firmware/blob/master/workspace/read_monitor/atm90e32_registers.py) and [atm90e32_u.py](https://github.com/BitKnitting/energy_monitor_firmware/blob/master/workspace/read_monitor/atm90e32_u.py) from workspace/read_monitor.
  - [config.py](https://github.com/BitKnitting/energy_monitor_firmware/blob/master/workspace/config/config.py) from workspace/config.
  - [wifi_connect.py](https://github.com/BitKnitting/energy_monitor_firmware/blob/master/workspace/join_wifi/wifi_connect.py) from workspace/join_wifi.
  - [send_reading](https://github.com/BitKnitting/energy_monitor_firmware/blob/master/workspace/send_reading/send_reading.py) from workspace/send_reading.
  - [app_error.py](https://github.com/BitKnitting/energy_monitor_firmware/blob/master/workspace/errors/app_error.py) from workspace/errors.
  - Create `config.json` and copy it to the lib directory.  The `config.json` file looks like:  
  ```
  {
    "monitor":"bambi-07052019",
    "project_id":"my-firebase-projectid-00989"
 }  


The monitor name is in the Database under the homeowner's member record.  
![monitor name in db](images/Database/monitor_node.png) 
  
The monitor name is created when the homeowner becomes a FitHome member.  The conceptual model is a homeowner becomes a FitHome member for one month.  The homeowner uses the FitHome App to start their month of training.  During this process, one of the available monitors is assigned to the owner.  To uniquely identify this month of use, the monitor name assigned to the homeowner is appended with the date the homeowner signed up for FitHome membership.  In this example, the monitor named "bambi" was assigned to the homeowner.  The homeowner signed up on July 5th, 2019.  

# Software Design
[main.py](https://github.com/BitKnitting/energy_monitor_firmware/blob/master/workspace/main.py) gives us a general flow of the code.
## Monitor Status
The goal with monitor status is to quickly and simply figure out why we're not getting readings into the db when we think we should be getting them.

Two LEDs - one red (errors) one green (energy reading sent to database) are used to let us the status of the monitor when the code is loading or running.  
### Program Start
The code in [main.py] starts off by blinking the green LED 4 times.  This lets us know that the ESP32 has been able to load and start the code in main.py.  E
### Errors
Errors are identified in [app_error.py](https://github.com/BitKnitting/energy_monitor_firmware/blob/master/workspace/errors/app_error.py) as simply Python classes. e.g.:  
```
class NoMonitor():
    # When the atm class is initialized, it writes calibration info to
    # registers.  This error means the microcontroller couldn't write to
    # the atm.  Most likely the monitor isn't plugged in or the SPI
    # connection isn't working properly.
    number = 301
    explanation = 'Could not contact a monitor.'
    blinks = 1
 ```
 Each error is assigned a number of blinks.  If the red LED starts blinking, we need to check how many blinks happen and then figure  out from there is we can debug why the monitor is not working.
 ### All OK
 Every time a reading is sent to the db, the green light blinks.  If we watch the monitor for a time within which a reading should occur, the green LED should blink.  If it doesn't, there is a really, really good chance readings are not being made or at least not being sent to the db.
 ## Connecting to WiFi
 A big chunk of the firmware code is dedicated to handling wifi.  The monitor either knows the ssid and password of the homeowner's wifi (and of course is close enough to get a usable signal) or does not.  

 From [main.py](https://github.com/BitKnitting/energy_monitor_firmware/blob/master/workspace/main.py):

 - Load the class that will handle all the wifi calls: `from wifi_connect import WifiAccess`
 - Make an instance of the wifi class: `join_wifi = WifiAccess()`
 - Connect to the wifi network: `join_wifi.get_connected()`
 ### Monitor Install
 When the monitor is first installed, the homeowner's wifi ssid and password is not known.  The `__init__` method of the WifiAccess class tries to read the SSID and password using the `read_config` method in [config.py](https://github.com/BitKnitting/energy_monitor_firmware/blob/master/workspace/config/config.py).  If the SSID and password can't be retrieved, the class's wifi_state variable is set to no_ssid_pwd.  If the SSID and password were retrieve, wifi_state is set to not_connected.

 #### Setting SSID and password
 _Note: There must be a cleaner way to get the SSID and password!_

 Monitor install focuses on the no_ssid_password state.  When WifiAccess()'s `get_connected()` method figures out the monitor doesn't know the wifi's SSID and password, it moves the code into being a web server acting as a wireless Access Point with the SSID `fithome_abc`.   An Access Point shows up within the Mac's/PC's list of wifi networks.

![wifi ssid](images/EnergyMonitorFirmware/wifi_ssid.png)   
- Connect to the `fithome_abc` Access Point on your Mac/PC.  If you don't see the Access Point and the monitor isn't blinking red, there's a good chance the monitor is too far from the wifi signal.  Right now, we have a poor solution - get the wifi signal to be stronger in the area where the monitor is...hmmm....
- Once connected to `fithome_abc`, open a browser window and enter the address `192.168.4.1`.  At this point, you should be "talking" with the monitor's AP firmware and see a page that shows the SSIDs available in the homeowner's house.

![wifi client](images/EnergyMonitorFirmware/wifi_client.png)  

Click on the radio button of the SSID the monitor should use and type in the password.  If the monitor can log in, you'll see:    
  
![wifi successful login](images/EnergyMonitorFirmware/wifi_success_ssid_password.png)  
Behind the scenes, the code in `wifi_connect.py` is using `<form action="configure"` to figure out if the homeowner has sent the SSID and password.  
```
  try:
      tag = ure.search("(?:GET|POST) /(.*?)(?:\\?.*?)? HTTP",
                        request).group(1).decode("utf-8").rstrip("/")
  except Exception:
      tag = ure.search(
          "(?:GET|POST) /(.*?)(?:\\?.*?)? HTTP", request).group(1).rstrip("/")
  if tag == "":
      # Get the ssid and password.
      self._handle_ssid_pwd_ui(client)
      # Try to join the self.wifi.
  elif tag == "configure":
      self._handle_join_wifi(client, request)
```                    
`_handle_join_wifi()` takes care of adding the SSID and password into the config file in case the monitor's firmware needs it again.  It also connects to the network. _Note: We haven't spent time exploring this, but from what we can tell, the ESP32 stores the SSID and password somewhere (EEPROM? FLASH?).  It doesn't seem to access the variables from the config file, rather can just log in._
### Ongoing wifi
Once the monitor knows the SSID and password, the monitor should be able to connect to the homeowner's wifi.
# Monitor Readings
Reading the atm9e32 registers relies on the [ATM90e32 class](https://github.com/BitKnitting/energy_monitor_firmware/blob/master/workspace/atm90_e32/atm90e32_u.py).  
## Calibration 
The code in main.py shows several values being passed into the initialization of at atm90e32 instance:  
```


ATM90e32(lineFreq, PGAGain, VoltageGain, CurrentGainCT1, 0, CurrentGainCT2)
 ```
The default values are discussed in [Circuit Setup's documentation](https://github.com/CircuitSetup/Split-Single-Phase-Energy-Meter#calibration).  

We use the default values for:  
```
lineFreq = 4485  # 4485 for 60 Hz (North America)
PGAGain = 21     # 21 for 100A (2x), 42 for >100A (4x)
```
What is left to calibrate are the voltage and current gain values.  These are important, because they can cause havoc with the accuracy of the voltage, current, power readings.

It is more likely the voltage readings will need calibration than the current.  
### Voltage Calibration
The gain value is tied to the transformer we are using.  We decided to standardize on the [Jameco 9V power supply, part no. 157041](https://www.jameco.com/shop/ProductDisplay?catalogId=10001&langId=-1&storeId=10001&productId=157041).  The default voltage gain for a 9V AC transformer was 42080.  With this setting for voltage gain, our readings were over 15 watts higher than what the actual voltage was.  

To determine the actual voltage, we used the extremely useful [Kill-A-Watt](https://amzn.to/2Mcjkt7).

To calibrate the voltage gain, we used the formula/info in [the app note](https://github.com/BitKnitting/energy_monitor_firmware/blob/master/docs/Atmel-46103-SE-M90E32AS-ApplicationNote.pdf) _see section 4.2.6 Voltage/Current Measurement Calibration where it discusses using existing voltage gain_

where:
- reference voltage = reading from Kill-A-Watt
- voltage measurement value = the reading for voltage we got from initializing the atm90e32 instance with the voltage gain value and reading the `line_voltageA` property.

new `VoltageGain` = reference voltage/voltage measurement * current voltage gain

e.g. using a different 9V transformer:
- Kill-A-Watt shows V = 121.5
- reading shows voltage at 117.5
- current `VoltageGain` is 36650

new `Voltage Gain = 121.5/117.5*36650 = 37898`

Calculate the value, and change the `VoltageGain` to the calculated value.
### Current Calibration
We found the default current gain gave current readings close to what we got with the Kill-A-Watt.  Because it was easy to do so, we set the `CurrentGainCT1` and `CurrentGainCT2` values to our calculation, using the current readings in place of the voltage readings as discussed in the app note.



## Initializing the ATM90e32 Class
We start by initializing an instance of the ATM90e32 twice with a time delay in between.  We have found that there are times when initializing only once fails to be able to send correct power readings.  We then go into a - hopefully - endless while True loop:
- check the system status register to verify we can "talk" correctly with the atm90e32.
- send a reading to the database.
## Get Readings

### Send Readings
The [SendReadings() class](https://github.com/BitKnitting/energy_monitor_firmware/blob/master/workspace/send_reading/send_reading.py) handles sending power readings to the database.  Given how rich the atm90e32 readings are, there are many different readings we could send back.  We chose to send only power readings since these are the only ones we need to gain and provide insight on a homeowner's energy use.  

The SendReadings() class uses the REST PUT API to send power readings to the FitHome db.  We are currently using Firebase RT.  
  
Each reading is formatted:  
```
<Firebase reference>/readings/<monitor name>/<Unix Epoch timestamp>/
{{"P": 1002.4,"I":12.2}}
```
#### Getting the Timestamp
The path to a power reading in the db includes the Unix Epoch timestamp.  To know what timestamp to use, we must:
* Know the current date/time as a timestamp. 
* Set the ESP32's date/time to the timestamp.  
* Get the timestamp from the ESP32 when sending a power reading and build up the db path.
##### Get the Current date/time from Firebase
We get the current timestamp from the Firebases db.  We do this in [`SendReadings() __init__()`](https://github.com/BitKnitting/energy_monitor_firmware/blob/master/workspace/send_reading/send_reading.py)  
```
path = 'https://fithome-9ebbd.firebaseio.com/current_timestamp/.json'
data = '{ "timestamp":{".sv": "timestamp"} }'
response = requests.put(path, data=data)
```
`response.json()` gives us the Unix Epoch timestamp. `{'timestamp': 1568821802745}`.  We then use micropython's utime library to interpret the date and time.  
##### Set the ESP32 date/time
Now we have the Epoch Unix timestamp.  We need to set the ESP32 to that time.  To do this:
* The Firebase timestamp is in ms.  We need to divide the timestamp by 1000.
* The Firebase timestamp uses a different epoch than the ESP32/micropython timestamp,  as pointed out within [the utime micropython docs](https://docs.micropython.org/en/latest/library/utime.html):
_Time Epoch: Unix port uses standard for POSIX systems epoch of 1970-01-01 00:00:00 UTC. However, embedded ports use epoch of 2000-01-01 00:00:00 UTC._
  We need to subtract out the number of seconds between the Unix and micropython Epoch time: `time_diff = 946684800`.
Using the timestamp from above:
```
import utime
ts = 1568821802745 // 1000
time_diff = 946684800
year,month,day,hour,minute,second, dayofweek,dayofyear = utime.localtime(ts-time_diff)
```
As noted in [the micropython docs](https://docs.micropython.org/en/latest/library/utime.html), "_The current calendar time may be set using machine.RTC().datetime(tuple) function..._"
```
import utime
import machine
rtc = machine.RTC()
# Set the time on the ESP32
rtc.datetime((year,month,day,dayofweek,hour,minute,second,0))
# Check to see if the time is set right.
now = utime.time()
# Ask what the current time is.
tm = utime.localtime(now)
print(tm)
```
Here's an _UGH_ we ran across... notice how `dayofweek` is in the middle of setting the datetime within the rtc instance.  This is different than what the documenation shows.  Looking at [the code](https://github.com/micropython/micropython/blob/master/ports/esp32/machine_rtc.c):
```
mp_obj_new_int(tm.tm_year),
mp_obj_new_int(tm.tm_mon),
mp_obj_new_int(tm.tm_mday),
mp_obj_new_int(tm.tm_wday),
mp_obj_new_int(tm.tm_hour),
mp_obj_new_int(tm.tm_min),
mp_obj_new_int(tm.tm_sec),
mp_obj_new_int(tv.tv_usec)
```
# Send Reading
Recall the last node on the db path to the power readings is created by a timestamp.  We need a string timestamp in Unix Epoch time.  
```
import utime
now = utime.time()
# now is the number of seconds since 2000-01-01... We need to convert tis to the number of seconds since 1970-01-01.
time_diff = 946684800
now_unix = now + time_diff
now_unix_str = str(now_unix)
```
_Note: we could multiply by 1000 to be in the same units as the Firebase timestamp (i.e.: ms).  However, we cannot find a reason to do this.  Since the monitor is the only hardware that doesn't use Unix epoch, it made sense to use Unix epoch without ms._

 

