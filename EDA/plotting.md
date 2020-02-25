
# Overview
Now that we have readings in [mongodb on the Rasp Pi](mongodb) and we can [retrieve the readings into a pandas DataFrame](readings), let's look at some plots.  We consider the code provided here as "starter code."  We are not attempting to make a stable service.  Rather, provide a useful subset of exploratory python code that can be easily modified.
## The Interactive Plot
This code will generate an interactive plot using the active power readings in the mongo database.  The Date Slider ranges from the first to the last date where there are readings.  As the user moves the Date Slider's slider, the plot is updated to the Date Slider's new date.  
![bokeh interactive plot](EDA/images/bokeh_interactive_plot.png)  
_Note: Our testing found it took an average of 7 seconds to get the readings from the mongo database and create the bokeh plot._
  
# Flask + Bokeh Server
Flask is a great way to interact with the Raspberry Pi from an HTML page or a Rest API.  Bokeh is a great python plotting framework.  Plots can be made with or without running a Bokeh Server.  We chose to run a Bokeh server within our Flask app in order to include a bokeh DateSlider widget that interacts with the plot.

## Challenges

- Getting the Bokeh server to run on our 32 bit Raspian Raspberry Pi (we are using a Raspberry Pi 3 B +).
# Setup The Project
Clone the [FitHome_EDA GitHub project](https://github.com/BitKnitting/FitHome_EDA) and set up a Virtual Environment for the FitHome_EDA project [like we did for the electricity monitor](ElectricityMonitor/#set-up-the-project).

# The Flask App
Our Flask app resides in [app_interactive.py](https://github.com/BitKnitting/FitHome_EDA/blob/c2c178e566770c67903ccd75fdf21d45ab9c697a/app_interactive.py)
# Bokeh Server
A significant portion of the Flask app is devoted to setting up and maintaining access to the bokeh server.  We used [bokeh's flask_embed.py](https://github.com/bokeh/bokeh/blob/1.4.0/examples/howto/server_embed/flask_embed.py) example as our initial template.  

## Web Sockets
The bokeh server uses web sockets to communicate with the widgets on the HTML page.  We were unfamiliar with web sockets and found the following articles useful:  
- [Websockets Server Tutorial with code](https://os.mbed.com/cookbook/Websockets-Server)  As with bokeh, this example uses the [Tornado library](https://www.tornadoweb.org/en/stable/guide/intro.html) to implement a websocket server.  
- [Control Raspberry Pi GPIOs with WebSockets (taking a closer look)](https://www.hackster.io/dataplicity/control-raspberry-pi-gpios-with-websockets-af3d0c#toc-taking-a-closer-look-4) This part of the article does a great job at providing images with text to walk us through web sockets using the Tornado framework.

## The Server Starts Here  
The template code - flask_embed.py - is not documented.  From what we can gather, the important stuff to know includes:
## The Server() Instance
```  
Thread(target=bk_worker).start() -> bk_worker() -> server.start()....
```  
starts the bokeh server in motion.  A key line to understand is in ```bk_worker()```:  
  
```
server = Server({'/bkapp': make_interactive}, io_loop=IOLoop(), allow_websocket_origin=["*"])  
```
We are instantiating a bokeh Server with the ```/bkapp``` route associated to the ```make_interactive``` function.  We allow_websocket_origin to "*" to avoid getting messages similar to ```ERROR - Refusing websocket connection from Origin 'http://x.x.x.x:' ```.
## server_document
A key concept in understanding bokeh is to understand a [bokeh document](https://docs.bokeh.org/en/latest/docs/reference/document.html?highlight=documents).  _...which is a container for Bokeh Models to be reflected to the client side BokehJS library...A Bokeh Document is a collection of Bokeh Models (e.g. plots, tools, glyphs, etc.)_  The ```script``` that will be inserted into our ```interactive.html``` template:  
  
```
script = server_document("http://192.168.86.249:5006/bkapp")  
```  
Tells the client (HTML) that our interactive bokeh plot is located at this URL - our Rasp Pi server. From bokeh's ```server.py``` code:  
  
```
   Args:
        url (str, optional) :
            A URL to a Bokeh application on a Bokeh server (default: "default")

            If ``"default"`` the default URL ``{DEFAULT_SERVER_HTTP_URL}`` will be used.
```
_Note: you may want to use the environment variable, a name, or a static IP to avoid IP reassignment._   
# Bokeh Interactive Plot
The code that creates the interactive plot is found in [```flask_make_interactive.py```](__TBD: GITHUB LOCATION__).  There is a ```flask_make_interactive.py``` and a ```bokeh_make_interactive.py``` because the bokeh server + flask uses a different method to pass around the document (e.g.: ```bokeh_make_interactive.py``` uses ```curdoc```).
## UHOH - RASP PI is 32 bit!
The Rasp Pi 3 uses a 32 bit operating system.  Bokeh assumes a 64 bit OS.  For the most part, we are ok.  Where we run into challenges is with the bokeh ```DateSlider``` widget.  When the bokeh server handles the date that the user slid the slider to on the html client, it sends a 64 bit timestamp.  
_NOTE: I thought the code I changed was in sliders.py...now I can't find it...ignoring for now._