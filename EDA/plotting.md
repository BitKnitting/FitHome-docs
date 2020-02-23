# Overview
Our goal is to provide Jupyter notebooks so that you can quickly start explore the power readings and customize the output.  We see these notebooks as a "splashing around" effort that will give you a head start in customizing your power readings analysis.
# Overview
Now that we have readings in [mongodb on the Rasp Pi](mongodb) and we can [retrieve the readings into a pandas DataFrame](readings), let's look at some plots.
# Flask + Bokeh Server
Flask is a great way to interact with the Raspberry Pi from an HTML page or a Rest API.  Bokeh is a great python plotting framework that includes a server piece so that bokeh widgets - like the date_slider widget we use - can dynamically update the interactive plot.  

The challenges we had include:  
- Getting Flask + Bokeh server to work together.
- Getting the Bokeh server to run on our 32 bit Raspian Raspberry Pi (we are using a Raspberry Pi 3 B +).
# Setup The Project
- Set up a Virtual Environment [like we did here](ElectricityMonitor/#set-up-the-repo)
Clone the [FitHome_EDA GitHub project](__TBD: LINK TO FITHOME_EDA GITHUB PROJECT__)
# app_interactive.py
Our Flask app resides in [app_interactive.py](__TBD: PERMANENT LINK ON GITHUB__)
## Starting the Flask app
We took the simplest approach to getting the Flask app running - using ```app.run```:  

```
 app.run(host='0.0.0.0', port=8000, debug=True)  
```


We'll do this by writing a Flask App that provides interesting statistics and interactive data visualization.  Our goal is not to create a polished interactive dashboard for data exploration, but rather to give you building blocks that provide enough "how to" so that you can extend any way you like.
# Set up the Project

- Set up a venv similar to what we have done for the monitor